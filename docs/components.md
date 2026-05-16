# UI Components

## Component Libraries

| Library | Purpose |
|---|---|
| **shadcn/ui** | Pre-built accessible components (added via CLI) |
| **radix-ui** | Headless primitives used by shadcn (`Slot.Root`, etc.) |
| **class-variance-authority (CVA)** | Type-safe variant styling |
| **lucide-react** | Icon set |

## Adding shadcn Components

Always use the CLI — never copy-paste component code manually:
```bash
npx shadcn@latest add <component-name>
# e.g.
npx shadcn@latest add input
npx shadcn@latest add card
npx shadcn@latest add dialog
```

Generated files land in `components/ui/`. Do not rename or move them.

## Component Style (CVA + cn)

Follow the `Button` component pattern when building custom primitive-style components:

```tsx
import * as React from "react"
import { cva, type VariantProps } from "class-variance-authority"
import { cn } from "@/lib/utils"

const badgeVariants = cva(
  "inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground",
        secondary: "bg-secondary text-secondary-foreground",
        destructive: "bg-destructive text-destructive-foreground",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
)

type BadgeProps = React.HTMLAttributes<HTMLDivElement> &
  VariantProps<typeof badgeVariants>

export function Badge({ className, variant, ...props }: BadgeProps) {
  return (
    <div
      className={cn(badgeVariants({ variant }), className)}
      {...props}
    />
  )
}
```

### Rules
- Use `cn()` (from `@/lib/utils`) for all `className` merging — never string concatenation
- Accept and spread `className` so consumers can override styles
- Use `data-slot` attributes on root elements for CSS targeting (see `Button` for reference)
- Use `React.ComponentProps<"element">` for forwarding all native HTML props

## Radix Primitives

Access Radix through the `radix-ui` package (not individual `@radix-ui/*` packages):
```tsx
import { Slot } from "radix-ui"
// use Slot.Root, Dialog.Root, etc.
```

## Icons

Import icons individually from `lucide-react`:
```tsx
import { Link2, Copy, Trash2 } from "lucide-react"

// In JSX — icons inherit text color and scale with font-size
<Link2 className="size-4" />
```

Do not import the entire icon library.

## Accessibility

- Always provide meaningful `aria-label` on icon-only buttons
- Prefer shadcn components that include built-in ARIA attributes
- Use semantic HTML elements — `<button>` for actions, `<a>` for navigation
