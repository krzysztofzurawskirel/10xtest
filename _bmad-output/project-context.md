---
project_name: '10xtest'
user_name: 'Krzysztofzurawski'
date: '2026-03-15'
sections_completed: ['technology_stack', 'language_rules', 'framework_rules', 'code_quality', 'workflow', 'critical_rules']
status: 'complete'
rule_count: 45
optimized_for_llm: true
---

# Project Context for AI Agents

_This file contains critical rules and patterns that AI agents must follow when implementing code in this project. Focus on unobvious details that agents might otherwise miss._

---

## Technology Stack & Versions

### Core Framework
- **Astro** 5.13.7 — SSR mode (`output: "server"`), node adapter (standalone)
- **React** 19.2.4 — Islands architecture via `@astrojs/react`
- **TypeScript** 5.9.3 — Strict mode (extends `astro/tsconfigs/strict`)
- **Tailwind CSS** 4.2.1 — Via `@tailwindcss/vite` plugin

### Backend & Auth
- **Supabase SSR** 0.9.0 — Cookie-based server sessions
- **Supabase JS** 2.99.1 — Client library
- **Environment**: Server-only secrets via `astro:env/server`

### UI Components
- **shadcn/ui** — "new-york" style variant
- **class-variance-authority** 0.7.1 — Component variants
- **Radix UI** — Primitives (react-slot 1.1.2)
- **Lucide React** 0.487.0 — Icons

### Development Tools
- **Node.js** 22.14.0 (per .nvmrc)
- **ESLint** 9.23.0 — Flat config with TypeScript, React, Astro plugins
- **Prettier** — With astro plugin
- **Husky** 9.1.7 + **lint-staged** — Pre-commit hooks

### Key Dependencies
- **clsx** 2.1.1 + **tailwind-merge** 3.5.0 — For `cn()` helper
- **tw-animate-css** 1.4.0 — Animation utilities

### Version Constraints
- React 19 requires React Compiler plugin (enabled in ESLint)
- Tailwind 4 uses Vite plugin approach (not PostCSS)
- Astro 5 uses `astro:env` for typed environment variables

---

## Critical Implementation Rules

### Language-Specific Rules (TypeScript)

**Path Alias:**
- Use `@/*` for all imports from `src/` — configured in tsconfig.json
- Example: `import { cn } from "@/lib/utils"` not `"../../lib/utils"`

**TypeScript Configuration:**
- Strict mode enabled via `astro/tsconfigs/strict`
- JSX runtime: `react-jsx` with `jsxImportSource: "react"`
- No explicit React imports needed in JSX files

**Import/Export Patterns:**
- Use named exports for utilities and components
- Default exports only for page-level React components (form components)
- API routes export uppercase handlers: `export const POST`, `export const GET`

**Type Declarations:**
- Extend `App.Locals` in `src/env.d.ts` for middleware context
- Use `import type` for type-only imports
- Supabase User type: `import("@supabase/supabase-js").User`

**Error Handling:**
- API routes: redirect with error query param on failure
- Forms: client-side validation before submit, server error display
- Use early returns for error conditions

### Framework-Specific Rules

#### Astro Rules
- **SSR Mode**: All pages server-rendered by default (`output: "server"`)
- **API Routes**: Must export `export const prerender = false`
- **Middleware**: Defined in `src/middleware.ts`, uses `defineMiddleware`
- **Supabase Access**: Create client with `createClient(context.request.headers, context.cookies)`
- **Auth State**: Access via `context.locals.user` (set by middleware)
- **Protected Routes**: Defined in `PROTECTED_ROUTES` array in middleware
- **Environment Vars**: Use `astro:env/server` for server secrets, not `import.meta.env`

#### React Rules (Islands)
- **NO "use client"**: This is Astro, not Next.js — never use Next.js directives
- **Islands Architecture**: React only for interactive components
- **Static Content**: Use `.astro` components for layouts and static content
- **Hooks Location**: Extract custom hooks to `src/components/hooks/`
- **Form Pattern**: Client-side validation + native form submission to API routes
- **State Management**: Local state with useState, no global state library

#### Supabase Integration
- **Client Creation**: Use `createClient()` from `@/lib/supabase` in routes
- **Cookie Sessions**: SSR client handles cookies automatically
- **Auth Check**: `supabase.auth.getUser()` returns `{ data: { user } }`
- **Middleware Pattern**: Resolve user on every request, attach to `context.locals`

### Code Quality & Style Rules

**Linting & Formatting:**
- ESLint 9 flat config with strict TypeScript rules
- Prettier with `prettier-plugin-astro`
- Pre-commit hooks: `eslint --fix` on `.ts,.tsx,.astro`, `prettier --write` on `.json,.css,.md`
- React Compiler plugin enabled (`react-compiler/react-compiler: "error"`)

**File Naming:**
- Components: `PascalCase.tsx` (React), `PascalCase.astro` (Astro)
- Utilities/services: `kebab-case.ts`
- API routes: `kebab-case.ts` in `src/pages/api/`
- Pages: `kebab-case.astro` in `src/pages/`

**Code Organization:**
- `src/components/` — React and Astro components
- `src/components/ui/` — shadcn/ui components only
- `src/components/auth/` — Auth-related React components
- `src/lib/` — Utilities and services
- `src/pages/api/` — API endpoints
- `src/layouts/` — Page layouts

**Tailwind & Styling:**
- ALWAYS use `cn()` helper for conditional/merged classes
- NEVER concatenate class strings manually
- Use shadcn/ui component variants via CVA
- Use `size-*` for equal width/height (e.g., `size-4` not `w-4 h-4`)

**Component Patterns:**
- Props interface defined inline or above component
- Destructure props in function signature
- Use Lucide icons with `className="size-4"`

### Development Workflow Rules

**Development Server:**
- Run: `npm run dev` — starts on port 3000
- Build: `npm run build` — production build with SSR
- Preview: `npm run preview` — preview production build

**Pre-commit Hooks (Husky + lint-staged):**
- Auto-runs `eslint --fix` on staged `.ts`, `.tsx`, `.astro` files
- Auto-runs `prettier --write` on staged `.json`, `.css`, `.md` files
- Commits will fail if linting errors cannot be auto-fixed

**Environment Setup:**
- Copy `.env.example` to `.env` for local development
- Required vars: `SUPABASE_URL`, `SUPABASE_KEY`
- Local Supabase: `npx supabase start` (requires Docker)

**Database Migrations:**
- Location: `supabase/migrations/`
- Naming: `YYYYMMDDHHmmss_short_description.sql`
- Always enable RLS on new tables
- Create granular per-operation, per-role policies

**Adding shadcn/ui Components:**
- Install: `npx shadcn@latest add [component-name]`
- Components go to `src/components/ui/`
- Uses "new-york" style variant

### Critical Don't-Miss Rules

**Anti-Patterns to AVOID:**
- ❌ NEVER use `"use client"` or other Next.js directives — this is Astro
- ❌ NEVER import Supabase client directly in components — use `context.locals` in routes
- ❌ NEVER use `import.meta.env` for secrets — use `astro:env/server`
- ❌ NEVER concatenate Tailwind classes — always use `cn()` helper
- ❌ NEVER create React components for static content — use `.astro` files
- ❌ NEVER use lowercase API route exports — use `POST`, `GET` (uppercase)

**Edge Cases to Handle:**
- Auth state may be `null` — always check `context.locals.user` before accessing
- Cookie parsing may return empty string — handle gracefully
- Form validation errors need both client-side state AND server-side error display
- Protected routes redirect happens in middleware, not in page components

**Security Rules:**
- Environment variables `SUPABASE_URL` and `SUPABASE_KEY` are server-only secrets
- Never expose Supabase keys to client-side code
- Always validate/sanitize form input with Zod in API routes
- Enable RLS on all Supabase tables — no exceptions

**Performance Patterns:**
- Use React only for interactive islands, not entire pages
- Prefer `.astro` for static content to minimize client JS
- Use `React.memo()` for components that render often with same props
- Extract expensive calculations to `useMemo`

---

## Usage Guidelines

**For AI Agents:**
- Read this file before implementing any code
- Follow ALL rules exactly as documented
- When in doubt, prefer the more restrictive option
- Update this file if new patterns emerge

**For Humans:**
- Keep this file lean and focused on agent needs
- Update when technology stack changes
- Review quarterly for outdated rules
- Remove rules that become obvious over time

---

_Last Updated: 2026-03-15_
