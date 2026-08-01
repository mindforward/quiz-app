<!-- BEGIN:nextjs-agent-rules -->
# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` before writing any code. Heed deprecation notices.
<!-- END:nextjs-agent-rules -->

# Revision App — 練習平台

Quiz revision app (P5 中文 / HCIA AI) — Next.js + Supabase + Tailwind.

## Stack

- **Next.js 16** (Turbopack) + React 19 + TypeScript — read `node_modules/next/dist/docs/` before writing code (breaking changes vs older Next.js).
- **Supabase** (PostgreSQL) — project `azpvtdhqvfksefuwfueb`. Client: `src/lib/supabase.ts` (anon key, `NEXT_PUBLIC_SUPABASE_URL` + `NEXT_PUBLIC_SUPABASE_ANON_KEY`).
- **Tailwind CSS v4** via `@tailwindcss/postcss` (CSS-first config, no `tailwind.config.js`).
- **KaTeX** (`katex`) for math rendering — `src/components/Math.tsx` / `MathText.tsx`.

## Data model (Supabase tables)

- `quiz_banks` — id, name, description, created_at
- `questions` — id, quiz_bank_id, question_number, question_text, hint, image_url
- `answer_options` — id, question_id, text, rationale, is_correct, sort_order

Quiz pages load `QuestionWithOptions[]` (question + nested answer_options) via supabase client.

## Routes

- `/` — server component: lists quiz banks from `quiz_banks`
- `/quiz/[id]` — quiz player (client): question count picker, multi-select support, hint reveal, review mode, browser TTS (speechSynthesis)
- `/api/add-image-column` — adds image_url column (DB migration helper)

## Local quiz data

- `data/*.json` — question banks in `{"questions": [{questionNumber, question, hint, answerOptions}]}` format
- Import to Supabase: `python3 scripts/import-quiz.py <json> <bank_name>` (needs `SUPABASE_SERVICE_KEY` env)
- `scripts/` also has import-chinese.py, do-import.py, bump-version.sh

## Versioning

- `version.json` holds `{major, minor, patch}`; `scripts/generate-version.sh` regenerates `src/app/version.ts` (DO NOT edit that file manually). Build runs it automatically.
- Version bump: `scripts/bump-version.sh`.

## Build & deploy

- `npm run build` → Next.js static/server output; deploy to Vercel (project `revision-app`, domain revision.fredchung.hk).
- Env needed on Vercel: `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_KEY` (for import scripts).
- Workflow: commit + push to GitHub (mindforward/revision-app) → Vercel auto-deploys.

## Conventions

- UI text in Traditional Chinese (zh-HK).
- Follow the user's bug-fix rule: fix bugs ONLY, never change UI/existing features unless explicitly asked.
