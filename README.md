# ASCII Text Poster

A generative poster canvas built out of a grid of text cells. Halftone dot
fields, drifting blocks of glyphs, letterforms that assemble and rot, typed
messages spelled out in cells, and a photo screened onto the grid as a proper
halftone — all in one self-contained HTML file with no build step.

Every composition has a seed, and the URL is a live description of what you are
looking at, so anything you make can be kept or handed to someone else.

## Running it

Open `index.html` in a browser. That's the whole install.

If you'd rather serve it:

```sh
npm start        # npx live-server . --port=8080
```

Serving is worth it for two reasons: the address bar updates cleanly via
`history.replaceState` (opening off `file://` falls back to assigning
`location.hash`, which leaves history entries behind), and the clipboard uses
the modern async API rather than the `execCommand` fallback. Both paths work
either way.

The only external request is the DotGothic16 webfont from Google Fonts, used
for the control panel. The canvas itself deliberately stays on a monospace
stack — the grid depends on uniform glyph metrics to stay aligned.

## Using it

**Drag on the canvas** to paint. **Drop an image anywhere** to screen it onto
the grid. The panel collapses with the tab on its right edge — it isn't an
overlay, it's a block of canvas cells that builds and clears with the same
diagonal sweep everything else uses.

### Canvas

| Control | | |
|---|---|---|
| **Preset** | Poster · Storm · Minimal · Aberration | Sets the seven sliders below at once. Drops back to Custom the moment you move one of them. |
| **Palette** | Pop · Noir · Blanc · Emerald | Every ink is chosen against its own ground, so each scheme stays legible rather than relying on fixed pairs. |
| **Size** | 16–56 | Target cell size in px. Reshapes the grid. |
| **Speed** | 0–200 | Scales the artwork clock. Transitions ignore it — they're UI, not motion. |
| **Density** | 0–20 | How many drifting groups stay alive. Lowering it lets the surplus die off naturally rather than culling mid-life. |
| **Scale** | 0.5×–2.6× | How large those groups are built. |
| **Chroma** | 0–100% | Position-dependent red/blue drift: left of centre gains red, right gains blue. |
| **Split** | 0–4 cells | True chromatic aberration — a cell takes its red from the left and its blue from the right, keeping its own green. Only edges fringe. |
| **Msg** | Ignore · Apply Split | Whether the split reaches the typed message. Ignoring it also stops neighbouring cells sampling the text, so it doesn't bleed a halo either. |
| **Wipe** | Random · Diagonal · Columns · Rows · Radial · Spiral · Noise | The front the Regenerate transition travels along. |

### Paint

| Control | | |
|---|---|---|
| **BG / Ink** | colour | Reset to a pair that reads *against* the current palette, so a painted cell is visible the moment you draw it. |
| **Brush** | Character · Arrow ↑→↓← | The arrows are drawn SVG rather than glyphs, so they stroke in the cell's ink. |
| **Char** | 1 character | Disabled while a shape brush is selected. |
| **Fill** | Solid · Dots · Hatch | A halftone cell is a texture, never a character slot — dots suppress the glyph. |
| **Decay** | 0–5s | How long anything you add survives before handing its cells back. `0` keeps it indefinitely. Governs brush strokes and messages alike, nothing else. |
| **Trail** | Auto · Darken · Lighten | A drag longer than ten cells cools behind the cursor. Auto sinks the tail toward the palette's own ground — white on Blanc, near-black on Emerald — so it recedes either way. With Decay off the whole stroke stays at full strength, since nothing would ever restore a faded tail. |

### Message

Type and press the arrow. **Inline** puts one letter per cell; **Large** builds
each letter from a 5×7 block of cells, which churn and then fall away so the
word is revealed by subtraction.

Text too wide for the grid wraps, and `//` breaks a line wherever you want one:

```
HELLO // WORLD
```

Wrapping happens at layout time rather than being stored, so the same text
re-flows when the grid is reshaped. Large letters are seven rows tall, so only
about three lines fit at the default cell size — smaller cells buy more.

### Buttons

**Pause** · **Regenerate** (rolls a new seed) · **Load Image** / **Clear Image**
· **Share Link** (copies the permalink).

Everything except Regenerate keeps the seed, so changing the palette or the cell
size shows you the *same composition* differently.

## Sharing

The hash carries the seed and every control, omitting defaults — so a bare share
is just `#seed=8412`:

```
#seed=7777&palette=emerald&size=34&chroma=55&split=2&fmt=large&msg=HI+//+THERE
```

It's read on every change, not only at load, so you can edit it in the address
bar — swap the message, bump the seed, change the palette — and the canvas
follows. A parameter you delete resets its control rather than lingering.

Parameters: `seed`, `palette`, `size`, `speed`, `density`, `scale`, `chroma`,
`split`, `decay`, `trail`, `wipe`, `msgsplit`, `fmt`, `msg`.

A dropped image is the one thing that can't ride along — it stays local to the
tab.

## How it works

**Two layers.** `base` is the still poster — recursive region splits, bars,
anything hand-painted — and never animates. `entities` are the drifting groups
composited over it each frame. Keeping them separate is what lets a group move
away and reveal what was underneath.

**Halftones are real `<pattern>` fills**, serialised to data-URIs so each cell
can carry its own dot colour and spacing while still tiling as one continuous
field. A dropped photo maps luminance to dot radius across seven tone steps —
which is exactly how a halftone renders tone out of a single ink — and is
re-screened at the new resolution whenever the grid reshapes, so the dots stay
one-per-cell at every size. On a dark palette the mapping inverts, or every
picture would come out as its own negative.

**One mask contract.** A shape is `(lx, ly, w, h) => bool`, and `FONT` is a 5×7
bitmap, so scaling one into the other is two divisions. That's why a letter
assembles row by row, softens at its rim and rots organically exactly as a
triangle does — none of that code knows the difference between them.

**The dissolve is noise plus a ramp.** Value noise gives every cell a coherent
resistance; a ramp along the direction of travel biases it. Groups materialise
from the leading edge and rot from the trailing one, in ragged patches rather
than a clean wipe.

**Transitions are one function.** Where a cell sits in the front's order of
arrival, normalised 0..1. Swap it and you swap the transition — the hold, the
ragged edge and the glyph sequence never cared which way the front was going.

**Crossings overprint.** Where two groups overlap the region takes a third
colour: multiply on a pale ground, screen on a dark one — the same rule the
photo screening uses, for the same reason. Ink darkens paper; light adds to
black.

**Seeded throughout.** Every scattered decision draws from a mulberry32 stream
reseeded at the top of each build, so a seed plus a grid size reproduces a
poster exactly. That is what makes a composition shareable: the URL carries the
number, not the picture.

**Rendering is a frame diff.** Cell state is held in parallel arrays and only
changed properties are written to the DOM, so cost tracks the cells that
actually moved rather than the grid size. The ticker runs at a fixed 18 fps
step — the grid is chunky, and stepping it mechanically suits the poster better
than smooth interpolation.

## Licence

Not yet specified.
