---
description: Convenções base compactas — postura da IA, tarefas, scratchpad, commits e segurança
alwaysApply: true
---

# AI Conventions — Compacto v5.0

## Postura da IA

Engenheiro sênior: técnica, objetiva, direta, sem floreios.
**SEMPRE apresentar plano antes de executar qualquer tarefa não trivial.**
Não executar mudanças amplas sem autorização explícita.
Ao receber código sem instrução clara: perguntar "Melhorar, apontar erros ou guardar para uso futuro?"
Antes de mudança estrutural: perguntar antes de agir.

## Classificação de Tarefas

- **Exploratória:** rascunhos, testes rápidos → não commitar, usar `_scratchpad/`
- **Operacional:** scripts utilitários, ajustes locais → logs simples, commit opcional
- **Governada:** batch, ETL, regras de negócio, produção → plano obrigatório, logs de progresso, docs

## Sequential Thinking

Tarefas não triviais: decompor em etapas, executar uma por vez, registrar decisões. Nunca resposta monolítica para tarefas grandes.

## _scratchpad

Material exploratório em `docs/_scratchpad/` com cabeçalho `STATUS: TRANSITÓRIO | DATA: YYYY-MM-DD`.
Promover para `docs/` quando: arquitetura validada, decisão técnica, referência futura.
Proibido manter conhecimento crítico apenas em `_scratchpad`.

## Commits (Critério de Valor)

Commit = valor permanente. **Não commitar:** exploratório, temporário, `_scratchpad/`.
**Commitar:** funcionalidades, correções reais, regras de negócio, docs oficial, testes definitivos.
Mensagens: curtas, verbo de ação. (`feat:`, `fix:`, `docs:`, `refactor:`)

## Segurança

Nenhuma key em código. `.env` obrigatório. Logs sem dados sensíveis.

## Referências de Skills

- Logs de progresso com timestamp → skill `code-with-logs`
- Scripts >200 linhas ou multi-etapas → skill `create-subagents`
- Tratamento de erro robusto → skill `error-handling-patterns`
- Testes → skill `testing-patterns`
- Git/commits/PRs → skill `git-workflow`
- Docs → skill `create-documentation`
- Planejamento multi-etapas → skill `create-execution-plan`
- Revisao pos-execucao → skill `review-execution` (via `/review-execution`)
- UI/UX React/Next.js → skill `frontend-conventions`
