Publish the token system and components to the connected Figma file.

Reads from: `tokens/system/`, `components/`
Requires: Figma MCP server connected, Figma file URL in CLAUDE.md

## Steps

1. Load the figma-use skill before any use_figma calls
2. Read `tokens/system/primitives.json` and `tokens/system/semantic.json`
3. In Figma:
   a. Create a **Variables** collection named "Design Tokens" with Light and Dark modes
   b. Populate color variables from the semantic token system — use the tier-2 semantic names as variable names (e.g. `color/background/default`)
   c. Reference primitives as the raw values; semantic tokens reference primitives where possible
4. For each component in `components/`:
   a. Use get_design_context or generate_figma_design to create a matching Figma component
   b. Bind color properties to the token variables (not hardcoded values)
   c. Create variants matching the component's props
   d. Publish each as a Figma component in a "Components" page
5. Create a **Token Reference** frame on a "Tokens" page showing all color tokens as swatches with their names
6. Report back with a summary of what was created and any manual steps needed in Figma

## Notes
- Always load figma-use skill before calling use_figma
- If the Figma file URL is not in CLAUDE.md, ask for it before proceeding
- Prefer creating variables over hardcoded fills

## Auto-layout sizing — hug vs fixed

Getting "hug contents" right requires three things to all be correct at once. Missing any one of them leaves the frame with a fixed height.

### 1. Call `resize()` before setting sizing modes — never after

`resize()` resets both axes to `FIXED`. Set your desired sizing mode **after** the resize call:

```js
// WRONG — resize overrides the AUTO you just set
frame.primaryAxisSizingMode = 'AUTO';
frame.resize(280, 10); // ← resets height back to FIXED

// CORRECT — set AUTO after resize
frame.resize(280, 10);
frame.primaryAxisSizingMode = 'AUTO'; // height now hugs
frame.counterAxisSizingMode = 'FIXED'; // width stays 280
```

### 2. Set both axes explicitly on every frame

Figma defaults to `FIXED` on both axes. Always declare what you want:

| Intent | primaryAxisSizingMode | counterAxisSizingMode |
|--------|----------------------|-----------------------|
| Hug width and height | `AUTO` | `AUTO` |
| Fixed width, hug height (e.g. cards, inputs) | `AUTO` | `FIXED` (+ call resize for width first) |
| Fill parent on one axis | set on child via `layoutSizingHorizontal/Vertical` | — |

### 3. Set `layoutSizingHorizontal/Vertical` on every direct child

Children inside auto-layout frames don't fill their parent by default. Forgetting this is what makes auto-layout parents shrink-wrap to the wrong size:

```js
// Text that should span the full width of its parent:
parent.appendChild(textNode);
textNode.layoutSizingHorizontal = 'FILL'; // must be set AFTER appendChild

// Inner frame that should fill parent width:
parent.appendChild(innerFrame);
innerFrame.layoutSizingHorizontal = 'FILL'; // same rule
```

`'FILL'` and `'HUG'` can only be set **after** the node is appended to an auto-layout parent — setting them before throws.

### Checklist before finishing any component

- [ ] No frame has `resize()` called after its sizing mode was set to `AUTO`
- [ ] Every direct child of an auto-layout frame has explicit `layoutSizingHorizontal` and `layoutSizingVertical`
- [ ] Text nodes that should span the full width have `layoutSizingHorizontal = 'FILL'`
- [ ] After `combineAsVariants`, children are repositioned manually (they stack at 0,0 with FIXED sizes)
