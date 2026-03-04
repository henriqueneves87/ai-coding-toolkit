---
name: summarize-session
description: Resumo de sessão e sugestão de próximos passos para continuidade.
---

# summarize-session — Resumo de Sessão de Trabalho

Versão: 1.0  
Tipo: Command  
Uso: Manual (ao final de uma sessão de trabalho)

---

## Objetivo

Gerar um resumo estruturado do que foi feito na sessão atual, para garantir continuidade em sessões futuras e evitar perda de contexto.

---

## Uso

```
/summarize-session
```

---

## Comportamento Obrigatório

Ao receber `summarize-session`, a IA DEVE:

1. **Analisar a conversa atual** e identificar:
   - O que foi discutido
   - O que foi implementado
   - O que foi decidido
   - O que ficou pendente
   - Problemas encontrados

2. **Gerar resumo estruturado** no formato abaixo

3. **Sugerir** atualização de `context.md` se houve avanço real

---

## Formato Obrigatório

```markdown
# Resumo da Sessão — YYYY-MM-DD

## O que foi feito
- [item 1]
- [item 2]
- [item 3]

## Decisões tomadas
- [decisão 1]: [motivo]
- [decisão 2]: [motivo]

## Arquivos modificados
- [arquivo 1] — [o que mudou]
- [arquivo 2] — [o que mudou]

## Pendências
- [ ] [pendência 1]
- [ ] [pendência 2]

## Problemas encontrados
- [problema 1] — [status: resolvido/pendente]

## Próxima sessão deve
- [ação 1]
- [ação 2]

## Prompt sugerido para continuar
> [prompt otimizado para a próxima sessão, incluindo contexto necessário]
```

---

## Regras

- Não inventar — listar apenas o que realmente aconteceu
- Ser objetivo — frases curtas
- Incluir prompt sugerido para continuidade
- Sugerir `/update-context` se houve avanço real

---

## Integração

- `update-context` — para registrar avanço real no context.md
- `context-boot` — o prompt sugerido deve funcionar com context-boot na próxima sessão
