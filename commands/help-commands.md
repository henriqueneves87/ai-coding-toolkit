---
name: help-commands
description: Lista todos os comandos com exemplos de uso.
---

# /help-commands

Guia de referencia rapida dos commands ativos do AI Coding Toolkit v6.0.

---

## Comandos Essenciais (uso frequente)

### /start-session
**O que faz:** Inicializa sessao de trabalho — carrega contexto dinamico do projeto (context.md), confirma convencoes ativas, informa espaco de contexto disponivel. Substitui `/context-boot` + `/apply-conventions`.

**Quando usar:** Inicio de toda sessao de trabalho.

---

### /create-execution-plan
**O que faz:** Cria plano de execucao estruturado para tarefas multi-etapas — decomposicao em blocos, dependencias, criterios de aceite, notas para IA, ordem de execucao, prompt de execucao autocontido.

**Quando usar:** Qualquer tarefa com 3+ etapas, refatoracao grande, features complexas, bugs com causa raiz desconhecida.

**Nota:** Ativado via skill `create-execution-plan`. O command dispara a skill automaticamente.

---

### /update-context
**O que faz:** Atualiza `docs/00_overview/context.md` com o avanco real da sessao — decisoes tomadas, estado do sistema, proximo passo. Sincroniza `AGENTS.md` se fase ou foco mudaram.

**Quando usar:** Ao concluir uma etapa relevante ou ao final da sessao.

---

### /review-execution
**O que faz:** Revisao pos-execucao em camadas — triagem mecanica (pool incluso) + analise profunda seletiva (Sonnet/GPT-5.4, so items flagrados). Gera relatorio de inconsistencias e prompt de handoff para analise profunda.

**Quando usar:** Apos executar um execution plan ou concluir fase de desenvolvimento.

**Escopos:** `scope=plan` | `scope=docs` | `scope=full`

---

### /decision-needed
**O que faz:** Para a execucao e apresenta opcoes tecnicas com trade-offs para decisao humana — formato estruturado com contexto, opcoes, impactos e recomendacao.

**Quando usar:** Qualquer bifurcacao tecnica relevante (arquitetura, stack, abordagem).

---

## Comandos de Projeto

### /project-init
**O que faz:** Bootstrap completo de projeto novo — cria `AGENTS.md`, `docs/00_overview/context.md`, `roadmap.md`, toda a arvore de pastas e arquivos de arquitetura.

**Quando usar:** Inicio de qualquer projeto novo.

---

### /legacy-audit
**O que faz:** Audita sistema legado em 4 fases — mapeamento, extracao de regras de negocio, diagnostico tecnico, relatorio executivo + prompt de handoff para reescrita. Cada fase gera artefato persistente em disco.

**Quando usar:** Avaliar sistema existente para reescrita ou modernizacao.

**Escopos:** `path="caminho"` | `projeto="nome"`

---

### /architecture-review
**O que faz:** Auditoria DRY-RUN do projeto — mapeia duplicacoes, inconsistencias, violacoes de convencao, debito tecnico. Apenas leitura, sem alteracoes.

**Quando usar:** Mensalmente ou antes de uma refatoracao grande.

---

## Comandos Operacionais

### /optimize-context
**O que faz:** Compacta `context.md` movendo conteudo historico para `context_history.md` — reduz consumo de tokens.

**Quando usar:** Quando context.md ultrapassar 300 linhas ou sessoes estiverem lentas.

---

### /checkpoint-and-branch
**O que faz:** Cria checkpoint seguro antes de mudancas estruturais — verifica estado do repo, cria branch, documenta ponto de retorno.

**Quando usar:** Antes de refatoracoes amplas, migracoes, mudancas de schema.

---

### /decision-report
**O que faz:** Gera relatorio tecnico versionado de uma decisao — contexto, alternativas, justificativa, riscos. Salvo em `docs/05_decisions/reports/`.

**Quando usar:** Decisoes arquiteturais que precisam de rastreabilidade.

---

### /summarize-session
**O que faz:** Gera resumo estruturado da sessao — o que foi feito, decisoes, pendencias, proximos passos.

**Quando usar:** Ao encerrar uma sessao longa ou trocar de contexto.

---

### /help-commands
**O que faz:** Exibe este guia.

---

## Referencia Rapida

| Command | Frequencia | Momento |
|---------|------------|---------|
| `/start-session` | Toda sessao | Inicio da sessao |
| `/create-execution-plan` | Moderado | Tarefas com 3+ etapas |
| `/update-context` | Frequente | Fim de etapa / sessao |
| `/review-execution` | Moderado | Apos executar plano |
| `/decision-needed` | Moderado | Bifurcacoes tecnicas |
| `/legacy-audit` | Ocasional | Avaliar sistema legado |
| `/project-init` | Uma vez | Inicio do projeto |
| `/architecture-review` | Mensal | Auditoria proativa |
| `/optimize-context` | Moderado | context.md > 300 linhas |
| `/checkpoint-and-branch` | Moderado | Antes de mudancas arriscadas |
| `/decision-report` | Ocasional | Decisoes com rastreabilidade |
| `/summarize-session` | Ocasional | Encerramento de sessao |
| `/help-commands` | Ocasional | Referencia |

---

## Commands removidos (v6.0)

| Antigo | Substituido por |
|--------|-----------------|
| `/context-boot` | `/start-session` |
| `/apply-conventions` | `/start-session` |

---

## Camadas de contexto

| Camada | Arquivo | Quem le | Frequencia |
|--------|---------|---------|------------|
| Estavel | `AGENTS.md` | Cursor (automatico) | Raramente muda |
| Dinamica | `context.md` | `/start-session` (manual) | A cada sessao |
