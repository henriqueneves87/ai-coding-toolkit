# review-execution — REVISAO POS-EXECUCAO

Versao: 1.0
Tipo: Command
Uso: Manual (invocado pelo usuario)
Escopo: Revisao de implementacao vs fontes de verdade (plano, docs, roadmap)

---

## OBJETIVO

Verificar sistematicamente se o que foi planejado/documentado corresponde ao que foi realmente implementado. Gera relatorio de inconsistencias e oferece criar plano de correcao.

**Fecha o ciclo:** Plan → Execute → **Review** → (Fix) → Review ✓

---

## USO

```
/review-execution
/review-execution scope=plan path="docs/00_overview/execution_plans/003_checkout.md"
/review-execution scope=docs
/review-execution scope=full
```

---

## COMPORTAMENTO OBRIGATORIO DA IA

Ao receber `/review-execution`, a IA DEVE:

### 1. Determinar escopo

Se o usuario nao informou escopo, perguntar:

> Qual o escopo da revisao?
> - **plan** — Verificar se o execution plan foi implementado como especificado
> - **docs** — Verificar se a documentacao reflete o codigo real
> - **full** — Ambos (com estrategia de contexto para nao sobrecarregar)

### 2. Identificar fonte de verdade

**Para scope=plan:**
- Se caminho informado: usar o execution plan indicado
- Se nao informado: buscar o plano mais recente em `docs/00_overview/execution_plans/` (numero mais alto)
- Se nao encontrar: perguntar ao usuario

**Para scope=docs:**
- Usar `docs/` do projeto
- Priorizar: spec de entidades, roadmap, arquitetura
- Ignorar: _scratchpad, changelogs, debug guides

**Para scope=full:**
- Combinar ambas as fontes

### 3. Aplicar skill `review-execution`

Executar as 4 fases da skill:
- Fase 0: Parse leve → gerar checklist compacto
- Fase 1: Subagentes cirurgicos → verificar apenas arquivos relevantes
- Fase 2: Consolidacao → relatorio de inconsistencias
- Fase 3: Handoff → perguntar sobre plano de correcao

### 4. Salvar relatorio

Salvar em `docs/05_decisions/reports/review_exec_{NNN}_{nome}_{YYYY-MM-DD}.md`

### 5. Apresentar resultado

Mostrar ao usuario:
1. Resumo executivo (tabela de severidades)
2. Top 5 inconsistencias mais criticas
3. Perguntar: "Quer que eu gere um execution plan de correcao?"

---

## ESTRATEGIA DE CONTEXTO

Este command usa engenharia de contexto para evitar dumbing:

1. **Parse leve** — extrai checklist da fonte de verdade sem ler codigo
2. **Subagentes cirurgicos** — cada um recebe apenas checklist + caminhos de arquivo
3. **Retorno compacto** — subagentes reportam apenas inconsistencias
4. **Relatorio enxuto** — so o que falhou, nao o que esta OK

---

## INTEGRACAO COM COMMANDS

| Antes | Depois |
|-------|--------|
| `/create-execution-plan` (planejar) | `/review-execution scope=plan` (revisar) |
| Execucao orquestrada (implementar) | `/review-execution scope=docs` (validar docs) |
| `/review-execution` (encontrar gaps) | `/create-execution-plan` (plano de correcao) |

---

## INTEGRACAO COM SKILLS

A IA deve aplicar a skill `review-execution` ao receber este command.

---

## EXEMPLO DE USO

```
/review-execution scope=plan path="docs/00_overview/execution_plans/003_checkout_multi_meio.md"
```

**Resultado esperado:**
1. IA le o plano, extrai checklist de tarefas
2. Subagentes verificam cada tarefa no codigo
3. Relatorio: "T3 nao implementada, T5 parcial (2 de 4 campos), T7 superficial (stub)"
4. Pergunta: "Quer gerar plano de correcao para T3, T5, T7?"

---

## REGRA FINAL

Execucao sem revisao e divida tecnica invisivel.
Review e o unico mecanismo que transforma "acho que esta pronto" em "verificado e documentado".
**Sem review, o ciclo de desenvolvimento e aberto.**

--- End Command ---
