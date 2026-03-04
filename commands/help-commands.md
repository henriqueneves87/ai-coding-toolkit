# /help-commands

Guia de referencia rapida dos 12 commands ativos do AI Coding Toolkit v5.1.

---

## Comandos Essenciais (uso frequente)

### /project-init
**O que faz:** Bootstrap completo de projeto novo — cria `docs/00_overview/context.md`, `roadmap.md`, toda a arvore de pastas numeradas (`00_overview/` ate `07_changelog/`, `_scratchpad/`, `execution_plans/`) e arquivos de arquitetura.

**Quando usar:** Inicio de qualquer projeto novo ou quando o projeto carece de estrutura documental.

**Quando o Cursor falha sem ele:** A IA trabalha sem contexto, repete perguntas sobre o projeto, nao sabe o estado atual, nao tem onde registrar decisoes. Cada sessao comeca do zero.

---

### /context-boot
**O que faz:** Carrega o contexto minimo do projeto no inicio da sessao — le `context.md`, `roadmap.md` e lista de tarefas pendentes. Orienta a IA sobre o estado atual sem desperdicar tokens.

**Quando usar:** Inicio de toda sessao de trabalho.

**Quando o Cursor falha sem ele:** A IA ignora o que ja foi decidido, repropoe solucoes descartadas, perde continuidade entre sessoes.

---

### /apply-conventions
**O que faz:** Executa checklist pre-geracao de codigo — verifica se as skills aplicaveis estao sendo usadas (logs, erros, testes, subagentes), se o plano foi apresentado, se nao ha hardcode.

**Quando usar:** Antes de gerar qualquer codigo relevante.

**Quando o Cursor falha sem ele:** Codigo gerado sem logs, sem tratamento de erro, sem plano apresentado. Violacoes silenciosas das convencoes.

---

### /update-context
**O que faz:** Atualiza `docs/00_overview/context.md` com o avanco real da sessao — decisoes tomadas, estado do sistema, proximo passo.

**Quando usar:** Ao concluir uma etapa relevante ou ao final da sessao.

**Quando o Cursor falha sem ele:** O context.md fica desatualizado. Proximas sessoes partem de estado incorreto.

---

### /create-execution-plan
**O que faz:** Cria um plano de execucao estruturado para tarefas multi-etapas — decomposicao em blocos, dependencias, criterios de aceite, notas para IA, ordem de execucao.

**Quando usar:** Qualquer tarefa com 3+ etapas, refatoracao de codigo grande, features complexas, bugs com causa raiz desconhecida.

**Quando o Cursor falha sem ele:** A IA executa tudo de uma vez sem plano, mistura etapas, nao registra decisoes intermediarias, gera codigo amplo sem autorizacao.

---

### /decision-needed
**O que faz:** Para a execucao e apresenta opcoes tecnicas com trade-offs para decisao humana — formato estruturado com contexto, opcoes, impactos e recomendacao.

**Quando usar:** Qualquer bifurcacao tecnica relevante (arquitetura, stack, abordagem de dados, seguranca).

**Quando o Cursor falha sem ele:** A IA escolhe por conta propria, frequentemente a opcao mais simples ou comum, sem considerar restricoes do projeto.

---

### /checkpoint-and-branch
**O que faz:** Cria checkpoint seguro antes de mudancas estruturais — verifica estado do repo, cria branch, documenta o ponto de retorno.

**Quando usar:** Antes de refatoracoes amplas, migracoes, mudancas de schema, qualquer operacao dificil de reverter.

**Quando o Cursor falha sem ele:** Mudancas destrutivas sem ponto de rollback. Trabalho perdido em caso de erro.

---

### /optimize-context
**O que faz:** Compacta `context.md` movendo conteudo historico para `context_history.md` — reduz consumo de tokens em toda interacao.

**Quando usar:** Quando context.md ultrapassar 300 linhas, apos concluir fase/plano grande, ou quando sessoes estiverem lentas por excesso de contexto.

**Quando o Cursor falha sem ele:** context.md cresce indefinidamente, cada sessao consome milhares de tokens em informacao historica irrelevante para a tarefa atual.

---

## Comandos Situacionais (menos frequentes)

### /architecture-review
**O que faz:** Auditoria DRY-RUN do projeto — mapeia duplicacoes, inconsistencias, violacoes de convencao, debito tecnico. Apenas leitura, sem alteracoes.

**Quando usar:** Mensalmente ou antes de uma refatoracao grande.

**Quando o Cursor falha sem ele:** Debito tecnico acumula silenciosamente. Refatoracoes parciais sem visao do todo.

---

### /decision-report
**O que faz:** Gera relatorio tecnico versionado de uma decisao — contexto, alternativas consideradas, justificativa, riscos, data. Salvo em `docs/05_decisions/reports/`.

**Quando usar:** Decisoes arquiteturais relevantes que precisam de rastreabilidade.

**Quando o Cursor falha sem ele:** Decisoes importantes ficam apenas na cabeca do desenvolvedor ou em historico de chat efemero.

---

### /summarize-session
**O que faz:** Gera resumo estruturado da sessao — o que foi feito, decisoes tomadas, pendencias, proximos passos. Serve como insumo para `/update-context`.

**Quando usar:** Ao encerrar uma sessao longa ou ao trocar de contexto.

**Quando o Cursor falha sem ele:** Trabalho da sessao nao e documentado. Proxima sessao precisa reconstruir o que foi feito.

---

### /help-commands
**O que faz:** Exibe este guia.

**Quando usar:** Quando precisar lembrar qual command usar em cada situacao.

---

## Referencia Rapida

| Command | Frequencia | Momento |
|---------|------------|---------|
| `/project-init` | Uma vez | Inicio do projeto |
| `/context-boot` | Toda sessao | Inicio da sessao |
| `/apply-conventions` | Frequente | Antes de gerar codigo |
| `/update-context` | Frequente | Fim de etapa / sessao |
| `/create-execution-plan` | Moderado | Tarefas com 3+ etapas |
| `/decision-needed` | Moderado | Bifurcacoes tecnicas |
| `/optimize-context` | Moderado | context.md > 300 linhas |
| `/checkpoint-and-branch` | Moderado | Antes de mudancas estruturais |
| `/architecture-review` | Mensal | Auditoria proativa |
| `/decision-report` | Ocasional | Decisoes com rastreabilidade |
| `/summarize-session` | Ocasional | Encerramento de sessao longa |
| `/help-commands` | Ocasional | Referencia |

---

## Ver tambem

- `docs/manual.md` — Manual completo do toolkit (instalacao, fluxos, anti-patterns)
- `skills/` — Skills aplicadas automaticamente pela IA
- `rules/` — Rules de convencao carregadas em toda sessao
