# Plano: single_plugin_cursor_toolkit

Fase: N
Versao: 1.0
Status: NAO INICIADO
Modo de execucao: MANUAL
Ultima atualizacao: 2025-03-04
Pre-requisitos: Repo cursor-toolkit clonado. Estrutura atual com rules/, skills/, agents/, commands/ funcionando via install.sh (symlinks para ~/.cursor/).

---

## Objetivo

Migrar o cursor-toolkit para o formato de single plugin do Cursor, permitindo uso como plugin nativo (referência por repositório) em vez de symlinks manuais para ~/.cursor/.

---

## Diagnóstico / Contexto

### Situação atual

- **Estrutura:** rules/, skills/, agents/, commands/ na raiz
- **Instalação:** install.sh/install.ps1 cria symlinks para ~/.cursor/{skills,commands,agents,rules}
- **Rules:** 3 arquivos (.md) com frontmatter (description, alwaysApply)
- **Skills:** 13 pastas com SKILL.md, frontmatter com name/description já presente
- **Agents:** 8 arquivos .md com frontmatter name/description
- **Commands:** 11 arquivos .md — maioria sem frontmatter YAML explícito
- **Outros:** windsurf-toolkit/, trae-toolkit/, templates/, docs/ — não alterados neste plano

### Formato single plugin (cursor/plugin-template)

- Um único `.cursor-plugin/plugin.json` na raiz
- Sem `marketplace.json` (formato single)
- Conteúdo (rules, skills, agents, commands) na raiz
- Frontmatter obrigatório em rules (.mdc), skills, agents; recomendado em commands
- Logo opcional em `assets/`

### Gap

| Item | Atual | Necessário |
|------|-------|------------|
| Manifest | Inexistente | `.cursor-plugin/plugin.json` |
| Rules | .md com frontmatter | .mdc ou .md com frontmatter (verificar compatibilidade Cursor) |
| Commands | Sem frontmatter | Adicionar name, description |
| Instalador | Symlinks | Manter para fallback; documentar uso via plugin |

---

## Decisões técnicas

| Decisão | Escolha | Justificativa |
|---------|---------|---------------|
| Formato plugin | Single plugin | Uso próprio, um único pacote |
| Rules | Manter .md | Cursor aceita .md; .mdc é opcional no template |
| install.sh | Manter | Fallback para quem não usar plugin |
| Logo | Omitir ou placeholder | Uso próprio, não publicação |
| windsurf/trae | Não alterar | Escopo apenas Cursor |
| MCP | Não criar mcp.json na raiz | MCP é por projeto; não faz parte do plugin core |

---

## Tarefas

### T1 — Criar .cursor-plugin/ e plugin.json

- **Complexidade:** mínima
- **Estimativa:** ~5 min
- **Arquivos:** `.cursor-plugin/` (pasta), `.cursor-plugin/plugin.json` (arquivo)
- **Depende de:** nenhuma
- **Conflita com:** nenhuma
- **Status:** pendente
- **Critério de aceite:** Arquivo plugin.json existe e é JSON válido
- **Notas para IA:**

  - **Pasta:** Criar `c:\Users\User\cursor-toolkit\.cursor-plugin\`
  - **Arquivo:** Criar `c:\Users\User\cursor-toolkit\.cursor-plugin\plugin.json`
  - **Conteúdo:**
  ```json
  {
    "name": "cursor-toolkit",
    "displayName": "Cursor Toolkit",
    "version": "0.1.0",
    "description": "Skills, commands, agents e rules para Cursor — uso pessoal.",
    "author": { "name": "Cursor Toolkit" },
    "license": "MIT",
    "keywords": ["cursor", "toolkit", "workflows", "skills", "agents"],
    "logo": "assets/logo.svg"
  }
  ```
  - **Logo:** Se `assets/logo.svg` não existir, criar placeholder SVG (24x24) ou remover a chave "logo" do JSON temporariamente.
  - **Convenção:** Nome em kebab-case, lowercase.

---

### T2 — Verificar frontmatter em rules

- **Complexidade:** baixa
- **Estimativa:** ~10 min
- **Arquivos:** `rules/ai-conventions-compact.md`, `rules/skills-auto-apply.md`, `rules/project-specific-template.md`
- **Depende de:** nenhuma
- **Conflita com:** nenhuma
- **Status:** pendente
- **Critério de aceite:** Todos os rules têm frontmatter com `description`; compatibilidade com formato plugin confirmada
- **Notas para IA:**

  - **Arquivos a verificar:**
    - `rules/ai-conventions-compact.md` — já tem `description`, `alwaysApply`
    - `rules/skills-auto-apply.md` — verificar frontmatter
    - `rules/project-specific-template.md` — verificar frontmatter
  - **Template Cursor:** Rules podem usar `.mdc` ou `.md`; frontmatter com `description` é obrigatório no template.
  - **Ação:** Se algum rule não tiver `description`, adicionar. Não converter .md para .mdc a menos que a documentação Cursor exija.

---

### T3 — Adicionar frontmatter aos commands

- **Complexidade:** baixa
- **Estimativa:** ~15 min
- **Arquivos:** `commands/*.md` (11 arquivos)
- **Depende de:** nenhuma
- **Conflita com:** nenhuma
- **Status:** pendente
- **Critério de aceite:** Cada command tem frontmatter com `name` e `description`
- **Notas para IA:**

  - **Lista de commands:** project-init, context-boot, apply-conventions, update-context, create-execution-plan, decision-needed, checkpoint-and-branch, architecture-review, decision-report, summarize-session, help-commands
  - **Formato frontmatter:**
  ```yaml
  ---
  name: nome-do-command
  description: Descrição breve do que faz.
  ---
  ```
  - **Regra:** Inserir no início do arquivo, antes do primeiro conteúdo (título ou texto).
  - **name:** kebab-case, igual ao nome do arquivo sem .md (ex: `apply-conventions`)
  - **description:** Uma frase objetiva (ex: "Checklist pré-geração: logs, erros, plano, skills.")

---

### T4 — Criar assets/logo.svg (opcional)

- **Complexidade:** mínima
- **Estimativa:** ~5 min
- **Arquivos:** `assets/` (pasta se não existir), `assets/logo.svg`
- **Depende de:** T1 (plugin.json referencia logo)
- **Conflita com:** nenhuma
- **Status:** pendente
- **Critério de aceite:** Logo existe ou chave "logo" removida de plugin.json
- **Notas para IA:**

  - **Opção A:** Criar `assets/logo.svg` minimal (ícone 24x24 ou 64x64, SVG simples — ex: quadrado, engrenagem, ou texto "CT")
  - **Opção B:** Remover chave `"logo"` de plugin.json se assets/ não for criado
  - **Decisão:** Para uso próprio, placeholder é suficiente. Criar SVG minimal.

---

### T5 — Atualizar README com instruções de uso como plugin

- **Complexidade:** baixa
- **Estimativa:** ~15 min
- **Arquivos:** `README.md`
- **Depende de:** T1
- **Conflita com:** nenhuma
- **Status:** pendente
- **Critério de aceite:** README documenta como usar o toolkit como plugin Cursor
- **Notas para IA:**

  - **Local:** `c:\Users\User\cursor-toolkit\README.md`
  - **Adicionar seção** (após "Instalação" ou "O que está incluído"):
  - **Título:** "Uso como plugin Cursor"
  - **Conteúdo:** Explicar que o repo segue o formato single plugin. Como referenciar: (a) Cursor Settings → Plugins → Adicionar repositório local/path; (b) ou clonar e apontar o Cursor para o diretório. Incluir link para cursor/plugin-template se houver.
  - **Manter** a seção de instalação via install.sh (symlinks) como alternativa.
  - **Formato:** Markdown, sem duplicar excessivamente o conteúdo existente.

---

### T6 — Atualizar install.sh para mencionar plugin

- **Complexidade:** mínima
- **Estimativa:** ~5 min
- **Arquivos:** `install.sh`, `install.ps1` (se existir)
- **Depende de:** T1
- **Conflita com:** nenhuma
- **Status:** pendente
- **Critério de aceite:** Scripts mencionam que o toolkit também pode ser usado como plugin
- **Notas para IA:**

  - **install.sh:** Adicionar echo ou comentário no início: "Alternativa: use como plugin Cursor (formato single plugin). Ver README."
  - **install.ps1:** Mesmo ajuste se o arquivo existir.
  - **Não alterar** a lógica de symlinks; apenas informativo.

---

## Ordem de execução

```
T1 → [T2 + T3 + T4] → T5 → T6
```

- T1 primeiro (cria manifest)
- T2, T3, T4 podem rodar em paralelo (arquivos distintos)
- T5 e T6 após T1 (documentação depende do manifest existir)

---

## Notas para IA (globais)

- **Workspace:** c:\Users\User\cursor-toolkit
- **Estrutura atual:** rules/, skills/, agents/, commands/ na raiz; windsurf-toolkit/, trae-toolkit/, templates/, docs/ não devem ser alterados estruturalmente
- **Referência:** cursor/plugin-template (single plugin = sem marketplace.json, um plugin.json na raiz)
- **Skills a aplicar:** create-documentation para alterações em README/docs
- **Commit:** Uma tarefa por commit, mensagens: `feat: add plugin manifest`, `docs: add frontmatter to commands`, etc.

---

## Métricas de sucesso

- [ ] Arquivo `.cursor-plugin/plugin.json` existe e é válido
- [ ] Todos os rules têm frontmatter com description
- [ ] Todos os commands têm frontmatter com name e description
- [ ] README documenta uso como plugin
- [ ] install.sh/install.ps1 mencionam alternativa plugin
- [ ] Cursor reconhece o repo como plugin (teste manual após implementação)

---

## Rollback

- **Backup necessário antes:** Não (mudanças aditivas; não remove nada existente)
- **Como reverter:** Remover `.cursor-plugin/`, reverter frontmatter dos commands, reverter README e install
- **Ponto de não-retorno:** Nenhum — todas as alterações são reversíveis
