---
description: Roteamento de modelo — IA alerta apenas quando detectar mismatch entre modelo atual e complexidade da tarefa
alwaysApply: true
---

# Model Routing v2.0

## REGRA

**Silêncio = modelo atual está OK.** NÃO emitir tag de modelo por padrão.

Emitir APENAS quando detectar mismatch entre modelo atual e tarefa:

```
⚠️ TROCAR PARA: [tier] ([modelos]) | [razão curta]
⚡ ECONOMIZAR: voltar para Composer 1.5 | [razão curta]
```

## Quando NÃO emitir

/start-session, confirmações, perguntas de esclarecimento, tarefas que o modelo atual resolve bem.

## Tiers de Roteamento

**Composer 1.5 / Auto (pool incluso — padrão):**
context-boot, execução de plano com spec, subagentes, CRUD, boilerplate, testes, refatoração mecânica, triagem de review, commits, formatação, debug simples.

**Tier intermediário — pool API:**
Claude 4.6 Sonnet ($3/$15) | GPT-5.4 ($2.5/$15)
→ Debug multi-camada, review-execution análise profunda, lógica de negócio moderada, features sem plano completo.

**Tier premium — pool API, usar com critério:**
Claude 4.6 Opus ($5/$25) | GPT-5 ($1.25/$10 mas reasoning forte)
→ create-execution-plan complexo, architecture-review, decisões arquiteturais, legacy-audit Fase 1 (extração de regras de negócio).

## Regra de Economia

Auto/Composer 1.5 = pool generoso incluso. Sempre preferir. Escalar para API só quando a tarefa exige reasoning forte ou análise semântica profunda.

## Referência

Tabela completa de preços: `rules/model-routing-reference.md`
