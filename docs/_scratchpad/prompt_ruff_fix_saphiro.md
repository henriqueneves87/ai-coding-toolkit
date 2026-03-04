STATUS: TRANSITÓRIO
DATA: 2025-02-24
CONTEXTO: prompt para iniciar correções Ruff no saphiro_baas

---

# Prompt para iniciar conversa no saphiro_baas

Copie e cole o bloco abaixo ao abrir o workspace do saphiro_baas:

---

O CI está falhando por erros do Ruff. Preciso que você corrija todos os pontos abaixo:

**E402** — Module level import not at top of file:
- `scripts/_adhoc/test_bmp_scope_individual.py` L35
- `scripts/_adhoc/test_bmp_movimento_hml.py` L33
- `scripts/_adhoc/test_bmp_impressao_boleto_hml.py` L33
- `scripts/_adhoc/gerar_tef_hml_movimentos.py` L20
- `scripts/_adhoc/_archive/test_pix_transfer_real.py` L21

**F541** — f-string sem placeholders (trocar por string normal):
- `scripts/_adhoc/gerar_tef_hml_movimentos.py` L192
- `scripts/_adhoc/gerar_pix_hml_movimentos.py` L102
- `scripts/_adhoc/_archive/test_cedente_carteira_local.py` L61

**F841** — Variável atribuída mas nunca usada:
- `scripts/_adhoc/gerar_tef_hml_movimentos.py` L149 — `name_to_key`

**F401** — Import não utilizado:
- `scripts/_adhoc/_bmp_direct_common.py` L13 — `typing.Any`

Apresente o plano de alterações antes de executar. Ao concluir, rodar `ruff check .` para validar.
