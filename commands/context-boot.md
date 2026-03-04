---
name: context-boot
description: Carrega contexto mínimo do projeto para ancoragem rápida da IA (<=500 tokens).
---

# context-boot — Context Engineering BOOT

Carrega contexto mínimo do projeto para ancoragem rápida da IA.

---

## Uso

```
/context-boot
```

---

## Objetivo

Alinhar IA ao projeto com custo mínimo de tokens (<=500 tokens).

---

## Comportamento

### 1. Ler Project Overlay (se existir)

- `docs/00_overview/context.md` ou `docs/00_overview/context_core.md` (prioridade máxima)
- `docs/docs_index.md` (apenas para mapear estrutura)
- Se não existir, usar fallback de descoberta mínima

### 2. Fallback (se não houver docs)

- Listar estrutura de diretórios principais
- Localizar pontos de entrada (main.py, index.js, app.py, etc.)
- Identificar arquivos de configuração (package.json, requirements.txt, etc.)
- Mapear dependências principais

### 3. Ler Arquitetura (se existir)

- `docs/01_architecture/summary.md` ou `architecture.md` (apenas primeiras 20 linhas)
- `docs/02_business_rules/core.md` ou `rules.md` (apenas primeiras 20 linhas)

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
- Usar Project Overlay quando disponível
- Fallback para descoberta mínima se não houver docs

---

## Integração

- `context-focus` — próximo nível de profundidade
- `context-deep` — leitura cirúrgica de arquivos específicos
