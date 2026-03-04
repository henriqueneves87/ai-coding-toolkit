---
name: update-context
description: Atualiza context.md e seção "Estado atual" do AGENTS.md com política de compactação e síntese.
---

# update-context — Compact & Progressive Memory Engine

Atualiza a memória longa do projeto sem gerar dumping,
mantendo context.md como índice executivo e não como repositório bruto.

**Princípio:** memória = síntese + referência, nunca dump.

---

## Uso

```
/update-context
```

Atualiza `docs/00_overview/context.md` seguindo política de compactação obrigatória.

---

## Regra de Ouro

**context.md NÃO é log detalhado.**  
**É mapa mental executivo.**

Detalhes → docs especializados  
Resumo → context.md

---

## Arquivos Canônicos

- **Camada estável (lida automaticamente pelo Cursor):**
  `AGENTS.md` (raiz do projeto)

- **Índice executivo:**
  `docs/00_overview/context.md`

- **Detalhes:**
  `docs/05_decisions/`
  `docs/04_operations/`
  `docs/validation/`
  `docs/reports/`

---

## Política de Compactação (INQUEBRÁVEL)

Ao atualizar:

1. Máx 5–7 linhas por evento (padrão)
2. Até 10–12 linhas para decisões arquiteturais complexas
3. Foco em decisão, impacto e consequência
4. Proibido colar logs, código ou dumps
5. Sempre referenciar documento completo

---

## Estrutura Obrigatória (Mantida)

```markdown
# Contexto do Projeto - [Nome]

Última Atualização: YYYY-MM-DD (Resumo em ≤12 palavras)
Versão: X.Y

---

## Objetivo Atual

## Estado do Sistema

### [Componente] — STATUS

## Decisões Consolidadas

### Decisão N — [Título]

## Restrições

## Próximo Passo

## Métricas-Chave

## Referências

## Regras Críticas
```

---

## Processo de Atualização

Sempre executar em **4 fases**.

### Fase 1 — Classificar Evento

Antes de escrever, classificar:

**Tipo:**
- `FEATURE` — Nova funcionalidade
- `MIGRATION` — Migration aplicada
- `BUGFIX` — Correção crítica
- `ARCH` — Decisão arquitetural
- `DATA` — Correção de dados
- `OPS` — Mudança operacional

**Impacto:**
- `LOCAL` — Afeta componente específico
- `SISTÊMICO` — Afeta múltiplos componentes
- `REGULATÓRIO` — Afeta compliance/auditoria
- `FINANCEIRO` — Afeta cálculos financeiros

### Fase 2 — Gerar Síntese Executiva

Formato:

`[Tipo] + [Ação] + [Resultado] + [Impacto]`

**Exemplo:**

```
MIGRATION: corrigido cálculo da receita líquida, eliminando divergência no KPI diário
```

### Fase 3 — Atualizar context.md

Aplicar apenas:

1. Última Atualização
2. Estado do Sistema (se afetado)
3. Decisões (se aplicável)
4. Próximo Passo (se mudou)

**Nunca atualizar tudo.**

### Fase 3.1 — Sincronizar AGENTS.md (se existir)

Se `AGENTS.md` existir na raiz do projeto, atualizar **apenas** a seção "Estado atual":

```markdown
## Estado atual

Fase: [fase atual]
Foco: [foco atual — 1 linha]
```

Regras:
- Máximo 2-3 linhas
- Só atualizar se fase ou foco mudaram
- Não duplicar conteúdo do context.md
- Se AGENTS.md não existir, ignorar (não criar)

### Fase 4 — Criar/Referenciar Documento Completo

Se houver:
- análise longa
- validação
- relatório
- investigação

Criar em pasta própria e referenciar.

**Exemplo:**

```
Ver: docs/05_decisions/reports/DR_012_kpi_fix.md
```

---

## Padrão para Novas Decisões

```markdown
### Decisão N — [Título] ✅

Data: YYYY-MM-DD  
Tipo: ARCH | DATA | OPS | FINANCE  
Impacto: LOCAL | SISTÊMICO | REGULATÓRIO

Resumo:
(≤5 linhas padrão, ≤10 linhas para decisões arquiteturais)

Motivo:
(≤3 linhas)

Consequência:
(≤3 linhas)

Referência:
- caminho/do/doc.md
```

---

## Padrão para Estado do Sistema

```markdown
### [Componente] — OPERACIONAL ✅

Atualizado: YYYY-MM-DD

Estado:
(≤4 linhas)

Riscos Ativos:
- Nenhum | [listar]
```

---

## Anti-Dumping (Obrigatório)

É **PROIBIDO:**

❌ Copiar relatórios  
❌ Colar SQL  
❌ Colar logs  
❌ Colar código  
❌ Repetir documentação

**Sempre:** resumir + linkar.

---

## Métrica Interna de Qualidade

Uma atualização é válida se:

- ✅ Lida em < 2 minutos
- ✅ Entendida sem contexto externo
- ✅ Aponta para docs completos
- ✅ Não gera ambiguidade

---

## Exemplo Correto

```markdown
Última Atualização: 2026-01-27 (Correção definitiva da receita líquida)

### Pipeline KPI — OPERACIONAL ✅

Atualizado: 2026-01-27

Estado:
Cálculo de receita corrigido e validado para jan/2026.

Riscos Ativos:
- Nenhum

### Decisão 14 — Reprocessamento pós-correção ✅

Data: 2026-01-27  
Tipo: DATA  
Impacto: FINANCEIRO

Resumo:
Reprocessar todos os dias de jan/2026 após correção da migration.

Motivo:
Eliminar divergência histórica.

Consequência:
Base alinhada ao EDI.

Referência:
- docs/05_decisions/reports/DR_011_kpi_fix.md
```

---

## Quando NÃO Atualizar

❌ Exploração  
❌ Tentativas  
❌ Protótipos  
❌ Ajustes triviais  
❌ Hipóteses

**Usar:** `docs/_scratchpad/`

---

## Integração com Context Engineering

Para projetos que usam Context Engineering, este command pode ser usado em conjunto com:

- `context-boot` — Para carregar contexto inicial
- `context-focus` — Para focar em documentos específicos
- `context-audit` — Para auditorias que geram mudanças de contexto
- `context-feature` — Para features que alteram contexto

Este command atualiza o `context.md` após qualquer mudança relevante identificada pelos commands acima.

---

## Regra Final**context.md = índice executivo**  
**docs/* = memória detalhada**Se virar dump, perdeu função.
