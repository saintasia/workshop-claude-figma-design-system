Generate React components from the token system and add them to the Vite preview gallery.

Reads from: `tokens/system/`
Writes to: `components/` and `preview/`

## Steps

1. Read `tokens/system/semantic.json` and `tokens/system/tokens.css`
2. Scaffold a minimal Vite + React preview app in `preview/` if it doesn't exist yet:
   - `preview/index.html` — mounts the app, imports `tokens.css`
   - `preview/main.tsx` — React entry point
   - `preview/Gallery.tsx` — component gallery page
   - `preview/package.json` — minimal deps: vite, react, react-dom
   - `preview/vite.config.ts`
3. For each component requested (or infer a base set from the tokens: Button, Badge, Card, Input, Text):
   - Create `components/<ComponentName>.tsx`
   - Use only CSS custom properties from the token system (no hardcoded colors)
   - Export all meaningful variants as named exports or a `variants` prop
   - Add the component and all its variants to `preview/Gallery.tsx`
4. The gallery should have a theme toggle (sets `data-theme="dark"` on `<html>`) so both modes are verifiable
5. Print instructions to run the preview: `cd preview && npm install && npm run dev`

## Component conventions
- Functional components, TypeScript
- Styles via inline `style` prop using CSS vars, or a co-located `<ComponentName>.module.css`
- No external UI library dependencies — tokens only
- Each component file exports a single default component plus a `<ComponentName>Preview` named export that renders all variants (used by Gallery)
