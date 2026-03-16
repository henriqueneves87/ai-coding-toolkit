# legacy-audit — Command

Versao: 1.0
Tipo: Command
Uso: Manual (invocado pelo usuario)

---

## USO

```
/legacy-audit
/legacy-audit path="caminho/do/projeto"
/legacy-audit path="caminho/do/projeto" projeto="nome-do-projeto"
```

---

## COMPORTAMENTO

Ao receber `/legacy-audit`:

### 1. Identificar projeto

- Se `path` informado: usar como raiz do codebase
- Se `projeto` informado: usar como nome nos artefatos
- Se nao informado: perguntar caminho e nome do projeto

### 2. Criar pasta de artefatos

Criar `docs/legacy_audit/{projeto}/` se nao existir.

### 3. Verificar estado (retomada)

Verificar quais artefatos ja existem na pasta:
- `00_mapa_codebase.md` → Fase 0 ja feita
- `01_regras_negocio.md` → Fase 1 ja feita
- `02_diagnostico_tecnico.md` → Fase 2 ja feita
- `03_relatorio_executivo.md` → Fase 3 ja feita

Se alguma fase ja foi concluida, retomar da proxima pendente.

### 4. Aplicar skill `legacy-audit`

Executar a fase atual:
- Fase 0: mapeamento (Composer/pool incluso)
- Fase 1: extracao de regras de negocio (recomenda Opus)
- Fase 2: diagnostico tecnico (recomenda Sonnet)
- Fase 3: consolidacao e handoff (Composer/pool incluso)

Ao final de cada fase, salvar artefato e gerar prompt para a proxima fase.

### 5. Apresentar resultado da fase

Informar:
1. O que foi feito nesta fase
2. Artefato salvo (caminho)
3. Proxima fase e modelo recomendado
4. Prompt para a proxima fase (se aplicavel)

---

## INTEGRACAO

| Antes | Depois |
|-------|--------|
| `/legacy-audit` (auditar) | `/create-execution-plan` (planejar reescrita) |
| Execucao da reescrita | `/review-execution scope=plan` (validar) |

--- End Command ---
