# Spec — vinculos-e-onboarding

status: entregue
entrega: demo (POC de handoff — não é código de produção)
ciclo: 1
atualizada: 2026-07-15

## Resultado esperado
Na Tela A (Catálogo Virtual, `app.yberaclub.com`), a Influenciadora vê — sem scroll, acima do
grid de produtos — o estado de vínculo de cada canal (Wake, Shopee, TikTok) e, quando precisa
agir, resolve isso **sem sair da tela**: um link contextual abre o modal certo (Modal A —
primeira vinculação, ou Modal B — reautorização) direto na Tela A. **Sucesso checável olhando:**
o painel mostra os 3 canais com o estado correto (vinculado / nunca vinculou / vínculo expirado);
clicar no link do canal pendente abre o modal com o tom certo pro estado (onboarding vs.
reautorização); nenhum modal navega pra fora da Tela A.

## Usuário primário
**Influenciadora (Parceira da rede Club Brasil).** Já vende via múltiplos canais e monitora
ganhos no Escritório Virtual. **Não acessa o HUB diretamente** — opera via front do Escritório,
que consome os endpoints do HUB. Para ela, o estado de vínculo precisa ser **visível e acionável**
(o oposto do comparador público, que oculta o que falta).

## Escopo
- **Dentro:** painel de vínculos no topo da Tela A (Wake / Shopee / TikTok, 3 estados cada:
  vinculado, nunca vinculou, vínculo expirado); link contextual por canal pendente; **Modal A**
  (primeira vinculação: instrução + vídeo tutorial, tom onboarding); **Modal B** (reautorização:
  mesma estrutura de instrução+vídeo, copy e ícone distintos — não trata a influenciadora como
  novata); estado "nunca vinculou" tratado como comum (não exceção), por causa da incerteza de
  cobertura no 1º ciclo.
- **Fora:** comparador público e link encurtado (→ spec `comparador-publico`, já entregue);
  qualquer dado financeiro/comissão (domínio exclusivo do Escritório, fora do HUB); área logada
  da influenciadora no HUB (não existe — H-UX-02); grid de produtos e botões "Copiar"/"Comprar"
  em si (estrutura já estabelecida, não é objeto desta demanda — só o painel de vínculos que fica
  acima dele); fluxo técnico de OAuth/refresh de token (RF-11, engenharia); admin/configuração de
  canais.

## Restrições
- A influenciadora **não tem área logada no HUB** — tudo o que ela vê está no Escritório Virtual.
- HUB **não exibe** comissão, ganhos ou dado financeiro (nesta jornada nem em nenhuma).
- Estado de vínculo é **visível e acionável** para a influenciadora — o inverso do comparador
  público, que oculta. Não confundir os dois padrões.
- **Sem navegação para fora da Tela A**: todo o fluxo de vinculação/reautorização é via
  link → Modal, nunca painel de configurações separado ou troca de página.
- Modal A e Modal B compartilham estrutura (instrução + vídeo) mas **tom e copy distintos** —
  nunca tratar reautorização como se fosse primeira vez.
- Guardrail visual: fidelidade ao Escritório Virtual real, via cache da skill
  `dev-mode-virtual-office` (tokens `kz-*`/`b2c-*`, snippets reais, assets reais) — não o
  `design-system/tokens.json` genérico desta pasta. Ver "Decisões anteriores".

## Direção estética
> Registrada retroativamente na Fase 03 (o portão apontou a ausência — geração via
> `dev-mode-virtual-office` pulou o passo da `gerar-com-guardrails` que normalmente obriga
> esta seção antes de gerar).
- **Aposta:** fidelidade total ao Escritório Virtual real — sem invenção visual, sem direção
  editorial nova. O "craft" aqui é a réplica fiel (sidebar, topbar, cards, badges, tokens
  `b2c-*`/`kz-*`, Syne/Nunito reais), não uma composição ousada como no comparador público.
- **Por quê:** é um componente que passa a viver **dentro** de um app existente e consolidado
  (dashboard de influencer) — inovação visual aqui é risco, não ganho. O app real já é sóbrio
  (cards brancos, sombras quase nulas, layout flat); a direção certa é continuar assim.
- **Onde inova de fato:** só no conteúdo/estrutura do painel de vínculos em si (que não existe
  no app hoje) — não na linguagem visual, que é 100% herdada do real.

## Decisões anteriores
- H-UX-04 **resolvida**: onboarding OAuth é conduzido inteiramente via Escritório Virtual, sem
  tela própria no HUB — via link → Modal, sem navegação para fora da Tela A (Brief v1.1).
- 3 estados por canal (não 2): vinculado / nunca vinculou / vínculo expirado — o estado
  "expirado" é novo nesta versão (origem RF-11 do DRP) e a POC de referência anterior
  (`poc-hub-ybera.vercel.app`) não o contemplava; precisa ser atualizado.
- Shopee deixou de ser "canal simples" — agora exige OAuth individual igual ao TikTok
  (decisão Q-10 do DRP v1.8, validada por Engenharia/Matheus).
- Origem do dado de status: lido direto do banco (RF-10), não inferido/calculado.
- Fatiamento desta spec definido no ciclo do `comparador-publico`: HUB-001 cobre 2 jornadas —
  esta é a segunda (Influenciadora/Escritório), a primeira (Cliente Final/comparador) já entregue.

## Premissas por risco
| # | Premissa | Criticidade | Evidência | Dono | Prazo |
|---|----------|-------------|-----------|------|-------|
| 1 | Cobertura real de influenciadoras que vão autorizar Shopee/TikTok no 1º ciclo é baixa → "nunca vinculou" será o estado mais comum (Q-11/Q-12 do DRP) | alta | escalado, pendente | Comercial + CTO | retorno pendente no DRP |
| 2 | Modal B (reautorização) reaproveitar estrutura do Modal A com só copy/ícone distintos basta pra não gerar confusão emocional ("mas eu já fiz isso!") | média | decisão de produto, não validada com usuário | Design | validar na peça |
| 3 | Painel de vínculos cabe "acima do grid, sem scroll" nas larguras reais do Escritório (mobile e desktop) sem espremer o grid | média | depende do layout real (dev-mode-virtual-office) | Design | validar na captura/peça |

## Quebra de tarefas
1. Componente de status por canal — a peça-núcleo (3 estados: vinculado / nunca vinculou /
   vínculo expirado), usando os snippets reais do Escritório (`status-badges.html`,
   `influencer-card.html`) como base de fidelidade.
2. Painel de vínculos completo (3 canais lado a lado) integrado à Tela A real (sidebar + topbar +
   grid capturados pelo dev-mode-virtual-office).
3. Modal A — primeira vinculação (instrução + vídeo, tom onboarding).
4. Modal B — reautorização (mesma estrutura, tom e ícone distintos — não é novata).
5. Estado "nunca vinculou" como cenário comum — validar que o painel não fica "vazio/pobre"
   quando a maioria dos canais está nesse estado (ligunderado à premissa #1).
6. Tela A completa montada (painel + grid real) + percurso: clicar no link do canal certo abre
   o modal certo.

## Critérios de verificação
O portão (Fase 03) checa, olhando a peça renderizada:
- Os 3 canais aparecem com o estado correto e visualmente distinguível (vinculado / nunca
  vinculou / vínculo expirado) — nunca oculto (ao contrário do comparador público).
- Link do canal "nunca vinculou" abre o Modal A; aviso do canal "vínculo expirado" abre o
  Modal B — modais visivelmente distintos em tom/copy/ícone, mesma estrutura de instrução+vídeo.
- Nenhum clique navega pra fora da Tela A (tudo via modal).
- Painel fica acima do grid, sem empurrar o layout pra scroll extra.
- Fidelidade ao Escritório real: tokens, tipografia e componentes batem com a captura da
  `dev-mode-virtual-office` (não valores inventados).

## Rastro
- [2026-07-14] [01] Demanda fatiada do ciclo `comparador-publico` (HUB-001 cobre 2 jornadas). Esta é a 2ª spec — jornada da Influenciadora/Escritório Virtual.
- [2026-07-14] [01] Enquadrado a partir dos insumos existentes (`brief.md` v1.1, seções "Padrão de Vinculação" e "Telas de referência"). Usuário confirmou: enquadrar a partir do que já está nos insumos, sem trazer insumo novo.
- [2026-07-14] [01] Geração desta demanda vai usar a skill `dev-mode-virtual-office` diretamente (cache real do Escritório: tokens, snippets, assets) em vez de `gerar-com-guardrails`/tokens.json genérico — decisão do designer, registrada como restrição de guardrail acima.
- [2026-07-14] [01] Fora de escopo: grid de produtos e botões Copiar/Comprar em si (estrutura já estabelecida) — só o painel de vínculos que fica acima dele é objeto desta spec.
- [2026-07-15] [02] Skill `dev-mode-virtual-office` instalada em `~/.claude/skills/` com cache curado (captura de 2026-06-26): confirmada a rota real `/catalog` = "Catálogo de Produtos" (Tela A), sem painel de vínculos ainda — é o componente novo desta demanda. Fonte de verdade: `design_system.json` moderno (Tailwind/shadcn, tokens b2c-*/kz-*, Syne/Nunito) — o `SUMMARY.md` da skill descreve o tema Skote/Poppins legado, desatualizado; ignorado.
- [2026-07-15] [02] POC gerada via `dev-mode-virtual-office` (não `gerar-com-guardrails`, por escolha do designer) em `~/ybera-pocs/vo-vinculos-e-onboarding/index.html`. Monta sobre markup/classes reais capturados (sidebar, topbar, card `/catalog`, badges de `status-badges.html`) + assets reais copiados (logo, avatar). Cobre as tarefas 1-4 e 6 da quebra: painel com os 3 estados (Wake vinculado / Shopee nunca vinculou / TikTok expirado, cenário didático com os 3 juntos), Modal A e Modal B funcionais (JS nativo, ESC, foco, sem navegação pra fora da Tela A).
- [2026-07-15] [02] AJUSTE DE FIDELIDADE CONSCIENTE: o par bg/texto do badge "Aprovado" capturado em `status-badges.html` (bg-b2c-positive-30/text-b2c-positive-20) mede 2,08:1 de contraste — falha WCAG. Trocado para bg-b2c-positive-10/text-b2c-positive-40 (mesmo palette, "soft badge", 5,6:1) nos 3 badges de vínculo. Registrar como Δ a levar para o guardião do DS do Escritório (o badge real do app tem esse problema).
- [2026-07-15] [02] FIX DE ACESSIBILIDADE (medido no render, 2 rodadas): alvos de toque abaixo de 44px em 3 elementos — link de canal pendente/expirado (36px→44px), ícones do topbar (40px→44px), botão fechar do modal (36px→44px). Todos corrigidos e re-verificados (desktop 1280px + mobile 375px). Contraste dos links re-checado contra o fundo real (card branco, não cinza): 4,71:1 e 5,07:1 — passam.
- [2026-07-15] [02] Peça APROVADA pelo designer (veredito no percurso: painel + os 2 modais funcionais, tom distinto confirmado). Avança para o portão de qualidade (Fase 03).
- [2026-07-15] [03] PORTÃO (rubric v3) — achados e correções:
  - [BLOQUEIO 4.1/4.2, RESOLVIDO] Contraste do badge (2,08:1→5,6:1) e 3 alvos de toque <44px (link de canal, ícones do topbar, botão fechar) — corrigidos e re-medidos em desktop+mobile.
  - [AJUSTE 4.3, RESOLVIDO] Foco vazava pro conteúdo de fundo com o modal aberto (17 elementos focáveis fora do modal). Corrigido com `inert` no `.shell` ao abrir; verificado via JS que o foco não escapa mais.
  - [AJUSTE 6.1, RESOLVIDO] Seção "Direção estética" faltava na spec (pulada por termos usado dev-mode-virtual-office em vez de gerar-com-guardrails). Documentada retroativamente acima: fidelidade total ao Escritório real é a aposta consciente.
  - [AJUSTE 2.4] Ícones da sidebar são emoji, não os ícones reais do app (Material Design Icons/Font Awesome 5, confirmado no SUMMARY da skill). Mesma pendência já registrada no Δrubric v2 do ciclo comparador-publico — não relançar, já é conhecida.
  - [ACHADO 3.2, para o designer decidir severidade] Não há skeleton de loading para o painel enquanto o status é buscado do banco (RF-10 implica fetch assíncrono) — hoje o painel "aparece pronto". Rubric lista "carregando" como estado bloqueante, mas o contexto (painel pequeno dentro de página já carregada, não uma tela cheia) pode justificar tratar como ajuste, não bloqueio. Veredito é do designer.
  - [VALIDADO — task 5] Cenário com os 3 canais em "nunca vinculou" (majoritário, premissa #1) testado: painel permanece calmo, acionável, sem parecer vazio/pobre. PASSA.
  - PASSA sem ressalvas: 1.1/1.2/1.3 (fidelidade e escopo), 2.1/2.2/2.3 (tokens/reuso/escala), 3.1/3.3/3.4 (percurso, hierarquia, copy), 5.1 (critério verificável), 6.2/6.3 (acabamento à altura do app real / não-genérico).
  - Recomendação: **editar** só se o designer julgar o loading necessário agora; senão, **aprovar** com a nota registrada como aprendizado (Δrubric candidato: distinguir "loading de tela" vs "loading de componente" no critério 3.2).
- [2026-07-15] [03] Designer escolheu: adicionar o loading agora. Skeleton implementado (3 linhas pulsando, mesma forma dos cards reais: ícone/nome/badge/texto) + `role=status` para leitor de tela + `prefers-reduced-motion` respeitado. Troca skeleton→real após breve espera simulada (700ms, sem backend real nesta POC).
- [2026-07-15] [03] BUG PEGO NA VERIFICAÇÃO (mesma classe do bug já visto no ciclo comparador-publico): `.vinc-grid{display:grid}` sobrepunha o atributo `hidden` nativo — os dois blocos (skeleton e real) apareciam empilhados ao mesmo tempo. Corrigido com `[hidden]{display:none!important}` global. Verificado via JS (`getComputedStyle`) que agora só um bloco renderiza por vez. **Aprendizado recorrente — candidato a Δrubric/checklist**: sempre que uma peça usa `hidden` + classes de layout (grid/flex) no mesmo elemento, checar essa sobreposição antes de dar por certo.
- [2026-07-15] [03] PORTÃO — zero bloqueios, zero ajustes em aberto. Recomendação: **APROVAR**. Pendências não-bloqueantes para Fase 04: Δrubric (ícones de sidebar ainda emoji — mesma pendência do ciclo anterior; distinguir loading de tela vs componente no item 3.2; checklist de `hidden`+layout). Aguardando veredito final do designer.
- [2026-07-15] [03] APROVADO pelo designer. Peça sai do loop 02⇄03. Avança para Fase 04 (entregar-e-realimentar).
- [2026-07-15] [04] ENTREGA carimbada DEMO (POC de handoff). Canônico em `~/ybera-pocs/vo-vinculos-e-onboarding/` (convenção da skill); cópia rastreável em `pecas/vinculos-e-onboarding/entrega/` (+ HANDOFF.md). Status da spec → entregue.
- [2026-07-15] [04] APRENDIZADOS ROTEADOS:
  - Δrubric: +item 3.5 (checar `hidden`+layout via computed style — bug recorrente, 2ª vez) e nota em 3.2 (loading de tela vs. componente) em `rubric.md`, changelog v4.
  - nota: `SUMMARY.md` da skill `dev-mode-virtual-office` desatualizado (descreve tema legado como se fosse o atual) — sem destino interno a este projeto, registrado em `aprendizados.md` para ação numa sessão futura.
  - Pendências não-design carimbadas no HANDOFF: ícones reais do Escritório (Guardião do DS/Eng), vídeo/copy real dos modais (Suporte/Conteúdo), loading/backend real (Eng na integração), cobertura Q-11/Q-12 (Comercial+CTO), refresh de token RF-11 (Eng).
- [2026-07-15] [04] CICLO 1 FECHADO. Os Δ aplicados (rubric v4) já valem para qualquer ciclo seguinte, incluindo o do comparador-publico se reaberto.
