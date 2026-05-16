# Authentication — Clerk

## Setup

Clerk is configured in `app/layout.tsx` via `<ClerkProvider>`. The middleware at `middleware.ts` (root) controls which routes are protected.

## Required Environment Variables

```
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/dashboard
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/dashboard
```

## Middleware (Route Protection)

Create/maintain `middleware.ts` at the project root:

```ts
// middleware.ts
import { clerkMiddleware, createRouteMatcher } from "@clerk/nextjs/server"

const isPublicRoute = createRouteMatcher([
  "/",
  "/sign-in(.*)",
  "/sign-up(.*)",
  "/:slug",          // short-link redirect is public
])

export default clerkMiddleware(async (auth, req) => {
  if (!isPublicRoute(req)) {
    await auth.protect()
  }
})

export const config = {
  matcher: ["/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)", "/(api|trpc)(.*)"],
}
```

## Getting the Current User

### In Server Components & Server Actions
```ts
import { auth } from "@clerk/nextjs/server"

const { userId } = await auth()
if (!userId) throw new Error("Unauthorized")
```

### In Client Components
```tsx
"use client"
import { useUser } from "@clerk/nextjs"

export function ProfileButton() {
  const { user, isLoaded } = useUser()
  if (!isLoaded) return null
  return <span>{user?.firstName}</span>
}
```

## UI Components

Use Clerk's built-in components — do not build custom auth forms:

```tsx
import {
  ClerkProvider,
  SignInButton,
  SignUpButton,
  UserButton,
  Show,
} from "@clerk/nextjs"

// Show/hide by auth state (already set up in layout.tsx)
<Show when="signed-out">
  <SignInButton />
  <SignUpButton />
</Show>
<Show when="signed-in">
  <UserButton />
</Show>
```

## Sign-In / Sign-Up Pages

Use Clerk's hosted components in `app/(auth)/`:
```tsx
// app/(auth)/sign-in/[[...sign-in]]/page.tsx
import { SignIn } from "@clerk/nextjs"

export default function SignInPage() {
  return <SignIn />
}
```

## Security Rules

- **Always** verify `userId` server-side before any database read/write
- **Never** trust `userId` from the client — always read it from `auth()` on the server
- **Never** expose another user's data — always filter queries by `userId`
- **Never** store Clerk's `userId` as a foreign key without validating it came from `auth()`
