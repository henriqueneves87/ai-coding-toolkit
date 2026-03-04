---
name: frontend-conventions
description: React/Next.js conventions — performance, Server/Client Components, a11y, data fetching, feedback patterns, responsive design, TypeScript strict.
---

# Frontend Conventions — React / Next.js

## Quando aplicar

Aplicar sempre que o trabalho envolver UI/UX em arquivos `.ts`, `.tsx`, `.css`, `.mdx`.

## Fluxo obrigatório

1. **Consultar Magic-MCP** (21st.dev) quando disponível, antes de propor/alterar UI.
2. Ler o design system do projeto (tokens + specs). Se não existir, criar tokens semânticos mínimos.
3. Trabalhar incremental — não fazer mudanças amplas sem autorização.

---

## Server vs Client Components (App Router)

- **Padrão é Server Component** — só adicionar `'use client'` quando necessário (hooks, event handlers, browser APIs).
- Manter `'use client'` o mais baixo possível na árvore de componentes.
- Nunca importar Server Component dentro de Client Component diretamente — usar children/slots.
- Usar `loading.tsx`, `error.tsx` e `not-found.tsx` em cada rota relevante.

## Performance

- **Bundle size:** usar `next/dynamic` para componentes pesados. Evitar importar libs inteiras (`import { Button } from 'lib'`, não `import lib`).
- **Imagens:** sempre `next/image` com `width`, `height` e `alt`. Usar `priority` apenas no LCP.
- **Async waterfalls:** nunca fazer fetch sequencial quando podem ser paralelos (`Promise.all`). Preferir fetch no Server Component.
- **Code splitting:** um componente = uma responsabilidade. Lazy load para modais, drawers, gráficos.
- **Re-renders:** `useMemo`/`useCallback` apenas quando há evidência de problema (profiler). Não otimizar prematuramente.

## Data Fetching

- **Server Components:** fetch direto no componente (sem useEffect). Usar `cache` e `revalidate` do Next.js.
- **Client Components:** SWR ou TanStack Query para cache e revalidação. Nunca `useEffect` + `fetch` + `setState` manual.
- **Evitar waterfalls:** buscar dados no nível mais alto possível e passar via props/context.
- **Server Actions:** usar para mutações (forms, CRUD). Revalidar com `revalidatePath`/`revalidateTag`.

## Feedback & Polish

- **Toast** para ações (CRUD, submit, delete).
- **Skeleton** para listagens, tabelas e cards durante loading.
- **Empty state** para listas vazias (mensagem + ação sugerida).
- **Estados completos:** hover / focus-visible / disabled / loading / error em inputs e botões.
- **Ícones:** SVG via lucide-react ou similar. Sem emojis para estados.
- **Tokens semânticos:** evitar hex/rgb hardcoded. Usar variáveis CSS ou Tailwind.

## Acessibilidade (a11y)

- **HTML semântico:** `<nav>`, `<main>`, `<section>`, `<article>`, `<button>` (não `<div onClick>`).
- **Alt text:** toda `<img>` e `next/image` com `alt` descritivo. Decorativas: `alt=""`.
- **Keyboard:** todo elemento interativo acessível via Tab. Focus visível obrigatório.
- **ARIA mínimo:** `aria-label` em botões com apenas ícone. `aria-live` para conteúdo dinâmico (toasts, alerts).
- **Contraste:** mínimo 4.5:1 para texto normal, 3:1 para texto grande.

## Responsividade

- **Mobile-first:** estilos base para mobile, breakpoints para telas maiores.
- **Breakpoints:** usar os do Tailwind (`sm`, `md`, `lg`, `xl`) ou tokens do projeto.
- **Touch targets:** mínimo 44x44px para botões e links em mobile.
- **Testar:** verificar em 320px (mobile mínimo) e 1440px (desktop padrão).

## TypeScript

- **Strict mode** obrigatório (`strict: true` no tsconfig).
- Props tipadas com `interface` ou `type`. Nunca `any` em props de componente.
- Preferir `ComponentProps<'button'>` para estender elementos nativos.
- Exportar types quando usados por mais de um componente.

## Estrutura de Componentes

- **Colocation:** componente + styles + types + tests na mesma pasta quando complexo.
- **Naming:** PascalCase para componentes, camelCase para hooks, kebab-case para arquivos.
- **Barrel exports:** usar `index.ts` apenas em pastas de componentes públicos. Evitar barrel exports profundos (impacta bundle).
- **Composição sobre configuração:** preferir children/slots a props booleanas (`<Card><CardHeader/></Card>` > `<Card showHeader={true}/>`).

---

## Checklist (rápido)

- [ ] Server Component por padrão, `'use client'` só quando necessário
- [ ] Sem hex hardcoded — tokens semânticos
- [ ] Toast / skeleton / empty state aplicados
- [ ] Estados completos: hover / focus-visible / disabled / loading
- [ ] HTML semântico, alt text, keyboard nav
- [ ] `next/image` com width/height/alt
- [ ] Sem async waterfalls — fetch paralelo
- [ ] Props tipadas, sem `any`
- [ ] Mobile-first, touch targets 44px
- [ ] `npm run build` sem erros
