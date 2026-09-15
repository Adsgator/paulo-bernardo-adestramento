# CLAUDE.md — Paulo Bernardo · Cão Belo Adestramento

Landing page de página única para o adestrador Paulo Bernardo (São José dos
Campos e região). Projeto de cliente da Adsgator.

Leia este arquivo inteiro antes de qualquer edição.

---

## O que este projeto é

Uma réplica **1:1 do design do Figma**
(`figma.com/design/v7qbDL7cgCBIVfH8my6qTE/CLIENTES`), páginas
`1778-52` (desktop, `1920w light`) e `1817-3` (mobile, `390w light`).

- **Não existe `manifesto.md`.** O trabalho é feito seção a seção, comparando
  o render com o Figma. O cliente aprovou o design; mudanças visuais só quando
  ele pede.
- Quando precisar de medida exata, extraia do Figma via API
  (`FIGMA_API_KEY` no ambiente do usuário) ou dos exports em `_ref/`, não
  estime no olho.
- Os exports PNG do Figma têm uma moldura preta de 9px em todos os lados —
  descontar ao medir.

---

## Stack

- **Astro 5** + TypeScript strict
- **Tailwind CSS v4** — CSS-first, **sem `tailwind.config.js`**. Os valores dos
  tokens ficam em `src/styles/tokens.css`; `src/styles/global.css` os importa e
  os registra como utilitários Tailwind num bloco `@theme inline`.
- **GSAP + ScrollTrigger** — animações de entrada, em
  `src/components/islands/ScrollAnimations.tsx` (`client:load` no BaseLayout).
- **Lenis** — smooth scroll, integrado ao ScrollAnimations pelo padrão oficial
  da lib (`lenis.on("scroll", ScrollTrigger.update)` + `gsap.ticker.add(lenis.raf)`).
- **framer-motion** — só no banner de cookies (`CookieBanner.tsx`). O menu
  mobile do Header **não** usa React/framer-motion: é vanilla JS (`<script>`
  dentro do próprio `Header.astro`, toggle de classe `hidden` num painel
  inline, sem overlay fullscreen nem portal).
- **astro-icon** + `@iconify-json/lucide` estão instalados mas **não são usados**
  hoje: os poucos ícones do layout são SVG inline, e o do WhatsApp é o
  componente `WhatsAppIcon.astro`. Se for adicionar um ícone novo de UI,
  prefira `astro-icon` a colar mais um `<svg>` na mão.

---

## Tokens — a regra mais importante

Todo valor visual (cor, fonte) vem de `src/styles/tokens.css`.

- **Não hardcode cor nem fonte.** Use o utilitário Tailwind correspondente
  (`bg-primary`, `text-text-main`, `font-serif`) ou `var(--t-...)` em CSS
  escopado.
- Tamanhos: como o alvo é 1:1 com o Figma, os componentes usam valores
  arbitrários do Tailwind (`text-[38.4px]`, `leading-[35.2px]`, `max-w-[930px]`)
  com o número exato do design. Isso é intencional aqui — não troque por
  escala genérica.
- Para reidentificar o cliente, edite só `tokens.css`. Os nomes dos tokens
  não mudam.

Tokens de cor (nome → uso no design do Paulo):

| Token | Classe | Uso |
|---|---|---|
| `--t-primary` (#000) | `text-primary` `bg-primary` | texto forte, ícone preto sobre amarelo |
| `--t-primary-dark` | `bg-primary-dark` | hover |
| `--t-secondary` (#FFFF00) | `bg-secondary` `text-secondary` | amarelo da marca: CTA, barras, acentos |
| `--t-background` | `bg-background` | fundo branco |
| `--t-surface` / `--t-surface-alt` | `bg-surface` `bg-surface-alt` | seções alternadas (Mercury) |
| `--t-overlap` (#F1F1F1) | `bg-overlap` | corpo das caixas sobrepostas (Seashell) |
| `--t-dark` (#000) | `bg-dark` | Treino em grupo (preto puro) |
| `--t-diferenciais` (#8C8C8C) | `bg-diferenciais` | fundo cinza de Diferenciais e Contato |
| `--t-footer` | `bg-footer` | Abbey (#454546) do rodapé |
| `--t-text-main` / `--t-text-soft` / `--t-text-muted` | `text-text-*` | corpo, subtítulo, metadado |
| `--t-text-strong` (#1F2124) | `text-text-strong` | perguntas do FAQ (Shark) |
| `--t-text-on-dark` (#C9C9C9) | `text-text-on-dark` | corpo sobre fundo escuro (Silver) |
| `--t-divider` (#D5D8DC) | `border-divider` | divisor do accordion (Iron) |
| `--t-input-border` (#D0D5DD) / `--t-placeholder` (#667085) | — | campos do formulário |
| `--t-star` (#F5A623) | `text-star` | estrelas do Google nas avaliações |
| `--t-wa` | `bg-wa` | verde do WhatsApp |

Classes de layout: `.container-wide` (`w-[90%]` + `max-w-[1300px]`),
`.section-py`, `shadow-card`, `shadow-float`.

---

## Classes utilitárias prontas (`global.css`)

Use em vez de remontar do zero:

- **`.btn-primary`** — CTA amarelo, raio 10px, texto preto, hover, easing.
  É o botão de WhatsApp de todas as seções.
- **`.btn-ghost`**, **`.btn-secondary-gold`** — existem, hoje sem uso no
  clone.
- **`.band-yellow`** — caixa sobreposta entre seções (Hero→Serviços,
  Sobre→Treino): fundo Seashell, barra amarela de 6px no topo e 1px na base.
  Mesma medida no mobile e no desktop.

### CTA de WhatsApp dentro de uma seção

```astro
---
import WhatsAppIcon from "../global/WhatsAppIcon.astro";
---
<a href={WA_LINK} target="_blank" rel="noopener noreferrer"
   class="btn-primary ..." data-tracking="whatsapp-<secao>" data-section="<secao>">
  <WhatsAppIcon class="h-[25px] w-[25px] shrink-0" />
  <span class="whitespace-nowrap">Fale agora pelo WhatsApp!</span>
</a>
```

- O ícone é **sempre** `WhatsAppIcon.astro`, nunca um `<svg>` colado. O path
  vive em `src/data/whatsapp-icon.ts` (isolado num módulo `.ts` porque uma
  island React já precisou dele no passado e não conseguia importar um
  `.astro` — hoje só o `WhatsAppIcon.astro` o usa). Mudou o desenho? Só
  nesse arquivo.
- O texto vai em `<span class="whitespace-nowrap">` e o ícone leva
  `shrink-0` — sem isso o rótulo quebra em duas linhas em telas estreitas.
- O botão **flutuante** já existe (`WhatsAppFloat.astro`) — não recrie.

---

## Animações de entrada

Adicione atributos; o `ScrollAnimations.tsx` cuida do resto. Todas respeitam
`prefers-reduced-motion` (desligam).

| Atributo | Efeito |
|---|---|
| `data-animate` | sobe 30px + fade, `power2.out` 0.6s |
| `data-animate-left` / `data-animate-right` | entra 30px da lateral + fade |
| `data-animate-group` no pai + `data-animate-item` nos filhos | stagger de 0.1s |
| `data-animate-scale`, `data-parallax`, `data-counter` | implementados, sem uso no clone |

Regra de bom senso: um bloco grande usa `data-animate` (ou left/right); listas
e grades de cards usam `group`/`item`. Nada agressivo — o objetivo é fluidez,
não chamar atenção. O Hero anima o bloco de texto inteiro de uma vez (não faça
stagger no conteúdo do LCP).

**Ao tirar screenshot para conferência**, force a visibilidade — o GSAP mantém
os elementos com `opacity:0` até entrarem na viewport:

```js
await page.addStyleTag({ content:
  "[data-animate],[data-animate-left],[data-animate-right],[data-animate-group],[data-animate-item]{opacity:1!important;transform:none!important}" });
```

---

## Dark mode

O design é **light-only**. Existe um script anti-flash no BaseLayout e uma
paleta `.dark` em `tokens.css`, mas **não há toggle** (foi removido no clone) e
a paleta dark não é mantida em dia. Não invista nela a menos que o cliente
peça. `theme-color` no BaseLayout ainda usa `#ffd400`/`#141414` antigos —
alinhar a `#FFFF00`/`#000000` se for mexer ali.

---

## Regras absolutas de código

1. `<Image />` de `astro:assets`, nunca `<img>` nativo (SVG inline trivial ok).
   Vídeo é a exceção: `<video>` nativo servido de `public/` (o `<Image>` só
   processa imagem).
2. Sem `any` no TypeScript.
3. Sem `!important` no CSS (o override de screenshot acima é feito por script
   de teste, não fica no código).
4. Lighthouse ≥ 95 nas 4 métricas é requisito.
5. `npm run check` (astro check + biome) limpo antes de dar por pronto.
   Se o biome reclamar de formatação, `npx biome check --write src/`.
6. `npm run build` sem erro nem warning.

---

## Comportamentos de UX — todos devem funcionar

### Header (`Header.astro`)
- Esconde ao rolar para baixo, reaparece ao rolar para cima. O handler usa
  `requestAnimationFrame` + limiar de 8px + estado guardado (sem isso o Lenis
  dispara eventos demais e o header pisca). Transição de 0.42s ease-out,
  `translateZ(0)` fixo para isolar a camada do `backdrop-blur`.
- Link ativo por seção via IntersectionObserver. **O `#hero-section` também é
  observado**: quando ele está na faixa, nenhum item de menu fica ativo —
  senão "Serviços" gruda ao voltar ao topo.
- Menu mobile: painel inline dentro do próprio `Header.astro` (vanilla JS,
  toggle de classe `hidden`), fecha ao clicar em link, fora ou no Esc.
- Nenhum link de menu aponta para `#`.

### WhatsApp flutuante (`WhatsAppFloat.astro`)
- Some quando `#hero-section` ou `#footer` estão na tela.
- Número/mensagem de `.env` (`PUBLIC_WA_NUMBER`, `PUBLIC_WA_MESSAGE`).

### Formulário "Solicite seu contrato" (`Contato.astro`)
- Envia para o **FormSubmit** (`formsubmit.co/ajax/<email>`), endpoint em
  `PUBLIC_FORM_ENDPOINT`. Grátis, sem cadastro.
- O Paulo recebe os dados por e-mail; o **contrato (PDF) baixa na hora** no
  navegador (`public/contrato-adestramento.pdf`), com link de fallback visível
  se o download automático for bloqueado. Nenhum serviço grátis anexa arquivo
  na autorresposta — por isso o download no cliente.
- O JS lê `form.getAttribute("action")`, **não** `form.action` (a propriedade
  resolve para a URL da página quando o atributo está vazio, e o POST cairia na
  home com um falso "enviado").
- Ativação única: o primeiro envio real dispara um e-mail de confirmação do
  FormSubmit; o Paulo clica uma vez no link.

### IDs estruturais obrigatórios
- Hero: `id="hero-section"`. Footer: `id="footer"`. `<main id="main-content">`.
- Cada seção com `id` igual ao item de menu (`#servicos`, `#sobre`,
  `#avaliacoes`, `#faq`, `#contato`, mais `#diferenciais`, `#treino-em-grupo`,
  `#parceiros`).

### Acessibilidade
- `alt` em toda imagem; `aria-label` em controle sem texto.
- Skip link já no BaseLayout. Focus visível em tudo.

### Páginas legais e 404
- `politica-de-privacidade.astro` e `termos-de-uso.astro`: dados do cliente
  preenchidos (CNPJ 37.040.687/0001-07, e-mail ph_bernardo@yahoo.com.br,
  hospedagem Vercel, formulário FormSubmit). Sem `TODO` pendente.
- `404.astro` é standalone (sem BaseLayout), com header/footer próprios.
- O `<header>` fixo dessas três páginas (política, termos, 404) tem
  `transform: translateZ(0)` na `<Image>`/`<img>` da logo. **Não remova**:
  sem isso o Chrome deixa um artefato de rasterização (uma faixa amarela
  fina) na base do SVG circular, visível só na composição final da página
  (não aparece isolando o elemento nem inspecionando o DOM/CSS — é bug de
  compositing do navegador com SVG dentro de `position:fixed`).

### Árvore de links (`arvore-de-links.astro`)
- Réplica 1:1 do Figma (nodes `52:49` desktop / `52:128` mobile). Página
  standalone tipo Linktree — sem `BaseLayout`, sem Header/Footer/WhatsApp
  flutuante/ScrollAnimations do site principal. Mesmo padrão do `404.astro`.
- Slug mantido: `/arvore-de-links` (era a URL da página em produção).
- Imagem de fundo: `src/assets/images/img-links-bg.webp` (cliente manda o
  arquivo atualizado; copiar para essa pasta e trocar o import se mudar).
- Foto de perfil: `src/assets/images/img-links-perfil.png`.
- Links: WhatsApp (`PUBLIC_WA_NUMBER`/`PUBLIC_WA_MESSAGE` do `.env`),
  Instagram (mesma URL do `Footer.astro`) e o site
  (`PUBLIC_SITE_URL`). Ícones são SVG inline com `fill="currentColor"`
  (mesmo padrão do `WhatsAppIcon.astro`), não `astro-icon`.

---

## Antes de dar por pronto — sinais de entrega incompleta

- WhatsApp flutuante não some sobre Hero ou Footer.
- Link de menu apontando para `#`.
- Menu mobile não fecha ao clicar fora ou num link.
- CTA de WhatsApp com `<svg>` colado em vez de `<WhatsAppIcon>`, ou sem
  `whitespace-nowrap` no rótulo.
- `<img>` nativo, ou `<Image>`/`<video>` sem `width`/`height` (CLS).
- Página legal com `TODO` ou placeholder.
- Sobreposição das `.band-yellow` cobrindo conteúdo da seção seguinte
  (conferir o vão com o Figma — Hero→Serviços e Sobre→Treino).
- `.env` com `PUBLIC_WA_NUMBER`, `PUBLIC_GTM_ID`, `PUBLIC_SITE_URL` ou
  `PUBLIC_FORM_ENDPOINT` vazios.
- `npm run check` ou `npm run build` com erro ou warning.
- Arquivos temporários (`_*.mjs`, `_*.png`, `_ref/`) commitados.

---

## Estrutura

```
src/
  components/
    global/   → SkipLink, WhatsAppFloat, WhatsAppIcon, GTM
    islands/  → CookieBanner, ScrollAnimations (React)
    sections/ → Header, Hero, Servicos, Diferenciais, Sobre, TreinoEmGrupo,
                Avaliacoes, Parceiros, FAQ, Contato, Footer
  data/       → whatsapp-icon.ts (path SVG compartilhado Astro + React)
  layouts/    → BaseLayout.astro (head, SEO, GTM, Consent Mode, WhatsApp, cookies)
  pages/      → index.astro (composição), 404, politica-de-privacidade,
                termos-de-uso, arvore-de-links (standalone, tipo Linktree —
                sem Header/Footer/WhatsApp flutuante do site principal)
  styles/     → tokens.css (edite aqui), global.css (@theme + utilitárias)
public/
  video/      → treino-em-grupo.mp4 + poster
  contrato-adestramento.pdf, favicon.png, og-image.png, _headers
```

Composição (`index.astro`):
`<BaseLayout>` → `<Header />` + `<main id="main-content">` (as 10 seções na
ordem acima) `</main>` + `<Footer />`.

---

## Deploy

- Hospedagem: **Vercel** (deploy automático por push no GitHub).
- Antes de publicar: trocar `PUBLIC_FORM_ENDPOINT` do e-mail de teste
  (`info@adsgator.com.br`) para o do cliente (`ph_bernardo@yahoo.com.br`) e
  confirmar `PUBLIC_WA_NUMBER` (hoje `5512987063611`, extraído do flyer).
- As `PUBLIC_*` do `.env` precisam ser recriadas no painel da Vercel.
