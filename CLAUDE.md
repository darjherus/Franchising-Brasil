# Projeto — Site Franchising Brasil (ABF)

Site institucional do programa Franchising Brasil (ABF), em desenvolvimento como Design Component.

## Arquivos

- **`Site Franchising Brasil.dc.html`** — o site inteiro num único DC: 7 páginas + listagem/artigo de notícias + 84 páginas de perfil de marca. É aqui que quase todo trabalho acontece.
- **`Design System Franchising Brasil.dc.html`** — referência visual (cores, tipografia, componentes).
- **`i18n.js`** — dicionário PT/EN/ES (`window.FB_I18N`): `t` (strings de interface das 7 páginas, menu, rodapé), `articles` (as 4 notícias em cada idioma), `LABELS` (128 rótulos das fichas comerciais), `SEGS` (67 segmentos) e helpers `tLabel` / `tSeg` / `tValue`.
- **`empresas.js`** — dados das 84 franquias participantes (`window.EMPRESAS`), extraídos do PDF oficial da ABF: nome, segmento, logo, descrição, ficha comercial, contato.
- **`facetas.js`** — camada de filtros. `window.FB_FACETS` (84 objetos, mesma ordem de `FB_EMPRESAS`) normaliza as fichas — que usam 104 rótulos diferentes para os mesmos campos — em 9 campos: `sg` (macro-segmento), `in` (internacional y/n), `co` (países ISO-2), `un`/`iv`/`fd`/`ff`/`pb`/`fm` (faixas de unidades, investimento em US$, fundação, início no franchising, payback, faturamento). `window.FB_FACET_I18N` traz rótulos PT/EN/ES de grupos, valores, países e da interface de busca. Gerado por script a partir de `empresas.js` — se `empresas.js` mudar, regerar.
- **`img/marcas/*.png`** — 83 logos de marcas. Kekala não tem logo (renderiza como card tipográfico).
- **`img/logo-fb-site.svg`** — logo do programa no header.
- **`fonts/`** — Sanuk (serif/display) + família sans usada no corpo.

## Arquitetura do DC

- Navegação por estado, sem rotas: `state.page` ∈ `home | quem | empresas | beneficios | participar | agenda | noticias | artigo | contato | marca`. Handlers `navQuem`, `navEmp`, `navBen`, `navPart`, `navAge`, `navNot`, `navCon`, `navHome` vêm de `goPage(nome)`; `openArticle(i)` e `openBrand(i)` abrem artigo/marca. Cada página é um `<sc-if value="{{ isXxx }}">` no template.
- Notícias: 4 matérias com citação, blocos "Sobre…" e navegação anterior/próximo (`artPrev`/`artNext`).
- Header sticky: logo sangrando à esquerda (`margin-left:calc(50% - 50vw - 76px)`), nav centralizada com `gap:26px`, bloco direito (PT/EN/ES + ícone de busca + botão CONTATO) sangrando à direita (`margin-right:calc(50% - 50vw + 20px)`). Abaixo de ~1100px vira menu hambúrguer (`narrow` / `burgerDisplay` / `navStyle`).
- Régua de logos (163 marcas, duas faixas com animação pausa-zoom) é `background-image` por performance — não voltar a renderizar 163 `<img>`.
- Rodapé: colunas de links, redes sociais (Facebook, Instagram, LinkedIn) e newsletter com envio para E-goi (`onNewsletter`).

## Convenções visuais

- Azul `#2d529f` (institucional, header/rodapé), índigo `#363794`, amarelo `#fbb92b` e `#ffd201` (hover/destaque), verde e cinza das divisas de seção, preto `#1d1d1b`.
- Cápsula de título de página: bloco colorido sangrando pela esquerda, sobreposto à divisa da seção (`transform:translateY(-50%)`), título em uppercase 800 + linha de apoio.
- Estilos sempre inline (regra do DC). Só `@font-face`, `@keyframes` e resets no `<helmet>`.
- Sem emoji. Sem gradientes decorativos além do herói da home (verde → azul → índigo).

## Idiomas

`state.lang` ('pt' | 'en' | 'es'), persistido em `localStorage.fb_lang` e refletido em `document.documentElement.lang`. Textos vêm de `{{ t.chave }}` no template; `renderVals()` monta `t` a partir de `window.FB_I18N[lang].t`. Botão do idioma ativo branco com texto azul, inativos amarelos. Fichas das 84 marcas: rótulos, segmentos e unidades traduzidos por mapa; as descrições longas seguem em português (como enviadas pelas marcas), com nota explícita em EN/ES.

## Busca, filtros e URLs

- Estado inteiro serializado em `#b=<base64url>` (`{p,b,a,l,q,f,s}` = página, slug da marca, índice do artigo, idioma, busca, filtros, overlay aberto). `syncUrl(push)` grava com `pushState` na navegação e `replaceState` em filtros/busca; `applyUrl()` lê no mount e em `popstate`/`hashchange`. Hash (e não path) porque o site roda offline e dentro do editor.
- Lupa do header abre overlay de busca (Esc ou Cmd/Ctrl+K). Busca em marcas (nome, ficha, descrição, países nos 3 idiomas), notícias, páginas institucionais, segmentos e países — estes dois últimos como atalho que aplica o filtro.
- Barra de filtros (`data-facet-bar`) aparece só na página de empresas participantes e no overlay. Contagens por opção são recalculadas ignorando o próprio grupo (OR dentro do grupo, AND entre grupos), opção `__` = "não informado".
- Página da marca: etiquetas clicáveis derivadas das facetas levam ao catálogo já filtrado.
- Botão "Copiar link" na barra do catálogo copia a URL com filtros e busca aplicados.

## Trabalho pendente

1. Descrições longas das 84 marcas em EN/ES (aguardando decisão do cliente: tradução nossa ou textos oficiais).
2. Logo da Kekala, se o cliente enviar.
3. Ajustes finais de design/conteúdo conforme revisão.
4. Regerar o bundle offline (`Site Franchising Brasil (offline).html`) — ainda não contém busca/filtros.
