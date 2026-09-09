# Paulo Bernardo · Cão Belo Adestramento

Landing page de página única para o adestrador de cães Paulo Bernardo
(São José dos Campos e região). Réplica 1:1 do design do Figma.

Feito pela [Adsgator](https://adsgator.com.br).

## Stack

- [Astro 5](https://astro.build) + TypeScript strict
- Tailwind CSS v4 (CSS-first, sem `tailwind.config.js`)
- GSAP + ScrollTrigger + Lenis (animações e smooth scroll)
- framer-motion (menu mobile e banner de cookies)

## Desenvolvimento

```bash
npm install
cp .env.example .env   # preencha os valores
npm run dev            # http://localhost:4321
```

Scripts:

| Comando | O quê |
|---|---|
| `npm run dev` | servidor de desenvolvimento |
| `npm run build` | build de produção em `dist/` |
| `npm run preview` | serve o build local |
| `npm run check` | `astro check` + `biome check` (tem que passar limpo) |

## Variáveis de ambiente (`.env`)

Todas com prefixo `PUBLIC_` (expostas no cliente). Ver `.env.example`.

| Variável | Uso |
|---|---|
| `PUBLIC_SITE_URL` | URL canônica, sitemap, OG |
| `PUBLIC_GTM_ID` | Google Tag Manager |
| `PUBLIC_WA_NUMBER` / `PUBLIC_WA_MESSAGE` | link de WhatsApp (só dígitos, com DDI) |
| `PUBLIC_SEO_*` | título, descrição e keywords padrão |
| `PUBLIC_FORM_ENDPOINT` | endpoint do FormSubmit (`formsubmit.co/ajax/<email>`) |

## Deploy

Hospedagem na **Vercel**, deploy automático por push na branch principal.
As variáveis do `.env` precisam ser recriadas no painel da Vercel.

**Antes de publicar:**

1. `PUBLIC_FORM_ENDPOINT` → e-mail do cliente (`ph_bernardo@yahoo.com.br`),
   hoje está com um e-mail de teste.
2. Confirmar `PUBLIC_WA_NUMBER` com o cliente.
3. No primeiro envio real do formulário, clicar no link de confirmação que o
   FormSubmit manda para o e-mail cadastrado (ativação única).

## Arquitetura

Ver [`CLAUDE.md`](./CLAUDE.md) — convenções de código, tokens, comportamentos
de UX obrigatórios e o que conferir antes de considerar pronto.
