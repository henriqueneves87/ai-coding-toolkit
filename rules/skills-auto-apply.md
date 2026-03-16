---
description: Tabela de auto-aplicação de skills e lista de agents disponíveis
alwaysApply: true
---

# Skills — Auto-Aplicação

| Contexto | Skill |
|----------|-------|
| Script Python executável (`main()`, `if __name__`) | `code-with-logs` |
| Script >200 linhas ou múltiplas etapas | `create-subagents` |
| Criação/atualização de docs | `create-documentation` |
| Código com try/except ou I/O | `error-handling-patterns` |
| Criação de testes | `testing-patterns` |
| Commits, branches, PRs | `git-workflow` |
| UI/UX em React/Next.js | `frontend-conventions` |
| Planejamento ou tarefa multi-etapas | `create-execution-plan` |
| Revisao pos-execucao via `/review-execution` | `review-execution` |
| context.md > 300 linhas ou `/optimize-context` | `optimize-context` |
| Auditoria de sistema legado via `/legacy-audit` | `legacy-audit` |

# Agents Disponíveis

- `debugger` — bugs e comportamento inesperado
- `test-writer` — criação de testes
- `code-reviewer` — revisão de qualidade e segurança
- `migration-applier` — migrations SQL
- `context-audit-agent` — auditorias evidence-first
- `context-feature-agent` — features spec-first
- `context-migration-agent` — migrations safety-first
- `context-refactor-agent` — refatoração incremental
