# Prompts de Execucao — Single Plugin Cursor Toolkit

> **Runbook operacional.** Copie, cole, execute, finalize.
> Ultima atualizacao: 2025-03-04

---

## Como funciona

### Modo Manual (uma tarefa por conversa)

1. Abra o workspace `c:\Users\User\cursor-toolkit` no Cursor
2. Copie o PROMPT da tarefa desejada
3. Cole numa conversa nova
4. A IA executa, commita, marca status no plano
5. Nova conversa para a proxima tarefa

### Modo Sequencial (várias tarefas em uma conversa)

1. Copie o PROMPT BLOCO correspondente
2. Cole numa conversa
3. A IA executa todas as tarefas do bloco em sequência
4. Commits atômicos por tarefa

---

## Tabela de tarefas

| ID | Tarefa | Status |
|----|--------|--------|
| T1 | Criar .cursor-plugin/ e plugin.json | pendente |
| T2 | Verificar frontmatter em rules | pendente |
| T3 | Adicionar frontmatter aos commands | pendente |
| T4 | Criar assets/logo.svg | pendente |
| T5 | Atualizar README com uso como plugin | pendente |
| T6 | Atualizar install com menção ao plugin | pendente |

---

## Ordem de execucao

```
T1 → [T2 + T3 + T4] → T5 → T6
```

---

## PROMPT T1 — Criar plugin manifest

```
Ler o execution plan em docs/execution_plans/002_single_plugin_cursor_toolkit.md

Executar a tarefa T1.

CONTEXTO:
- Workspace: c:\Users\User\cursor-toolkit
- Objetivo: Criar manifest do plugin single para Cursor
- Referencia: cursor/plugin-template (single plugin = sem marketplace.json)

TAREFA T1:
- Criar pasta .cursor-plugin/
- Criar .cursor-plugin/plugin.json com: name, displayName, version, description, author, license, keywords
- name: "cursor-toolkit", displayName: "Cursor Toolkit"
- Se assets/ não existir, criar assets/ e assets/logo.svg mínimo (SVG 24x24) OU remover chave "logo" do JSON

REGRAS:
- Commitar com: feat: add plugin manifest
- Marcar T1 como "concluido" no execution plan
- Responder em português (BR)
```

---

## PROMPT T2+T3+T4 — Frontmatter e logo (bloco paralelo)

```
Ler o execution plan em docs/execution_plans/002_single_plugin_cursor_toolkit.md

Executar as tarefas T2, T3 e T4.

CONTEXTO:
- Workspace: c:\Users\User\cursor-toolkit
- T1 já executada (.cursor-plugin/plugin.json existe)
- Referencia: "Notas para IA" de cada tarefa no plano

TAREFAS:

T2 — Verificar frontmatter em rules
- Arquivos: rules/ai-conventions-compact.md, rules/skills-auto-apply.md, rules/project-specific-template.md
- Garantir que todos têm frontmatter com "description"
- Se faltar, adicionar

T3 — Adicionar frontmatter aos commands
- Arquivos: commands/*.md (project-init, context-boot, apply-conventions, update-context, create-execution-plan, decision-needed, checkpoint-and-branch, architecture-review, decision-report, summarize-session, help-commands)
- Formato: --- \n name: nome-kebab-case \n description: Frase breve \n ---
- Inserir no início de cada arquivo, antes do primeiro conteúdo

T4 — Criar assets/logo.svg
- Criar assets/ se não existir
- Criar assets/logo.svg com SVG mínimo (24x24 ou 64x64) — ícone simples (engrenagem, "CT", ou quadrado)
- plugin.json já referencia "assets/logo.svg"

REGRAS:
- Commitar cada tarefa separadamente (3 commits)
- Mensagens: docs: add frontmatter to rules, docs: add frontmatter to commands, feat: add plugin logo
- Marcar T2, T3, T4 como "concluido" no execution plan
- Responder em português (BR)
```

---

## PROMPT T5 — Atualizar README

```
Ler o execution plan em docs/execution_plans/002_single_plugin_cursor_toolkit.md

Executar a tarefa T5.

CONTEXTO:
- Workspace: c:\Users\User\cursor-toolkit
- Objetivo: Documentar uso do toolkit como plugin Cursor
- Referencia: cursor/plugin-template (formato single plugin)

TAREFA T5:
- Adicionar seção "Uso como plugin Cursor" no README.md
- Explicar que o repo segue formato single plugin
- Mencionar como referenciar (Settings → Plugins ou path)
- Manter seção install.sh como alternativa
- Link para cursor/plugin-template se aplicável

REGRAS:
- Commitar com: docs: add plugin usage to README
- Marcar T5 como "concluido" no execution plan
- Responder em português (BR)
```

---

## PROMPT T6 — Atualizar install

```
Ler o execution plan em docs/execution_plans/002_single_plugin_cursor_toolkit.md

Executar a tarefa T6.

CONTEXTO:
- Workspace: c:\Users\User\cursor-toolkit
- Objetivo: Instalador mencionar alternativa plugin

TAREFA T6:
- Em install.sh: adicionar echo ou comentário no início mencionando uso como plugin (ver README)
- Em install.ps1 (se existir): mesmo ajuste
- Não alterar lógica de symlinks

REGRAS:
- Commitar com: docs: install scripts mention plugin option
- Marcar T6 como "concluido" no execution plan
- Responder em português (BR)
```

---

## PROMPT GENÉRICO — Próxima tarefa pendente

```
Ler docs/execution_plans/002_single_plugin_cursor_toolkit.md

Identificar a primeira tarefa com status "pendente".
Executar essa tarefa seguindo exatamente as "Notas para IA" do plano.

CONTEXTO:
- Workspace: c:\Users\User\cursor-toolkit
- Plano: single plugin para uso próprio
- Ordem: T1 → T2,T3,T4 → T5 → T6

Ao concluir:
- Commitar com mensagem descritiva (feat: ou docs:)
- Marcar tarefa como "concluido" no plano
- Responder em português (BR)
```

---

## Mapa de conflitos de arquivo

| Arquivo | Tarefas |
|---------|---------|
| rules/*.md | T2 |
| commands/*.md | T3 |
| assets/logo.svg | T4 |
| README.md | T5 |
| install.sh, install.ps1 | T6 |

Nenhum conflito entre tarefas (arquivos distintos).

---

## Estimativa total

| Bloco | Tarefas | Tempo |
|-------|---------|-------|
| 1 | T1 | ~5 min |
| 2 | T2+T3+T4 | ~30 min |
| 3 | T5 | ~15 min |
| 4 | T6 | ~5 min |
| **Total** | | **~55 min** |
