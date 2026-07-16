# Pendências de tokens (Δtokens roteados)

> Δtokens que emergiram de ciclos de design mas **não podem ser resolvidos inventando
> valores** — dependem de decisão do guardião do DS. Registrados aqui em vez de
> corromper `tokens.json` (que é gerado do Figma). Cada item cita o ciclo de origem.

## 1. Biblioteca de ícones — AUSENTE
- **Origem:** ciclo `comparador-publico` (2026-07-14).
- **Problema:** `tokens.json` cobre cor, espaço, raio, tipografia e efeitos, mas **não
  define biblioteca de ícones**. As peças usaram emoji como placeholder (📍 ✓ ⚠ 🔗 🛍️).
- **Decisão pendente:** qual biblioteca de ícones o DS adota (ex.: set próprio, Lucide,
  Phosphor), com nomes/tokens e tamanhos alinhados à escala.
- **Dono:** guardião do DS. **Impacto:** todas as peças com ícone.

## 2. Marca de canal (Shopee / TikTok / Ybera.com) — SEM TOKEN
- **Origem:** ciclo `comparador-publico` (2026-07-14).
- **Problema:** o comparador precisa exibir a identidade de cada canal. Não há token de
  asset/logo nem de cor de marca de canal no DS. Solução provisória: logos em
  `pecas/comparador-publico/assets/` (Shopee.svg, tiktok.svg, ybera.svg).
- **Decisão pendente:** formalizar assets de marca de canal como tokens/assets do DS
  (e, se houver cor de marca aplicada em UI, defini-la — sem inventar; usar a oficial da marca).
- **Dono:** guardião do DS. **Impacto:** comparador e qualquer superfície multi-canal.

---
Changelog:
- 2026-07-14 — criado no fechamento do ciclo `comparador-publico` (Fase 04).
