# Project Overview

## What This Project Is

A full-stack **URL shortener** built with Next.js 16 App Router. Users can create short links, view click analytics, and manage their links behind an authenticated dashboard.

## Core Goals

- Shorten long URLs to memorable slugs
- Track click counts per link
- Restrict link management to authenticated users (Clerk)
- Persist data in a PostgreSQL database (Drizzle ORM)

## Tech Stack

| Layer | Technology | Version |
|---|---|---|
| Framework | Next.js (App Router) | 16.x |
| Language | TypeScript | 5.x (strict) |
| Styling | Tailwind CSS | 4.x |
| UI Components | shadcn/ui (radix-nova style) | latest |
| Icons | lucide-react | latest |
| Animation | tw-animate-css | latest |
| Auth | Clerk | 7.x |
| ORM | Drizzle ORM | 0.45.x |
| Database | PostgreSQL (via `pg`) | — |
| Variant styling | class-variance-authority (CVA) | 0.7.x |
| Class merging | clsx + tailwind-merge | latest |

## Environment Variables

All secrets are stored in `.env` and **never** committed. Required variables:

```
DATABASE_URL=       # PostgreSQL connection string
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=
```

## Do Not Add

- Redux / Zustand / other global state managers (use React state + server actions)
- Axios (use native `fetch`)
- CSS-in-JS libraries
- Any ORM other than Drizzle
- Any auth library other than Clerk
