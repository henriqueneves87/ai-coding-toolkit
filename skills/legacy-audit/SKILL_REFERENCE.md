---
name: legacy-audit
description: Audita sistema legado, extrai regras de negocio e gera diagnostico tecnico para reescrita. Usa subagentes por dominio para controlar contexto. Gera prompt de handoff para create-execution-plan. Use com /legacy-audit ou pedido explicito de auditoria de sistema existente. NAO e auto-aplicavel.
---

# Legacy Audit v1.0 (referencia completa)

> Versao compacta para uso diario: `SKILL.md`
> Esta versao contem exemplos, templates completos e detalhamento de cada fase.

## ATIVACAO

**Esta skill NAO e auto-aplicavel.** So ativa quando:

- Comando `/legacy-audit` invocado pelo usuario
- Usuario pede auditoria de sistema existente explicitamente
- Usuario quer reescrever/modernizar um sistema

**NUNCA disparar automaticamente.**

## Quando NAO usar

- Bug pontual com causa raiz conhecida (usar `debugger`)
- Refatoracao pequena e localizada (usar `context-refactor-agent`)
- Review de plano ja executado (usar `review-execution`)
- Projeto novo sem codigo legado

---

## Conceito

Auditoria estruturada de sistema legado que extrai o **valor** (regras de negocio) e diagnostica os **problemas** (divida tecnica), gerando artefatos persistentes que alimentam um plano de reescrita.

```
Legacy Audit → Regras de Negocio + Diagnostico → Execution Plan → Reescrita
```

**Principio:** O codigo legado e descartavel. As regras de negocio sao o ativo. A auditoria preserva o ativo e descarta o resto.

---

## Principios de Engenharia de Contexto

O maior desafio de auditar um legado com IA e o contexto. Um sistema de 20k linhas pode consumir 50-80k tokens — metade do contexto util.

### Estrategias aplicadas:

1. **Subagentes por dominio:** cada subagente le apenas os arquivos do seu dominio (~2-5k linhas), nao o sistema inteiro
2. **Fases em conversas separadas:** cada fase roda numa conversa limpa, referenciando artefatos salvos em disco
3. **Artefatos persistentes:** cada fase gera um arquivo MD. Se a conversa morrer, o progresso esta salvo
4. **Extracao sem codigo:** regras de negocio sao descritas em linguagem natural, nao em blocos de codigo
5. **Mapeamento antes de leitura:** Fase 0 cria um mapa leve antes de qualquer leitura profunda

---

## Fases de Execucao

### Fase 0 — Mapeamento do Codebase (Composer/pool incluso)

**Objetivo:** Criar mapa leve do sistema sem ler codigo.

Reutiliza a Fase 0 do `create-execution-plan`:

1. Agente pai coleta estrutura de diretorios
2. Identifica dominios (services, models, api, components, etc.)
3. Ate 4 subagentes paralelos mapeiam 1 dominio cada:
   - Classes e funcoes publicas (assinaturas, nao implementacao)
   - Dependencias entre modulos
   - Resumo de 1 linha por arquivo
4. Agente pai consolida em Mapa do Codebase

**Template de prompt para subagente de mapeamento:**

```
Mapeie o dominio {dominio} do projeto em {caminho}.

Para cada arquivo, liste:
- Nome e tamanho (linhas)
- Classes e funcoes publicas (apenas assinaturas)
- Dependencias (imports de outros modulos do projeto)
- Resumo de 1 linha do proposito

NAO retorne codigo completo, apenas assinaturas e resumos.
Retorne em formato MD estruturado.
```

**Artefato gerado:**

```markdown
# Mapa do Codebase — {Projeto}

Data: YYYY-MM-DD
Total: N arquivos, ~N linhas

## Dominios

### src/services/ (N arquivos, ~N linhas)
- payment_service.py: ProcessPayment, RefundPayment. Depende de: core/exceptions
- appointment_service.py: CreateAppointment, CancelAppointment. Depende de: models/appointment

### src/models/ (N arquivos, ~N linhas)
- patient.py: Patient (name, cpf, phone, email, birth_date)
- appointment.py: Appointment (patient_id, doctor_id, datetime, status, notes)

### src/api/ (N arquivos, ~N linhas)
- routes_patient.py: GET/POST/PUT /patients
- routes_appointment.py: GET/POST/PUT/DELETE /appointments

### Dependencias entre dominios
api → services (injecao)
services → models (queries)
services → integrations (APIs externas)

## Proximo passo
Fase 1: extrair regras de negocio por dominio (usar Opus).
Prompt de continuacao salvo em: {caminho_do_prompt}
```

**Salvar:** `docs/legacy_audit/{projeto}/00_mapa_codebase.md`

Ao final da Fase 0, gerar prompt para a Fase 1 (autocontido, para colar numa conversa nova com Opus).

---

### Fase 1 — Extracao de Regras de Negocio (Opus/Premium — pool API)

**Objetivo:** Extrair TODAS as regras de negocio do sistema em linguagem natural.

**Esta e a fase mais valiosa da auditoria. Justifica modelo premium.**

Subagentes por dominio (max 4 paralelos). Cada um recebe:
- Lista de arquivos do dominio (do mapa da Fase 0)
- Instrucao detalhada de extracao

**Template de prompt para subagente de extracao:**

```
Voce e um analista de sistemas senior. Leia os arquivos do dominio {dominio}
e extraia as regras de negocio em linguagem natural.

ARQUIVOS:
{lista_de_arquivos_com_caminhos}

EXTRAIR:

1. ENTIDADES
   Para cada entidade (model/schema/tabela):
   - Nome
   - Campos com tipos e restricoes (required, unique, default, enum values)
   - Relacionamentos (FK, 1:N, N:N)
   - Soft delete? Timestamps? Audit fields?

2. FLUXOS DE NEGOCIO
   Para cada operacao (CRUD + operacoes especiais):
   - Nome do fluxo (ex: "Agendar consulta")
   - Pre-condicoes (o que precisa ser verdade antes)
   - Passos (1, 2, 3...)
   - Pos-condicoes (o que muda no sistema apos)
   - Tratamento de erro (o que acontece se falhar)

3. VALIDACOES
   Para cada validacao encontrada:
   - O que valida (ex: "CPF deve ser unico")
   - Onde se aplica (criacao, edicao, ambos)
   - Mensagem de erro (se houver)

4. INTEGRACOES EXTERNAS
   Para cada API/servico externo:
   - Nome do servico
   - O que faz (ex: "envia SMS de confirmacao")
   - Quando e chamado (trigger)
   - O que acontece se falhar (fallback)

5. PERMISSOES E ROLES
   - Quais roles existem
   - O que cada role pode fazer
   - Guards/middlewares de autorizacao

REGRAS DE EXTRACAO:
- Extraia o QUE o sistema faz, NAO como o codigo esta escrito
- NAO copie blocos de codigo — descreva a regra em linguagem natural
- Se a logica for confusa ou ambigua, marque como [AMBIGUO] com sua melhor interpretacao
- Se encontrar codigo morto ou feature incompleta, marque como [MORTO] ou [INCOMPLETO]
- Se encontrar regra de negocio hardcoded (magic numbers, strings), extraia o valor e marque como [HARDCODED]
- Retorne em formato MD estruturado
```

**Agente pai consolida** os retornos dos subagentes em documento unico, organizando por dominio.

**Artefato gerado:**

```markdown
# Regras de Negocio — {Projeto}

Data: YYYY-MM-DD
Fonte: codigo legado auditado
Dominios auditados: N

## Resumo
- Entidades: N
- Fluxos de negocio: N
- Validacoes: N
- Integracoes: N
- Items [AMBIGUO]: N (requerem validacao com stakeholder)
- Items [MORTO]: N (codigo morto identificado)
- Items [INCOMPLETO]: N (features nao finalizadas)

---

## Entidades

### Paciente
- **Campos:** nome (string, required), cpf (string, unique, required), ...
- **Relacionamentos:** 1:N com Consulta, 1:1 com Prontuario
- **Soft delete:** sim (campo deleted_at)

### Consulta
- **Campos:** ...
- **Relacionamentos:** ...

---

## Fluxos de Negocio

### Agendar Consulta
- **Pre-condicoes:** paciente cadastrado, medico disponivel no horario
- **Passos:**
  1. Verificar disponibilidade do medico
  2. Criar registro de consulta com status "agendada"
  3. Enviar SMS de confirmacao ao paciente [INTEGRACAO: Twilio]
  4. Bloquear horario na agenda do medico
- **Pos-condicoes:** consulta criada, horario bloqueado, SMS enviado
- **Erro:** se SMS falhar, consulta e criada mesmo assim (fire-and-forget)

### Cancelar Consulta
- **Pre-condicoes:** consulta existe, status != "realizada"
- **Passos:** ...
- [AMBIGUO] Nao esta claro se o cancelamento libera o horario automaticamente

---

## Validacoes

| Regra | Contexto | Onde |
|-------|----------|------|
| CPF deve ser unico | Cadastro de paciente | criacao |
| Horario nao pode ser no passado | Agendamento | criacao e edicao |
| [HARDCODED] Consulta minima de 30min | Agendamento | criacao |

---

## Integracoes

| Servico | Funcao | Trigger | Fallback |
|---------|--------|---------|----------|
| Twilio | SMS confirmacao | Apos agendamento | Fire-and-forget |
| Google Calendar | Sync agenda | Apos agendamento | Nao implementado [INCOMPLETO] |

---

## Permissoes

| Role | Pode fazer |
|------|------------|
| admin | Tudo |
| medico | Ver proprios pacientes, gerenciar propria agenda |
| recepcionista | Agendar/cancelar consultas, cadastrar pacientes |

---

## Proximo passo
Fase 2: diagnostico tecnico (usar Sonnet).
```

**Salvar:** `docs/legacy_audit/{projeto}/01_regras_negocio.md`

Ao final, gerar prompt para Fase 2.

---

### Fase 2 — Diagnostico Tecnico (Sonnet/Intermediario — pool API)

**Objetivo:** Avaliar qualidade tecnica e identificar problemas.

Subagentes por area de avaliacao (max 4 paralelos):

**Subagente 1 — Padroes e consistencia:**
- Naming conventions (snake_case vs camelCase misturado?)
- Estrutura de pastas (logica? ou arquivos jogados?)
- Patterns (usa repository? service layer? ou tudo misturado?)
- Tratamento de erro (consistente? ou cada arquivo faz diferente?)

**Subagente 2 — Divida tecnica:**
- Codigo morto (funcoes nunca chamadas, imports nao usados)
- Duplicacao (mesma logica em multiplos lugares)
- Acoplamento (dependencias circulares, god classes)
- Complexidade ciclomatica (funcoes com 10+ if/else aninhados)

**Subagente 3 — Performance:**
- Queries N+1 (loop com query dentro)
- Falta de paginacao (SELECT * sem LIMIT)
- Falta de cache (dados que nao mudam sendo buscados toda vez)
- Payloads grandes (retornando campos desnecessarios)
- Renders desnecessarios (React sem memo/useMemo onde deveria)

**Subagente 4 — Seguranca:**
- SQL injection (queries montadas com string concatenation)
- Dados sensiveis em logs ou responses
- Auth/authz fraca (endpoints sem guard)
- Secrets hardcoded
- CORS permissivo

**Template de prompt para subagente de diagnostico:**

```
Voce e um auditor tecnico. Avalie os arquivos abaixo focando em {area}.

MAPA DO CODEBASE:
{mapa_resumido}

ARQUIVOS PARA AVALIAR:
{lista_de_arquivos}

AVALIAR:
{criterios_especificos_da_area}

REGRAS:
- Retorne APENAS problemas encontrados (o que esta OK, descarte)
- Para cada problema: arquivo, linha/funcao, descricao, severidade (CRITICO/ALTO/MEDIO/BAIXO)
- NAO sugira correcoes — apenas diagnostique
- Se nao encontrar problemas na area, retorne "Nenhum problema encontrado em {area}"

FORMATO:
| # | Arquivo | Local | Problema | Severidade |
```

**Artefato gerado:**

```markdown
# Diagnostico Tecnico — {Projeto}

Data: YYYY-MM-DD

## Resumo
| Area | Critico | Alto | Medio | Baixo | Total |
|------|---------|------|-------|-------|-------|
| Padroes | N | N | N | N | N |
| Divida tecnica | N | N | N | N | N |
| Performance | N | N | N | N | N |
| Seguranca | N | N | N | N | N |
| **Total** | N | N | N | N | N |

## Problemas por Area

### Padroes e Consistencia
| # | Arquivo | Local | Problema | Severidade |
|---|---------|-------|----------|------------|

### Divida Tecnica
...

### Performance
...

### Seguranca
...

## Proximo passo
Fase 3: consolidacao e handoff (usar Composer).
```

**Salvar:** `docs/legacy_audit/{projeto}/02_diagnostico_tecnico.md`

---

### Fase 3 — Relatorio Executivo e Handoff (Composer/pool incluso)

**Objetivo:** Consolidar tudo e gerar prompt para reescrita.

Agente pai le os 3 artefatos anteriores e gera:

**1. Relatorio executivo:**

```markdown
# Relatorio de Auditoria — {Projeto}

Data: YYYY-MM-DD

## Visao Geral
- **Sistema:** {descricao}
- **Tamanho:** N arquivos, ~N linhas
- **Stack:** {stack atual}

## Regras de Negocio Extraidas
- Entidades: N
- Fluxos: N
- Validacoes: N
- Integracoes: N
- Items ambiguos: N (requerem validacao)
- Items mortos/incompletos: N

## Saude Tecnica
- Problemas criticos: N
- Problemas altos: N
- Top 5 problemas:
  1. {problema}
  2. {problema}
  ...

## Recomendacao

**[REESCREVER / REFATORAR]**

Justificativa: {baseada em quantidade de problemas, inconsistencia de padroes, e escopo da mudanca desejada}

Se reescrever:
- Stack sugerida: {nova stack}
- Banco sugerido: {novo banco}
- Estimativa de escopo: {N tarefas, N conversas}

Se refatorar:
- Areas prioritarias: {lista}
- Estimativa: {N tarefas}

## Artefatos desta auditoria
- Mapa: `docs/legacy_audit/{projeto}/00_mapa_codebase.md`
- Regras: `docs/legacy_audit/{projeto}/01_regras_negocio.md`
- Diagnostico: `docs/legacy_audit/{projeto}/02_diagnostico_tecnico.md`
- Prompt de reescrita: `docs/04_operations/prompts_legacy_{NNN}_{projeto}.md`
```

**2. Prompt de handoff para reescrita:**

```markdown
# Prompt de Reescrita — {Projeto}

> Cole este bloco numa conversa nova com Opus para gerar o execution plan.
> Ultima atualizacao: YYYY-MM-DD

## PROMPT (copie e cole)

```
Crie um execution plan para reescrever o sistema {projeto}.

CONTEXTO:
- Auditoria completa em: docs/legacy_audit/{projeto}/
- Regras de negocio extraidas: docs/legacy_audit/{projeto}/01_regras_negocio.md
- Diagnostico tecnico: docs/legacy_audit/{projeto}/02_diagnostico_tecnico.md

DECISOES:
- Nova stack: {stack}
- Novo banco: {banco}
- Manter: todas as regras de negocio do arquivo 01_regras_negocio.md
- Descartar: codigo morto marcado como [MORTO]
- Validar com stakeholder: items marcados como [AMBIGUO]

RESTRICOES:
- {restricao 1}
- {restricao 2}

Leia os arquivos de auditoria e gere o execution plan seguindo a skill create-execution-plan.
O plano DEVE cobrir todas as entidades e fluxos do arquivo de regras de negocio.
```

## Artefatos de referencia
- 00_mapa_codebase.md
- 01_regras_negocio.md
- 02_diagnostico_tecnico.md
```

**Salvar relatorio:** `docs/legacy_audit/{projeto}/03_relatorio_executivo.md`
**Salvar prompt:** `docs/04_operations/prompts_legacy_{NNN}_{projeto}.md`

---

## Fluxo Completo de Conversas

```
Conversa 1 (Composer):
  /legacy-audit path={caminho}
  → Fase 0: mapeamento
  → Salva 00_mapa_codebase.md
  → Gera prompt para Fase 1

Conversa 2 (Opus):
  Cola prompt da Fase 1
  → Fase 1: extracao de regras de negocio
  → Salva 01_regras_negocio.md
  → Gera prompt para Fase 2

Conversa 3 (Sonnet):
  Cola prompt da Fase 2
  → Fase 2: diagnostico tecnico
  → Salva 02_diagnostico_tecnico.md
  → Gera prompt para Fase 3

Conversa 4 (Composer):
  Cola prompt da Fase 3
  → Fase 3: consolidacao + handoff
  → Salva 03_relatorio_executivo.md
  → Salva prompts_legacy_{NNN}.md

Conversa 5 (Opus):
  Cola prompt de handoff
  → /create-execution-plan para reescrita

Conversa 6+ (Composer):
  Execucao do plano de reescrita
```

Cada conversa e independente. O prompt de cada fase referencia os artefatos das fases anteriores.

---

## Integracao com Skills

| Fase | Skills utilizadas |
|------|-------------------|
| Fase 0 (mapeamento) | Reutiliza Fase 0 do `create-execution-plan` |
| Fase 3 (handoff) | Gera input para `create-execution-plan` |
| Execucao | `code-with-logs`, `create-subagents`, `error-handling-patterns` |
| Pos-execucao | `review-execution` para validar reescrita |

---

## Anti-patterns

- Ler codebase inteiro numa unica conversa (estoura contexto)
- Copiar blocos de codigo nas regras de negocio (extrair o QUE, nao o COMO)
- Misturar extracao de regras com diagnostico tecnico (fases separadas, modelos diferentes)
- Pular a Fase 1 e ir direto para reescrita (perde regras de negocio — o ativo mais valioso)
- Usar Composer para extracao de regras (precisa reasoning forte para interpretar codigo confuso)
- Rodar todas as fases na mesma conversa (cada fase consome contexto significativo)
- Reescrever sem validar items [AMBIGUO] com stakeholder (pode implementar regra errada)
- Gerar execution plan sem referenciar o arquivo de regras (plano fica incompleto)

---

## Regra Final

Codigo legado e arqueologia: o valor esta enterrado nas regras de negocio.
Auditoria sem extracao de regras e demolir sem salvar a planta.
**O sistema novo deve fazer tudo que o antigo faz — mas melhor.**
