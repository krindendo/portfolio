# AGENTS.md

This file provides guidance to AI coding agents when working with code in this repository.

## Commands

- `npm run dev`: start the dev server at `localhost:4321`
- `npm run build`: run `astro check`, then build to `dist/`. Type errors fail the build.
- `npm run preview`: serve the built site locally
- `npx astro check`: type-check only

There is no test suite or linter, so `npm run build` is the only automated check.

## Architecture

This is a single-page static portfolio/CV built with Astro 4 and Tailwind CSS 3. It has no UI framework and no content collections.

- **Page content** is hardcoded HTML in `src/pages/index.astro`. Each CV section repeats the same markup: a `<section>` with a label column and a content column. Copy that markup when adding a section. Commented-out `<li>` entries in the skills lists are deliberately hidden items.
- **Page shell:** `src/layouts/Base.astro` composes `LayoutHead`, `Header`, the page slot and `Footer`. `LayoutHead` outputs the `<head>` contents without a `<head>` wrapper and imports `src/styles/global.css`.
- **Site metadata** lives in `src/site.config.ts`, which feeds the meta tags, the footer and the `site` option in `astro.config.ts`. The name in `Header.astro` and the page copy are hardcoded separately.
- **Production URL** appears in both `src/site.config.ts` and `public/robots.txt`. Change both together.

## Theming

Dark mode is driven by the `data-theme` attribute on `<html>`, not a `.dark` class. The `darkMode` setting in `tailwind.config.mjs` maps the `dark:` variant to `[data-theme="dark"]`.

- `Base.astro` hardcodes `data-theme="dark"`, but the script in `Header.astro` replaces it on the client. That script uses the `theme` value in localStorage, then the system preference. It also handles the toggle button and follows system changes.
- Semantic colors such as `background`, `foreground` and `muted` are HSL CSS variables defined per theme in `global.css` and exposed through `tailwind.config.mjs`. A new semantic color has to be added in both files, for both themes.
- `index.astro` also uses raw palette classes such as `text-blue-600` and `text-zinc-600`, which don't change with the theme unless paired with a `dark:` variant.

## Conventions and gotchas

- **Import aliases:** `tsconfig.json` defines `@/components/*`, `@/layouts/*`, `@/types` and `@/site-config`. `@/assets/*`, `@/data/*` and `@/utils/*` are also defined, but those directories don't exist yet. There is no alias for styles, so they use relative imports.
- **PDF CV:** `marko-samek-cv.pdf` in the repo root is printed from the built page, using the `print:` classes and the `@page` rule in `global.css`. Reprint it whenever the page content changes.
- **Strict types:** the project extends `astro/tsconfigs/strictest`, and `astro check` enforces this during the build.
- **Missing assets:** `LayoutHead.astro` and `manifest.webmanifest` reference files that aren't in `public/`: `favicon.ico`, `icon.svg`, `apple-touch-icon.png`, `social-card.png`, `192x192.png` and `512x512.png`. Only `favicon.svg` exists, and nothing links to it.
