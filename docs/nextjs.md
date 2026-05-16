# Next.js App Router Patterns

> This project uses Next.js **16** with the App Router. The Pages Router is not used.
> Read `node_modules/next/dist/docs/` for the authoritative API reference before using any Next.js API.

## Server vs Client Components

### Default: Server Components
Every file in `app/` is a **React Server Component (RSC)** by default. This is the preferred model.

```tsx
// app/dashboard/page.tsx — Server Component (no directive needed)
import db from "@/db"
import { links } from "@/db/schema"
import { eq } from "drizzle-orm"
import { auth } from "@clerk/nextjs/server"

export default async function DashboardPage() {
  const { userId } = await auth()
  const userLinks = await db.select().from(links).where(eq(links.userId, userId!))
  return <LinkList links={userLinks} />
}
```

### When to Use `"use client"`
Add `"use client"` only when the component needs:
- `useState`, `useEffect`, or other React hooks
- Browser-only APIs (`window`, `localStorage`)
- Event handlers that are not passed as props

```tsx
"use client"
// components/LinkForm.tsx
import { useState } from "react"

export function LinkForm() {
  const [url, setUrl] = useState("")
  ...
}
```

**Rule:** Push `"use client"` as far down the component tree as possible.

## Data Fetching

- **Server Components:** `await db.select()...` directly in the component — no `useEffect`
- **Mutations:** Use **Server Actions** (`"use server"`) — do not create API routes for simple CRUD
- **API Routes** (`app/api/`): Only for webhooks, external integrations, or truly public endpoints

### Server Action Pattern
```ts
// app/dashboard/actions.ts
"use server"

import { auth } from "@clerk/nextjs/server"
import db from "@/db"
import { links } from "@/db/schema"
import { revalidatePath } from "next/cache"

export async function createLink(url: string, slug: string) {
  const { userId } = await auth()
  if (!userId) throw new Error("Unauthorized")

  await db.insert(links).values({ url, slug, userId })
  revalidatePath("/dashboard")
}
```

## Routing

| Pattern | Usage |
|---|---|
| `app/page.tsx` | Public homepage |
| `app/dashboard/page.tsx` | Authenticated dashboard |
| `app/[slug]/page.tsx` | Dynamic redirect route |
| `app/(auth)/sign-in/page.tsx` | Clerk sign-in (route group, no URL segment) |
| `app/api/links/route.ts` | API route handler |

### Dynamic Redirect Route
```tsx
// app/[slug]/page.tsx
import { redirect, notFound } from "next/navigation"
import db from "@/db"
import { links } from "@/db/schema"
import { eq } from "drizzle-orm"

export default async function SlugPage({ params }: { params: { slug: string } }) {
  const [link] = await db.select().from(links).where(eq(links.slug, params.slug))
  if (!link) notFound()
  redirect(link.url)
}
```

## Metadata
Define `metadata` exports in `layout.tsx` and `page.tsx` — never use `<head>` tags directly:
```ts
export const metadata: Metadata = {
  title: "Link Shortener",
  description: "Shorten and track your links",
}
```

## Loading & Error UI
- `loading.tsx` — automatic Suspense boundary for the route segment
- `error.tsx` — must be a Client Component (`"use client"`)
- `not-found.tsx` — rendered by `notFound()` helper

## Middleware
Use `middleware.ts` at the project root for Clerk route protection. Do not add other logic to middleware.
