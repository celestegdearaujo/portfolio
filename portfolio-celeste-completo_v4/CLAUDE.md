# CLAUDE.md

Guia de contexto para o Claude (ou qualquer dev) trabalhar neste projeto sem precisar redescobrir tudo do zero.

## O que é este projeto

Página inicial (home) do portfólio de **Celeste Gouvea**, designer com foco em e-learnings e animação digital.
É um arquivo único (`portfolio-celeste.html`) autocontido — HTML + CSS + JS inline, sem build step, sem framework.
Baseado 1:1 num design de referência (Figma/Adobe XD), reconstruído em HTML/CSS a partir de:
- Exportações `.xd` e `.fig` do projeto original (dados de posição, cor e texto extraídos programaticamente).
- Um `tela-inicial-2.svg` exportado do design, usado como fonte de verdade para posições absolutas, cores em hex e nomes de fonte por elemento.
- Prints de referência (`tela-inicial-print1.png`, `print2.png`) para conferência visual.

## Stack

- **HTML/CSS/JS puro**, um único arquivo (`portfolio-celeste.html`).
- Fonte real **Futura PT** (Book/Light/Medium/Demi/Bold) via `@font-face`, arquivos `.otf` em `assets/fonts/`.
  → Futura PT é paga; os arquivos foram fornecidos pelo usuário. Não usar Google Fonts para essa família.
- `Segoe UI` é usado propositalmente (não é bug) em alguns elementos específicos: pills INÍCIO/SUCESSO, e labels dos skip-links (“Ir para o Conteúdo/Menu”) — assim está no arquivo de design original.
- Sem dependências externas, sem CDN (exceto se algo futuro exigir).

## Arquitetura de layout (importante antes de mexer em qualquer posição)

O design é um canvas de **1920×1450px fixo**, posicionado com `position:absolute` (classe `.abs`) e coordenadas literais em px — não é responsivo por natureza, é *escalado*.

Estrutura em 3 camadas sobrepostas:

```
#app
├── #bg-fixed        → position:fixed, cobre o viewport inteiro (fundo + gradiente escurecendo pra baixo)
├── #chrome-fixed     → position:fixed, largura 1920px, contém HEADER + SIDEBAR. Não rola nunca.
└── #page-scale-wrapper
    └── #page-scale   → position:relative (fluxo normal do documento), contém o CARD DE CONTEÚDO
                         (tabs, breadcrumb, vídeo, journey, texto). Isso é o que rola.
```

- `--scale` (CSS var) = `window.innerWidth / 1920`, recalculado no resize via JS (`updateScale()`).
- `#chrome-fixed` e `#page-scale` usam `transform: scale(var(--scale))` com `transform-origin: top left`.
- `#page-scale-wrapper` tem sua altura setada via JS (`1450 * scale`) para o documento saber quanto rolar —
  **se adicionar conteúdo abaixo do rodapé atual, precisa aumentar esse `1450` no JS**, senão o scroll corta.
- Header, background e sidebar ficam fixos na tela; só o card de conteúdo (à direita) rola.

⚠️ **Não migrar isso para um grid/flex responsivo sem querer** — todo o posicionamento dos elementos depende
de coordenadas absolutas calibradas a partir do design original. Qualquer elemento novo deve ser posicionado
do mesmo jeito (`class="abs"` + `left/top` em px, dentro do bloco `#chrome-fixed` ou `#page-scale`, conforme
se deve ficar fixo ou rolar).

## Paleta e tipografia (valores exatos, extraídos do SVG do design)

| Uso                                          | Valor                    |
|-----------------------------------------------|--------------------------|
| Rosa forte (CLIENTE/PROJETO/ANO/CATEGORIA, tab ativa) | `#ff2da0` (`--pink-bold`) |
| Rosa claro (pills INÍCIO/SUCESSO, destaque "Celeste") | `#fe80f1` (`--pink-light`) |
| Texto padrão sidebar/parágrafos                | `#d9d9d9` (`--text-dim`) |
| Texto skip-links                               | `#626262` (`--text-dimmer`) |
| Texto hint do mouse                            | `#595959` (`--text-mouse`) |
| Texto escuro (pill DESAFIO)                    | `#171717` (`--text-dark`) |
| Fundo geral                                    | `#0a0a0c` |

Fonte padrão: `Futura PT` (fallback `Jost`, depois `sans-serif`).
Pesos usados: 300 (Light), 400 (Book — é o "regular" da família), 500 (Medium), 600 (Demi), 700 (Bold).

## Ícones e imagens (`assets/`)

Todos os ícones/imagens abaixo são **ativos reais do design** (não são ilustrativos/placeholder):

| Arquivo | Uso |
|---|---|
| `background.png` | fundo fixo de tela inteira |
| `logo.png` | logo "Celeste Gouvea" no header |
| `img-ilustracao.png` | ilustração "Olá" + retrato, na sidebar |
| `img-ferramentas.png` | tira de ícones decorativa (toolbar) atrás da ilustração |
| `journey-line.png` | linha decorativa atrás dos pills Início/Desafio/Sucesso |
| `thumb-video.png` | poster do `<video>` antes do play |
| `video-1.mp4` | vídeo real tocado no card de conteúdo |
| `icons/icone-acessibilidade.png` | botão de acessibilidade (header) |
| `icons/icone-altocontraste.png` | botão de alto contraste (header) |
| `icons/icone-darkmode.png` | botão de tema claro/escuro (header) |
| `icons/icone-calendario.png` | botão de calendário (barra de filtros) |
| `icons/icone-teclado.png` | ícone nos skip-links |
| `icons/icone-alvo.png` | ícone (bullseye) dos 3 itens do menu lateral |
| `icons/icone-seta-normal.png` / `icone-seta-hover.png` | seta `>` dos itens de menu, com troca real no hover |
| `icons/icone-mouse.png` | cursor usado no hint "Navegue pelo menu..." |
| `icons/icone-tracejado.png` | traço divisor vertical no hint do mouse |
| `fonts/FuturaPT*.otf` | fonte real via `@font-face` |

### ⚠️ Arquivos órfãos em `assets/` (não referenciados no HTML atual — sobras de iterações anteriores)
Podem ser **removidos com segurança** ao fazer uma limpeza, ou mantidos como histórico:
- `bg-photo.png` (substituído por `background.png`)
- `barra-lateral.png` (substituído por `img-ferramentas.png`)
- `mouse-icon.png` na raiz de `assets/` (substituído por `icons/icone-mouse.png`)
- `video-thumb.png` (substituído por `thumb-video.png`)
- `icons/icone-estrela.png` (não usado ainda — reservado para decoração futura)

## Seções da página

1. **Header (fixo)** — logo, skip-links, botões A-/A+ (tamanho de fonte), alto contraste, tema, acessibilidade.
2. **Sidebar (fixa)** — ilustração + apresentação da Celeste, menu (Início / Resumo profissional / LinkedIn),
   hint de navegação por mouse.
3. **Card de conteúdo (rola)** — abas de filtro (Todos/E-learning/Apresentações/Animação/Outros), ordenação,
   breadcrumb (Cliente/Projeto/Ano/Categoria), player de vídeo custom, trilha "jornada" (Início → Desafio →
   Sucesso) com texto lorem ipsum abaixo.

## Interatividade já implementada

- Abas alternam estado `.active` (visual apenas — ainda não filtram conteúdo real, porque só existe 1 projeto de exemplo).
- Dropdown "Ordenar por" abre/fecha e atualiza o label.
- Item de menu lateral marca `.current` ao clicar.
- **Vídeo real** (`<video>` custom, não usa controles nativos do navegador):
  - play/pause central (`#playBtn`) e no mini player (`#miniPlay`), sincronizados;
  - `#playBtn` faz hover-zoom suave e **some** (fade + scale down) quando o vídeo está tocando, volta ao pausar;
  - barra de progresso arrastável (`#progressTrack`), clique ou drag para dar seek;
  - mudo, velocidade (ciclo 1x → 1.5x → 2x → 0.5x), tela cheia.
- Botões de acessibilidade do header **funcionam de verdade**:
  - A+/A- ajustam `--font-scale` (aplicado via `calc(Npx * var(--font-scale))` nos elementos de texto que usam essa variável — nem todo texto usa, checar antes de assumir que "aumentar fonte" cobre 100% da página);
  - alto contraste aplica `filter: contrast(1.35) saturate(1.25)` em `#app`;
  - tema aplica `filter: invert(1) hue-rotate(180deg)` em `#app` (é uma solução rápida/hack, não é um dark/light mode "de verdade" com paleta própria).

## Convenções ao editar

- Toda posição nova deve seguir o padrão `class="abs"` + `style="left:Npx; top:Npx; ..."`, dentro do bloco correto
  (`#chrome-fixed` se for fixo, `#page-scale` se deve rolar com o conteúdo).
- Cores devem usar as CSS vars existentes (`--pink-bold`, `--pink-light`, `--text-dim`, etc.), não hardcodar hex novo
  sem necessidade.
- Fontes: usar `calc(Xpx * var(--font-scale))` em qualquer `font-size` de texto de leitura (parágrafos, labels,
  pills), para continuar compatível com os botões A+/A-. Ícones e elementos decorativos não precisam disso.
- Antes de mudar posição/tamanho de algo, checar se existe referência exata no `tela-inicial-2.svg` (fonte de
  verdade) antes de estimar visualmente — o histórico deste projeto teve várias correções por causa de posições
  chutadas que depois teve que corrigir com dado exato do SVG.
- Se adicionar conteúdo que aumente a altura do card, lembrar de aumentar a constante `1450` no JS
  (`updateScale()`), senão a página corta o final no scroll.

## Estrutura de pastas

```
portfolio-celeste.html
assets/
  background.png
  logo.png
  img-ilustracao.png
  img-ferramentas.png
  journey-line.png
  thumb-video.png
  video-1.mp4
  fonts/
    FuturaPTBook.otf
    FuturaPTLight.otf
    FuturaPTMedium.otf
    FuturaPTDemi.otf
    FuturaPTBold.otf
  icons/
    icone-acessibilidade.png
    icone-altocontraste.png
    icone-alvo.png
    icone-calendario.png
    icone-darkmode.png
    icone-estrela.png      (não usado ainda)
    icone-mouse.png
    icone-seta-hover.png
    icone-seta-normal.png
    icone-teclado.png
    icone-tracejado.png
```

## Pendências conhecidas / próximos passos possíveis

- Abas de filtro são só visuais — falta lógica real de filtrar projetos quando houver mais de 1 card.
- Divisor da sidebar em `top:776px` (entre Resumo profissional e LinkedIn) não foi recalibrado com o mesmo
  ajuste fino que os outros dois — conferir se precisa do mesmo tratamento.
- Limpeza dos assets órfãos listados acima.
- "Modo claro" atual é um filtro CSS (`invert`), não uma paleta light de verdade — considerar substituir por
  uma paleta dedicada se for pra produção.
