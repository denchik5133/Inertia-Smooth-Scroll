# Inertia Smooth Scroll

A lightweight, zero-dependency implementation of physics-based inertial scrolling for the web — no libraries, no frameworks, just vanilla JavaScript.

## What it does

Standard browser scrolling stops the moment you lift your finger from the trackpad or wheel. This implementation adds **momentum**: the page continues moving after input ends and gradually decelerates — the same feel you get on iOS and Android.

### Features

- **Inertial wheel scrolling** — scroll speed is preserved and decays smoothly via a friction coefficient
- **Velocity accumulation** — fast spinning builds more momentum than slow spinning
- **Custom scrollbar** — fully custom-built DOM scrollbar with smooth click-to-position animation and drag support, replacing the native one to avoid any browser conflicts
- **Middle-mouse auto-scroll** — the browser's native middle-click auto-scroll mode works without interference
- **Progress bar** — thin top bar shows current scroll position at a glance
- **Zero dependencies** — pure HTML, CSS, and JavaScript in a single file

## How it works

The engine runs a `requestAnimationFrame` loop that manages three values:

| Variable | Role |
|---|---|
| `targetY` | Where the page should eventually reach |
| `currentY` | Where the page actually is right now (lags behind `targetY`) |
| `velocity` | Inertia accumulated from wheel input |

Each frame:
1. `velocity` is multiplied by `friction` (0.92) — momentum decays
2. `velocity` is added to `targetY` — target keeps moving from inertia
3. `currentY` steps toward `targetY` by `smoothness` (0.08) — produces the easing
4. `window.scrollTo` is called with `currentY`

### Scrollbar

The native browser scrollbar is hidden via CSS (`scrollbar-width: none` + `::-webkit-scrollbar { display: none }`). A custom `<div>` scrollbar is rendered in its place with two interaction modes:

- **Click on track** → sets `targetY`, the animation loop eases to that position
- **Drag thumb** → `mousemove` calls `window.scrollTo` immediately (no frame delay) and syncs `currentY` and `targetY` directly — this is what prevents jitter

The animation loop is paused (`window.scrollTo` is skipped) during thumb drag and middle-mouse auto-scroll to avoid conflicting writes to the scroll position.

## Tuning

All physics constants are at the top of the `InertiaScroll` class:

```js
this.smoothness = 0.08;      // Easing speed toward target (lower = slower/smoother)
this.friction = 0.92;        // Inertia decay per frame (higher = longer coast)
this.wheelMultiplier = 0.4;  // Scroll distance per wheel tick
this.maxVelocity = 15;       // Maximum inertia speed (px/frame)
```

## Browser support

Works in all modern browsers that support `requestAnimationFrame` and `window.scrollTo` — Chrome, Firefox, Safari, Edge.

## License

MIT — use freely in personal and commercial projects.
