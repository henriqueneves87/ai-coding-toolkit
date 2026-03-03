# create-execution-plan — Gerar Execution Plan Otimizado para IA

Versao: 2.0
Tipo: Command
Uso: Manual (invocado pelo usuario)
Escopo: Planejamento de tarefas multi-etapas, correcoes de bug, features, refatoracoes, mapeamento de codebase grande

---

## Objetivo

Gerar um execution plan estruturado e detalhado que qualquer IA consiga executar sem ambiguidade, com suporte a mapeamento de codebase grande (Fase 0) e execucao paralela via subagentes (modo orquestrado). O plano e salvo em `docs/00_overview/execution_plans/` com numeracao sequencial e segue o formato padrao da skill `create-execution-plan` v2.0.

---

## Uso

```
/create-execution-plan [descricao da tarefa]
```

Variantes:

```
/create-execution-plan --map [descricao]     → Forca Fase 0 (mapeamento de codebase)
/create-execution-plan --parallel [descricao] → Forca modo orquestrado
```

---

## Comportamento OBRIGATORIO

Ao receber este comando, a IA DEVE aplicar a skill `create-execution-plan` v2.0 e seguir este fluxo:

### 1. Avaliar escopo e decidir Fase 0

| Situacao | Fase 0? |
|----------|---------|
| Codebase desconhecido ou >10 arquivos envolvidos | SIM |
| Refatoracao de codigo grande (>500 linhas) | SIM |
| Flag `--map` presente | SIM |
| Bug pontual com arquivos conhecidos | NAO |
| Feature pequena com contexto claro | NAO |

Se Fase 0 ativada: executar mapeamento progressivo com subagentes (ver skill).

### 2. Carregar contexto do projeto

- Ler `docs/00_overview/context.md` (se existir)
- Se nao existir, executar context-boot minimo (estrutura + stack + estado)
- Identificar: stack, convencoes, banco, deploy

### 3. Classificar o tipo de tarefa

| Tipo | Requer diagnostico? | Foco |
|------|---------------------|------|
| Bug/Hotfix | SIM (com evidencias) | Causa raiz + correcao |
| Feature | NAO (contexto basta) | Especificacao + implementacao |
| Refatoracao | PARCIAL (estado atual + Fase 0) | Antes/depois + criterio |
| Migracao | SIM (estado banco) | Validacao + aplicacao + verificacao |

### 4. Coletar evidencias (para bugs)

ANTES de planejar, a IA DEVE:

- Ler os logs de erro fornecidos pelo usuario
- Consultar banco se necessario (via MCP Supabase)
- Ler os arquivos de codigo envolvidos
- Mapear: o que existe vs o que deveria existir

**NUNCA pular esta etapa para bugs. Plano sem diagnostico e achismo.**

### 5. Gerar o plano

Aplicar a skill `create-execution-plan` e gerar o documento com TODAS as secoes obrigatorias:

1. Cabecalho (fase, versao, status, modo de execucao, pre-requisitos)
2. Objetivo (1-2 frases)
3. Diagnostico/Contexto (com evidencias para bugs, mapa do codebase para refatoracoes)
4. Decisoes tecnicas (tabela)
5. Tarefas T1..TN (com Notas para IA detalhadas, estimativa, campo "Conflita com")
6. Ordem de execucao (grafo de dependencias com blocos paralelos)
7. Notas para IA globais (IDs, convencoes, restricoes)
8. Metricas de sucesso (verificaveis)
9. Rollback/Contingencia (quando aplicavel)

### 6. Escolher modo de execucao

| Criterio | Modo |
|----------|------|
| Tarefas precisam de revisao humana entre etapas | MANUAL |
| Blocos paralelos claros, sem conflito de arquivo | ORQUESTRADO |
| Mix: parte precisa revisao, parte pode ser automatizada | HIBRIDO |
| Flag `--parallel` presente | ORQUESTRADO |
| Padrao (sem flag) | MANUAL |

### 7. Salvar e apresentar

- Determinar proximo numero sequencial: contar `.md` em `docs/00_overview/execution_plans/` + 1 (zero-padding de 3 digitos: `001`, `002`, etc.)
- Salvar em `docs/00_overview/execution_plans/{NNN}_{nome_snake_case}.md`
- Apresentar resumo ao usuario:
  - Tabela de tarefas com estimativas
  - Ordem de execucao (blocos paralelos destacados)
  - Mapa de conflitos de arquivo
  - Modo de execucao escolhido
- **PERGUNTAR:** "Posso executar o plano? Modo: [MANUAL/ORQUESTRADO/HIBRIDO]"
- **NAO executar sem confirmacao explicita**

### 8. Gerar Prompt Universal de execucao

APOS salvar o execution plan, gerar AUTOMATICAMENTE o arquivo de prompt universal:

- Usar o MESMO numero do plano gerado acima
- Salvar em `docs/04_operations/prompts_execucao_{NNN}_{nome_snake_case}.md`
- Seguir o modelo de `docs/04_operations/prompts_execucao_saphiro.md` (se existir no projeto)
- Incluir AMBOS os prompts: manual e orquestrado
- Incluir mapa de conflitos de arquivo
- Ver estrutura completa na skill `create-execution-plan` v2.0

---

## Integracao com Skills

Ao gerar tarefas, referenciar skills aplicaveis:

| Tarefa envolve | Skill a referenciar |
|----------------|---------------------|
| Script Python executavel | `code-with-logs` |
| Script >200 linhas | `create-subagents` |
| Migration SQL | Padrao Validacao → Aplicacao → Verificacao |
| Documentacao | `create-documentation` |
| Tratamento de erros | `error-handling-patterns` |

---

## Integracao com Commands

Este command pode ser combinado com:

- `/governed-task` — para tarefas governadas que precisam de plano
- `/context-boot` — para carregar contexto antes de planejar
- `/apply-conventions` — validacao pre-geracao apos plano aprovado

---

## Exemplos de uso

### Bug com diagnostico

```
/create-execution-plan corrigir erros de pagamento no checkout que aparecem sequencialmente

[usuario cola logs de erro]
```

**Resultado:** Plano com diagnostico (mapa de campos faltando), tarefas com snippets exatos, ordem de execucao, metricas de sucesso. Salvo como `001_checkout_erros_pagamento.md`. Modo manual.

### Refatoracao de codebase grande

```
/create-execution-plan --map refatorar modulo de pagamentos para separar responsabilidades
```

**Resultado:** Fase 0 ativada (mapeamento com subagentes), plano com mapa do codebase, tarefas com antes/depois, blocos paralelos. Salvo como `002_refatoracao_pagamentos.md`. Modo orquestrado.

### Feature com execucao paralela

```
/create-execution-plan --parallel implementar modulo de boletos com registro, consulta, cancelamento e impressao
```

**Resultado:** Plano com tarefas independentes em blocos paralelos, prompt orquestrado para subagentes. Salvo como `003_modulo_boletos.md`. Modo orquestrado.

---

## Regra Final

Plano bom economiza horas de debug.
Plano ruim gera retrabalho.
Fase 0 economiza contexto. Modo orquestrado economiza tempo.
**Investir 10 minutos no plano salva 2 horas na execucao.**

--- End Command ---
