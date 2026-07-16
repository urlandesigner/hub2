# Handoff — Painel de Vínculos + Onboarding OAuth (Escritório Virtual)

**Demanda:** `vinculos-e-onboarding` · **Ciclo:** 1 · **Data:** 2026-07-15
**Carimbo:** 🟡 **DEMO / POC de handoff** — NÃO é código de produção.
**Veredicto:** aprovado no portão de qualidade (Fase 03), zero bloqueios após correções.

---

## O que é

Componente novo para a **Tela A (Catálogo Virtual, `app.yberaclub.com/catalog`)**: um painel de
vínculos por canal (Wake, Shopee, TikTok) com 3 estados — vinculado / nunca vinculou / vínculo
expirado — e os 2 modais de onboarding OAuth (Modal A — primeira vinculação; Modal B —
reautorização). Resolve H-UX-04: todo o fluxo acontece via link → Modal, sem sair da Tela A.

Escopo, restrições e premissas completos em `spec-vinculos-e-onboarding.md` (cópia incluída).

## Onde está

| Local | O que é |
|---|---|
| `~/ybera-pocs/vo-vinculos-e-onboarding/` | **Canônico** — convenção da skill `dev-mode-virtual-office`. Abrir `index.html` direto no navegador (usa caminhos relativos `assets/`). |
| `pecas/vinculos-e-onboarding/entrega/` (aqui) | Cópia rastreável dentro do projeto — `vo-vinculos-e-onboarding.html` + `assets/` + spec. |

**Como gerada:** com a skill `dev-mode-virtual-office`, direto sobre o cache real do Escritório
(captura de 2026-06-26) — sidebar, topbar e card da página `/catalog` real, badges de
`status-badges.html`, tokens `b2c-*`/`kz-*` e fontes Syne/Nunito reais. **Não** foi usada a
`gerar-com-guardrails`/`tokens.json` genérico — decisão do designer, registrada no rastro.

## O que tem

- **Painel de vínculos** acima do grid, sem scroll extra — Wake ✅ / Shopee ⚪ / TikTok ⚠️,
  os 3 estados juntos para inspeção rápida.
- **Skeleton de loading** (3 linhas pulsando) antes do status real aparecer — simula o fetch
  do RF-10; troca automática após ~700ms nesta POC (sem backend real).
- **Modal A** (primeira vinculação) e **Modal B** (reautorização) — funcionais, com tom, ícone
  e copy distintos; instrução numerada + placeholder de vídeo (claramente marcado como tal).
- Testado com o cenário **majoritário** da premissa de cobertura baixa (Q-11/Q-12): os 3 canais
  em "nunca vinculou" ao mesmo tempo — painel permanece calmo, não fica "vazio/pobre".

## Decisões de design (o "porquê")

- **Direção estética = fidelidade total**, não invenção visual (documentado retroativamente na
  spec após o portão apontar a ausência). O app real já é sóbrio; a inovação está só no
  conteúdo/estrutura do painel, que é novo.
- **"Nunca vinculou" tratado como estado comum**, não exceção — estilo neutro (não vermelho/
  alarmante), porque a cobertura real de autorização no 1º ciclo é incerta (Q-11/Q-12 do DRP).
- **Ajuste de contraste consciente**: o par de cor do badge "Aprovado" copiado do app real
  (`status-badges.html`) mede 2,08:1 — abaixo do WCAG AA. Usado um par do mesmo palette
  (`b2c-positive-10`/`b2c-positive-40`, 5,6:1) em vez de replicar o bug real.

## Guardrails visuais

Tokens e componentes vêm do **cache real do Escritório Virtual** (`~/.claude/skills/dev-mode-virtual-office/cache/`),
não do `design-system/tokens.json` deste projeto (que é o DS do comparador público — app
diferente). Fonte de verdade: `design_system.json` moderno (Tailwind/shadcn, tokens `b2c-*`/
`kz-*`, Syne/Nunito) — **não** o tema Skote/Poppins legado que o `SUMMARY.md` da skill descreve
desatualizadamente.

## Pendências carimbadas (não bloqueiam o handoff de design)

| # | Pendência | Dono | Prazo |
|---|-----------|------|-------|
| 1 | **Ícones da sidebar são emoji** — o app real usa Material Design Icons + Font Awesome 5; não substituídos nesta POC | Guardião do DS / Engenharia | na implementação |
| 2 | **Vídeo do Modal A/B é placeholder** — copy e asset real de tutorial a definir | Suporte / Conteúdo | antes do handoff oficial |
| 3 | **Loading simulado (700ms fixo)** — em produção, o tempo real depende do endpoint RF-10 | Engenharia | na integração |
| 4 | **Botões "verificar status" sem ação real** — nesta POC não há backend; em produção, disparam refetch do status | Engenharia | na integração |
| 5 | **Cobertura real Q-11/Q-12** — quantas influenciadoras autorizam Shopee/TikTok no 1º ciclo | Comercial + CTO | retorno pendente no DRP |
| 6 | **Refresh automático de token (RF-11)** — comportamento técnico do estado "vínculo expirado" | Engenharia | detalhamento pendente |

## Aprendizados desta peça (para Fase 04 — roteados separadamente)

- Bug recorrente: `display:grid` (ou `flex`) numa classe reaplicada a um elemento com `hidden`
  sobrepõe o `display:none` nativo — mesma causa-raiz já vista no ciclo `comparador-publico`.
  Candidato a checklist do portão.
- Rubric 3.2 ("carregando" como estado bloqueante) não distinguia loading de **tela** vs. de
  **componente** — decisão de severidade teve que ser levada ao designer manualmente.
