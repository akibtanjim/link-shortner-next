# Styling — Tailwind CSS v4

## Setup

This project uses **Tailwind CSS v4** with the new CSS-first configuration (no `tailwind.config.js`).
Theme tokens are defined as CSS custom properties in `app/globals.css` via the `@theme inline` block.

## Key Differences from Tailwind v3

- Configuration is in `globals.css`, not `tailwind.config.js`
- `@import "tailwindcss"` replaces the old `@tailwind base/components/utilities` directives
- Arbitrary values and CSS variables work the same way
- Dark mode uses a custom variant: `@custom-variant dark (&:is(.dark *))`

## Design Tokens

All colours, radii, and fonts are CSS custom properties. **Always use semantic tokens** — never hard-code raw colour values:

```tsx
// ✅ Use semantic tokens
<div className="bg-background text-foreground border-border" />
<div className="bg-primary text-primary-foreground" />
<div className="bg-muted text-muted-foreground" />

// ❌ Never hard-code colours
<div className="bg-white text-black" />
<div style={{ color: "#333" }} />
```

### Available Token Groups

| Token | Usage |
|---|---|
| `background` / `foreground` | Page background and default text |
| `primary` / `primary-foreground` | Brand CTAs and primary buttons |
| `secondary` / `secondary-foreground` | Secondary buttons, subtle elements |
| `muted` / `muted-foreground` | Backgrounds, placeholder text |
| `accent` / `accent-foreground` | Highlights, hover states |
| `destructive` | Error states, delete actions |
| `border` | All borders |
| `input` | Form input borders |
| `ring` | Focus rings |
| `card` / `card-foreground` | Card surfaces |

### Radius Tokens

Use the pre-defined radius scale — do not use arbitrary values:

```tsx
// ✅
<div className="rounded-sm" />   // calc(var(--radius) * 0.6)
<div className="rounded-md" />   // calc(var(--radius) * 0.8)
<div className="rounded-lg" />   // var(--radius)  ← default
<div className="rounded-xl" />   // calc(var(--radius) * 1.4)
```

## Dark Mode

Dark mode works via the `.dark` class on a parent element. Use the `dark:` variant:
```tsx
<p className="text-zinc-600 dark:text-zinc-400" />
```

## Typography

The project uses two Google Fonts defined in `layout.tsx`:
- `--font-geist-sans` → `font-sans` (default body font)
- `--font-geist-mono` → `font-mono`

```tsx
// ✅ Use utility classes
<code className="font-mono text-sm" />
<h1 className="font-semibold tracking-tight" />
```

## Class Ordering Convention

Follow Tailwind's recommended order (enforced by Prettier Tailwind plugin if added):
1. Layout (`flex`, `grid`, `block`)
2. Sizing (`w-`, `h-`, `max-w-`)
3. Spacing (`p-`, `m-`, `gap-`)
4. Typography (`text-`, `font-`, `leading-`)
5. Color (`bg-`, `text-`, `border-`)
6. Effects (`shadow-`, `opacity-`, `transition-`)
7. State variants (`hover:`, `focus:`, `dark:`)

## Do Not

- Do not write `style={{}}` props for anything achievable with Tailwind
- Do not add arbitrary colour values like `bg-[#abc123]` — add a token to `globals.css` instead
- Do not create separate CSS files for component styles — Tailwind classes only
