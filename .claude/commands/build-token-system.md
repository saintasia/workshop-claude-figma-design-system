Convert raw extracted tokens into a structured design token system with proper naming conventions and light/dark modes.

Reads from: `tokens/raw.json`
Writes to: `tokens/system/` directory

## Naming Convention to Apply

Use a three-tier token architecture:

**Tier 1 — Primitives** (`tokens/system/primitives.json`)
Raw values with semantic-free names. These are the source of truth.
```
color.blue.50 → #eff6ff
color.blue.500 → #3b82f6
color.neutral.0 → #ffffff
color.neutral.900 → #111827
```

**Tier 2 — Semantic tokens** (`tokens/system/semantic.json`)
Reference primitives, carry meaning, define modes.
```json
{
  "color": {
    "background": {
      "default": { "light": "{color.neutral.0}", "dark": "{color.neutral.900}" },
      "subtle": { "light": "{color.neutral.50}", "dark": "{color.neutral.800}" }
    },
    "text": {
      "default": { "light": "{color.neutral.900}", "dark": "{color.neutral.0}" },
      "muted": { "light": "{color.neutral.500}", "dark": "{color.neutral.400}" }
    },
    "border": {
      "default": { "light": "{color.neutral.200}", "dark": "{color.neutral.700}" }
    },
    "interactive": {
      "primary": { "light": "{color.blue.500}", "dark": "{color.blue.400}" },
      "primaryHover": { "light": "{color.blue.600}", "dark": "{color.blue.300}" }
    }
  }
}
```

**Tier 3 — Component tokens** (deferred — generated per component later)

## Steps

1. Read `tokens/raw.json`
2. Map raw color values to a named primitive scale (use the closest standard scale step: 50/100/200/300/400/500/600/700/800/900)
3. Infer semantic roles from usage context (if available) or from perceptual lightness:
   - Very light colors → background candidates
   - Very dark colors → text/foreground candidates
   - Mid-range saturated → interactive/accent candidates
4. Propose light and dark mode mappings for each semantic token — flag where you're uncertain and ask for confirmation
5. Generate `tokens/system/primitives.json` and `tokens/system/semantic.json`
6. Generate `tokens/system/tokens.css` — CSS custom properties for both modes:
```css
:root, [data-theme="light"] { --color-background-default: #ffffff; ... }
[data-theme="dark"] { --color-background-default: #111827; ... }
```
7. Update `docs/token-conventions.md` with a human-readable reference of all tokens and the naming rationale.
8. Print a summary and ask for confirmation on any ambiguous mode assignments before writing files.
