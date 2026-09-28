# theme/

## SVG click-to-zoom

Click (or tab to and press Enter on) any `.svg` image in the content and it
opens in a fullscreen pan/zoom viewer: drag to pan, wheel to zoom at the cursor,
double-click to zoom in, `+` / `-` / `0` (fit) / `Esc` on the keyboard, plus a
toolbar that auto-hides while you're looking at the diagram. The viewer themes
itself off mdBook's CSS variables, so it matches light/rust/coal/navy/ayu.

It is desktop-pointer only, on purpose: it bails out immediately on
`(pointer: coarse)` devices, where the platform's native pinch-to-zoom already
handles SVGs perfectly. So this is the fix for "my graphics are unreadable on
Windows", and phones are unaffected.

### Files

| File | Role |
| --- | --- |
| `svg-zoomer.js` | The viewer: decorator, pan/zoom canvas, lightbox, click/keyboard triggers. |
| `svg-zoomer.css` | `cursor:zoom-in` affordance + lightbox chrome. |
| `zoom-unstick.js` | Tags `<html>` with `.pinch-zoomed` during native pinch-zoom. |
| `custom.css` | The `html.pinch-zoomed` rules that `zoom-unstick.js` switches on — unsticks the menu bar so it doesn't ride along magnified over what you're zooming. |

All four are wired up in `book.toml` under `[output.html]` via `additional-css`
and `additional-js`. No build step, no dependencies.

### Where this came from

Copied verbatim from the `pushpopswap` book
(`https://github.com/ariadne-notes/pushpopswap`, `theme/`). **That repo's
`theme/README.md` is the source of truth** — it documents every theme asset,
what each one depends on, and the procedure for copying one into another mdBook
site.

Deliberately **not** copied: `mermaid-lazyload.js` and its vendored
`src/lazyload-js/mermaid-11.15.0.min.js` (3.2 MB), because this book has no
mermaid diagrams. `svg-zoomer.js` has a mermaid code path that simply lies
dormant; it listens for a `mermaid:rendered` event that nothing here fires. If
this book ever grows mermaid diagrams, copy those two files over and the zoomer
picks them up with no changes to it.

Also not copied: pushpopswap's `index.hbs`, `editable-extras.js`, and the rest
of its `custom.css` — all site-specific to that book.

### Re-syncing

The copy is byte-identical and should stay that way; fix bugs upstream in
pushpopswap and re-copy. To check for drift:

```sh
for f in svg-zoomer.js svg-zoomer.css zoom-unstick.js; do
  diff -u ../../pushpopswap/theme/"$f" theme/"$f"
done
```
