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
