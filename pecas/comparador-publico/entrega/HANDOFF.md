# Handoff — Comparador público (HUB Ybera)

**Demanda:** `comparador-publico` · **Ciclo:** 1 · **Data:** 2026-07-14 (re-sincronizado em 2026-07-16)
**Carimbo:** 🟡 **DEMO / protótipo de handoff** — NÃO é código de produção.
**Veredicto:** aprovado no portão de qualidade (Fase 03). Ciclo passou por uma **passada de craft** (direção estética editorial premium) após comparação com o processo v4.

> **Nota de re-sincronização (2026-07-16):** este pacote havia ficado desatualizado — várias rodadas de refino no arquivo de trabalho (`07-comparador-premium.html`) nunca tinham sido replicadas aqui, apesar do rastro da spec registrar "sincronizado em entrega/" a cada rodada. `07-comparador-premium.html`, `spec-comparador-publico.md` e este HANDOFF foram atualizados para refletir o estado atual. Ver `## Rastro` da spec para o histórico completo do que mudou desde 2026-07-14.

## ⭐ Entregável principal: `07-comparador-premium.html`
Versão premium (editorial + calor Ybera + 4 dimensões de comparação + chips honestos + motion),
aprovada como o entregável deste ciclo. As peças `01b`–`06` são o histórico da construção
(exploração de direções, estados e composição) — mantidas para rastro, não são o entregável.

**Direção Estética** (registrada na spec): editorial premium quente — hero escuro, headline Syne
em escala, chip da embaixadora, faixa de confiança, cards ricos (preço/frete/prazo/parcelamento),
card destacado com moldura preta (`--n-900`) + badge "⭐ Melhor oferta" + subtítulo "Canal
verificado" + chips de destaque derivados só do dado exibido, gradiente club/50, motion.

### Estados de exceção — COMPLETOS no skin premium
- `07-comparador-premium.html`: sucesso, degradado (1/2/3 canais), erro de CEP.
- `07b-estados-premium.html`: **loading, vazio, link-inválido/404** — portados para a linguagem
  premium (follow-up resolvido no mesmo ciclo). Em produção entram no `render()` da 07 conforme
  a disponibilidade real dos canais. A11y medida OK (contrastes ≥ 5,3:1; alvos de toque ≥ 44).

---

## O que é

Comparador público do HUB: página mobile-first, sem login, aberta por link curto
(`hub.ybera.com/r/{slug}`). Mostra o mesmo produto em até 3 canais (Shopee, TikTok Shop,
Ybera.com) com preço, frete e total, ordenados por **menor custo-benefício**, e leva o
cliente ao canal escolhido em 1 toque — com o vínculo da influenciadora preservado.

Escopo, restrições e premissas completos em `spec-comparador-publico.md` (cópia incluída).

## Peças (ordem de leitura, de dentro pra fora)

| Arquivo | O que é |
|---|---|
| `01b-card-de-canal-combinado.html` | Card de canal (peça-núcleo). Direção A+C: total como herói + linha compacta |
| `02-entrada-cep.html` | Barra de CEP progressiva — estados vazio / confirmado / erro |
| `03-lista-ordenacao.html` | Lista + ordenação, nos 2 momentos (antes/depois do CEP) |
| `04-estado-degradado.html` | 1 canal e 2 canais — ocultação graciosa (H-UX-03) |
| `05-estados-excecao.html` | Loading, vazio, erro de carga, link inválido/expirado |
| `06-pagina-completa.html` | Página completa navegável (versão austera, pré-craft) — histórico |
| **`07-comparador-premium.html`** | ⭐ **ENTREGÁVEL** — versão premium navegável. Digite um CEP e veja a reordenação por menor total |
| **`07b-estados-premium.html`** | ⭐ Estados de exceção no skin premium: loading, vazio, link-inválido/404 |

Abrir via servidor local (as peças usam `assets/`):
`cd entrega && python3 -m http.server 8000` → `http://localhost:8000/07-comparador-premium.html`

## Decisões de design (o "porquê")

- **Barra de CEP progressiva** (não portão): cards aparecem de cara; frete+total completam
  quando o CEP chega. Escolhido por fidelidade à restrição "sem fricção" e ao mobile-first.
- **Estado "1 canal só" é protagonista, não erro**: a incerteza de cobertura (Q-11/Q-12 do DRP)
  o torna comum. Sem comparação → sem badge, card ganha peso, CTA full-width.
- **Rótulo do 3º canal = "Ybera.com"** (Wake é só a plataforma; é o nome que o cliente reconhece).
- **Sem branding HUB explícito** (H-UX-05): experiência transparente.
- **Menor preço ≠ menor total**: os dados de exemplo provam o valor — Shopee tem o menor
  preço de produto, mas o frete grátis da Ybera.com faz ela vencer no total (custo-benefício).

## Guardrails visuais

Todos os valores de cor, espaçamento, raio e tipografia vêm do **KZ Design System**
(`design-system/tokens.json`). Fontes: Syne (títulos) + Nunito Sans (texto).
Card destacado usa `--n-900` (`#1E1E1F`) como moldura/CTA — texto branco sobre preto,
contraste ≈17:1 (WCAG AAA). Alvos de toque ≥ 44×44.

## Pendências carimbadas (não bloqueiam o handoff de design)

| # | Pendência | Dono | Prazo |
|---|-----------|------|-------|
| 1 | **Tokens hardcoded no HTML** — protótipo replica o KZ manualmente; produção deve consumir tokens.json | Engenharia | na implementação |
| 2 | **Marca de canal sem token** — logos vêm de `assets/`; DS não tem token de asset/marca de canal | Guardião do DS | ver `design-system/pendencias-tokens.md` |
| 3 | **Biblioteca de ícones** — 📍✓⚠🔗🛍️ são emoji placeholder; DS não define lib de ícones | Guardião do DS | ver `design-system/pendencias-tokens.md` |
| 4 | **Números de preço/frete** são ilustrativos — dados reais vêm dos endpoints do HUB | Engenharia | na integração |
| 5 | **Erro de atribuição < 2%** (P0) — rastreamento de clique por canal sem dado pessoal | Engenharia (Matheus) | antes da POC oficial |
| 6 | **Cobertura real Q-11/Q-12** — quantas influenciadoras autorizam Shopee/TikTok no 1º ciclo | Comercial + CTO | retorno pendente no DRP |

## O que Design devolve para Produto (do brief)

- Fluxos + estados de tela por capacidade (comparador): entregues nas peças 01b–06.
- Decisões de UX para a seção 12 da Feature HUB-001: ver "Decisões de design" acima + `## Rastro` da spec.
- Hipóteses: H-UX-01 (ordenar só por custo-benefício) e H-UX-03 (ocultar canal) prototipadas
  para validação; H-UX-05 (sem branding HUB) aplicada.
