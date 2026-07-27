# ASCII Text Poster

A generative poster canvas built out of a grid of text cells. Halftone dot
fields, drifting blocks of glyphs, letterforms that assemble and rot, typed
messages spelled out in cells, and a photo — or a clip, or your webcam —
screened onto the grid as a proper halftone. All in one self-contained HTML
file with no build step.

Every composition has a seed, and the URL is a live description of what you are
looking at, so anything you make can be kept or handed to someone else.

## Running it

Open `index.html` in a browser. That's the whole install.

If you'd rather serve it:

```sh
npm start        # npx live-server . --port=8080
```

Serving is worth it for three reasons. The address bar updates cleanly via
`history.replaceState` (opening off `file://` falls back to assigning
`location.hash`, which leaves history entries behind); the clipboard uses the
modern async API rather than the `execCommand` fallback; and **the camera needs
a secure context**, which `localhost` counts as but a page opened straight off
disk does not. The first two work either way — the camera is the one that
genuinely requires it, and will tell you so on the button if refused.

The only external request is the DotGothic16 webfont from Google Fonts, used
for the control panel. The canvas itself deliberately stays on a monospace
stack — the grid depends on uniform glyph metrics to stay aligned.

## Using it

**Drag on the canvas** to paint. **Drop an image or a video anywhere** to screen
it onto the grid, or hit **Camera**. The panel collapses with the tab on its
right edge — it isn't an overlay, it's a block of canvas cells that builds and
clears with the same diagonal sweep everything else uses.

### Canvas

| Control | | |
|---|---|---|
| **Preset** | Poster · Storm · Minimal · Aberration | Sets the seven sliders below at once. Drops back to Custom the moment you move one of them. |
| **Palette** | Pop · Noir · Blanc · Emerald · Ember · Gold · Cobalt | Every ink is chosen against its own ground, so each scheme stays legible rather than relying on fixed pairs. Four dark schemes (Noir grey, Emerald neon green, Ember neon orange, Cobalt blueprint blue) and three light ones (Pop colour, Blanc grey, Gold warm single-ink). |
| **Glow** | Auto · On · Off | Blooms each glyph in its own ink. **Auto** follows the palette, and only Emerald and Ember ask for it — saturated ink on a near-black ground is what neon *is*, whereas the same treatment on the grey schemes reads as a printing fault. The radius tracks the cell size. |
| **Size** | 16–56 | Target cell size in px. Reshapes the grid. |
| **Speed** | 0–200 | Scales the artwork clock. Transitions ignore it — they're UI, not motion. |
| **Density** | 0–20 | How many drifting groups stay alive. Lowering it lets the surplus die off naturally rather than culling mid-life. |
| **Scale** | 0.5×–2.6× | How large those groups are built. |
| **Ground** | 0–100% | How much of the still layer is present at all. At `0` it dissolves away and the canvas is left bare, so turning every slider down really does empty the screen — every layer can now be absent, which was not previously true of the ground. It recedes in patches through the same coherent noise as everything else, not as a uniform fade. Entities keep drifting over the bare canvas, and anything you painted stays. |
| **Renew** | 0–100% | How fast the ground rewrites itself. One field region at a time re-rolls its scheme and dissolves into it through coherent noise, so it arrives in patches rather than as a rectangle switching over. Bars and anything hand-painted are left alone — a renewal renews the ground, not what was put on it. About 70% of the ground differs after a minute at the default. |
| **Scroll** | 0–100% | Marches the whole still layer sideways, up to 9 cells a second, wrapping round rather than running out. Whole cells only — a half-cell shift has nowhere to land on a grid. Entities and the message don't scroll; they're overlays, so the effect reads as the ground travelling underneath them. |
| **Drift** | 0–100% | Scrolls the halftone patterns. The fields belong to the still layer, so this is the only motion available to them — the cells keep their colours and their edges, only the dots travel. A screened photo is exempt: drifting its dots would slide the tone off the thing it describes. |
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
| **Decay** | 0–5s | How long a brush stroke survives before handing its cells back. `0` keeps it indefinitely. Brush strokes only — the message keeps its own clock, below. |
| **Trail** | Auto · Darken · Lighten | A drag longer than ten cells cools behind the cursor. Auto sinks the tail toward the palette's own ground — white on Blanc, near-black on Emerald — so it recedes either way. With Decay off the whole stroke stays at full strength, since nothing would ever restore a faded tail. |

### Message

**Loop** (`off`, 2s, 4s, 6s, 8s) is how long a finished message holds before it
frays away and plays again. `off` leaves it up for good. It's separate from
Decay, so text and brush strokes no longer share one clock — a permanent message
over a fading drawing, or the reverse, are both possible now.

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

### Source

| Control | | |
|---|---|---|
| **Screen** | Dots · Edges | How a loaded image, clip or camera is drawn. **Dots** maps tone to dot radius across seven levels. **Edges** runs a Sobel over the same luminance and draws the result with the four rule glyphs — `— \ | /` — picking the one lying *along* the edge, so a picture comes out as line art tracing its own contours. |
| **Motion** | Ignore · Drives Spawns | With a live source, difference successive frames and place new groups where the change is. It couples the picture to the animation through *movement* rather than brightness, so the canvas reacts to you rather than merely displaying you. |

This whole section stays hidden until there is a source to apply it to, and
**Motion** appears a step later still: it has nothing to difference against on a
still image, so it waits for one that moves — a clip or the camera. Both keep
their values while hidden, so a shared link carrying `screen=edges` takes effect
the moment you load something.

The edge threshold is adaptive, taken from each frame's own mean gradient
rather than fixed: a dim room and a contrasty one want very different cuts, and
a fixed one would need riding every time the light changed.

### Buttons

**Pause** · **Regenerate** (rolls a new seed) · **Load Media** / **Clear
Source** · **Camera** / **Stop Camera** · **Share Link** (copies the permalink).

Everything except Regenerate keeps the seed, so changing the palette or the cell
size shows you the *same composition* differently.

### Live sources

A still is decoded once and re-screened whenever the grid changes. A clip or the
camera is the same path re-read on every step — that is all "live" means here.
It costs about **0.035 ms per step** against a 55.6 ms budget, because the
sampling canvas is the size of the grid (a few dozen pixels), not the size of
the frame.

Pause freezes the feed along with everything else, which is what makes it
possible to hold a frame you like and then paint on it. The camera view is
mirrored, the way a selfie camera is; a dropped clip is not. Stopping the camera
releases its tracks — the hardware light goes out.

If the camera is refused, the button says why: `Camera Denied`, `No Camera`,
`No Camera API` (not a secure context — see above), or `Camera Failed`.

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
`scroll`, `drift`, `ground`, `renew`, `split`, `decay`, `trail`, `wipe`, `msgsplit`, `loop`, `glow`, `screen`, `motion`,
`fmt`, `msg`.

An image, a clip or the camera is the one thing that can't ride along — a source
stays local to the tab.

## How it works

**Palettes fill identical slots**, so the composition logic never changes —
only the values do. Each also names the grounds a drifting group may take:
roughly a third of coloured blocks land on a vivid slot, so the bright end of a
scheme isn't confined to dot colours and bars. Repeats in that list weight the
pick, and it costs the same single draw whatever its length, so adding to it
changes what a seed looks like without changing what it lays out. What keeps a scheme legible is that ink is picked *against a
cell's own ground* rather than paired up in advance: `inkOnDark` and
`inkOnLight` for entities, `inkFor()` for bars. Every reachable ink-and-ground
combination across all seven palettes clears 1.6:1.

**Two layers.** `base` is the still poster — recursive region splits, bars,
anything hand-painted — and never animates. `entities` are the drifting groups
composited over it each frame. Keeping them separate is what lets a group move
away and reveal what was underneath.

**Halftones are real `<pattern>` fills**, serialised to data-URIs so each cell
can carry its own dot colour and spacing while still tiling as one continuous
field. A source maps luminance to dot radius across seven tone steps — which is
exactly how a halftone renders tone out of a single ink — and is re-screened at
the new resolution whenever the grid reshapes, so the dots stay one-per-cell at
every size. On a dark palette the mapping inverts, or every picture would come
out as its own negative.

**The glow is one class on the container**, not a property written to every
cell, so switching it costs a single mutation rather than thousands. It draws in
`currentColor`, which is what lets one rule serve every palette — a glyph blooms
in whatever ink it already had — and only cells actually holding a glyph pay for
it.

**Every layer can be absent.** Entities rot, messages decay, brush strokes hand
their cells back — but the ground was originally laid once and kept for the life
of a seed, so turning everything down still left a full canvas. `Ground` closes
that: it is a mask over the still layer, eased toward rather than jumped to, and
thresholded against a noise field held with the grid so it costs one array read
per cell rather than an fbm sample.

**Renewal runs on a stream of its own.** It draws on the same helpers the
composition does — `fieldScheme`, `pick`, `irand` — but swapping the generator
out for the duration and putting it back keeps it off the same *sequence*.
Sharing one would mean the Renew slider decided which entities spawned, and that
every seed rendered differently than it did before renewal existed. Two
deterministic sequences: one for the poster, one for its upkeep.

**Scrolling is a read offset, not a move.** The base keeps its own
coordinates, so painted cells, field regions and renewals all stay exactly where
they were put; only the mapping from a screen column to a base column changes.
Clicks are mapped the other way, so a brush mark lands under the cursor and then
travels with the canvas. Because the shift is a whole number of cells, adding
`scrollCells × cellW` to the same custom property the drift uses carries each
halftone pattern along with the block it belongs to, rather than leaving the dots
behind on screen.

**The fields scroll from one property.** Each patterned cell already carries a
`background-position` offset, which is what makes neighbouring cells tile as one
continuous field rather than as a grid of separate squares. Holding that offset
as a `calc()` against a shared custom property means the whole canvas of
halftones moves from a single write on the container — measured at 0.001 ms for
720 scrolling cells, against one write per cell per frame otherwise. The wrap is
2520px, which every tile size in use divides, so the reset lands on a tile
boundary and is invisible.

**Edges reuse the glyphs that were already there.** `SEQUENCES[0]` is
`['|', '/', '—', '\\']` — four directions, long since part of the vocabulary.
A Sobel gives a gradient per cell; turning it a quarter turn gives the edge's
own direction, which folds onto a half-circle (the glyphs read the same both
ways) and snaps to the nearest of the four. The luminance buffer is kept at full
precision rather than pre-quantised for exactly this: a gradient operator run
over seven levels is mostly measuring the quantiser.

**Motion is frame differencing at grid resolution.** Whatever changed since the
last step, thresholded against that frame's own peak so a dim room and a bright
one both give a usable set. Nothing is hot until a second frame has arrived —
differencing a picture against an empty buffer would report its own brightness
as movement.

**A camera is just an `<img>` that moves.** Canvas draws an image, a video and
another canvas identically, so a live source needed no new rendering path at
all — only re-reading the same one on each step. The two things that make it
cheap enough to do 18 times a second are that the sampling canvas is held rather
than allocated per call, and that the seven dot patterns are rebuilt only when
the ink changes, which means once per palette rather than once per frame.

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
