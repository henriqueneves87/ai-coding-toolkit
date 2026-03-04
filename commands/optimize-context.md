---
name: optimize-context
description: Compacta context.md para economia de tokens (arquiva histórico, compacta seções).
---

# optimize-context — Compactar context.md para Economia de Tokens

Versão: 1.0
Tipo: Command
Uso: Manual (invocado pelo usuário) ou automático (quando context.md > 300 linhas)

---

## Objetivo

Reduzir o tamanho de `context.md` movendo conteúdo histórico para `context_history.md` e compactando seções operacionais. Economiza tokens em toda interação sem perder informação.

---

## Uso

```
/optimize-context
```

---

## Comportamento OBRIGATÓRIO

Ao receber este comando, a IA DEVE aplicar a skill `optimize-context` e seguir este fluxo:

### 1. Medir estado atual

```
Contar linhas de context.md
Se < 300 linhas → reportar "context.md já está compacto (N linhas)" e parar
Se >= 300 linhas → prosseguir
```

### 2. Classificar cada seção

| Classificação | Critério | Ação |
|---------------|----------|------|
| OPERACIONAL | Afeta trabalho corrente | Manter em context.md (compactar) |
| HISTÓRICO | Fase concluída, decisão antiga, hotfix passado | Mover para context_history.md |
| REDUNDANTE | Duplica código, .env.example, Swagger | Remover (referência ao original) |

### 3. Criar/atualizar context_history.md

- Caminho: `docs/00_overview/context_history.md`
- Criar se não existir
- Append (não sobrescrever) conteúdo movido
- Manter organizado por seções (Fases, Decisões, Hotfixes, Referências)

### 4. Reescrever context.md

Aplicar regras de compactação da skill `optimize-context`:
- Status do projeto: tabela resumo (1 linha por fase)
- Decisões técnicas: top 10-15 ativas em tabela
- Referências: ~15 mais usadas
- Estrutura: 1º/2º nível apenas
- Remover: endpoints (→ Swagger), env vars (→ .env.example)

### 5. Validar e reportar

```
OTIMIZAÇÃO CONCLUÍDA

context.md: [antes] → [depois] linhas ([%] redução)
context_history.md: [linhas] linhas (histórico preservado)
```

---

## Quando Usar

| Situação | Ação |
|----------|------|
| context.md > 300 linhas | `/optimize-context` |
| Após concluir fase/plano grande | `/update-context` + `/optimize-context` |
| Início de sessão lento (muitos tokens) | `/optimize-context` |
| Periodicamente (mensal) | `/optimize-context` |

---

## Integração com Outros Commands

| Command | Relação |
|---------|---------|
| `/update-context` | Adiciona informação → optimize compacta |
| `/context-boot` | Carrega context.md → menor = mais rápido |
| `/project-init` | Cria context.md inicial → optimize mantém compacto |
| `/summarize-session` | Resume sessão → optimize arquiva histórico |

---

## Regra Final

`/update-context` alimenta. `/optimize-context` poda.
**Juntos mantêm context.md como índice executivo, não como dump.**
