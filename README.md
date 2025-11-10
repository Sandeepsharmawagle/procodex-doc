## ProCodeX

ProCodeX is a modern coding practice platform built with Next.js (App Router). It includes a Monaco-powered code editor, problem solving workflow, and an Admin Panel (UI-only) for managing problems and submissions. The UI uses Tailwind CSS (v4) with shadcn/ui components and supports dark mode via next-themes.

### Tech Stack
- Next.js 16 (App Router)
- React 19
- TypeScript
- Tailwind CSS v4
- shadcn/ui + Radix Primitives
- lucide-react icons
- next-themes (dark mode)
- Monaco Editor

---

## Quick Start

```bash
# install dependencies
npm install

# start dev server
npm run dev

# build production
npm run build

# start production
npm run start

# lint (base config)
npm run lint
```

App runs on `http://localhost:3000` by default.

---

## Project Structure

```
app/                      # App Router pages & API routes
  (pages)/
    admin/               # Admin panel routes (UI-only)
    problems/            # Public problem pages
    profile/             # Profile pages
  api/
    compile/             # Code compile/execute API
  globals.css            # Global styles (incl. scrollbar theming)
  layout.tsx             # Root layout

components/              # UI and feature components
  admin/                 # Admin panel components
  editor/                # Editor & problem workspace
  practice/              # Practice list & filters
  profile/               # Profile widgets
  ui/                    # shadcn/ui components
  app-sidebar.tsx        # App-wide sidebar used by Admin

data/
  problems.json          # Source of truth for problems (UI-only)

hooks/                   # Reusable React hooks
  admin/                 # Admin data hooks (mock/local)

lib/
  constants/             # Constants & configuration
  services/              # Compiler runner & API client
  types/                 # Shared TypeScript types
  utils/                 # Utilities (validation, formatting, problems utils)

public/                  # Static assets
scripts/                 # Utility scripts (e.g., add problems)
```

---

## Admin Panel (UI-Only)

- Login: simple email/password UI (no backend verification). State is stored in `localStorage` under:
  - `admin_authenticated`
  - `admin_email`
- Sidebar: uses `components/app-sidebar.tsx`
- Problems: CRUD UI over `data/problems.json` using mock helpers (`lib/utils/admin-mock.ts`)
- Submissions: mock view and filters
- Dashboard: stats cards and recent submissions (mocked)

Hydration safety:
- Client-only operations (localStorage) are accessed inside `useEffect`
- `app/layout.tsx` uses `suppressHydrationWarning` to play nicely with `next-themes`

---

## Problems & Test Cases

- Source: `data/problems.json`
- Types: `lib/types/editor.ts` (`Problem`, `TestCase`, etc.)
- Each problem can define:
  - `sampleTestCases`: visible in UI
  - `hiddenTestCases`: used for evaluation (not shown to users)
  - `starterCode` per language
  - `topics`, `companies`, `tags`, `constraints`, `examples`

Compiler/Runner:
- Client-side editor uses Monaco; execution/verification goes through the compile API at `app/api/compile/route.ts`
- Service entrypoint: `lib/services/compiler-service.ts`

---

## Styling & Theming

- Tailwind v4 with project tokens
- Global thin scrollbars with light/dark variants in `app/globals.css`
- shadcn/ui components are placed under `components/ui/*`

---

## Development Conventions

- Feature-first folders (admin, editor, practice, profile)
- Separate logic (hooks) from presentation (components)
- Barrel exports (`index.ts`) where appropriate
- Strong TypeScript types for all shared data structures

Imports:
```ts
import { Button } from "@/components/ui/button";
import { useAdminStats } from "@/hooks/admin";
import type { Problem } from "@/lib/types";
import { cn } from "@/lib/utils";
```

---

## Environment Variables

This project runs UI-only features by default. If you need to configure API base URLs:

```
NEXT_PUBLIC_API_URL=http://localhost:3000
NEXT_PUBLIC_API_TIMEOUT=30000
NEXT_PUBLIC_APP_NAME=ProCodeX
```

Create a `.env.local` file and add the variables you need.

---

## Testing Locally

- Open `/problems` to browse and search problems
- Open `/problems/[problemId]` to use the editor, run code, and submit against sample/hidden test cases
- Open `/admin/login` to access the Admin Panel (UI-only). Any non-empty email/password will proceed (mock).

---

## Troubleshooting

- Hydration mismatch:
  - Ensure client-only code runs inside `useEffect`
  - `app/layout.tsx` includes `suppressHydrationWarning` for `html/body`
- Styling/components:
  - Verify all shadcn/ui pieces exist under `components/ui/*`
- Build issues:
  - Delete `.next/` and re-run `npm run build`

---

## Contributing

1. Fork and create a feature branch
2. Follow existing code style and patterns
3. Keep components small and typed
4. Submit a PR with a clear description and screenshots (if UI changes)

---

## License

This project is licensed under the MIT License. See the LICENSE file if provided.
