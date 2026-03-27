Extract design tokens from a live webpage.

Usage: /extract-tokens <url>

Steps:
1. Fetch the page at the given URL using WebFetch
2. Also fetch the page's linked stylesheets if any (look for <link rel="stylesheet"> tags and fetch those URLs too)
3. Parse all CSS for:
   - Color values (hex, rgb, hsl, oklch, named colors) — include both hardcoded values AND CSS custom properties
   - Font families, sizes, weights, line heights
   - Spacing values (padding, margin, gap patterns that repeat)
   - Border radius values
   - Shadow definitions
   - Any existing CSS custom property definitions (--variable-name: value)
4. Deduplicate and cluster similar values (e.g. colors within 5% perceptual distance may be the same token)
5. Output a structured JSON to `tokens/raw.json` with this shape:
```json
{
  "source": "<url>",
  "extractedAt": "<ISO timestamp>",
  "colors": {
    "raw": ["#1a1a2e", ...],
    "cssVars": { "--primary": "#1a1a2e", ... }
  },
  "typography": {
    "fontFamilies": [],
    "fontSizes": [],
    "fontWeights": [],
    "lineHeights": []
  },
  "spacing": {
    "raw": []
  },
  "radii": [],
  "shadows": [],
  "notes": "Any observations about the design language"
}
```
6. Print a summary of what was found and flag anything ambiguous or that needs a human decision (e.g. "found 3 blues that may be the same token").
