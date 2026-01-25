# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Oh My CV! is a Markdown-based resume builder deployed as a static PWA at https://ohmycv.app. Users write resumes in Markdown with YAML front-matter, get real-time preview, and export to PDF. All data is stored locally in the browser using IndexedDB (via localforage) - there is no backend.

## Commands

```bash
# Install dependencies
pnpm install

# Build utility packages (required before first dev run)
pnpm build:pkg

# Start development server
pnpm dev

# Build for production
pnpm build

# Full release (build packages + site)
pnpm release

# Lint
pnpm lint
```

## Architecture

**Monorepo structure** using pnpm workspaces:
- `site/` - Main Nuxt 3 application
- `packages/` - Utility packages published under `@ohmycv/` namespace

**Tech stack:**
- Nuxt 3 + Vue 3 + TypeScript
- UnoCSS (with Tailwind presets) + Radix Vue + shadcn-nuxt for UI
- Monaco Editor for code editing
- Pinia for state management
- localforage for browser storage

**Key site directories (`site/src/`):**
- `pages/` - Nuxt pages (dashboard at `/`, editor at `/editor`)
- `components/ui/` - shadcn UI components (prefixed with `Ui`)
- `components/editor/` - Editor and preview components
- `components/dashboard/` - Resume management
- `composables/stores/` - Pinia stores (`data.ts` for resume content, `style.ts` for styling)
- `utils/markdown.ts` - MarkdownService class that chains markdown-it plugins

**Utility packages (`packages/`):**
- `markdown-it-katex` - LaTeX math rendering
- `markdown-it-cross-ref` - Cross-references for academic CVs
- `markdown-it-latex-cmds` - LaTeX commands (`\newpage`, `\\[10px]`)
- `front-matter` - YAML front-matter parser
- `dynamic-css` - Runtime CSS injection
- `google-fonts-loader` - Google Fonts API integration
- `case-police` - Auto-correct term casing (e.g., 'Github' → 'GitHub')
- `vue-smart-pages` - Dynamic page routing for resume pagination
- `vue-zoom` - Zoom/pan component
- `vue-shortcuts` - Keyboard shortcut handler

## Environment Variables

Create `site/.env` for Google Fonts support:
```
NUXT_PUBLIC_GOOGLE_FONTS_KEY="YOUR_API_KEY"
```

## Adding Translations

1. Create `site/src/i18n/[language-code].yaml` (reference `en.yaml`)
2. Add language to `site/configs/i18n.ts`
