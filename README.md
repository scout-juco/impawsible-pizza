# Impawsible Pizza

A one-button rhythm game. You're a cat climbing out of a pizza oven, up a chimney lined with pizza slices, and onto the roof.

The cat climbs on its own, clinging to one wall of the chimney. Tap to leap across to the other wall. Hold to keep bouncing back and forth: one leap across the chimney takes exactly one beat of the music. Slices and spinning pizzas are deadly from every side. Bumping into the underside of a ledge (outlined in red) is deadly too.

- **Leap:** Space / ↑ / W / click / tap. Hold to keep bouncing.
- **Pause:** Esc or P. **Mute:** M. **Music volume:** the slider on the title and pause screens, or `-` / `=` at any time.
- **Practice mode:** F drops a checkpoint flag while you're on a wall, X removes the last one (on-screen buttons on touch devices).
- **Cat skins:** ← / → on the title screen.

The game is one self-contained file, [`public/index.html`](public/index.html). It has no dependencies, build step, image files or external requests. The watercolour look is painted in code when the game loads: each sprite is drawn, then given wobbly edges, pigment pooling, colour bleed and granulation, and the whole scene sits under a paper-grain texture. The music and sound effects are synthesized with the Web Audio API.

## Run locally

```bash
python3 -m http.server 8787 --directory public
```

Then open http://localhost:8787. Or use `npx wrangler dev` to run it the way Cloudflare will.

## Deploy to Cloudflare

[`wrangler.jsonc`](wrangler.jsonc) serves `public/` as a Workers static-assets site:

```bash
npx wrangler deploy
```

## Embed it in another site

```html
<iframe
  src="https://impawsible-pizza.<your-subdomain>.workers.dev/"
  title="Impawsible Pizza"
  width="960" height="540"
  style="border:0; max-width:100%; aspect-ratio:16/9; height:auto"
  allow="autoplay; fullscreen">
</iframe>
```

The game fits whatever box it's given, down to a 16:9 frame on a phone. It pauses when the frame loses focus and never scrolls the host page. Best score, attempt count, skin, volume and mute settings are saved in `localStorage` when the browser allows it.

## Editing the level

The level is the `LEVEL` array near the top of the script. Rows are listed in climbing order, so the first row is the bottom of the chimney. Each row is exactly 10 characters wide:

| char | meaning |
| --- | --- |
| `#` | chimney brick (solid: cling to its sides, don't bump its underside) |
| `B` | pizza box (solid, same rules) |
| `>` / `<` | slice stuck to the wall on its left / right |
| `o` | spinning pizza |
| `m` | pizza swinging along an oven rack (it sits at its column when the cat reaches its row) |
| `[n, row]` | repeat a row n times |
| `{ hue, name }` | start a new colour section |

The cat climbs 8 rows a second, and crossing the standard 6-wide shaft takes one beat (4 rows). The physics run at a fixed 240 Hz and are fully deterministic, so a level edit can be checked for beatability by simulating every possible input.
