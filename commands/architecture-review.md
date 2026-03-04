---
name: architecture-review
description: Auditoria DRY-RUN do projeto — mapeia entropia, propõe reorganização (não altera código).
---

# architecture-review — AUDITORIA + REORGANIZAÇÃO GOVERNADA DO PROJETO (HARD STOP)

Versão: 1.2  
Tipo: Command  
Uso: Manual (invocado pelo usuário)  
Modo: AUDITORIA / DRY-RUN (SIMULAÇÃO — NÃO MODIFICA ARQUIVOS)

---

## OBJETIVO

Executar uma **auditoria completa e governada** do projeto para:

- mapear a arquitetura real (estado factual do código)
- inventariar arquivos e diretórios
- detectar entropia (duplicação, órfãos, lixo seguro)
- propor reorganização segura (mover / consolidar / arquivar)
- propor deleções apenas com evidência objetiva
- comparar estado real × roadmap
- sugerir melhorias de alto impacto e baixo risco

Este command **NUNCA executa mudanças automaticamente**.  
Ele produz **apenas um PLANO auditável**.

---

## RELAÇÃO COM CONTEXT ENGINEERING

Este command é **específico para reorganização arquitetural**. Para outros tipos de auditoria técnica, considere:

- **`context-audit`** — Para auditorias técnicas gerais, investigações, análise de evidências
  - Modo assistido interativo
  - Foco em evidências objetivas
  - Processo estruturado com Context Pack AUDIT

**Diferença:**
- `architecture-review`: Foco em **reorganização arquitetural** (mover arquivos, consolidar, arquivar)
- `context-audit`: Foco em **auditoria técnica** (investigar problemas, validar integridade, analisar performance)

Ambos podem ser usados em conjunto ou separadamente, dependendo do objetivo.

---

## PRÉ-REQUISITO OBRIGATÓRIO (HARD STOP)

Antes de iniciar a auditoria, o usuário deve executar:

- `checkpoint-and-branch`

### Confirmação exigida

A IA deve exigir que o usuário tenha fornecido:

- saída de `git status` (worktree limpo)
- saída de `git log -1 --oneline` (commit iniciando com `checkpoint:`)
- branch dedicado (`chore/*`)

❌ Sem essa confirmação, a auditoria é BLOQUEADA.

---

## REGRA DE ALERTA — CHECKPOINT GRANDE (GUARDRAIL)

Se o checkpoint reportado indicar:

- número elevado de arquivos alterados **OU**
- volume expressivo de linhas alteradas

A IA deve registrar explicitamente:

⚠️ **ALERTA:** checkpoint de grande volume.  
Rollback e revisão exigem atenção redobrada.

Este alerta **não bloqueia** a auditoria, mas deve constar no relatório.

---

## COMPORTAMENTO OBRIGATÓRIO DA IA

Ao receber `architecture-review`, a IA deve:

1. Entrar em **modo DRY-RUN**
2. Usar **Sequential Thinking**
3. NÃO mover arquivos
4. NÃO renomear arquivos
5. NÃO deletar arquivos
6. NÃO alterar código
7. NÃO criar novos scripts
8. NÃO atualizar documentação
9. Produzir relatório completo, estruturado e auditável
10. Finalizar com **HARD STOP**, pedindo autorização explícita

Qualquer modificação real neste modo é **ERRO DE GOVERNANÇA**.

---

## ESCOPO DA AUDITORIA (CHECKLIST OBRIGATÓRIO)

### A) INVENTÁRIO DO PROJETO

Para cada diretório relevante:

- propósito (1 linha)
- classificação:
  - ✅ PERMANENTE (canônico)
  - 🟡 TRANSITÓRIO (a consolidar / promover / arquivar)
  - ♻️ DUPLICADO
  - 🧟 ÓRFÃO
  - 🗑️ DELETÁVEL (baixo risco)

---

### B) ARQUITETURA REAL

- camadas existentes (ingestão, core, match, analytics, api, frontend)
- entrypoints:
  - endpoints
  - scripts CLI
  - jobs agendados
- contratos:
  - tabelas
  - views
  - funções
  - APIs

---

### C) DETECÇÃO DE ÓRFÃOS (OBRIGATÓRIA)

Um arquivo só pode ser classificado como órfão se **AO MENOS DUAS evidências** forem atendidas:

- não importado por nenhum módulo
- não chamado por nenhum entrypoint / scheduler / job
- não referenciado em `docs/`
- não citado em README / runbooks
- não mencionado no roadmap

A IA deve listar **explicitamente** quais evidências se aplicam.

---

### D) DETECÇÃO DE DUPLICAÇÃO (OBRIGATÓRIA)

Identificar:

- scripts com mesma finalidade
- múltiplas versões da mesma lógica
- documentos sobrepostos

Indicar:

- arquivos envolvidos
- recomendação (MERGE / KEEP / DELETE)
- risco

---

### E) DOCUMENTAÇÃO (GOVERNANÇA)

- `docs/` é SEMPRE canônico
- `_scratchpad/` é SEMPRE transitório

A IA deve:

- identificar conteúdo do `_scratchpad` elegível à promoção
- sugerir destino correto dentro de `docs/`
- apontar documentação oficial duplicada ou fragmentada

---

### F) ROADMAP × REALIDADE

- itens no roadmap sem implementação
- implementações sem registro no roadmap
- proposta de ajustes objetivos no roadmap

---

### G) MELHORIAS DE ALTA ALAVANCAGEM (TOP 5)

Somente melhorias:

- de alto impacto
- baixo risco
- custo baixo ou médio
- sem refatoração estrutural ampla

---

## SAÍDA OBRIGATÓRIA (FORMATO)

O relatório DEVE conter, nesta ordem:

### 1) STATUS DO CHECKPOINT

- branch
- commit
- worktree
- alerta de volume (se aplicável)

---

### 2) MAPA DE ARQUITETURA (RESUMO)

- visão por camadas
- fluxo principal de dados
- pontos críticos conhecidos

---

### 3) INVENTÁRIO E CLASSIFICAÇÃO

Tabela/lista por diretório com:
- classificação
- justificativa curta

---

### 4) PLANO DE REORGANIZAÇÃO (DRY-RUN)

Tabela com:

- AÇÃO: MOVE | RENAME | ARCHIVE | MERGE | KEEP
- ATUAL
- SUGERIDO
- MOTIVO
- RISCO: baixo | médio | alto
- EVIDÊNCIAS (mínimo 2)

---

### 5) LISTA DE DELEÇÃO SEGURA (SEPARADA)

Somente itens com:

- risco baixo
- evidências explícitas (mínimo 2)

---

### 6) GAP ANALYSIS — ROADMAP

- faltas
- excessos
- ajustes sugeridos

---

### 7) MELHORIAS RECOMENDADAS (TOP 5)

Cada item com:
- benefício
- custo (baixo/médio)
- impacto

---

### 8) PRÓXIMO PASSO (HARD STOP)

Encerrar obrigatoriamente com:

⛔ **Execução BLOQUEADA.**  
Este relatório é um DRY-RUN.  
Confirme explicitamente se deseja executar o plano em etapas governadas.

---

## EXECUÇÃO APÓS APROVAÇÃO (REFERÊNCIA)

Se aprovado pelo usuário, a execução deve seguir:

1. MOVE / ARCHIVE (sem deletar)
2. Consolidação de docs + roadmap
3. DELETE baixo risco
4. Verificação final (scripts/testes canônicos)

Cada etapa:
- commit atômico
- reversível
- sem "mega commit"

---

## REGRA FINAL DO COMMAND

Sem `checkpoint-and-branch` confirmado + autorização explícita do usuário,  
nenhuma modificação é permitida.

---

=== FIM DO COMMAND architecture-review v1.2 ===
