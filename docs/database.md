# Database — Drizzle ORM + PostgreSQL

## Client

The Drizzle client is a singleton defined in `db/index.ts`. Always import it as:
```ts
import db from "@/db"
```

Never instantiate `drizzle()` anywhere else.

## Schema

All table definitions live in `db/schema.ts`. Follow this pattern:

```ts
// db/schema.ts
import { pgTable, text, integer, timestamp, boolean } from "drizzle-orm/pg-core"

export const links = pgTable("links", {
  id:        text("id").primaryKey().$defaultFn(() => crypto.randomUUID()),
  userId:    text("user_id").notNull(),
  url:       text("url").notNull(),
  slug:      text("slug").notNull().unique(),
  clicks:    integer("clicks").notNull().default(0),
  createdAt: timestamp("created_at").notNull().defaultNow(),
  updatedAt: timestamp("updated_at").notNull().defaultNow(),
})

export type Link    = typeof links.$inferSelect
export type NewLink = typeof links.$inferInsert
```

### Rules
- Column names use **snake_case** in the database; TypeScript fields use **camelCase** (Drizzle maps automatically)
- Always export `$inferSelect` and `$inferInsert` types — never manually duplicate them
- Primary keys use `crypto.randomUUID()` (no numeric auto-increment)
- Every table needs `createdAt` and `updatedAt` timestamps
- `notNull()` on every column unless null is intentionally meaningful

## Migrations

Run migrations with Drizzle Kit:
```bash
npx drizzle-kit generate   # generate migration SQL
npx drizzle-kit migrate    # apply migrations to the database
npx drizzle-kit studio     # open Drizzle Studio (local GUI)
```

**Never** manually edit files in the `drizzle/` output directory.

## Query Patterns

```ts
import db from "@/db"
import { links } from "@/db/schema"
import { eq, desc } from "drizzle-orm"

// SELECT
const allLinks = await db.select().from(links)
const userLinks = await db.select().from(links).where(eq(links.userId, userId))

// INSERT
const [newLink] = await db.insert(links).values({ url, slug, userId }).returning()

// UPDATE
await db.update(links).set({ clicks: sql`${links.clicks} + 1` }).where(eq(links.slug, slug))

// DELETE
await db.delete(links).where(eq(links.id, id))
```

### Rules
- Use `.returning()` after INSERT/UPDATE when you need the resulting row
- Prefer Drizzle's query builder over raw SQL
- Use `drizzle-orm/sql` tagged `sql` template only when the query builder cannot express the operation
- Always scope queries to the authenticated `userId` — never return another user's data

## Environment

`DATABASE_URL` must be set in `.env`. The config is loaded via `dotenv/config` in `db/index.ts` and `drizzle.config.ts`. Do not hard-code any credentials.
