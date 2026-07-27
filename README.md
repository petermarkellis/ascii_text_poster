# ASCII Text Poster

A generative poster canvas built out of a grid of text cells. Halftone dot
fields, drifting blocks of glyphs, letterforms that assemble and rot, typed
messages spelled out in cells, and a photo — or a clip, or your webcam —
screened onto the grid as a proper halftone. All in one self-contained HTML
file with no build step.

Every composition has a seed, and the URL is a live description of what you are
looking at, so anything you make can be kept or handed to someone else.

**[See it running →](https://petermarkellis.github.io/ascii_text_poster/)**

![A message set in large letters — each one built from a block of cells that churn and fall away — over a green halftone grid of drifting glyph blocks, dot fields and rule marks.](screenshots/screenshot_1.jpg)

*The canvas with the panel collapsed. Every layer here is the same grid of text
cells: the dot fields, the blocks of drifting glyphs, and the letterforms, which
are revealed by subtraction as the cells around them rot away.*

![The same canvas with the control panel open down the left edge, showing sliders for size, speed, density, scale, scroll, drift, ground, renew, chroma and split.](screenshots/screenshot_2.jpg)

*The panel open. It isn't an overlay — it's a block of canvas cells that builds
and clears with the same diagonal sweep as everything else, which is why the
composition stops rather than slides underneath it.*

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

Every control carries a `?` marker. Hover it, tap it, or reach it with the
keyboard and a note explains what that control does — the same descriptions
tabulated below, at the point of use. The note appears off the panel's right
edge rather than inside it, so it can be wider than the panel and doesn't cover
the control it is describing. Escape closes it, as does moving away or touching
anything else.

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
| **Brush** | Character · Arrow ↑→↓← · Ghost | The arrows and the ghost are drawn SVG rather than glyphs, so they stroke in the cell's ink. |
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

The canvas opens on `WELL HEY THERE` in **Large**, revealed with the animation
rather than dropped in settled. Since defaults are omitted from the hash, that
is what a bare `#seed=…` link now shows — clearing the field gives an explicit
empty `msg=`, which round-trips back to a blank canvas.

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
is just `#seed=8412`. Note the consequence: changing a default changes how every
link that omitted it renders.

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

**Groups can arrive as texture.** About a quarter of them carry a dot screen or
a hatch instead of glyphs, drawing on the same two fills the ground and the bars
already use, so the drifting layer shares the poster's vocabulary rather than
being glyphs and nothing else. A dotted group drops its glyphs by the same house
rule the fields obey and drifts as a block of pure tone; a hatched one keeps
them, exactly as a hatched bar does. Shapes and letters are left out on purpose —
both are read by a silhouette assembling and rotting out of churning glyphs, and
screening one over is how you lose what makes it legible as a letter at all.

Two details fall out of the design rather than being chosen. The texture's
offset is anchored to the screen grid, because that is what makes neighbouring
cells tile as one field and what lets drift move all of them from a single
write — so a group that travels reads as a window opening onto a fixed screen.
Locking the texture to the group would cost a write per filled cell per frame,
which is the thing that design exists to avoid. And where two groups cross, the
texture is dropped: it was coloured against its own group's ground, which the
overprint has just replaced, so a crossing reads as the flat third colour it
already is rather than carrying a screen in an ink picked for a ground that is
no longer there.

The roll comes off the seed each group has already drawn rather than from a
fresh call to the generator, for the same reason the ground's tone does: a new
draw would shift the stream for every group spawned after it, and every existing
seed would play a different set.

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

**A drawn glyph is just another token.** A cell's contents are a string, and a
handful of those strings mean "draw this" instead of "type this" — the eight
arrow frames and a ghost. Both are stroked in `currentColor` and carry no width
or height, so one markup string serves every palette and the cell's own CSS
sizes it. The ghost is one in thirty-three of the main glyph set: it reads as a
face, and a face repeated across a block stops being a mark and becomes
wallpaper, so it is weighted to turn up as a find rather than as a texture.
Lengthening that list costs the same single draw, so it changes what a seed
looks like without changing what it lays out.

What separates the two is that an arrow can be *spun* — it is one path rotated
about an origin, so a cell already holding one takes the next frame as a single
attribute write rather than a reparse. A ghost has three paths and no angle, so
it is only ever written whole. The angle map is therefore the "can this be
spun?" test rather than the "is this drawn?" test, and the cell remembers which
token it is showing rather than merely that it is showing one — otherwise a
ghost handed an arrow would rotate the wrong path.

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

## Publishing

The page carries its own metadata — description, Open Graph, X card, and a
`WebApplication` block of JSON-LD whose `sameAs` ties the page to its author
rather than treating the profile as an unrelated outbound link. One description
serves all three: they are read by different machines but by the same person,
and a link that describes itself one way in a tweet and another in a search
result reads as two different pages.

`og:url`, `canonical` and `og:image` are absolute, pointing at the GitHub Pages
address below. Scrapers resolve a relative `og:image` these days but the Open
Graph spec asks for an absolute one, and it costs nothing to give it. The card
is `OG_Social.jpg` at 1200×630, which is why that file ships with the site
rather than being ignored alongside the local screenshots.

**Changing where the site lives means changing four strings**: `og:url`,
`canonical`, `og:image` and `twitter:image`, plus `url` and `image` in the
JSON-LD.

The favicon is an inline SVG data-URI rather than a file, so the page stays one
thing with nothing to build and nothing to serve beside it. It is the app's own
halftone — teal dots ramping larger across the canvas ground, which is the idea
the whole thing is built on. Safari before 16.4 ignores SVG favicons and shows
none rather than a broken one; a `.png` alongside is the fix if that matters,
at the cost of the single-file property.

### Hosting

The site is the repository — one HTML file, one image, no build step — so
GitHub Pages serves it directly from `main`:

**<https://petermarkellis.github.io/ascii_text_poster/>**

`.nojekyll` is there to stop Pages running the tree through Jekyll on the way
out. Nothing here is named in a way Jekyll would eat today, but the file costs
nothing and removes a class of surprise.

Serving it this way also gets the two things `file://` can't do: a secure
context, which the **camera** requires, and a clean `history.replaceState` for
the address bar.

## Credits

By [Peter Ellis](https://x.com/pellisdesign).

## Licence

Not yet specified.
