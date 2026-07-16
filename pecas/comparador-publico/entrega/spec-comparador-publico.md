# Spec — comparador-publico

status: ativa
entrega: demo (protótipo de handoff — não é código de produção)
ciclo: 1 (reaberto — refino da peça premium a pedido do designer)
atualizada: 2026-07-14

## Resultado esperado
O Cliente Final, ao abrir um link curto (`hub.ybera.com/r/{slug}`) no celular, sem login,
vê o mesmo produto nos canais disponíveis (Wake, Shopee, TikTok Shop) com preço, frete e
total, ordenados por menor custo-benefício, e clica para comprar no canal que escolher —
com o vínculo da influenciadora preservado. **Sucesso checável olhando:** na tela renderizada,
o cliente consegue comparar preço+frete+total lado a lado e chegar ao canal de compra em 1
toque, sem ver canal indisponível, sem login e sem precisar saber que é um HUB Ybera.

## Usuário primário
**Cliente Final (consumidor anônimo).** Recebe a indicação por redes sociais, WhatsApp ou
live. Sem login, sem cadastro, no celular em rede 4G. Quer comparar e comprar onde for mais
conveniente sem pesquisar por conta própria nem abrir mão das condições de quem indicou.

## Escopo
- **Dentro:** comparador público mobile-first; card por canal com preço, frete e total;
  ordenação por menor custo-benefício (preço + frete); ocultação de canal sem estoque, sem
  preço ou sem vínculo; entrada de CEP para cálculo de frete (transitório); CTA de compra por
  canal; estados de tela: vazio, loading, sucesso, erro, canal indisponível/oculto, **link
  inválido/expirado**; rastreamento de clique por canal sem dado pessoal.
- **Fora:** painel de vínculos, status por canal e onboarding OAuth da influenciadora
  (→ spec `vinculos-e-onboarding`); link encurtado gerado pelo Escritório (Cap. 2, insumo do
  comparador, não superfície desta spec); exibição de comissão/ganhos (domínio do Escritório);
  histórico de links (H2); Mercado Livre (H2); admin; internacionalização (v1 = Brasil).

## Restrições
- Acesso **público** — sem autenticação, sem fricção, sem login.
- **Mobile-first**, 4G como referência: LCP < 2,5s, TTI < 3,5s.
- Canal indisponível/sem vínculo é **ocultado**, nunca exibido com dado vazio ou erro.
- CEP **transitório** — sem armazenamento, sem fingerprinting (LGPD-mínima).
- HUB **não exibe** comissão, ganhos ou dado financeiro.
- Guardrails visuais: só tokens de `design-system/tokens.json` (paleta rosa/branco Ybera).
- Canal não faz parte do link — o HUB resolve o canal no carregamento.

## Direção estética
> Definida na passada de craft (via frontend-design). Aposta: **editorial premium quente**.
- **Produtos-régua:** editorial de beleza premium (Aesop/Sephora refinado); a versão v4/E07 como piso de acabamento.
- **Personalidade:** premium · quente · editorial · confiável · sofisticado-feminino (Ybera Club = beleza/haircare).
- **Composição:** hero editorial escuro (foto do produto grande, headline Syne em escala clamp, eyebrow, chip "indicado por"); faixa de confiança; comparador com calor Ybera (acentos club/pink + gradiente club/50 no fundo); rodapé escuro.
- **Uso da marca:** calor Ybera SIM (club como acento, não como fundo chapado), mantendo transparência ao cliente (H-UX-05 = não precisa saber que é HUB, mas é claramente Ybera premium).
- **Dimensões de comparação enriquecidas:** preço, frete, **prazo de entrega**, **parcelamento**, + chips de destaque HONESTOS (derivados só do dado exibido: "Frete grátis", "Menor preço", "Entrega mais rápida", "Sem juros").
- **Motion:** reveal escalonado no load; hover-lift nos cards; press-scale nos botões; foco visível (respeita prefers-reduced-motion).
- **Guardrail:** 100% tokens KZ. Escala display do hero (clamp) é composição sobre a família Syne, não token novo.

## Decisões anteriores
- 3 canais em H1: Wake, Shopee, TikTok Shop (DRP v1.8).
- H-UX-05 (sem branding HUB explícito) tratada como decisão de partida, ainda a validar olhando.
- POC de referência publicada (`poc-hub-ybera.vercel.app`) — estilo/estrutura, não handoff.

## Premissas por risco
| # | Premissa | Criticidade | Evidência | Dono | Prazo |
|---|----------|-------------|-----------|------|-------|
| 1 | Ordenar só por menor custo-benefício (preço+frete), sem filtros, basta pro cliente decidir (H-UX-01) | alta | palpite | Design | validar na POC deste ciclo |
| 2 | Rastreamento de clique por canal sem dado pessoal mantém erro de atribuição < 2% (P0) | alta | pendente | Engenharia (Matheus) | antes da POC oficial |
| 3 | Cobertura real de vínculo é baixa no 1º ciclo → comparador exibirá com frequência 1 só canal (Q-11/Q-12) | alta | escalado, pendente | Comercial + CTO | retorno pendente no DRP |
| 4 | Ocultar canal indisponível (não exibir erro) não gera confusão no cliente (H-UX-03) | média | palpite | Design | validar na POC |
| 5 | Cliente converte sem saber que é HUB Ybera (H-UX-05) | média | palpite | Design + Produto | validar na POC |

## Quebra de tarefas
1. Card de canal (preço, frete, total, CTA comprar) — a peça de dentro pra fora.
2. Entrada de CEP + cálculo de frete (estado antes/depois de informar CEP).
3. Lista/ordenação dos cards por menor custo-benefício.
4. Estado degradado: 1 canal só disponível (premissa #3 — projetar bem, é comum).
5. Estados de exceção: loading, vazio, erro de carga, link inválido/expirado.
6. Página completa mobile-first montada + verificação de percurso.

## Critérios de verificação
O portão (Fase 03) checa, olhando a peça renderizada no viewport mobile:
- Os cards mostram preço + frete + total e estão ordenados do menor pro maior custo-benefício.
- Canal sem estoque/preço/vínculo não aparece — e o layout não "quebra" com 1 só canal.
- CTA de compra leva ao canal certo em 1 toque; nada pede login.
- Nenhum dado financeiro da influenciadora nem branding HUB obrigatório aparece.
- Estados vazio/loading/erro/link-inválido existem e são coerentes com os tokens.

## Rastro
- [2026-07-14] [01] Demanda HUB-001 fatiada em 2 specs (`comparador-publico` + `vinculos-e-onboarding`) — brief cobre 2 jornadas/2 usuários/4 capacidades, largo demais pra 1 página. Decisão do usuário.
- [2026-07-14] [01] Iniciado por `comparador-publico` — sustenta direto o North Star (GMV) e a meta de conversão. Decisão do usuário.
- [2026-07-14] [01] Estado "1 canal só" elevado a tarefa de projeto (não exceção), por conta da incerteza de cobertura (Q-11/Q-12).
- [2026-07-14] [01] Spec aprovada pelo designer (veredito humano). Portão de saída da Fase 01 passa → status `ativa`.
- [2026-07-14] [02] Peça 01 (card de canal) gerada em 3 direções (A linha compacta / B breakdown explícito / C total dominante) em `pecas/comparador-publico/01-card-de-canal.html`. Só tokens KZ. Aguardando escolha do designer.
- [2026-07-14] [02] TOKEN FALTANDO (candidato a Δtokens): não há token de cor/logo de marca de canal (Shopee, TikTok, Wake). Canais representados em monograma neutro (surface/tertiary + text/primary). Decisão pendente: representação neutra vs. marca de canal.
- [2026-07-14] [02] Designer escolheu: (1) combinar direções A+C; (2) usar logos de marca do canal via imagem, resto seguindo o DS. Gerada `01b-card-de-canal-combinado.html` (total dominante + linha compacta).
- [2026-07-14] [02] PENDÊNCIA DE INSUMO: a pasta `assets/` de logos NÃO existia no projeto — foi criada em `pecas/comparador-publico/assets/`, aguardando `shopee`/`tiktok`/`wake` (.png ou .svg). Peça usa fallback de monograma neutro até os arquivos chegarem. Bloqueia o fechamento visual da peça, não a estrutura.
- [2026-07-14] [02] Logos integrados: Shopee.svg, tiktok.svg, e ybera.svg para o canal "Wake" (não há logo Wake próprio — Wake é a plataforma da Loja Ybera). Chip com fundo surface/primary + borda para dar contraste ao logo. Pendência de insumo resolvida.
- [2026-07-14] [02] A DECIDIR (rótulo): canal "Wake" mostrado com ícone Ybera — confirmar se o rótulo pro cliente final é "Wake", "Loja Ybera" ou outro. Afeta reconhecimento no comparador.
- [2026-07-14] [02] Card de canal (01b, A+C) APROVADO pelo designer, com 1 correção: rótulo do canal "Wake" → "Ybera.com" (é o nome que o cliente final reconhece; Wake é só a plataforma). Correção aplicada. RESOLVE a decisão de rótulo acima.
- [2026-07-14] [02] Peça 01 concluída na Fase 02 (aguarda portão de qualidade). Próxima peça: entrada de CEP + cálculo de frete (tarefa 2).
- [2026-07-14] [02] Peça 02 (entrada de CEP) gerada em 2 direções (1 portão / 2 barra progressiva), com estados vazio/confirmado/erro em `pecas/comparador-publico/02-entrada-cep.html`. Só tokens KZ. Aguardando escolha do designer.
- [2026-07-14] [02] NOTA (candidato a Δtokens): ícones usados como placeholder de emoji (📍 ⚠ ✓). O tokens.json não define biblioteca de ícones — a lib/ícone real precisa ser definida antes do handoff.
- [2026-07-14] [02] Designer escolheu Direção 2 (barra progressiva) para o CEP — mais fiel ao "sem fricção"/mobile-first. Decisão de arquitetura: comparador mostra cards de cara; frete+total completam ao informar CEP. Próxima peça: lista/ordenação (compõe card + barra de CEP).
- [2026-07-14] [02] Peça 03 (lista + ordenação) gerada em `pecas/comparador-publico/03-lista-ordenacao.html`: compõe barra de CEP + cards, nos 2 momentos (antes do CEP = ordem por preço; depois = ordem por total com frete). Card "antes do CEP" mostra preço + "frete a calcular", sem badge de melhor custo. Dados ilustrativos escolhidos p/ provar o valor (menor preço ≠ menor total). Aguardando veredito.
- [2026-07-14] [02] NOTA (consistência): números de preço/frete divergem entre 01b e 03 (ilustrativos). Alinhar na peça de página final (tarefa 6) — não é decisão de design.
- [2026-07-14] [02] Peça 03 (lista + ordenação) APROVADA pelo designer, sem correções. Próxima peça: estado degradado (1 canal só).
- [2026-07-14] [02] Peça 04 (estado degradado) gerada em `pecas/comparador-publico/04-estado-degradado.html`: variações 1 canal (card solo protagonista, sem badge, CTA full-width, sem denúncia de ausência — H-UX-03) e 2 canais (lista normal com badge). Aguardando veredito.
- [2026-07-14] [02] Peça 04 (estado degradado) APROVADA pelo designer, sem correções. Próxima peça: estados de exceção.
- [2026-07-14] [02] Peça 05 (estados de exceção) gerada em `pecas/comparador-publico/05-estados-excecao.html`: loading (skeleton leve), vazio (produto indisponível), erro de carga (falha técnica, "tentar de novo"), link inválido/expirado (mensagem + ir p/ Ybera.com). Vazio ≠ link inválido tratados como casos distintos. Aguardando veredito.
- [2026-07-14] [02] NOTA (candidato a Δtokens): ícones de exceção (🛍️ ⚠️ 🔗) são emoji placeholder — mesma pendência de biblioteca de ícones já registrada.
- [2026-07-14] [02] Peça 05 (estados de exceção) APROVADA pelo designer, sem correções. Próxima e última peça: página completa mobile navegável.
- [2026-07-14] [02] Peça 06 (página completa) gerada em `pecas/comparador-publico/06-pagina-completa.html`: NAVEGÁVEL (JS). Hero do produto sem branding HUB (H-UX-05) + barra de CEP sticky + lista + rodapé LGPD ("CEP não é armazenado"). Percurso verificado no viewport mobile: CEP 01310-100 → barra confirma → reordena por menor total → Shopee vira "melhor custo", Ybera.com (menor preço) cai p/ 3º pelo frete. Inclui estado de erro de CEP (00000-000). Números alinhados nesta peça → RESOLVE a nota de consistência.
- [2026-07-14] [02] Todas as 6 peças da quebra de tarefas geradas. Fim da Fase 02. NENHUMA peça é "final": o conjunto precisa ir ao /portao-de-qualidade (Fase 03) antes de qualquer entrega.
- [2026-07-14] [03] PORTÃO — veredicto recomendado: **EDITAR**. Bloqueios só de acessibilidade (pontuais, correção cirúrgica); fidelidade/tokens/percurso/critério passam. Medições no render mobile:
  - [BLOQUEIO 4.1] Badge "Melhor custo": green-700 (#33922A) sobre green-100 (#E6F6E5) = 3.53:1 — abaixo de 4.5:1 exigido p/ texto pequeno (12px bold). Presente em 01b, 03, 04, 06.
  - [BLOQUEIO 4.2] Alvos de toque < 44×44: botão "OK" 52×40, link "Alterar" 46×19, input CEP altura 40. (CTA "Comprar" 100×44 passa.) Presente em 02, 03, 04, 06.
  - [fora do rubric] Ícones em emoji placeholder (📍 ✓ ⚠ 🔗 🛍️) — DS não tem biblioteca de ícones. → candidato a Δrubric + Δtokens na Fase 04.
  - [nota 3.4/consistência] Números de preço/frete divergem entre 01b e 03 (a 06 já consolidou). Alinhar as demais no fechamento.
  - PASSA: 1.1/1.2/1.3 (fidelidade), 2.1/2.2/2.3 (tokens de cor/espaço/tipo, reuso, escala), 3.1 (percurso verificado ao vivo), 3.2 (vazio/loading/erro/sucesso cobertos), 3.3/3.4, 5.1 (critério verificável no percurso).
  - ROTA: editar → volta à Fase 02, corrige SOMENTE os 2 bloqueios. Loop 02⇄03 continua até "aprovar". Veredicto final é do designer.
- [2026-07-14] [03] Veredicto do designer: EDITAR (confirmado). Fix do badge escolhido: escurecer texto.
- [2026-07-14] [02→03] Correção cirúrgica aplicada (só os 2 bloqueios, resto preservado):
  - Bloqueio 4.1: badge "Melhor custo" texto/ícone green-700 → green-800 (#2B6A25). Medido: 5,85:1 sobre green-100 (≥4,5:1 ✓). Aplicado em 01b, 03, 04, 06.
  - Bloqueio 4.2: alvos de toque → 44px. Medido no render (06): OK 52×44, input CEP 213×44, "Alterar" 54×44, "Comprar" 100×44. Aplicado em 02, 03, 04, 06.
  - Percurso re-verificado: intacto, nada quebrou. AMBOS OS BLOQUEIOS RESOLVIDOS.
- [2026-07-14] [03] RE-RODADA DO PORTÃO: zero bloqueios; critério de sucesso verificado no percurso completo. Recomendação: **APROVAR**. Pendências não-bloqueantes p/ Fase 04: Δrubric/Δtokens (biblioteca de ícones), Δtokens (token de marca de canal). Aguardando veredito final do designer.
- [2026-07-14] [03] APROVADO pelo designer. Comparador sai do loop 02⇄03. Avança para Fase 04 (entregar-e-realimentar).
- [2026-07-14] [04] ENTREGA carimbada DEMO (protótipo de handoff). Empacotado em `pecas/comparador-publico/entrega/` (6 peças + assets + cópia da spec + HANDOFF.md). Status da spec → entregue.
- [2026-07-14] [04] APRENDIZADOS ROTEADOS:
  - Δrubric: +item 2.4 (ícones/assets de fonte definida no DS) em `rubric.md`, changelog v2. Nasceu da falha real dos emojis placeholder.
  - Δtokens: 2 pendências registradas em `design-system/pendencias-tokens.md` (biblioteca de ícones; marca de canal). NÃO editei tokens.json — não invento valores; green-800 do fix já existia no DS.
  - nota: 2 comentários em `aprendizados.md` (consistência de dados entre peças; POC de referência a atualizar).
  - Pendências não-design carimbadas no HANDOFF: atribuição <2% (Eng), cobertura Q-11/Q-12 (Comercial+CTO), tokens hardcoded e números ilustrativos (Eng na implementação).
- [2026-07-14] [04] CICLO 1 FECHADO. Os Δ aplicados são entrada da próxima Fase 01.
- [2026-07-14] [meta] Comparação com processo v4 (E07): o resultado ficou "correto porém genérico". Causa: processo compacto não tinha fase de direção estética nem craft no rubric, e frontend-design não foi invocado. CORRIGIDO O PROCESSO: gerar-com-guardrails agora exige frontend-design + Direção Estética; template ganhou seção `## Direção estética`; rubric ganhou seção 6 (craft), changelog v3; CLAUDE.md ganhou regra 5b. Ciclo reaberto p/ passada de craft.
- [2026-07-14] [02·craft] Peça 07 (`07-comparador-premium.html`) gerada sob a Direção Estética: hero editorial escuro + calor Ybera (club/pink, gradiente club/50) + faixa de confiança + cards ricos (preço/frete/prazo/parcelamento) + chips de destaque honestos + motion. Navegável (CEP→reordena). 2 bugs corrigidos na geração: (a) `[hidden]` faltando fazia a barra de CEP mostrar 2 estados juntos; (b) badge "Melhor custo" aparecia antes do CEP (sem total) — agora só depois.
- [2026-07-14] [03·craft] PORTÃO (rubric v3) sobre a 07: a11y medida OK (badge 5,87:1 · chip 7,78:1 · frete 6,58:1 · toques ≥44). Craft (§6) PASSA com folga. Fidelidade/tokens/percurso/critério OK. ÚNICO ACHADO — [BLOQUEIO 3.2] cobertura de estados: a 07 cobre sucesso + degradado (1/2/3 canais por dado) + erro de CEP, mas loading, vazio e link-inválido ainda só existem nas peças 04/05 no estilo austero antigo — precisam ser portados p/ o skin premium. Recomendação: EDITAR (portar 3 estados).
- [2026-07-14] [03·craft] Veredito do designer: APROVAR a 07 como entregável premium. Escopo aceito = caminho feliz + CEP + degradado (1/2/3) + erro de CEP. Estados loading/vazio/link-inválido movidos para FOLLOW-UP carimbado (existem no estilo austero em 04/05; portar ao skin premium num próximo ciclo). Dentro do escopo aceito: zero bloqueios.
- [2026-07-14] [04·craft] RE-ENTREGA: peça 07 adicionada a `entrega/` como entregável principal; HANDOFF atualizado (carimbo demo, direção estética, follow-up dos estados). Status → entregue. Ciclo 1 re-fechado no patamar premium.
- [2026-07-14] [02·craft] FOLLOW-UP RESOLVIDO: peça 07b (`07b-estados-premium.html`) porta loading, vazio e link-inválido/404 para o skin premium. Portão: a11y medida OK (vazio 8,04:1 · 404 16,66:1 · eyebrow 5,31:1 · toques 48px). Cobertura de estados (rubric 3.2) agora COMPLETA no skin premium (07 = sucesso/degradado/erro-CEP; 07b = loading/vazio/link-inválido). Bloqueio 3.2 resolvido — zero bloqueios no conjunto. Adicionada a `entrega/`.
- [2026-07-14] [02·craft] PASSE DO TRIO (emil-design-eng + ux-copy) sobre 07/07b:
  - emil: corrigido BUG — reveal com `animation forwards` fixava o transform e matava o hover-lift; trocado por reveal via transição + IntersectionObserver (scroll-trigger, stagger 70ms por card, interrompível). `.reveal` 600→500ms. `:active` no "alterar". reduced-motion neutraliza estado inicial do card.
  - ux-copy: estado vazio "Tenta de novo em alguns minutos" → "Tente de novo em instantes — o link continua valendo"; botão "Atualizar" → "Tentar de novo". Resto da copy mantido (já sólido).
  - Verificado no render mobile: stagger dispara ao entrar no viewport (antes tocava fora da tela, invisível); hover-lift funcional; sem regressão de a11y (cores/alvos inalterados). 07/07b re-sincronizadas em `entrega/`.
- [2026-07-14] [02·craft] FIX DE ROBUSTEZ (07): o reveal-on-scroll deixava os cards dependentes do IntersectionObserver — se o IO não disparasse (grid já em viewport no load, ou scroll não processado), os cards ficavam presos em opacity:0. Trocado por reveal defensivo: cards em/próximos do viewport revelam já (stagger 70ms); abaixo da dobra via IO; + rede de segurança (setTimeout 900ms revela qualquer card preso). Card nunca fica invisível por falha do IO. Verificado no render.
- [2026-07-14] [02·refino] HERO calibrado a pedido do designer (estava extenso, sem hierarquia): lead 3 linhas → 1 frase ("Compare o mesmo produto entre os canais oficiais e compre onde for melhor"); reordenado por importância (h1 → valor → CTA+preço → indicação); chip da influenciadora movido p/ abaixo do CTA e aliviado (sem pílula, avatar 40→32). Verificado no render desktop. Sem impacto de a11y.
- [2026-07-15] [02·refino] Chip "Indicada por Ana C." movido do meio da coluna esquerda (abaixo do CTA) para uma linha de topo (`.hero-top`), ao lado do wordmark, alinhado à direita — a pedido do designer. `flex-wrap` garante que quebra pra 2ª linha em telas estreitas em vez de espremer. Verificado no render desktop (1280px); hero ficou mais limpo sem o chip empilhado. Sincronizado em `entrega/`.
- [2026-07-15] [02·refino] Wordmark do header trocado: ícone `ybera.svg` + `<span>YBERA</span>` (texto falso em Syne) → logo real `assets/logo-black.svg` (logotype oficial), com `filter:brightness(0) invert(1)` pra ficar branco no hero escuro — a pedido do designer. Aplicado só no header (canto superior); o rodapé ainda usa a versão ícone+texto antiga, não mexido (fora do pedido). Verificado no render — legível em branco a 22px de altura. Sincronizado em `entrega/`.
- [2026-07-15] [02·refino] H1 do hero trocado: "Liso de salão, em casa." → "A melhor oferta do **Óleo de Mirra** num só lugar." — a pedido do designer, com destaque (`<em>`, pink club-400) em "Óleo de Mirra". Verificado no render: quebra em 3 linhas equilibradas, sem overflow. Sincronizado em `entrega/`.
- [2026-07-15] [meta] INCONSISTÊNCIA DE NOMENCLATURA (a decidir): o resto da peça ainda chama o produto de "Realinhamento Térmico" (eyebrow, título da página, alt da foto, nome nos cards de canal), enquanto o frasco na foto real tem "MIRRA" escrito nele e o H1 agora diz "Óleo de Mirra" — o próprio novo H1 corrige um nome que já estava errado. Não alterado em cascata (fora do pedido pontual); flagged para o designer decidir se unifica tudo para "Óleo de Mirra"/"Mirra".
