---
name: create-execution-plan
description: Cria execution plans otimizados para IA executar tarefas multi-etapas com suporte a mapeamento de codebase grande e execucao paralela via subagentes. Use quando receber pedido de planejamento, correcao de bug complexo, feature multi-etapas, refatoracao de codigo grande, ou qualquer tarefa que precise de plano antes de executar. Aplica automaticamente ao detectar /create-execution-plan.
---

# Create Execution Plan v3.0 (orquestrado only)

## APLICACAO AUTOMATICA OBRIGATORIA

**Esta skill DEVE ser aplicada automaticamente SEMPRE que detectar:**

- Pedido de planejamento ou plano de execucao
- Correcao de bug complexo (multiplos arquivos/camadas)
- Feature com mais de 3 tarefas
- Tarefa que envolve backend + frontend + banco
- Refatoracao de codebase grande (>500 linhas ou >10 arquivos)
- Comando `/create-execution-plan`

## Quando NAO usar

- Tarefa com 1-2 passos triviais (ex: renomear variavel, ajustar texto)
- Hotfix de uma linha com causa raiz obvia
- Tarefas puramente exploratorias (usar `_scratchpad`)
- Ajuste de configuracao ou .env

## O que e um Execution Plan

Documento estruturado que permite a uma IA executar tarefas complexas **sem ambiguidade**. Cada tarefa contem contexto suficiente (arquivo, linha, codigo exato) para execucao direta.

**Principio:** A IA que executa o plano pode ser DIFERENTE da que criou. O plano deve ser autocontido.

---

## Fase 0 — Mapeamento de Codebase (OBRIGATORIO para codigo grande)

### Quando ativar

- Codebase desconhecido ou parcialmente conhecido pela IA
- Refatoracao que envolve >10 arquivos ou >500 linhas
- O usuario fornece codigo grande e pede plano
- A IA nao tem contexto suficiente para planejar com confianca

### Problema que resolve

Ler todo o codigo na mesma conversa **estoura o contexto**. A Fase 0 constroi um mapa leve do codebase ANTES de planejar, sem colar codigo inteiro.

### Estrategia: Mapeamento Progressivo com Subagentes

**Passo 1 — Estrutura superficial (agente pai)**

O agente pai coleta a arvore de diretorios e identifica dominios:

```
Ler estrutura de pastas (ls/tree superficial)
Identificar dominios: src/services/, src/api/, src/core/, etc.
Listar arquivos por dominio (nome + tamanho em linhas)
```

Resultado: mapa de dominios com lista de arquivos (SEM ler conteudo).

**Passo 2 — Mapeamento profundo por dominio (subagentes paralelos)**

Disparar ate 4 subagentes simultaneos via Task tool, cada um mapeando um dominio:

```
Task(subagent_type="explore", prompt="Mapeie src/services/: para cada arquivo, liste classes, funcoes publicas, dependencias entre modulos, e um resumo de 1 linha do proposito. NAO retorne codigo completo, apenas assinaturas e resumos.")

Task(subagent_type="explore", prompt="Mapeie src/core/: ...")

Task(subagent_type="explore", prompt="Mapeie src/api/: ...")
```

Cada subagente retorna um **resumo compacto** (~50-100 linhas), nao o codigo inteiro.

**Passo 3 — Consolidacao (agente pai)**

O agente pai recebe os resumos e consolida em um **Mapa do Codebase**:

```markdown
## Mapa do Codebase

### src/services/ (4 arquivos, ~800 linhas)
- payment_service.py: ProcessPayment, RefundPayment. Depende de: core/exceptions, adapters/bmp
- pix_service.py: CreateQR, Transfer, Refund. Depende de: adapters/bmp, core/config
...

### src/core/ (3 arquivos, ~200 linhas)
- config.py: Settings via env vars
- exceptions.py: BmpApiError, AccountNotFoundError, PersistenceError
...

### Dependencias entre dominios
services → core (config, exceptions)
services → adapters (bmp)
api → services (injecao)
```

**Passo 4 — Leitura cirurgica**

Com o mapa em maos, o agente pai le APENAS os arquivos relevantes para a tarefa (nao o codebase inteiro).

### Resultado da Fase 0

O mapa e incluido na secao "Contexto / Diagnostico" do execution plan. Ele permite:
- Planejar tarefas com caminhos exatos
- Identificar dependencias entre arquivos
- Saber o que pode ser paralelizado (arquivos independentes)

---

## Anatomia Obrigatoria do Execution Plan

Todo execution plan DEVE conter estas secoes, nesta ordem:

### 1. Cabecalho

```markdown
# Plano: [Nome descritivo em snake_case]

Fase: [N ou Hotfix]
Versao: 1.0
Status: NAO INICIADO | EM ANDAMENTO | CONCLUIDO
Modo de execucao: ORQUESTRADO (fixo)
Ultima atualizacao: YYYY-MM-DD
Pre-requisitos: [lista ou "nenhum"]
```

### 2. Objetivo (1-2 frases)

O resultado esperado ao concluir o plano. Sem jargao, direto.

### 3. Diagnostico / Contexto

**Para bugs/hotfixes (OBRIGATORIO com evidencias):**

- **Logs reais** (copiar trecho relevante do erro)
- **Queries** (resultado de consultas ao banco)
- **Codigo** (trecho exato onde o problema ocorre, com arquivo e linha)
- **Mapa de dados** (tabela mostrando o que existe vs o que falta)

**Para features/refatoracoes:**

- Situacao atual (o que existe)
- Mapa do codebase (se Fase 0 foi executada)
- Regras de negocio relevantes

**Regra:** Diagnostico sem evidencia e achismo. SEMPRE incluir evidencia para bugs.

### 4. Decisoes tecnicas

```markdown
| Decisao | Escolha | Justificativa |
|---------|---------|---------------|
```

### 5. Tarefas (formato T1..TN)

Cada tarefa DEVE conter TODOS os campos abaixo:

```markdown
### T1 -- [Nome claro da tarefa]

- **Complexidade:** minima | baixa | media | alta
- **Estimativa:** ~15min | ~1h | ~2-3h
- **Arquivos:** [lista exata de arquivos criados/modificados]
- **Depende de:** nenhuma | T0 | T1, T2
- **Conflita com:** nenhuma | T3 (mesmo arquivo)
- **Status:** pendente | em andamento | concluido
- **Criterio de aceite:** [frase que confirma que esta pronto]
- **Notas para IA:**

  [Contexto especifico que a IA precisa para executar esta tarefa.
   DEVE incluir:
   - Caminho exato do arquivo
   - Linha ou bloco de codigo a alterar (quando aplicavel)
   - Snippet "antes" e "depois" (quando a alteracao e pontual)
   - Restricoes especificas desta tarefa
   - Qualquer ID, constante ou convencao necessaria
   - Regras de negocio que impactam esta tarefa]
```

**Campo "Conflita com":** Indica tarefas que tocam o MESMO arquivo. Tarefas que conflitam NAO podem rodar em paralelo, mesmo que nao tenham dependencia logica.

**Regra de ouro das Notas para IA:** Se uma IA diferente pegar apenas esta tarefa, ela DEVE conseguir executar sem ler o resto do plano.

**Exemplo de Notas para IA (boa):**

```markdown
- **Arquivo:** `src/services/pix_service.py` (linha ~45, metodo `create_transfer`)
- **Antes:** `data = {"valor": amount, "chave": key}`
- **Depois:** `data = {"valor": amount, "chave": key, "codigoOperacaoCliente": reference_id}`
- **Restricao:** campo e opcional; se nao informado, gerar UUID v4
- **Constante:** `from uuid import uuid4`
- **Convencao:** valores monetarios em Decimal, NAO centavos
```

### 6. Ordem de execucao

```markdown
## Ordem de execucao

Bloco A (CRITICO — paralelo):
  T1 + T2 + T3

Bloco B (SEQUENCIAL):
  T4 → T5

Bloco C (paralelo):
  T6 + T7

Sequencia completa:
  [T1 + T2 + T3] → [T4 → T5] → [T6 + T7] → verificacao final
```

Notacao:
- `+` = paralelo (podem rodar simultaneamente)
- `→` = sequencial (depende do anterior)
- `[ ]` = bloco nomeado
- Tarefas com "Conflita com" NUNCA ficam no mesmo grupo paralelo

### 7. Notas para IA (globais)

Contexto critico que afeta TODAS as tarefas:

- IDs de recursos (Supabase project ID, merchant IDs, etc.)
- Convencoes do projeto (logger, exceptions, patterns)
- Restricoes globais (PCI, nao criar arquivos, etc.)
- Deploy info (Docker, portas, dominios)
- Skills a aplicar (`code-with-logs` para scripts, `create-subagents` para scripts >200 linhas)

### 8. Metricas de sucesso

Lista de itens verificaveis que confirmam conclusao:

```markdown
- [metrica objetiva 1]
- [metrica objetiva 2]
```

**Regra:** Metricas devem ser verificaveis (testavel, consultavel, observavel). "Funcionar" NAO e metrica.

### 9. Rollback / Contingencia

**Obrigatorio para:** migrations, alteracoes em producao, mudancas de schema.
**Opcional para:** features novas, refatoracoes internas.

```markdown
## Rollback

- **Backup necessario antes:** [sim/nao — o que fazer]
- **Como reverter:** [passos de rollback]
- **Ponto de nao-retorno:** [a partir de qual tarefa nao da para reverter facilmente]
```

---

## Modo de Execucao: ORQUESTRADO (unico)

A IA orquestradora dispara subagentes paralelos para blocos de tarefas independentes, tudo na mesma conversa do Cursor. Nao ha modo manual nem hibrido — sempre orquestrado.

**Como funciona:**

```
1. IA orquestradora le o plano e identifica o proximo bloco
2. Para blocos paralelos: dispara ate 4 subagentes via Task tool
3. Cada subagente recebe prompt autocontido com:
   - Notas para IA da tarefa especifica
   - Notas globais do plano
   - Instrucoes de conclusao (marcar status, NAO commitar)
4. IA orquestradora aguarda todos os subagentes
5. Consolida resultados, resolve conflitos se houver
6. Commita o bloco inteiro com mensagem descritiva
7. Avanca para o proximo bloco
```

**Regras do modo orquestrado:**
- Maximo 4 subagentes simultaneos (limite do Cursor)
- Tarefas que conflitam em arquivo NUNCA rodam em paralelo
- Subagentes NAO commitam — apenas o orquestrador commita
- Se um subagente falhar, o orquestrador para e reporta

**Template de prompt para subagente:**

```
Execute a tarefa {TN} do plano {caminho_do_plano}.

Notas para IA da tarefa:
{notas_da_tarefa}

Contexto global:
{notas_globais}

Regras:
- Execute SOMENTE esta tarefa
- NAO commite (o orquestrador fara isso)
- Marque a tarefa como "concluido" no plano
- Retorne: arquivos criados/alterados e resultado
```

---

## Sub-planos

### Quando usar

- Plano com mais de 10 tarefas
- Blocos independentes que podem ser executados em paralelo por agentes diferentes
- Tarefas com dominios distintos (ex: backend vs frontend vs banco)

### Formato

Plano principal referencia sub-planos por arquivo:

```markdown
## Sub-planos

- Bloco A (Backend): `execution_plans/plano_bloco_a.md`
- Bloco B (Frontend): `execution_plans/plano_bloco_b.md`
```

Cada sub-plano segue o mesmo formato (cabecalho, tarefas, notas para IA, metricas).

---

## Prompt de Execucao (OBRIGATORIO — orquestrado only)

Apos salvar o execution plan, SEMPRE gerar o arquivo de prompt de execucao.

**Salvar em:** `docs/04_operations/prompts_execucao_{NNN}_{nome_snake_case}.md` (mesmo numero do plano)

### Estrutura obrigatoria

O arquivo contem um **unico bloco de texto** copiavel. Nao ha prompt manual — apenas orquestrado.

```markdown
# Prompts de Execucao — [Nome do Plano]

> **Runbook operacional.** Copie o bloco abaixo inteiro, cole numa conversa nova, execute.
> Ultima atualizacao: YYYY-MM-DD

## PROMPT ORQUESTRADO (copie e cole)

```
[bloco de codigo — prompt 100% direcionado a IA orquestradora]
```

## Tabela de tarefas
## Ordem de execucao
## Mapa de conflitos de arquivo
## Estimativa
```

### Conteudo do prompt (obrigatorio)

O bloco DEVE incluir inline (sem depender de context-boot):

1. **Caminho do execution plan** — para a IA ler as Notas para IA da tarefa
2. **Instrucao de identificacao** — encontrar proxima tarefa/bloco pendente
3. **Caminhos do projeto** — workspace, referencias somente leitura
4. **Contexto critico** — schema, logger, exceptions, convencoes obrigatorias
5. **Contexto especifico do plano** — decisoes tecnicas, formatos de dados, regras de negocio, mapeamento de campos, constantes/IDs
6. **Instrucoes pos-conclusao** — marcar concluido, commitar, push, mostrar resultado
7. **Instrucao de paralelismo** — identificar bloco paralelo e disparar subagentes via Task tool
8. **Template de prompt para subagente** — com placeholders para notas da tarefa
9. **Regra de conflito** — tarefas que compartilham arquivo rodam sequencialmente
10. **Regra de commit** — subagentes NAO commitam, orquestrador commita o bloco

### Mapa de conflitos de arquivo

Tabela que mostra quais tarefas tocam os mesmos arquivos:

```markdown
| Arquivo | Tarefas que alteram |
|---------|---------------------|
| src/services/pix_service.py | T4, T5 |
| src/api/routes.py | T3, T6 |
```

Tarefas que aparecem na mesma linha NAO podem rodar em paralelo.

### Regras do prompt de execucao

- O prompt DEVE ser autocontido — colar numa conversa nova basta
- Nao depender de context-boot ou context.md (incluir contexto inline)
- Incluir convencoes especificas que impactam a execucao
- Um bloco por conversa, um commit por bloco (orquestrador commita)

---

## Integracao com Skills

Ao gerar o plano, referenciar skills aplicaveis nas Notas para IA:

| Contexto da tarefa | Skill a referenciar |
|--------------------|---------------------|
| Script Python executavel | `code-with-logs` |
| Script >200 linhas ou multi-etapas | `create-subagents` |
| Documentacao | `create-documentation` |
| Migration SQL | Padrao Validacao → Aplicacao → Verificacao |
| Tratamento de erros | `error-handling-patterns` |

---

## Workflow de Criacao

1. **Avaliar escopo** — codebase grande? → Ativar Fase 0 (Mapeamento)
2. **Fase 0 (se aplicavel)** — mapear codebase com subagentes paralelos
3. **Ler contexto** — context.md ou context-boot do projeto
4. **Analisar pedido** — entender escopo e tipo (bug, feature, refactor, migracao)
5. **Coletar evidencias** — logs, queries, codigo (OBRIGATORIO para bugs)
6. **Gerar diagnostico** — causa raiz documentada (ou contexto para features)
7. **Planejar tarefas** — T1..TN com Notas para IA detalhadas
8. **Mapear conflitos** — quais tarefas tocam os mesmos arquivos?
9. **Definir ordem** — grafo de dependencias + blocos paralelos
10. **Definir metricas** — como saber que deu certo
11. **Definir rollback** — como reverter se der errado (quando aplicavel)
12. **Salvar** — em `docs/00_overview/execution_plans/{NNN}_{nome}.md` (ver Regra de Numeracao abaixo)
13. **Gerar prompt de execucao** — em `docs/04_operations/prompts_execucao_{NNN}_{nome}.md` (mesmo numero do plano, unico bloco orquestrado)
14. **Apresentar ao usuario** — resumo + pedir confirmacao antes de executar
15. **NAO executar sem confirmacao explicita**

---

## Regra de Numeracao de Arquivos

Todo execution plan e seu prompt de execucao DEVEM receber um numero sequencial de 3 digitos com zero-padding.

### Como numerar

1. Contar quantos arquivos `*.md` existem em `docs/00_overview/execution_plans/`
2. Proximo numero = contagem + 1 (com zero-padding: `001`, `002`, ..., `010`, `011`, etc.)
3. Usar o MESMO numero para o plano e para o prompt de execucao

### Formato dos nomes

```
docs/00_overview/execution_plans/NNN_nome_snake_case.md
docs/04_operations/prompts_execucao_NNN_nome_snake_case.md
```

### Exemplos

```
docs/00_overview/execution_plans/001_checkout_multi_meio.md
docs/04_operations/prompts_execucao_001_checkout_multi_meio.md

docs/00_overview/execution_plans/002_banking_basico.md
docs/04_operations/prompts_execucao_002_banking_basico.md

docs/00_overview/execution_plans/010_refatoracao_pagamentos.md
docs/04_operations/prompts_execucao_010_refatoracao_pagamentos.md
```

### Beneficio

O numero garante que ao abrir a pasta vc ve os planos em ordem cronologica de criacao.
O arquivo com numero mais alto e sempre o mais recente.

---

## Anti-patterns (PROIBIDOS)

- Tarefa sem "Notas para IA" (IA nao sabe o que fazer)
- Plano de bug sem diagnostico com evidencia
- Dependencias implicitas (T3 precisa de T1 mas nao declara)
- Conflitos de arquivo nao mapeados (T2 e T4 editam mesmo arquivo mas estao em bloco paralelo)
- Metricas genericas ("funcionar", "ficar bom")
- Tarefas vagas ("melhorar o codigo")
- Plano com mais de 10 tarefas sem sub-planos
- Notas para IA sem caminhos de arquivo
- Snippets de codigo sem indicar onde inserir (antes/depois de qual bloco)
- Tentar ler codebase inteiro sem Fase 0 (estoura contexto)
- Subagentes paralelos editando o mesmo arquivo (conflito de escrita)

---

## Regra Final

Plano sem contexto e promessa vazia.
Tarefa sem "Notas para IA" e tarefa que vai falhar.
Fase 0 economiza contexto. Modo orquestrado economiza tempo.
**Execution plan e contrato entre quem planeja e quem executa.**
