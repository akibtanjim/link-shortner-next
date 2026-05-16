# File & Folder Structure

## Top-Level Layout

```
linkshortner/
├── app/                  # Next.js App Router (routes, layouts, pages)
├── components/
│   └── ui/               # shadcn/ui primitive components ONLY
├── db/
│   ├── schema.ts         # Drizzle table definitions
│   └── index.ts          # Drizzle client singleton
├── lib/
│   └── utils.ts          # Shared utility functions (cn, etc.)
├── docs/                 # Agent instruction markdown files
├── public/               # Static assets
├── drizzle.config.ts     # Drizzle Kit configuration
├── next.config.ts        # Next.js configuration
├── tsconfig.json
└── AGENTS.md             # Agent entry point
```

## App Router Conventions

```
app/
├── layout.tsx            # Root layout (ClerkProvider, fonts, header)
├── page.tsx              # Homepage (public)
├── globals.css           # Global styles & Tailwind theme
├── (auth)/               # Route group: Clerk sign-in / sign-up pages
├── dashboard/            # Protected: user link management
│   ├── layout.tsx
│   └── page.tsx
├── [slug]/               # Dynamic: redirect short links
│   └── page.tsx
└── api/                  # API Route Handlers
    └── links/
        └── route.ts
```

## Naming Conventions

| Artifact | Convention | Example |
|---|---|---|
| React components | PascalCase | `LinkCard.tsx` |
| Pages / layouts | lowercase `page.tsx` / `layout.tsx` | Next.js enforced |
| Route groups | `(groupName)` parentheses | `(auth)` |
| Utility functions | camelCase | `generateSlug.ts` |
| DB schema tables | camelCase variable, snake_case column | `links`, `created_at` |
| CSS / class helpers | camelCase | `cn()` |

## Path Aliases

The `@/` alias maps to the project root. Always use it for imports:

```ts
// ✅ correct
import { cn } from "@/lib/utils"
import db from "@/db"

// ❌ wrong
import { cn } from "../../lib/utils"
```

## Component Placement Rules

- **`components/ui/`** — only shadcn/radix primitive wrappers (Button, Input, Card…)
- **`components/`** — feature-level composed components (e.g., `LinkForm`, `LinkTable`)
- **`app/`** — page-level components and layouts; no reusable logic here
