---
name: legacy-audit
description: Audita sistema legado, extrai regras de negocio e gera diagnostico tecnico para reescrita. Usa subagentes por dominio para controlar contexto. Gera prompt de handoff para create-execution-plan. Use com /legacy-audit ou pedido explicito de auditoria de sistema existente. NAO e auto-aplicavel.
---

# Legacy Audit v1.0 (compacto)

> Referencia completa com exemplos e templates: `SKILL_REFERENCE.md`
> Consultar SKILL_REFERENCE.md quando: primeira auditoria ou duvida sobre formato dos artefatos.

## ATIVACAO

**NAO e auto-aplicavel.** So ativa com `/legacy-audit` ou pedido explicito.
**NAO usar para:** bug pontual (debugger), refatoracao pequena (context-refactor-agent), review de plano (review-execution).

## QUANDO USAR

- Sistema existente que precisa ser reescrito ou modernizado
- Codebase com "vibe coding" acumulado, padroes inconsistentes
- Migracao de banco de dados ou stack
- Extracao de regras de negocio de codigo legado
- Avaliacao de divida tecnica antes de decidir reescrever vs refatorar

## PRINCIPIOS

1. **Regras de negocio sao o ativo.** Codigo e descartavel, regras nao.
2. **Extrair o QUE, nao o COMO.** O sistema novo pode ter arquitetura diferente.
3. **Contexto e o gargalo.** Codebase inteiro nao cabe numa conversa. Subagentes por dominio.
4. **Cada fase gera artefato persistente.** Se a conversa morrer, o progresso esta salvo em arquivos.

## FASES

### Fase 0 — Mapeamento (Composer/pool incluso)

Reutiliza Fase 0 do `create-execution-plan`.

1. Agente pai: estrutura superficial (tree, dominios, lista de arquivos SEM conteudo)
2. Ate 4 subagentes paralelos: mapear 1 dominio cada (classes, funcoes publicas, dependencias)
3. Consolidar em Mapa do Codebase

**Salvar:** `docs/legacy_audit/{projeto}/00_mapa_codebase.md`

### Fase 1 — Extracao de regras de negocio (Opus/Premium — pool API)

**Esta e a fase mais valiosa. Justifica modelo premium.**

Subagentes por dominio (max 4 paralelos), cada um recebe:
- Lista de arquivos do dominio (do mapa)
- Instrucao: extrair regras de negocio, NAO copiar codigo

**Template de prompt para subagente de extracao:**

```
Voce e um analista de sistemas. Leia os arquivos do dominio {dominio} e extraia:

1. ENTIDADES: nome, campos, tipos, restricoes, relacionamentos
2. FLUXOS: passo a passo de cada operacao de negocio (ex: "agendar consulta")
3. VALIDACOES: regras que o sistema impoe (ex: "nao permite agendamento no passado")
4. INTEGRACOES: APIs externas, webhooks, servicos terceiros
5. PERMISSOES: quem pode fazer o que (roles, guards)

REGRAS:
- Extraia o QUE o sistema faz, NAO como o codigo esta escrito
- NAO copie blocos de codigo — descreva a regra em linguagem natural
- Se encontrar logica confusa/ambigua, marque como [AMBIGUO] com sua melhor interpretacao
- Se encontrar codigo morto ou feature incompleta, marque como [MORTO] ou [INCOMPLETO]
- Retorne em formato MD estruturado

ARQUIVOS DO DOMINIO:
{lista_de_arquivos}
```

Agente pai consolida os retornos em documento unico.

**Salvar:** `docs/legacy_audit/{projeto}/01_regras_negocio.md`

### Fase 2 — Diagnostico tecnico (Sonnet/Intermediario — pool API)

Subagentes avaliam (max 4 paralelos):

- **Padroes:** inconsistencias de estilo, naming, estrutura
- **Divida tecnica:** codigo morto, duplicacao, acoplamento forte, dependencias circulares
- **Performance:** queries N+1, renders desnecessarios, falta de cache, payloads grandes
- **Seguranca:** SQL injection, dados sensiveis expostos, auth fraca

Cada subagente recebe o mapa + arquivos do seu escopo. Retorna APENAS problemas encontrados.

**Salvar:** `docs/legacy_audit/{projeto}/02_diagnostico_tecnico.md`

### Fase 3 — Relatorio consolidado e handoff (Composer/pool incluso)

Agente pai gera:

1. **Relatorio executivo** com:
   - Resumo do sistema (entidades, fluxos, tamanho)
   - Quantidade de regras extraidas
   - Top 10 problemas tecnicos por severidade
   - Recomendacao: reescrever vs refatorar (com justificativa)
   - Estimativa de escopo da reescrita

2. **Prompt de handoff** para `/create-execution-plan` contendo:
   - Regras de negocio extraidas (inline ou referencia ao arquivo)
   - Decisoes tecnicas sugeridas (novo banco, nova stack, nova arquitetura)
   - Restricoes da reescrita

**Salvar relatorio:** `docs/legacy_audit/{projeto}/03_relatorio_executivo.md`
**Salvar prompt:** `docs/04_operations/prompts_legacy_{NNN}_{projeto}.md`

## ARTEFATOS

| Fase | Arquivo | Modelo |
|------|---------|--------|
| 0 | `docs/legacy_audit/{projeto}/00_mapa_codebase.md` | Composer |
| 1 | `docs/legacy_audit/{projeto}/01_regras_negocio.md` | Opus |
| 2 | `docs/legacy_audit/{projeto}/02_diagnostico_tecnico.md` | Sonnet |
| 3 | `docs/legacy_audit/{projeto}/03_relatorio_executivo.md` | Composer |
| 3 | `docs/04_operations/prompts_legacy_{NNN}_{projeto}.md` | Composer |

Cada fase gera artefato independente. Se a conversa morrer, retomar da ultima fase salva.

## FLUXO DE CONVERSAS

```
Conversa 1 (Composer): /legacy-audit → Fase 0 (mapeamento)
Conversa 2 (Opus):     Fase 1 (regras de negocio) — usar prompt gerado pela Fase 0
Conversa 3 (Sonnet):   Fase 2 (diagnostico tecnico) — usar mapa da Fase 0
Conversa 4 (Composer): Fase 3 (consolidacao + handoff)
Conversa 5 (Opus):     /create-execution-plan — usar prompt de handoff
Conversa 6+ (Composer): execucao do plano
```

Cada conversa e independente. O prompt de cada fase referencia os artefatos das fases anteriores.

## ANTI-PATTERNS

- Ler codebase inteiro numa unica conversa (estoura contexto)
- Copiar blocos de codigo nas regras de negocio (extrair o QUE, nao o COMO)
- Misturar extracao de regras com diagnostico tecnico (fases separadas)
- Pular a Fase 1 e ir direto para reescrita (perde regras de negocio)
- Usar Composer para extracao de regras (precisa reasoning forte)
- Rodar todas as fases na mesma conversa (contexto)
