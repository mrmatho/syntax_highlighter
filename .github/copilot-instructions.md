# Copilot Agent Instructions

## Repository Overview

This repository contains a single-page web application (`index.html`) that syntax-highlights Python code for pasting into presentation tools (PowerPoint, Google Slides, Keynote, etc.). There is no build process, no package manager, and no test suite — the entire app runs in the browser from a single self-contained HTML file.

## File Structure

```
index.html    — The entire application (HTML + CSS + JS, ~700 lines)
README.md     — Short usage note
```

## Architecture

The app is fully client-side. Key sections inside `index.html`:

| Section | What it does |
|---|---|
| `<style>` block | All CSS for layout, panels, buttons, and theme button colours |
| `DEFAULT_TOKENS` array | Defines every syntax token type with its default colour, bold, and italic settings |
| `THEMES` object | Three preset colour themes: `dark`, `corporate`, `fun` |
| `CLASS_MAP` object | Maps `hljs-*` CSS class names to internal token IDs |
| `tagVariables()` | Post-processes highlight.js output to tag bare identifiers as variables (highlight.js 11.x does not emit `hljs-variable` tokens for Python) |
| `highlight()` | Calls `hljs.highlight()` then `tagVariables()` |
| `inlineStyle()` | Walks the highlighted DOM and converts CSS classes to inline `style` attributes for clipboard output |
| `buildPreviewStyle()` | Generates a `<style>` tag for the live preview panel |
| `refreshPreview()` | Orchestrates highlighting and updates both the preview and the HTML output textarea |

## Dependencies

- **highlight.js 11.9.0** — loaded from cdnjs CDN (no local copy).  
  `https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js`

## Token System

Token types are defined in `DEFAULT_TOKENS`. Each token has:
- `id` — internal key (e.g. `"keyword"`, `"variable"`)
- `label` — display name shown in the Settings panel
- `color` — default hex colour
- `bold` / `italic` — boolean formatting flags

When adding a new token type:
1. Add an entry to `DEFAULT_TOKENS`.
2. Add an entry to `CLASS_MAP` mapping the `hljs-*` class name to the new token `id`.
3. Add the same `id` with appropriate colours to each theme in the `THEMES` object.

## Known highlight.js Behaviour

highlight.js 11.9.0's Python grammar **does not emit `hljs-variable` tokens** for ordinary Python variables. The `tagVariables()` function works around this by scanning root-level text nodes in the highlighted output and wrapping bare identifiers (that are not Python keywords) in `<span class="hljs-variable">` elements.

## Default Settings

| Setting | Default value |
|---|---|
| Font family | `Consolas` |
| Font size | `24px` |
| Background colour | `#ffffff` |
| Default text colour | `#222222` |

## Making Changes

- **All changes are made directly in `index.html`** — there is no separate JS or CSS file.
- There is no build step; simply open `index.html` in a browser to test.
- There is no automated test suite. Verify changes manually in a browser.
- Preserve the single-file approach; do not split the app into separate files unless explicitly asked.
- When pasting the output into a presentation, use **Paste > Keep Source Formatting**.
