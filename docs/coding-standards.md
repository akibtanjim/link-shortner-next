# TypeScript & React Coding Standards

## TypeScript

### Strict Mode
`tsconfig.json` has `"strict": true`. This cannot be changed. Consequences:
- No implicit `any` — always type function parameters and return values
- No `@ts-ignore` or `@ts-expect-error` without a code comment explaining why
- Non-null assertions (`!`) are allowed only when the value is **provably** non-null

### Type Definitions
```ts
// ✅ Use `type` for object shapes and unions
type Link = {
  id: string
  slug: string
  url: string
  clicks: number
  createdAt: Date
}

// ✅ Use `interface` only when you need extension / merging
interface LinkWithUser extends Link {
  userId: string
}

// ❌ Do not use `any`
function process(data: any) { ... }

// ✅ Use `unknown` + narrowing instead
function process(data: unknown) {
  if (typeof data === "string") { ... }
}
```

### Imports
- Always use named exports; avoid default exports except for Next.js pages/layouts (enforced by the framework)
- Group imports: external packages → internal `@/` aliases → relative paths
- No unused imports

## React

### Component Style
Use **function declarations** (not arrow functions) for components:
```tsx
// ✅
export function LinkCard({ link }: { link: Link }) {
  return <div>{link.slug}</div>
}

// ❌
const LinkCard = ({ link }: { link: Link }) => <div>{link.slug}</div>
```

### Props
- Inline prop types for small components; extract a named `type Props = {}` for anything with 3+ props
- Never use `React.FC` — it adds implicit `children` and obscures return type

### Hooks
- Custom hooks live in `hooks/` (create the folder when the first hook is needed)
- Name custom hooks `use<PascalCase>`
- Never call hooks conditionally

### Keys in Lists
Always use a stable, unique key — never array index:
```tsx
// ✅
links.map((link) => <LinkCard key={link.id} link={link} />)

// ❌
links.map((link, i) => <LinkCard key={i} link={link} />)
```

### Error Boundaries
Use Next.js `error.tsx` files for route-level error handling. Do not create custom `ErrorBoundary` class components.

## General Code Style

- **No magic numbers** — extract to named constants
- **Early returns** over deeply nested conditionals
- **No console.log** in committed code — use `console.error` only for caught errors
- Functions should do one thing; keep them under ~40 lines
- Comments only where logic is non-obvious; do not narrate what the code says
