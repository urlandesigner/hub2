# Rubric — Portão de Qualidade

> Versionado junto com o processo. Cada ciclo pode adicionar itens via fase 04
> (Δrubric) — de preferência nascidos de falhas reais, não de listas imaginadas.
> Severidades: **bloqueio** (impede aprovação) · **ajuste** (corrige no editar) ·
> **nota** (registra, não trava).

## 1. Fidelidade à spec
- 1.1 [bloqueio] A peça resolve a tarefa única da spec — não uma tarefa parecida
- 1.2 [bloqueio] Nada essencial do escopo "dentro" está faltando
- 1.3 [ajuste] Nada do escopo "fora" entrou sem registro no Rastro

## 2. Design system e consistência
- 2.1 [bloqueio] Todo valor visual vem de tokens.json — zero valores inventados
- 2.2 [ajuste] Componentes existentes foram reusados antes de criar novos
- 2.3 [ajuste] Espaçamentos e hierarquia tipográfica seguem a escala do sistema
- 2.4 [ajuste] Ícones e assets de marca vêm de fonte definida no DS (biblioteca de ícones / token de asset), não de emoji ou placeholder. Placeholder sem destino de token vira pendência carimbada

## 3. Percurso e usabilidade
- 3.1 [bloqueio] O percurso completo funciona: entrada → tarefa → confirmação
- 3.2 [bloqueio] Estados cobertos: vazio, carregando, erro, sucesso. Nota: "carregando" de uma
      TELA inteira é sempre bloqueio; "carregando" de um COMPONENTE pequeno dentro de uma página
      já carregada pode ser julgamento do designer — reportar o achado e a severidade sugerida,
      mas deixar o veredito final para quem edita
- 3.3 [ajuste] Hierarquia visual guia para a ação principal
- 3.4 [ajuste] Textos de interface são claros e no idioma correto
- 3.5 [ajuste] Quando um elemento alterna estados via `hidden` + classe de layout (grid/flex/
      table) no mesmo seletor, existe uma regra `[hidden]{display:none!important}` (ou
      equivalente) garantindo que só um estado renderiza por vez — checar isso explicitamente
      via `getComputedStyle`, não só olhando o screenshot (bug recorrente: já apareceu 2x)

## 4. Acessibilidade (mínimo WCAG AA)
- 4.1 [bloqueio] Contraste de texto ≥ 4.5:1 (3:1 para texto grande)
- 4.2 [bloqueio] Alvos de toque ≥ 44×44 no mobile
- 4.3 [ajuste] Foco visível e ordem de navegação lógica

## 5. Critério de sucesso
- 5.1 [bloqueio] O critério de sucesso da spec é verificável na peça montada no
      percurso — se não dá pra checar olhando, o problema escala pra spec

## 6. Craft e direção estética
- 6.1 [ajuste] A peça cumpre a Direção Estética registrada na spec — não é só correta,
      é a aposta visual que foi decidida (personalidade, composição, uso da marca)
- 6.2 [ajuste] Acabamento à altura de produção: hierarquia editorial, respiro, e motion/
      estados de interação quando fazem sentido — não um wireframe funcional
- 6.3 [nota] Sem "genérico de IA": a peça tem intenção visível, não é o default seguro

---
Changelog do rubric:
- v1 (inicial) — base do kit
- v2 (2026-07-14, ciclo comparador-publico) — +2.4 (ícones/assets de fonte definida no DS). Nasceu de falha real: emojis usados como placeholder de ícone, item não coberto pelo rubric v1.
- v3 (2026-07-14, ciclo comparador-publico) — +seção 6 (Craft e direção estética). Nasceu de falha real: comparador aprovado como correto mas genérico, sem a ambição visual de um processo com direção estética. O rubric v2 não tinha como reprovar "correto porém sem craft".
- v4 (2026-07-15, ciclo vinculos-e-onboarding) — +3.5 (checar `hidden`+layout via computed style) e nota em 3.2 (loading de tela vs. componente). O item 3.5 nasceu de um bug que já apareceu 2x em ciclos diferentes (classe de layout sobrepondo `[hidden]`) — deixou de ser acaso.
