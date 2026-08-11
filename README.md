# CareCall: Ward Rush

An arcade nurse-call response game themed around a **CareCall** hospital nurse call system. Patients hit their call buttons, the annunciator lights up, and you sprint the ward to answer every call before the timer runs out.

Built with React 19, TypeScript, Vite and Tailwind CSS v4. The simulation and all art run on a single `<canvas>` — no game engine, no sprite assets.

---

## Gameplay

You are the nurse on shift. Calls appear on the **annunciator** panel and on the over-door lamps in the ward. Run to the room, stand at the call point until the reset ring fills, and the call clears. Clear every call in the shift, then return to the **nurses station** to finish.

**Three lives. Fifty shifts. A missed call costs a life.**

### Call types

| Colour | Call | Behaviour |
| --- | --- | --- |
| 🔴 Red | Emergency | Highest priority, shortest fuse, biggest score |
| 🟣 Purple | Keys | Collect the key ring from the nurses station first |
| 🟠 Amber | Nurse assist | A colleague needs a hand |
| 🟢 Green | Patient call | Standard bedside call |

### Complications

- **Rolling beds** career down the corridors. Getting hit stuns you, breaks your combo and makes you drop whatever you're carrying.
- **Residents** wander the halls and physically block you — push past and they'll let you know about it.
- **Meal calls** (🍽️) require a tray from the pantry, which slows your movement.
- **Toilet assists** (🚻) require escorting a resident to the WC at walking pace, against a countdown.
- **Accidents** (🧹) happen if an escort doesn't reach the WC in time. Fetch the mop from the nurses station and stand over the spill to clean it. Sprint through an uncleaned spill and you'll slip over. Spills left at the end of a shift reduce your bonus.
- **Lighting drops** on evening shifts and again on night shifts, reducing visibility to a torch radius.

Patients occupy every bed. They doze when settled, sit up and wave when they need you, and speak up — *"NURSE?"*, *"I'VE FALLEN!"*, *"I NEED THE LOO!"* — with a *"THANK YOU!"* once you answer.

### Scoring

Score scales with call priority, a speed bonus for time remaining, and a **combo multiplier** up to ×5 that resets on a missed call. Clearing a shift pays a completion bonus, a bonus per remaining life, and a perfect-shift bonus for zero misses. Every 10th shift grants an extra life. Top eight runs are saved to a local high-score table.

## Controls

| Action | Keyboard | Touch |
| --- | --- | --- |
| Move | `WASD` / arrow keys | Drag anywhere on the left of the screen (floating stick) |
| Dash | `Space` / `Shift` | `DASH` button, or tap the right of the screen |
| Pause | `Esc` / `P` | Pause button in the HUD |
| Mute | `M` | Speaker button in the HUD |
| Confirm / restart | `Enter` | On-screen buttons |

## Running locally

```bash
npm install
npm run dev      # dev server
npm run build    # production build -> dist/
npm run preview  # preview the production build
```

## Deploying

`vite-plugin-singlefile` inlines all JavaScript and CSS into a **single self-contained `dist/index.html`**. There are no hashed asset URLs, so there is no `base` path to configure — the file works from any subdirectory, a file:// URL, or a USB stick.

### GitHub Pages

A workflow is included at `.github/workflows/deploy.yml`. To enable it:

1. Push the repository to GitHub.
2. Go to **Settings → Pages** and set **Source** to **GitHub Actions**.
3. Push to `main`. The site publishes to `https://<user>.github.io/<repo>/`.

### Anywhere else

Run `npm run build` and drop `dist/index.html` onto Netlify, Vercel, Cloudflare Pages, S3, or any static host.

## Branding

The Miracle Electronics logo is loaded from `public/brand/` if present, so you can use the official artwork verbatim rather than the built-in vector approximation. Drop **either** of these in and it will be picked up automatically:

```
public/brand/miracle-electronics.svg   (preferred — scales cleanly)
public/brand/miracle-electronics.png   (use a transparent-background export)
```

If neither file exists, `src/components/BrandLogo.tsx` falls back to a hand-built SVG of the circuit-triangle mark. Note that the official wordmark is navy `#0A2E4E`, which disappears against the dark game UI — supply a **reversed (white text) export** for best results.

## Project structure

```
src/
  game/
    constants.ts   call types, tuning values, theme palette
    map.ts         ward geometry, collision solids, roam areas
    levels.ts      procedural difficulty curve for 50 shifts
    engine.ts      simulation: player, calls, beds, residents, scoring
    render.ts      canvas rendering (pre-rendered ward + dynamic layer)
    audio.ts       WebAudio synthesised sound effects
  components/
    GameCanvas.tsx game loop, input handling, follow camera
    Annunciator.tsx call panel (desktop + compact mobile variants)
    Hud.tsx        score, lives, combo, dash, shift progress
    Screens.tsx    start, pause, level complete, game over
  hooks/
    useHighScores.ts  localStorage high scores + level progress
```

### Performance notes

- The static ward (floors, walls, beds, furniture, signage) is rendered **once** to an offscreen canvas and blitted each frame; only entities, lighting and particles are redrawn.
- Radial-gradient glows are cached by colour and radius rather than rebuilt per frame.
- Particles are hard-capped, and HUD React state updates are throttled to ~16 Hz so the render loop is not tied to React re-renders.
- Device pixel ratio is clamped to 2 and the fixed timestep is clamped to 30 ms to avoid tunnelling after tab switches.

## Licence

No licence file is included yet — add one (MIT is the usual default for a project like this) before making the repository public, otherwise the default is "all rights reserved".

> **Note:** this is an unaffiliated fan/demo project. If "Miracle Electronics" or "CareCall" are real trademarks belonging to someone else, get permission or rename before publishing it publicly.
