---
name: project-init
description: Bootstrap de projeto novo — AGENTS.md, context.md, roadmap.md, estrutura completa de docs.
---

# /project-init — Bootstrap de Projeto Novo

Versão: 1.0
Tipo: Command
Uso: Manual (invocar no início de projetos novos)
Escopo: Criação da estrutura completa de docs + arquivos canônicos
Depende de: nenhuma

---

## Objetivo

Inicializar a estrutura de documentação padronizada do projeto: `AGENTS.md` na raiz, pastas numeradas, `context.md`, `roadmap.md` e arquivos de arquitetura. Após executar, o projeto estará pronto para usar `/create-execution-plan` e manter contexto vivo.

---

## Passo 1 — Coletar informações do projeto

Antes de criar qualquer arquivo, perguntar ao usuário:

1. **Nome do projeto** (ex: `saphiro-baas`, `meu-saas`)
2. **Objetivo em 1-2 frases** (ex: "API multi-tenant para gestão de associações")
3. **Stack principal** (ex: Python + FastAPI + Supabase)
4. **Fase atual** (ex: 0.1 — MVP, 1.0 — Produção)
5. **Próximo passo imediato** (ex: "Criar módulo de autenticação")

Se o usuário já forneceu essas informações no prompt, usar diretamente sem perguntar.

---

## Passo 2 — Criar AGENTS.md na raiz do projeto

O Cursor lê `AGENTS.md` automaticamente ao abrir o Agent (Cmd+I / Ctrl+I).
Este arquivo é a **camada estável** do contexto: o que o projeto é, stack e convenções.
A camada dinâmica (estado atual, decisões recentes) fica em `context.md`.

```markdown
# [Nome do Projeto]

[Objetivo em 1-2 frases]

## Stack

- [tecnologia 1]
- [tecnologia 2]

## Convenções

- Idioma das respostas: pt-BR
- Idioma do código: inglês
- Commits: `feat:`, `fix:`, `docs:`, `refactor:` (imperativo, sem ponto final)
- Keys: nunca em código, sempre em .env

## Estrutura

[Breve descrição dos diretórios principais do projeto]

## Estado atual

Fase: [fase atual]
Foco: [o que está sendo trabalhado agora]

## Contexto detalhado

Para estado atual, decisões e tarefas pendentes: `docs/00_overview/context.md`
Para roadmap: `docs/00_overview/roadmap.md`
```

Preencher com dados do Passo 1. A seção "Estado atual" deve ter no máximo 2-3 linhas.

---

## Passo 3 — Criar estrutura de pastas

Criar as seguintes pastas em `docs/`:

```
docs/
  00_overview/
  01_architecture/
  02_business_rules/
  03_api/
  04_operations/
  05_decisions/
    reports/
  06_testing/
  07_changelog/
  _scratchpad/
  execution_plans/
```

Criar `.gitkeep` nas pastas vazias para garantir versionamento.

---

## Passo 4 — Criar docs/00_overview/context.md

Estrutura canônica obrigatória:

```markdown
# Context — [Nome do Projeto]

Versão: [fase atual]
Última atualização: [data]
Command: /update-context

---

## Objetivo Atual

[Objetivo do projeto em 2-3 frases. O que resolve, para quem, qual o valor.]

---

## Estado do Sistema

[Status atual: o que está funcionando, o que está em desenvolvimento, o que está pendente.]

---

## Decisões Tomadas

| # | Decisão | Justificativa | Data |
|---|---------|---------------|------|
| 1 | [decisão] | [por quê] | [data] |

---

## Convenções Obrigatórias

- Stack: [tecnologias principais]
- Idioma do código: [inglês/português]
- Padrão de commits: [formato]
- Regras críticas: [ex: sempre usar MCP para Supabase]

---

## Restrições

- [Restrição 1: ex: sem hardcode de keys]
- [Restrição 2: ex: .env obrigatório]

---

## Próximo Passo

[Uma ação concreta e datada. Ex: "Implementar autenticação JWT — T3 do execution plan sprint-01"]

---

## Métricas de Sucesso

- [ ] [Critério 1]
- [ ] [Critério 2]

---

## Referências

- Execution plans: `docs/execution_plans/`
- Decisões: `docs/05_decisions/reports/`
- Roadmap: `docs/00_overview/roadmap.md`
```

---

## Passo 5 — Criar docs/00_overview/roadmap.md

Estrutura obrigatória:

```markdown
# Roadmap — [Nome do Projeto]

Última atualização: [data]

---

## Fase 0 — Fundação (status: [concluída|em andamento|pendente])

- [x] Estrutura de docs criada
- [x] Context.md inicializado
- [ ] [Próxima entrega]

---

## Fase 1 — [Nome da fase] (status: pendente)

- [ ] [Entrega 1]
- [ ] [Entrega 2]

---

## Backlog / Futuro

- [Ideia ou funcionalidade não priorizada]

---

## Histórico de Fases Concluídas

| Fase | Conclusão | Entregáveis |
|------|-----------|-------------|
| Fase 0 | [data] | Estrutura de docs |
```

---

## Passo 6 — Criar arquivos de arquitetura em docs/01_architecture/

### overview.md

```markdown
# Overview — [Nome do Projeto]

## Arquitetura Geral

[Diagrama textual ou descrição da arquitetura. Ex: frontend → API → banco]

## Componentes Principais

| Componente | Responsabilidade | Tecnologia |
|------------|-----------------|------------|
| [nome] | [o que faz] | [stack] |

## Fluxos Críticos

[Descrever os 2-3 fluxos mais importantes do sistema]
```

### conventions.md

```markdown
# Convenções — [Nome do Projeto]

## Código

- Linguagem: [Python/TypeScript/etc]
- Formatação: [ruff/prettier/etc]
- Nomeação: [snake_case/camelCase/etc]

## Git

- Branch padrão: main
- Formato de commit: `verbo: descrição curta`
- PRs: obrigatório para mudanças estruturais

## Banco de Dados

- [Convenções de nomeação de tabelas, colunas, etc]

## Segurança

- Keys: nunca em código, sempre em .env
- [Outras regras de segurança]
```

### integrations.md

```markdown
# Integrações — [Nome do Projeto]

| Serviço | Uso | Autenticação | Docs |
|---------|-----|--------------|------|
| [nome] | [para que serve] | [tipo de auth] | [link] |
```

---

## Passo 7 — Preencher com dados reais

Substituir todos os placeholders `[...]` com as informações coletadas no Passo 1.

Regras:
- Não deixar placeholder vazio — se não souber, escrever "A definir"
- context.md deve ter pelo menos Objetivo + Estado + Próximo Passo preenchidos
- roadmap.md deve ter pelo menos Fase 0 e Fase 1 esboçadas

---

## Passo 8 — Validação final

Confirmar os 8 itens antes de encerrar:

- [ ] `AGENTS.md` criado na raiz e preenchido
- [ ] `docs/00_overview/context.md` criado e preenchido
- [ ] `docs/00_overview/roadmap.md` criado com ao menos 2 fases
- [ ] `docs/01_architecture/overview.md` criado
- [ ] `docs/01_architecture/conventions.md` criado
- [ ] Pastas numeradas `00_overview/` a `07_changelog/` criadas
- [ ] `docs/_scratchpad/` criada
- [ ] `docs/execution_plans/` criada

Se algum item faltar, completar antes de encerrar.

---

## Próximo passo sugerido

Com a estrutura criada, usar `/create-execution-plan` para planejar a primeira entrega real do projeto.

```
/create-execution-plan
Objetivo: [descrever a primeira tarefa ou feature a implementar]
Contexto: docs/00_overview/context.md já criado
```

---

## Notas

- Este command substitui `/project-docs-init`
- Não duplicar conteúdo dos skills — apenas criar a estrutura e referenciar
- Para atualizar context.md depois: usar `/update-context`
- Para decisões técnicas: usar `/decision-needed` → `/decision-report`
