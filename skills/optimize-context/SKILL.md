---
name: optimize-context
description: Otimiza context.md para reduzir consumo de tokens. Arquiva histórico, compacta seções, remove redundâncias. Use quando context.md ultrapassar 300 linhas ou ao detectar /optimize-context.
---

# Optimize Context v1.0

## APLICAÇÃO

Executar quando:
- `context.md` ultrapassar **300 linhas**
- Usuário invocar `/optimize-context`
- Acúmulo de decisões técnicas ou marcos históricos no arquivo

## PRINCÍPIOS

1. **context.md = contexto operacional** — só o que a IA precisa para a tarefa ATUAL
2. **context_history.md = arquivo morto** — detalhes de fases concluídas, hotfixes, decisões passadas
3. **Anti-dumping:** síntese + referência ao arquivo canônico, nunca dump completo
4. **Regra dos 300:** context.md não deve ultrapassar ~300 linhas

## CHECKLIST DE OTIMIZAÇÃO

### 1. Seção "Status do Projeto" (maior vilã de tokens)

- **MOVER** para `context_history.md`: detalhes de fases/planos concluídos (checklists, blocos, subtarefas)
- **MANTER** em `context.md`: tabela resumo (fase | status) + gaps restantes + próximos passos
- **REGRA:** fase concluída = 1 linha na tabela, não 30 linhas de checklist

### 2. Seção "Decisões Técnicas"

- **MOVER** para `context_history.md`: decisões históricas (>30 dias sem impacto operacional)
- **MANTER** em `context.md`: top 10-15 decisões ativas (afetam trabalho corrente)
- **FORMATO:** tabela compacta (# | Decisão | Impacto)

### 3. Seção "Referências Rápidas"

- **REMOVER** referências a planos concluídos (>30 dias)
- **MANTER:** ~15 referências mais usadas
- **REGRA:** se precisa de referência histórica, está em `context_history.md`

### 4. Seções redundantes com código

- **Endpoints da API** → remover (redundante com código/Swagger)
- **Variáveis de ambiente** → remover (redundante com `.env.example`)
- **Estrutura do código** → compactar para 1º/2º nível (IA descobre detalhes via Glob/Grep)

## PROCESSO DE EXECUÇÃO

```
1. Contar linhas do context.md atual
2. Ler context_history.md (criar se não existir)
3. Para cada seção do context.md:
   a. Classificar: OPERACIONAL (manter) ou HISTÓRICO (mover)
   b. Mover conteúdo histórico para context_history.md
   c. Compactar conteúdo operacional
4. Reescrever context.md
5. Validar: < 300 linhas?
6. Reportar resultado
```

## TEMPLATE DE SAÍDA

Após otimização, reportar:

```
OTIMIZAÇÃO CONCLUÍDA

context.md: [antes] → [depois] linhas ([%] redução)
context_history.md: [linhas] linhas (histórico preservado)

Seções compactadas:
- [seção]: [antes] → [depois] linhas
- ...
```

## INTEGRAÇÃO

- Complementa `/update-context` — update adiciona, optimize compacta
- Complementa `/context-boot` — context menor = boot mais rápido e barato
- Referenciar `create-documentation` para formatação

## REGRA FINAL

Context compacto = IA mais rápida, mais barata, mais focada.
Context inflado = tokens desperdiçados em informação que não afeta a tarefa.
**Histórico existe para ser consultado, não para ser carregado em toda interação.**
