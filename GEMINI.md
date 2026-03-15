# Gemini Project Mandates

This project is an Astro-based web application integrated with the BMad Method. All AI interactions must adhere to the following standards:

## Technical Stack
- **Framework:** Astro v5 (SSR mode)
- **UI:** React 19 + TypeScript
- **Styling:** Tailwind CSS v4
- **Backend:** Supabase (Auth & Database)
- **Methodology:** BMad Method (Agile AI-Driven Development)

## Project Standards
- **Component Placement:** 
  - Astro components go in `src/components/` (standard UI) or `src/pages/` (routes).
  - React components go in `src/components/` and should use the `.tsx` extension.
- **Environment Variables:** Use Astro's `astro:env` for type-safe environment variables.
- **BMad Integration:** 
  - Save planning artifacts to `_bmad-output/planning-artifacts`.
  - Save implementation artifacts to `_bmad-output/implementation-artifacts`.
  - Maintain project documentation in the `docs/` directory.

## Operational Rules
- **Server:** The development server runs on port `3000`.
- **Git:** Maintain the existing starter history unless explicitly asked to reset.
- **NPM Cache:** Due to local permission issues, if `npm install` fails, use a local cache: `npm install --cache ./.npm-local-cache`.
