# Aprendizados (notas sem destino roteado)

> O que chega aqui é comentário — útil, mas não atribuível a Δspec, Δtokens ou Δrubric.
> Revisar periodicamente: alguns comentários amadurecem e ganham destino.

## Ciclo comparador-publico (2026-07-14)
- **Consistência de dados entre peças durante a prototipagem:** os números de preço/frete
  divergiram entre a peça 01b e a 03 até a 06 consolidar. Não é Δspec/tokens/rubric — é
  disciplina de processo: fixar o conjunto de dados de exemplo numa peça-fonte cedo. Comentário.
- **POC de referência (`poc-hub-ybera.vercel.app`) desatualizada:** o brief pede atualizá-la
  para os estados atuais. Fora do escopo desta demanda (é da jornada da influenciadora / POC
  oficial), mas fica a nota para não se perder.
- **Links do rodapé sem destino real** (2026-07-15): "Sobre", "Parceiros", "Política de
  privacidade", "Termos" foram adicionados ao rodapé com `href="#"` — a peça é estática/protótipo
  e essas páginas ainda não existem. Antes de ir pra produção, alguém precisa trocar os hrefs
  pelas URLs reais (ou confirmar que essas páginas existem no domínio Ybera).
- **Chip "Indicado por Ana Carla" também virou link sem destino real** (2026-07-15): mesma
  situação do item acima — `href="#"` até existir uma página real de perfil da influenciadora
  (ou outro destino definido pelo time). Mesma pendência antes de produção.
- **Abrir peças HTML via `file://` no Browser pane cacheia agressivamente** (2026-07-15): edições
  no disco não refletem mesmo com `location.reload()` — só resolve fechando e reabrindo com um
  cache-bust, ou (melhor) servindo a pasta via HTTP. Processo: rodar um servidor estático simples
  (Node puro, sem `python3 -m http.server` — esse falha neste sandbox porque `argparse` chama
  `os.getcwd()` incondicionalmente e o subprocesso não tem cwd válido) e apontar o Browser pane
  pra `http://localhost:PORTA/` em vez de abrir o arquivo direto. Vale considerar isso como
  primeiro passo padrão ao verificar qualquer peça neste processo.
  CORREÇÃO (mesmo dia): o bug de screenshot em branco após scroll no viewport desktop **não** é
  exclusivo de `file://` como cheguei a supor — ele se repetiu também servindo via HTTP, depois de
  mudanças via JS (ex.: `scrollTo` após um `submit` de formulário). Continua sendo contornável só
  com viewport mobile ou `scrollIntoView` + nova screenshot; não é um problema resolvido, é um
  quirk do ambiente que precisa do workaround de sempre.

## Ciclo vinculos-e-onboarding (2026-07-15)
- **`SUMMARY.md` da skill `dev-mode-virtual-office` está desatualizado/contraditório**: descreve
  o app como tema Skote/Bootstrap/Poppins, mas o próprio `design_system.json` da mesma captura
  diz explicitamente que isso é o tema LEGADO e que as telas modernas usam Tailwind/shadcn
  (tokens `b2c-*`/`kz-*`, Syne/Nunito). Sem destino interno a este projeto (o arquivo vive em
  `~/.claude/skills/dev-mode-virtual-office/`, fora deste repo) — não editado aqui para não
  mexer silenciosamente num asset de terceiros. Vale pedir, numa sessão futura, para regenerar
  ou corrigir esse `SUMMARY.md` na fonte.

