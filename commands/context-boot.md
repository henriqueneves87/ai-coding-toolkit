---
name: context-boot
description: Carrega contexto mínimo do projeto para ancoragem rápida da IA (<=500 tokens).
---

# context-boot — Context Engineering BOOT

Carrega contexto dinâmico do projeto para ancoragem rápida da IA.

---

## Uso

```
/context-boot
```

---

## Objetivo

Alinhar IA ao estado atual do projeto com custo mínimo de tokens (<=500 tokens).

A parte estável (stack, convenções, estrutura) é lida automaticamente pelo Cursor via `AGENTS.md`.
Este command foca na **camada dinâmica**: estado atual, decisões recentes, tarefas pendentes.

---

## Comportamento

### 1. Verificar AGENTS.md (raiz)

- Se `AGENTS.md` existir: a IA já tem stack, convenções e estrutura. **Não repetir.**
- Se não existir: incluir stack e convenções no Context Pack (fallback).

### 2. Ler contexto dinâmico

- `docs/00_overview/context.md` ou `docs/00_overview/context_core.md` (prioridade máxima)
- Focar em: Estado do Sistema, Decisões recentes, Próximo Passo, Riscos
- Se não existir, usar fallback de descoberta mínima

### 3. Fallback (se não houver docs nem AGENTS.md)

- Listar estrutura de diretórios principais
- Localizar pontos de entrada (main.py, index.js, app.py, etc.)
- Identificar arquivos de configuração (package.json, requirements.txt, etc.)
- Mapear dependências principais

### 4. Gerar Context Pack BOOT

**Formato obrigatório:**

```markdown
# Context Pack BOOT — [Nome do Projeto]

**Data:** YYYY-MM-DD HH:MM:SS

## Projeto (<=6 linhas)
[resumo executivo do projeto]

## Arquitetura (<=10 linhas)
[arquitetura resumida em bullets]

## Regras Críticas
- [regra 1]
- [regra 2]

## Invariantes
- [invariante 1]
- [invariante 2]

## Escopo Atual
[o que está sendo trabalhado agora]

## Riscos
- [risco 1]
- [risco 2]

## Próximos Arquivos (máx 5)
- [arquivo 1]
- [arquivo 2]
```

---

## Token Budget

**Máximo:** <=500 tokens

---

## Saída Final

Após gerar Context Pack, perguntar:

> "Posso seguir para ANALYZE/PLAN ou falta algo?"

---

## Regras

- Não fazer dump de conteúdo completo
- Priorizar síntese sobre volume
- Se AGENTS.md existir, não repetir stack/convenções no Context Pack
- Usar Project Overlay quando disponível
- Fallback para descoberta mínima se não houver docs nem AGENTS.md

---

## Camadas de contexto

| Camada | Arquivo | Quem lê | Frequência |
|--------|---------|---------|------------|
| Estável | `AGENTS.md` | Cursor (automático) | Raramente muda |
| Dinâmica | `context.md` | `/context-boot` (manual) | A cada sessão |

---

## Integração

- `context-focus` — próximo nível de profundidade
- `context-deep` — leitura cirúrgica de arquivos específicos
- `update-context` — atualiza context.md e sincroniza AGENTS.md
