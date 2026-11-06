# REBUILD COMMAND: Halo Carousel | Dark loader → tilted 3D image ring → painted-sky panorama with an infinite image carousel (DARK theme, 90% black)

Build ONE file, `index.html`, that reproduces the page described below exactly. Every number here is a requirement, not a suggestion. When this text and Appendix A disagree, Appendix A wins: it is the verified source and you may use it verbatim.

**Asset path: Path A (hosted).** All images load from `https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel`. Do not download, re-host, rename or replace them. Each carousel card shows one hosted photo (no videos, no text on them) and falls back to a gray shape when its `IMAGES` entry is empty. Photos are swapped through the `IMAGES` array (section 12).

Scope: only the home stage, meaning the entrance animation plus the infinite carousel hero. The header has NO Build / Brand / Learn nav and NO Human / Agent switch: only the "Halo Carousel." wordmark.

---

## 0. Global shell

- A single `index.html` with `<html lang="en">`, inline `<style>` and one inline `<script>` at the end of `<body>`. No build step, no framework, no libraries (no GSAP, no Three.js, no Framer Motion). All motion is hand-written: a cubic-bezier tween helper, a spring helper and one `requestAnimationFrame` loop.
- Fonts come from Google Fonts in ONE request: `family=Geist+Mono:wght@400;500&family=Geist:wght@400;500&family=Instrument+Serif&display=swap`.
  - `--font-serif: "Instrument Serif", Georgia, "Times New Roman", serif` (weight 400 only)
  - `--font-mono: "Geist Mono", ui-monospace, SFMono-Regular, Menlo, monospace`
  - `--font-sans: "Geist", ui-sans-serif, system-ui, sans-serif`
- Reset: `*,*::before,*::after{box-sizing:border-box;margin:0;padding:0;border:0 solid}`, `a{color:inherit;text-decoration:inherit}`, buttons get a transparent background and inherit their font and color.
- `html` gets `color-scheme:dark`. `html` and `body` both get `background:#1a1a1a; overflow:hidden; overscroll-behavior:none`. The page never scrolls.
- Film grain: `body::before`, `position:fixed; inset:-6%; background-image:var(--grain); opacity:.13; animation: grain .9s steps(5,end) infinite`. Keyframes: 0% translate(0) · 20% (-3%,2%) · 40% (2%,-3%) · 60% (-1%,-2%) · 80% (3%,1%) · 100% translate(0). At `(max-width:600px),(pointer:coarse)`: opacity .07, no animation. `--grain` is the SVG `feTurbulence` data URI given verbatim in Appendix A (240×240 tile, `baseFrequency='.85' numOctaves='3'`, alpha matrix `0 0 0 1.7 -.6`).

## 1. Tokens

| Token | Value |
|---|---|
| paper (page background = black at 90%, i.e. rgba(0,0,0,.9) over white) | `#1a1a1a` |
| paper-deep (frame background) | `#232323` |
| ink (all primary text, light on dark) | `#f3f3f1` |
| ink-soft | `#a3a3a5` |
| ink-muted | `#8e8e93` |
| surface (hover preview card) | `#2a2a2c` |
| night (AI preview card) | `#101012` |
| tech / PORTFOLIO | `#2F55FF`, text on it `#FFFFFF` |
| studio / AI SITES | `#EF3B2D`, text on it `#FFFFFF` |
| learn / TEMPLATES | `#FFC81A`, text on it `#111111` |
| ease-out (used everywhere unless stated) | `cubic-bezier(.22,1,.36,1)` |
| placeholder tones (cycled per card) | `#cfcfca`, `#c6c6c1`, `#d7d7d2`, `#bfbfba` |

## 1.5 Absolute coordinate table (reference canvas 1440 × 900)

Measured in the verified build. x / y / w / h are in px from the viewport's top-left.

| Element | x | y | w | h | z-index | Notes |
|---|---|---|---|---|---|---|
| `.site-header` | 0 | 0 | 1440 | 68 | 6 | padding `26px 40px 0`, flex, space-between |
| `.site-wordmark` "Halo Carousel." | 40 | 26 | 188 | 52 | – | 42px serif, the only header item; `.wordmark-first` "Halo" = 40,26,61,40 |
| `.stage` | 0 | 68 | 1440 | 832 | – | `height: calc(100svh - 68px)`, padding-top 4px, flex column |
| `.worldFrame` | 16 | 92 | 1408 | 631 | 1 (101 while entering) | margin `20px 16px 0`, `clip-path: inset(0 round 28px)` |
| `.wall` | 16 | 92 | 1408 | 631 | 2 (101 while entering) | fills the frame |
| `.below` | 0 | 723 | 1440 | 177 | 4 | 12-col grid, gap 24px, padding `clamp(18px,3svh,30px) 48px clamp(22px,4svh,38px)` |
| `h1.sentence` | 48 | 750 | 1002 | 114 | – | font-size `clamp(32px,3.6vw,52px)` = 51.84px |
| `dl.meta` | 1047 | 801 | 345 | 71 | – | numbers 48.96px serif, labels 11px mono |
| `.galleryLoader` (intro only) | 0 | 0 | 1440 | 900 | 100 | `position:fixed` inside the frame |
| `.loaderBrand` "Halo." (intro only) | 58 | 29 | 212 | 123 | 102 | `top:3.2vh; left:4vw`, `font-size: clamp(64px,9vw,150px)` = 129.6px |
| `.loaderCount` (intro only) | 1043 | 674 | 325 | 190 | – | bottom-right; `.loaderStatus` is at `bottom:4vh; right:5vw` |
| `.skipBtn` "↗" (intro only) | 58 | 811 | 44 | 44 | – | `bottom:5vh; left:4vw`, circle |

## 2. Brand (PIXEL CONTRACT)

- Name: **Halo Carousel**. `<title>Halo Carousel | Builder, creator, AI tinkerer</title>`.
- Header wordmark (text only, like the reference layout): `<span class="wordmark-first">Halo</span><span class="wordmark-last"> Carousel</span>.` in Instrument Serif 42px, `letter-spacing:-.03em`, `line-height:.95`. The link carries `aria-label="Halo Carousel, home"`.
- Hover / focus swap: the name slides up (`translateY(-105%)`, opacity 0) and a second line, **"Shipping playful ideas with AI."**, rises from `translateY(145%)` to 0 (transform .6s ease-out, opacity .4s).
- Favicon icon (new and minimal: a halo ring over a dot), used as an inline data-URI SVG and NOT shown in the header:
  ```svg
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32"><ellipse cx="16" cy="10" rx="11" ry="4.5" fill="none" stroke="#111" stroke-width="2.5"/><circle cx="16" cy="22" r="6" fill="#111"/></svg>
  ```
- Loader brand: **"Halo."** (5 glyphs, the last one is the period).
- The region label is `aria-label="Halo’s gallery"`.
- DO NOT draw the icon inside the header (the header is a text-only wordmark), redraw or restyle the icon, add gradients or outlines, or use any other name anywhere.

## 3. Page architecture and stacking (exact DOM order)

```
header.site-header#siteHeader            z 6, starts opacity 0 / visibility hidden
main
 section.stage#stage                     (data-focus="tech|studio|learn" while a world is hovered)
  div.worldFrame#worldFrame[data-open][data-entering]   isolation:isolate; clip-path
   div.worldView                         sky (depth 10) + petalsBack (depth 22)
   div.wallWrap                          position:absolute; inset:0
    p.loaderBrand#loaderBrand            fixed, z 102
    div.galleryLoader#galleryLoader    fixed, inset 0, z 100, paper background
    div.wall#wall[data-entering]         z 2 (101 while entering)
     div.wallLens#wallLens               absolute, inset:-100vh 0, carries `perspective`
      div.wallCenter#wallCenter          0×0 at 50%/50%, preserve-3d, the ring
       a.card × 16
   div.worldView.worldFront              z 3: clouds (depth 34), petalsFront (depth 48), motes (depth 40)
  div.below#below                        z 4
div.previewAnchor#previewAnchor          fixed, z 40
div.cursor-label#cursorLabel             fixed, z 1001
```

- The loader and the brand are **descendants of the clipped world frame**. While `[data-open]` is set, the frame clip-path is widened to cover the whole viewport: `inset(calc(var(--ft)*-1) calc(var(--fr)*-1) calc(var(--fb)*-1) calc(var(--fl)*-1) round 0px)`. `--ft/--fl/--fr/--fb` are written in JS from the frame's `getBoundingClientRect()` (top+scrollY, left, innerWidth−right, innerHeight−bottom−scrollY), updated through a `ResizeObserver` and on `resize`.
- `.worldView` is a full-viewport layer pinned to the viewport: `position:absolute; top:calc(var(--ft)*-1); left:calc(var(--fl)*-1); width:100vw; height:100svh; transform-origin:50% 45%`. While `[data-open]` it is `scale(1.07)`, otherwise `scale(1)`, with `transition: transform 1.4s cubic-bezier(.5,0,.1,1)`.
- DO NOT move the loader outside the frame, use `overflow:hidden` instead of clip-path, or put the sky inside the ring.

## 4. Loader (PIXEL CONTRACT)

- `.galleryLoader`: `position:fixed; inset:0; z-index:100; background:#1a1a1a`. On exit it gets `opacity:0` over `.9s ease-out` and then `display:none` (at +0.9s).
- **"Halo."**: each glyph sits in a `.loaderMask` (inline-block, overflow hidden, `vertical-align:bottom`). The inner span starts at `translateY(105%) rotate(8deg)` with `transform-origin: 0 100%` and animates to none over **1s ease-out, delay 0.1 + 0.08·i s** (i = 0…4). Font: Instrument Serif, `letter-spacing:-.03em`, `line-height:.95`, `display:flex`, `transform-origin:0 0`.
- **Caption** "LOADING GALLERY": Geist Mono 12px, `letter-spacing:.14em`, uppercase, color `#a3a3a5`, `white-space:pre`, margin `0 0 4px`. Scramble: starts 350 ms after load. With `k = floor(elapsed/45ms)`, characters with index < k (and spaces) are final, the next 6 are random picks from `ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789#%&*+=`, the rest are empty. It stops once k ≥ 15. On exit it fades in .3s.
- **Counter**: Geist Mono `font-size: clamp(80px,12vw,180px)`, `letter-spacing:-.09em`, `line-height:1.1`, tabular-nums, flex with `align-items:flex-end`. It is a 3-digit odometer: each digit is a `1.1em`-tall `overflow:hidden` window (margin-right −.09em except the last) over a column of 0–9. The column moves `translateY(-10·d %)` with `transition: transform .7s ease-out`. The counter wrapper rises from `translateY(100%)` to 0 (.9s ease-out, delay .2s) and exits to `translateY(-100%)` over `.5s cubic-bezier(.6,0,.8,.4)`. The "%" sign is 0.22em, `margin:0 0 .75em 22px`, `letter-spacing:0`.
- **Progress**: 19 front items settle one every 85 ms. Derive the count from elapsed time, `ready = min(19, floor(elapsed/85))`, so throttled timers catch up. Each change tweens the displayed value to `ready/19·100` over .65s ease-out. Loading is finished at ≈1.6s.
- **Skip button**: `↗`, 44×44 circle, `border:1px solid color-mix(in srgb,#f3f3f1 13%,transparent)`, font 18px, at `bottom:5vh; left:4vw`. Click, **Escape** or **Tab** triggers the instant end state (section 10).
- DO NOT show a percentage bar, a spinner or any fade-in on the whole loader, and do not center the counter.

## 5. Ring geometry (PIXEL CONTRACT, use these formulas exactly)

Two progress values drive everything: `formation` (0→1) and `expansion` f (0→1).

```
wallScale = CSS --wall-scale (.62 default; see section 13)
offA  = 0.44·innerHeight − (wallRect.top + wallRect.height/2)
sizeO = min(1.05, (innerWidth−32)/1240, 0.66·innerHeight/800) / wallScale
A     = wallScale · (sizeO + (1−sizeO)·f)

tilt        = −22·(1−f)            (deg)
roll        = −20·(1−f)            (deg)
radius      = 460 + 1340·f
perspective = 1000 + (450−1000)·f
scale       = 0.52 + (2.3−0.52)·f²
anchor      = −radius·sin(tilt) + 460·sin(−22°)·(1−f)
```

Written every frame:
- `wallCenter` custom properties: `--intro-offset: offA·(1−f) + anchor·A px`, `--intro-tilt`, `--intro-roll`, `--intro-x: 160·sin(roll)·A px`, `--intro-sz: A`, `--intro-scale: sizeO+(1−sizeO)·f`.
- `wallCenter` transform (CSS): `translate(var(--intro-x), var(--intro-offset)) rotateZ(var(--intro-roll)) rotateX(var(--intro-tilt)) scale3d(var(--s), var(--s), var(--intro-sz))` with `--s: calc(var(--wall-scale)*var(--intro-scale))`.
- `wallLens.style.perspective = perspective·A px` and `perspectiveOrigin = 50% calc(50% + offA·(1−f) px)`.

Slots (16) and recycling (`SLOTS = min(16, SLIDES.length)`, rotation `C` in degrees):
```
slotAngle(i, C) = ((((360·i/16 + C + 180) % 360) + 360) % 360) − 180
slotIndex(i, C, len) = (((i − floor((360·i/16 + C + 180)/360)·16 + min(3,len−1)) % len) + len) % len
```
Use `slotIndex(i, entered ? C : 0, len)` so recycling only starts after the entrance. When a slot's image index changes, refill that card (world, tone, image, title).

Per card placement (angle r, formation h):
```
n = ((r+180)%360+360)%360,  a = n/360
i = clamp((h − 0.7·a)/0.3, 0, 1),  s = 1 − (1−i)³
yaw = −180 + n·s
R   = radius − (1−s)·a·24
x = −sin(yaw)·R,  z = −cos(yaw)·R
behind = z·cos(tilt) ≥ perspective − 70      → visibility:hidden
transform = translate3d(x,0,z) rotateY(yaw) rotateZ(0) scale(scale)
opacity = min(1, 2.5·i)
data-outside when z > radius²/perspective    → .face gets rotateY(180deg)
```
After the entrance, hide any card with |angle| > 96°.

DO NOT use a CSS animation for the rotation, a different card count, a different radius or perspective curve, or `transform` on `.card` set from CSS.

## 6. Card (PIXEL CONTRACT)

- `a.card`: 270×480, `position:absolute; top:-240px; left:-135px; transform-style:preserve-3d; will-change:transform`, no `data-cursor`. Initial inline style: `opacity:0; transform: rotateY(angle) translateZ(-1800px) scale(2.3)`.
- `.tilt` (inset 0, preserve-3d) holds `.frame` and `.face`. Its transform each frame is `translateZ(z) rotateX(rx) rotateY(ry)` from three springs (stiffness 180, damping 24, mass 1). On mouse move over the card: `rx = −20·ny`, `ry = 24·nx`, `z = 45` (nx, ny ∈ [−.5,.5] from the card rect). Leaving or blurring resets all three to 0.
- `.face`: inset 0, `border-radius:20px`, `overflow:hidden`, `backface-visibility:hidden`, `background: var(--tone)` (the gray image slot, shown whenever `image` is empty), `box-shadow: 0 18px 36px -24px #00000073`. `.face::after` is a paper overlay (`#1a1a1a`, opacity 0, .5s) that turns to **.6** on cards whose internal world (index % 3 → tech, studio, learn) differs from `stage[data-focus]`, which is set while a highlighted word in the sentence is hovered.
- `.frame` (hover / focus outline, white-ish: `--world` on `.card` is `var(--color-ink)`): `inset:-9px; border:3px solid var(--world); border-radius:28px; box-shadow:0 40px 80px -30px var(--world); opacity:.95; clip-path: inset(0 100% 0 0 round 28px)` → hover / focus `inset(0 round 28px)` over `.55s ease-out`.
- Each card is ONLY the face (gray tone behind) plus an `<img class="slideImage">` (`position:absolute; inset:0; object-fit:cover`, `hidden` while `image` is empty). There is NO views pill, NO title label, NO arrow and NO video element.
- DO NOT put text, pills, gradients or icons on the faces, and do not round the cards to any value other than 20px.

## 7. Interaction (PIXEL CONTRACT)

- Rotation target `m` (deg) and its follower spring `C` (stiffness 150, damping 27, mass 1). Per frame, `s = min(delta/16.667, 3)`.
- Auto-rotate (not dragging, no focus inside the wall, no reduced motion): `m += speed·slow·s`. `speed = 0.3` after the entrance; during the intro it is `0.3·(0.5+0.5·f)·clamp((78−m)/20, 0, 1)`.
- Hover slow-down: every 3rd frame, while the mouse is inside the wall, `elementFromPoint` decides whether a card is under the cursor. Then `slow += ((hovering ? 0.2 : 1) − slow)·min(1, 0.08·s)`.
- Wheel (window, `passive:false`, ignored with ctrl or meta, always `preventDefault`, active only after the entrance): `m += 0.5·clamp(dominantDelta·modeFactor, −480, 480)`. modeFactor is 16 for line mode, innerHeight for page mode, 1 otherwise.
- Mouse drag (button 0 on the wall, after the entrance): `m = startM + 0.2·dominant(dx, dy)`, cursor `grabbing`. More than 4px of movement counts as a drag and cancels the following click for 50 ms. Touch works the same way (factor 0.2, ignored when the touch starts on a card).
- There is NO custom cursor label.
- DO NOT add arrow buttons, dots, scrollbars, a cursor label or inertia beyond the spring.

## 8. World frame and sky layers (PIXEL CONTRACT)

- Sky: `.skyLayer` at `inset:-4%`, an `<img>` with `object-fit:cover; object-position:60% 35%; opacity:.8`, `srcset` sky-sm 960w and sky 1672w, `sizes="105vw"`. On top of it, `::after` grain (opacity .07, the same keyframes as the body grain, `inset:-4%`; .04 and no animation on coarse or small screens).
- Clouds: `.cloudLayer` with `height:82%; bottom:-4%; left:-6%; right:-6%; z-index:3`, an image with `object-fit:cover; object-position:50% 100%`. The class `.cloudRise` starts the image at `opacity:0; translateY(18%)`. When the frame gets `[data-revealed]` it moves to `opacity:.92; transform:none` (transform 1.6s ease-out, opacity .9s).
- Petals (17), exact `[left%, top%, width px, duration s, delay s, color, layer]` rows are listed in Appendix A (`PETALS`). Each petal: height = 0.62·width, `border-radius:80% 0`, `opacity:.85`, `animation: petalDrift <dur>s linear infinite`. Every 3rd petal (index % 3 == 0, counted inside its layer) uses `petalDriftBack`. Keyframes: Drift 0% `(-6vw,-12vh) rotate(0)` opacity 0 → 8% opacity .85 → 50% `(14vw,18vh) 260deg` → 92% .85 → 100% `(34vw,46vh) 540deg` opacity 0. DriftBack 0% `(6vw,-12vh)` → 50% `(-12vw,16vh) -240deg` → 100% `(-28vw,44vh) -500deg`, with the same opacities.
- Motes (14, `MOTES` in Appendix A): radial gradient `#fff6d6f2 → #ffcd7873 45% → transparent 70%`, `moteRise` 16s ease-in-out infinite: 0% `scale(.6)` opacity 0 → 20% .9 → 50% `(14px,-9vh) scale(1)` .55 → 80% .85 → 100% `(-6px,-20vh) scale(.7)` opacity 0.
- Pointer parallax (fine pointer only): springs x, y (stiffness 40, damping 18, mass 1) on `clientX/innerWidth·2−1` and `clientY/innerHeight·2−1`. Each layer gets `translate(−x·depth px, −y·depth·0.5 px)`, with depths sky 10, back petals 22, clouds 34, motes 40, front petals 48.
- Frame clip: `transition: clip-path 1.1s cubic-bezier(.5,0,.1,1)` from the full-viewport inset to `inset(0 round 28px)` (20px at ≤600px).
- DO NOT replace the sky or clouds with CSS gradients, blur the sky, or animate the clouds in a loop.

## 9. Header, sentence and stats (PIXEL CONTRACT)

- The header contains only the wordmark. NO nav and NO mode switch.
- Header entrance (wordmark only): `opacity 0 → 1` over .6s with a .2s delay. `visibility` flips to visible at .2s on entry (and to hidden at .8s on exit). Triggered by the entrance (section 10).
- Sentence (literal, `id="home-heading"`):
  **I build portfolios for** [designers]¹ **, sites powered by** [AI]² **and free templates for** [freelancers]³ **. Or simply** [ask my AI]⁴ **.**
  Highlighted words: designers = tech `#2F55FF`, AI = studio `#EF3B2D`, freelancers = learn `#FFC81A`, ask my AI = ink `#111` (a `<button>`). Superscripts are `01`…`04`. Keep "designers,", "freelancers." and "ask my AI." in `white-space:nowrap` spans with their punctuation.
  - Type: Instrument Serif 400, `clamp(32px,3.6vw,52px)`, `line-height:1.1`, `letter-spacing:-.02em`, `text-wrap:balance`, max-width 1100px.
  - Required line breaks: 1440/1024/768 → "I build portfolios for designers, sites powered by AI and" | "free templates for freelancers. Or simply ask my AI.". 375 → "I build portfolios for designers," | "sites powered by AI and free" | "templates for freelancers." | "Or simply ask my AI.".
  - Every word (punctuation included) sits in a `.wordMask` (inline-block, overflow hidden, `padding-bottom:.08em; margin-bottom:-.08em`). It rises from `translateY(110%)` over .8s ease-out with a delay of `0.05 + 0.035·k` s, where k is the running word index across the whole sentence.
  - Underline: `height:max(3px,.055em); bottom:.02em`, `scaleX 0→1` over .7s ease-out, delay `0.9 + 0.08·n` (n = 1…4). Sup: mono, `max(10px,.19em)`, color `#6e6e73`, `top:.5em`, opacity 0→1 over .5s with delay `1 + 0.08·n`. Sups are hidden at ≤600px.
  - Hover on a highlighted word: the sentence gets `[data-hovering]`, other words drop to opacity .28, a marker block (`inset:.1em -.12em -.02em`) grows `scaleX 0→1` (.5s) behind the active word, whose text turns `var(--on)`.
  - Floating preview (fine pointers only): a 220px card (radius 16, `box-shadow:0 30px 60px -28px #000a, 0 0 0 1px rgba(243,243,241,.06)`) placed at `bottom:22px; left:18px` relative to an anchor that follows the cursor with springs (stiffness 260, damping 28, mass .6). The anchor's y is capped at `sentenceTop + 12`. It holds a 270px gray media block (the tone of the first card of that world), plus meta text: a `<b>` (8px square in the world color + label) and one line. The labels and lines are: PORTFOLIO "120+ sites live · Case studies, galleries and contact pages", AI SITES "3.2K prompts shipped · Chat widgets, smart search and auto-copy", TEMPLATES "46 clients served · Free starter kits, updated monthly". The AI preview has a `#1a1a1c` background, a serif 28px "What should your site say?" with a 2px `#6ee7b7` blinking caret, and the meta "Halo’s AI · ask about a portfolio, a template or how to add AI to your site". It enters with opacity and scale .9→1 over .35s ease-out and leaves in .18s.
- Stats (`dl.meta`, gap `clamp(20px,2.6vw,40px)`, each item a right-aligned column-reverse): **120+ SITES LIVE · 46 CLIENTS · 3.2K AI PROMPTS** (data-count values `120+`, `46`, `3.2K`). Numbers: serif `clamp(36px,3.4vw,52px)`, `line-height:1`, tabular-nums. Labels: mono 11px, `letter-spacing:.1em`, `#6e6e73`. Each number counts up from 0 over 1.6s ease-out with a delay of `0.8 + 0.1·i` s, keeping 1 decimal place only when the value has one (3.2K), and its suffix. The wrapper fades in from `translateY(8px)` over .6s with a .7s delay.
- DO NOT use underline text-decoration, change the words, or let the sentence wrap differently from the breaks listed above.

## 10. Entrance timeline (t = 0 at script start)

| t (s) | Event |
|---|---|
| 0.00 | `formation` tweens 0→1 over **2.6s linear**. Cards deal in from the front (yaw −180°) to their slots. |
| 0.10–1.42 | "Halo." glyphs rise (1s each, staggered 80ms). |
| 0.20 | The counter mask rises (.9s). |
| 0.35–1.03 | The caption scrambles to "LOADING GALLERY". |
| 0.085·k | Front item k settles; the counter tweens to `k/19·100` (.65s). Loading is done at ≈1.6s. |
| 2.60 | Formation is complete and loading is done, so the **reveal** starts: the loader fades out (.9s), the caption fades (.3s), the counter exits upward (.5s), the frame gets `data-revealed` (clouds rise), `expansion` tweens 0→1 over **1.9s cubic-bezier(.5,0,.15,1)**, and "Halo." flies to the header's "Halo" rect: `translate(dx,dy) scale(fontRatio)` over **1.3s cubic-bezier(.65,0,.35,1)**, with its period fading over .5s. |
| 3.50 | The loader gets `display:none`. |
| 3.65 | **Entrance** (reveal + 1.05s): the header fades in (.6s, delay .2), the frame drops `data-open` (clip to a 28px-radius inset, 1.1s) and `data-entering` (z back to 1), the sky scales 1.07→1 (1.4s), `.below` fades in (.55s), the words rise, underlines draw, sups appear, stats count up. |
| 4.50 | Expansion is complete, so the page counts as **entered**: rotation runs at 0.3°/frame, wheel/drag/touch are enabled, recycling starts, cards past ±96° are hidden, the wall drops `data-entering`. |
| 4.55 | The flying "Halo." is removed (entrance + 0.9s). The header wordmark is visible in its place. |

Instant end state (skip, Escape, Tab, or reduced motion): stop all tweens, hide the loader, remove the flying brand, set `formation = expansion = 1`, add `data-revealed`, mark the page entered, and apply the entrance with `data-immediate` (no transitions; words, underlines, sups and stats show final values).

## 11. Assets (hosted, use exactly)

| File | URL | Size | Use |
|---|---|---|---|
| sky.webp | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/sky.webp | 1672×941 | sky `src` + `srcset 1672w` |
| sky-sm.webp | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/sky-sm.webp | 960×541 | sky `srcset 960w` |
| clouds-front.webp | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/clouds-front.webp | 2172×724, alpha | front cloud bank |
| photo-01.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-01.jpg | 736×977 | card photo, slots where i % 10 = 0 |
| photo-02.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-02.jpg | 736×1308 | card photo, slots where i % 10 = 1 |
| photo-03.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-03.jpg | 736×1104 | card photo, slots where i % 10 = 2 |
| photo-04.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-04.jpg | 735×980 | card photo, slots where i % 10 = 3 |
| photo-05.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-05.jpg | 736×1104 | card photo, slots where i % 10 = 4 |
| photo-06.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-06.jpg | 736×736 | card photo, slots where i % 10 = 5 |
| photo-07.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-07.jpg | 736×736 | card photo, slots where i % 10 = 6 |
| photo-08.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-08.jpg | 736×920 | card photo, slots where i % 10 = 7 |
| photo-09.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-09.jpg | 735×916 | card photo, slots where i % 10 = 8 |
| photo-10.jpg | https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/photo-10.jpg | 736×920 | card photo, slots where i % 10 = 9 |

Nothing else is loaded (fonts come from Google Fonts). Cards load their photo with `<img src>` as soon as a slot is filled (no lazy loading, no video).

## 12. Image data and placeholder swap

- `PHOTOS` holds the 10 URLs of section 11 (`IMG_BASE + "photo-NN.jpg"`) and `IMAGES` is 30 strings where `IMAGES[i] = PHOTOS[i % 10]`. `SLIDES` is derived from it: `{ world: [tech,studio,learn][i%3], tone: TONES[i%4], image, title: "Image " + (i+1) }`.
- To change a picture, put another URL in `PHOTOS`/`IMAGES[i]` (portrait ratio ≈ 270×480 works best). The picture covers the gray face with `object-fit:cover`; an empty string keeps the gray shape. Nothing else changes. A photo that fails to load leaves the gray face visible.
- DO NOT add videos or text to the cards.

## 13. Responsive (explicit values)

| Breakpoint | Changes |
|---|---|
| default | `--wall-scale:.62` |
| `max-width:900px` | `--wall-scale:.5`. `.below` becomes 1 column, `row-gap:16px`, `padding:18px 20px 22px`, stats left-aligned |
| `max-width:600px` | header `padding:18px 20px 0; gap:12px`. Wordmark 32px, " Carousel" hidden. Stage `height:calc(100svh − 60px)`, padding-top 6px. Frame `margin:10px 10px 0`, radius 20px. Sentence `clamp(28px,8vw,36px)`, line-height 1.12, sups hidden. Loader status `bottom:7vh`, skip `bottom:8vh`, loader brand `top:2vh` |
| `max-height:760px` | `--wall-scale:.48` |
| `max-height:760px and max-width:600px` | `--wall-scale:.4` |
| `max-height:520px` | `--wall-scale:.28` |
| `max-height:450px` | counter 72px |

## 14. Accessibility

- `prefers-reduced-motion: reduce` → the loader and the flying brand get `display:none`, the instant end state applies, auto-rotation stops, grain, petals, motes and the caret stop animating, and every transition listed above is turned off. Wheel and drag jump the spring directly.
- Progressbar: `role="progressbar"`, `aria-label="Preparing gallery"`, min 0, max 100, `aria-valuenow` updated with each step. Skip button: `aria-label="Skip entrance animation"`.
- Decorative layers are `aria-hidden="true"`. The sky `alt` is "A hand-painted sky at golden hour" and the clouds `alt` is "A bank of painted clouds". Each card has `aria-label="Image <n>"`.

## 15. Final self-check (run before answering)

1. One `index.html`, `lang="en"`, no external JS, one Google Fonts link. Instrument Serif and Geist Mono report `loaded` in `document.fonts` (Geist sans is only the body fallback and may stay unloaded).
2. Network: R2 image requests are the sky variant picked by `srcset`, clouds-front and the photos of the cards on screen (up to the 10 `photo-NN.jpg`), all 200. No 404s and no console errors.
3. 16 `.card` elements inside `#wallCenter`; every card holds one photo (`<img class="slideImage">` with `src` set, 10 distinct URLs across the ring), no `<video>` and no text inside any card.
4. At 1440×900 the final state matches the coordinate table within ±2px (header 68px tall, frame 16/92/1408/631, sentence at x=48).
5. The sentence breaks exactly as listed in section 9 at 1440, 1024, 768 and 375 px wide.
6. The timeline matches section 10: the reveal starts at ≈2.6s and the page is entered by ≈4.5s.
7. After the entrance the ring turns right-to-left at 18°/s, slows to 20% while a card is hovered, and wheel and drag move it.
8. Skip, Escape and Tab all jump to the final state with no transitions.
9. No text other than the literals in this command and Appendix A, and no other brand name anywhere.
10. The header shows only "Halo Carousel." (no nav, no Human / Agent), the page background is `#1a1a1a`, and all text is light (`#f3f3f1`).

---

## Appendix A: complete verified source (`index.html`)

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Halo Carousel | Builder, creator, AI tinkerer</title>
<link rel="icon" type="image/svg+xml" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 32 32'%3E%3Cellipse cx='16' cy='10' rx='11' ry='4.5' fill='none' stroke='%23111' stroke-width='2.5'/%3E%3Ccircle cx='16' cy='22' r='6' fill='%23111'/%3E%3C/svg%3E">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Geist+Mono:wght@400;500&family=Geist:wght@400;500&family=Instrument+Serif&display=swap" rel="stylesheet">
<style>
/* ---------- tokens ---------- */
:root{
  --color-paper:#1a1a1a; --color-paper-deep:#232323; --color-ink:#f3f3f1; --color-ink-soft:#a3a3a5; --color-ink-muted:#8e8e93;
  --color-surface:#2a2a2c; --color-night:#101012; --color-tech:#2f55ff; --color-studio:#ef3b2d; --color-learn:#ffc81a; --color-on-contrast:#fff;
  --font-serif:"Instrument Serif",Georgia,"Times New Roman",serif;
  --font-mono:"Geist Mono",ui-monospace,SFMono-Regular,Menlo,monospace;
  --font-sans:"Geist",ui-sans-serif,system-ui,sans-serif;
  --grain:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='240' height='240'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.85' numOctaves='3' stitchTiles='stitch'/%3E%3CfeColorMatrix values='0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1.7 0 0 0 -.6'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  --ease-out:cubic-bezier(.22,1,.36,1);
}
/* ---------- reset ---------- */
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0;border:0 solid}
html{background:var(--color-paper);color:var(--color-ink);color-scheme:dark;overflow:hidden;overscroll-behavior:none}
body{background:var(--color-paper);font-family:var(--font-sans);-webkit-font-smoothing:antialiased;overflow:hidden;overscroll-behavior:none;line-height:1.5}
a{color:inherit;text-decoration:inherit}
button{font:inherit;color:inherit;background:transparent;cursor:pointer}
img,video{display:block;max-width:none}
sup{line-height:1}
::selection{background:color-mix(in srgb,var(--color-tech) 25%,transparent);color:var(--color-ink)}
:focus-visible{outline:2px solid var(--color-tech);outline-offset:4px}
body::before{content:"";z-index:0;pointer-events:none;background-image:var(--grain);opacity:.13;will-change:transform;animation:grain .9s steps(5,end) infinite;position:fixed;inset:-6%}
body>main{position:relative;z-index:1}
@keyframes grain{0%{transform:translate(0)}20%{transform:translate(-3%,2%)}40%{transform:translate(2%,-3%)}60%{transform:translate(-1%,-2%)}80%{transform:translate(3%,1%)}to{transform:translate(0)}}
@media (max-width:600px),(pointer:coarse){body::before{opacity:.07;animation:none}}

/* ---------- header ---------- */
.site-header{z-index:6;justify-content:space-between;align-items:center;gap:24px;padding:26px 40px 0;display:flex;position:relative;opacity:0;visibility:hidden;transition:opacity .6s ease .2s,visibility 0s linear .8s}
.site-header[data-in]{opacity:1;visibility:visible;transition:opacity .6s ease .2s,visibility 0s linear .2s}
.site-header[data-immediate]{transition:none}
.site-mark{display:block}
.site-left{align-items:baseline;gap:.3em;display:flex}
.site-wordmark{white-space:nowrap;clip-path:inset(0 -100vw 0 0);margin-bottom:-12px;padding-bottom:12px;display:grid;position:relative}
.wordmark-name{font-family:var(--font-serif);letter-spacing:-.03em;grid-area:1/1;font-size:42px;line-height:.95;transition:transform .6s var(--ease-out),opacity .4s}
.wordmark-line{opacity:0;position:absolute;top:0;left:0;transform:translateY(145%)}
.wordmark-first{line-height:.95;display:inline-block}
.site-mark:hover .wordmark-name:not(.wordmark-line),.site-mark:focus-visible .wordmark-name:not(.wordmark-line){opacity:0;transform:translateY(-105%)}
.site-mark:hover .wordmark-line,.site-mark:focus-visible .wordmark-line{opacity:1;transform:none}

/* ---------- stage ---------- */
.stage{--stage-top:4px;height:calc(100svh - 68px);padding:var(--stage-top) 0 0;flex-direction:column;display:flex;position:relative}
.worldFrame{z-index:1;isolation:isolate;background:var(--color-paper-deep);clip-path:inset(0 round 28px);flex:1;min-height:0;margin:20px 16px 0;transition:clip-path 1.1s cubic-bezier(.5,0,.1,1),opacity .6s;position:relative}
.worldFrame[data-open]{clip-path:inset(calc(var(--ft,160px)*-1) calc(var(--fr,60px)*-1) calc(var(--fb,420px)*-1) calc(var(--fl,60px)*-1) round 0px)}
.worldFrame[data-entering]{z-index:101}
.worldFrame[data-immediate],.worldFrame[data-immediate] .worldView,.worldFrame[data-immediate] .cloudRise img{transition:none}
.worldFrame>.wallWrap{position:absolute;inset:0}
.worldView{z-index:0;top:calc(var(--ft,92px)*-1);left:calc(var(--fl,16px)*-1);pointer-events:none;transform-origin:50% 45%;width:100vw;height:100svh;transition:transform 1.4s cubic-bezier(.5,0,.1,1);position:absolute;transform:scale(1)}
.worldFrame[data-open] .worldView{transform:scale(1.07)}
.worldFront{z-index:3}
.skyLayer,.cloudLayer,.petalsBack,.petalsFront,.motes{pointer-events:none;will-change:transform;position:absolute}
.skyLayer{z-index:0;inset:-4%}
.skyLayer img,.cloudLayer img{position:absolute;inset:0;width:100%;height:100%;object-fit:cover}
.skyLayer img{object-position:60% 35%;opacity:.8}
.skyLayer::after{content:"";background-image:var(--grain);opacity:.07;animation:grain .9s steps(5,end) infinite;position:absolute;inset:-4%}
@media (max-width:600px),(pointer:coarse){.skyLayer::after{opacity:.04;animation:none}}
.petalsBack{z-index:1;inset:0}
.cloudLayer{z-index:3;height:82%;bottom:-4%;left:-6%;right:-6%}
.cloudLayer img{object-position:50% 100%;opacity:.92}
.cloudRise img{opacity:0;transition:transform 1.6s var(--ease-out),opacity .9s;transform:translateY(18%)}
.worldFrame[data-revealed] .cloudRise img{opacity:.92;transform:none}
.petalsFront{z-index:3;inset:0}
.motes{z-index:4;inset:0}
.mote{background:radial-gradient(circle,#fff6d6f2,#ffcd7873 45%,#0000 70%);border-radius:50%;animation:moteRise 16s ease-in-out infinite;position:absolute}
@keyframes moteRise{0%{opacity:0;transform:translate(0) scale(.6)}20%{opacity:.9}50%{opacity:.55;transform:translate(14px,-9vh) scale(1)}80%{opacity:.85}to{opacity:0;transform:translate(-6px,-20vh) scale(.7)}}
.petal{opacity:.85;border-radius:80% 0;animation:petalDrift 28s linear infinite;position:absolute}
.petal[data-flip]{animation-name:petalDriftBack}
@keyframes petalDriftBack{0%{opacity:0;transform:translate(6vw,-12vh) rotate(0)}8%{opacity:.85}50%{transform:translate(-12vw,16vh) rotate(-240deg)}92%{opacity:.85}to{opacity:0;transform:translate(-28vw,44vh) rotate(-500deg)}}
@keyframes petalDrift{0%{opacity:0;transform:translate(-6vw,-12vh) rotate(0)}8%{opacity:.85}50%{transform:translate(14vw,18vh) rotate(260deg)}92%{opacity:.85}to{opacity:0;transform:translate(34vw,46vh) rotate(540deg)}}

/* ---------- image wall ---------- */
.wall{z-index:2;cursor:grab;touch-action:pinch-zoom;user-select:none;-webkit-user-select:none;width:100%;height:100%;position:relative}
.wall[data-entering],.wall[data-entering] .card{z-index:101;pointer-events:none}
.wallLens{pointer-events:none;position:absolute;inset:-100vh 0}
.wallCenter{--wall-scale:.62;--s:calc(var(--wall-scale)*var(--intro-scale,1));transform:translate(var(--intro-x,0px),var(--intro-offset,0px)) rotateZ(var(--intro-roll,0deg)) rotateX(var(--intro-tilt,0deg)) scale3d(var(--s),var(--s),var(--intro-sz,1));width:0;height:0;transform-style:preserve-3d;position:absolute;top:50%;left:50%}
.card{--world:var(--color-ink);pointer-events:auto;width:270px;height:480px;transform-style:preserve-3d;cursor:pointer;will-change:transform;position:absolute;top:-240px;left:-135px;outline:none}
.tilt{transform-style:preserve-3d;position:absolute;inset:0}
.frame{border:3px solid var(--world);clip-path:inset(0 100% 0 0 round 28px);box-shadow:0 40px 80px -30px var(--world);opacity:.95;pointer-events:none;border-radius:28px;transition:clip-path .55s var(--ease-out);position:absolute;inset:-9px}
.face{backface-visibility:hidden;color:#fff;border-radius:20px;position:absolute;inset:0;overflow:hidden;box-shadow:0 18px 36px -24px #00000073;background:var(--tone,#cfcfca)}
.card[data-outside] .face{transform:rotateY(180deg)}
.face::after{content:"";z-index:4;background:var(--color-paper);opacity:0;pointer-events:none;transition:opacity .5s;position:absolute;inset:0}
.stage[data-focus="tech"] .card:not([data-world="tech"]) .face::after,
.stage[data-focus="studio"] .card:not([data-world="studio"]) .face::after,
.stage[data-focus="learn"] .card:not([data-world="learn"]) .face::after{opacity:.6}
.slideImage{position:absolute;inset:0;width:100%;height:100%;object-fit:cover;pointer-events:none}
.slideImage{z-index:0}
@media (hover:hover){
  .card:hover .frame{clip-path:inset(0 round 28px)}
}
.card:focus-visible .frame{clip-path:inset(0 round 28px)}
@media (max-width:900px){.wallCenter{--wall-scale:.5}}
@media (max-width:600px){.wallCenter{--wall-scale:.5}}
@media (max-height:760px){.wallCenter{--wall-scale:.48}}
@media (max-height:760px) and (max-width:600px){.wallCenter{--wall-scale:.4}}
@media (max-height:520px){.wallCenter{--wall-scale:.28}}

/* ---------- loader ---------- */
.galleryLoader{z-index:100;background:var(--color-paper);color:var(--color-ink);position:fixed;inset:0;overflow:hidden;transition:opacity .9s var(--ease-out)}
.galleryLoader[data-exit]{opacity:0;pointer-events:none}
.loaderBrand{z-index:102;pointer-events:none;transform-origin:0 0;font-family:var(--font-serif);letter-spacing:-.03em;margin:0;font-size:clamp(64px,9vw,150px);line-height:.95;display:flex;position:fixed;top:3.2vh;left:4vw}
.loaderMask{vertical-align:bottom;display:inline-block;overflow:hidden}
.loaderMask>span{transform-origin:0 100%;display:inline-block}
.loaderBrand .loaderMask>span{transform:translateY(105%) rotate(8deg);animation:letterIn 1s var(--ease-out) forwards}
@keyframes letterIn{to{transform:none}}
.loaderStatus{flex-direction:column;align-items:flex-end;display:flex;position:absolute;bottom:4vh;right:5vw}
.loaderCaption{font-family:var(--font-mono);letter-spacing:.14em;text-transform:uppercase;color:var(--color-ink-soft);white-space:pre;margin:0 0 4px;font-size:12px;transition:opacity .3s}
.galleryLoader[data-exit] .loaderCaption{opacity:0}
.loaderCount{font-family:var(--font-mono);letter-spacing:-.09em;font-variant-numeric:tabular-nums;align-items:flex-end;font-size:clamp(80px,12vw,180px);font-weight:400;line-height:1.1;display:flex}
.loaderCount>.loaderMask{padding:0;display:block}
.loaderMask>.countRise{display:flex;transform:translateY(100%);animation:countIn .9s var(--ease-out) .2s forwards}
@keyframes countIn{to{transform:none}}
.galleryLoader[data-exit] .countRise{animation:countOut .5s cubic-bezier(.6,0,.8,.4) forwards}
@keyframes countOut{from{transform:none}to{transform:translateY(-100%)}}
.odometerDigit{letter-spacing:0;height:1.1em;margin-right:-.09em;display:block;overflow:hidden}
.odometerDigit:last-child{margin-right:0}
.odometerDigit>span{flex-direction:column;transition:transform .7s var(--ease-out);display:flex}
.odometerDigit>span>span{height:1.1em;line-height:1.1}
.loaderPercent{letter-spacing:0;margin:0 0 .75em 22px;font-size:.22em}
.skipBtn{border:1px solid color-mix(in srgb,var(--color-ink) 13%,transparent);border-radius:50%;width:44px;height:44px;font-size:18px;position:absolute;bottom:5vh;left:4vw}
@media (max-width:600px){.loaderStatus{bottom:7vh}.skipBtn{bottom:8vh}.loaderBrand{top:2vh}}
@media (max-height:450px){.loaderCount{font-size:72px}}
@media (prefers-reduced-motion:reduce){.galleryLoader,.loaderBrand{display:none}}

/* ---------- below: sentence + meta ---------- */
.below{z-index:4;flex:none;grid-template-columns:repeat(12,minmax(0,1fr));align-items:end;column-gap:24px;padding:clamp(18px,3svh,30px) 48px clamp(22px,4svh,38px);display:grid;position:relative;opacity:0;transition:opacity .55s var(--ease-out)}
.below[data-in]{opacity:1}
.below[data-immediate]{transition:none}
.below>:first-child{grid-column:1/span 9}
.below>:last-child{grid-column:10/span 3;justify-self:end}
.sentenceWrap{position:relative}
.sentence{max-width:1100px;font-family:var(--font-serif);letter-spacing:-.02em;text-wrap:balance;margin:0;font-size:clamp(32px,3.6vw,52px);font-weight:400;line-height:1.1}
.wordMask{vertical-align:bottom;margin-bottom:-.08em;padding-bottom:.08em;display:inline-block;overflow:hidden}
.wordMask>span{display:inline-block;transform:translateY(110%);transition:opacity .35s,color .35s,transform .8s var(--ease-out);transition-delay:0s,0s,var(--d,0s)}
.sentence[data-ready] .wordMask>span{transform:translateY(0)}
.sentence[data-immediate] .wordMask>span{transition:opacity .35s,color .35s}
.sentence[data-hovering] .wordMask>span{opacity:.28}
.choice{font:inherit;color:inherit;cursor:pointer;white-space:nowrap;padding:0;display:inline}
.choiceLabel{isolation:isolate;display:inline-block;position:relative}
.marker{z-index:-1;background:var(--world);transform-origin:0;transition:transform .5s var(--ease-out);position:absolute;inset:.1em -.12em -.02em;transform:scaleX(0)}
.choice[data-active] .marker{transform:none}
.nowrap{white-space:nowrap}
.choice sup{vertical-align:top;font-family:var(--font-mono);letter-spacing:.08em;color:var(--color-ink-soft);margin-left:.12em;font-size:max(10px,.19em);line-height:1;position:relative;top:.5em;opacity:0;transition:color .35s,opacity .5s linear var(--d,0s)}
.sentence[data-ready] .choice sup{opacity:1}
.underline{background:var(--world);transform-origin:0;height:max(3px,.055em);position:absolute;bottom:.02em;left:0;right:0;transform:scaleX(0);transition:background-color .35s,height .35s var(--ease-out),transform .7s var(--ease-out) var(--d,0s)}
.sentence[data-ready] .underline{transform:scaleX(1)}
.sentence[data-immediate] .underline,.sentence[data-immediate] .choice sup{transition:none}
.sentence[data-hovering] .choice[data-active] .wordMask>span{opacity:1;color:var(--on)}
.choice[data-active] sup{color:var(--color-ink)}
.choice:focus-visible{outline:none}
.choice:focus-visible .underline{height:max(4px,.09em)}
.metaWrap{opacity:0;transform:translateY(8px);transition:opacity .6s var(--ease-out) .7s,transform .6s var(--ease-out) .7s}
.metaWrap[data-in]{opacity:1;transform:none}
.metaWrap[data-immediate]{transition:none}
.meta{gap:clamp(20px,2.6vw,40px);margin:0;display:flex}
.meta>div{flex-direction:column-reverse;align-items:flex-end;gap:6px;display:flex}
.meta dt{font-family:var(--font-mono);letter-spacing:.1em;text-transform:uppercase;color:var(--color-ink-soft);font-size:11px}
.meta dd{font-family:var(--font-serif);letter-spacing:-.02em;font-variant-numeric:tabular-nums;margin:0;font-size:clamp(36px,3.4vw,52px);line-height:1}
@media (max-width:900px){
  .below{grid-template-columns:1fr;row-gap:16px;padding:18px 20px 22px}
  .below>:first-child,.below>:last-child{grid-column:1;justify-self:start}
  .meta>div{align-items:flex-start}
}
@media (max-width:600px){
  .site-header{gap:12px;padding:18px 20px 0}
  .wordmark-last{display:none}
  .wordmark-name{font-size:32px}
  .stage{--stage-top:6px;height:calc(100svh - 60px)}
  .worldFrame{clip-path:inset(0 round 20px);margin:10px 10px 0}
  .sentence{font-size:clamp(28px,8vw,36px);line-height:1.12}
  .choice sup{display:none}
}

/* ---------- hover preview ---------- */
.previewAnchor{z-index:40;pointer-events:none;position:fixed;top:0;left:0}
.preview{background:var(--color-surface);width:220px;box-shadow:0 30px 60px -28px #000a,0 0 0 1px color-mix(in srgb,var(--color-ink) 6%,transparent);transform-origin:0 100%;border-radius:16px;position:absolute;bottom:22px;left:18px;overflow:hidden;opacity:0;transform:scale(.9);transition:opacity .18s,transform .18s}
.preview[data-on]{opacity:1;transform:scale(1);transition:opacity .35s var(--ease-out),transform .35s var(--ease-out)}
.previewMedia{background:var(--tone,#cfcfca);height:270px;position:relative}
.previewMedia[hidden]{display:none}
.previewMeta{color:var(--color-ink-soft);padding:12px 14px 14px;font-size:12px;line-height:1.5}
.previewMeta b{font-family:var(--font-mono);letter-spacing:.1em;color:var(--color-ink);align-items:center;gap:7px;margin-bottom:4px;font-size:11px;font-weight:500;display:flex}
.previewMeta b i{background:var(--world);width:8px;height:8px}
.preview[data-ai]{background:var(--color-night)}
.preview[data-ai] .previewMeta{color:#a3a3a5}
.previewAsk{font-family:var(--font-serif);color:#fff;padding:22px 16px 6px;font-size:28px;line-height:1.1}
.caret{vertical-align:-.1em;background:#6ee7b7;width:2px;height:.9em;margin-left:4px;animation:blink 1s step-end infinite;display:inline-block}
@keyframes blink{50%{opacity:0}}
.sr-only{position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0 0 0 0);white-space:nowrap}

@media (prefers-reduced-motion:reduce){
  body::before,.skyLayer::after,.petal,.mote,.caret{animation:none}
  .worldFrame .cloudRise img,.wordMask>span,.underline,.choice sup,.marker,.frame,.wordmark-name{transition:none}
}
</style>
</head>
<body>
<header class="site-header" id="siteHeader">
  <div class="site-left">
    <a class="site-mark" href="#" aria-label="Halo Carousel, home">
      <span class="site-wordmark" aria-hidden="true">
        <span class="wordmark-name"><span class="wordmark-first">Halo</span><span class="wordmark-last"> Carousel</span>.</span>
        <span class="wordmark-name wordmark-line">Shipping playful ideas with AI.</span>
      </span>
    </a>
  </div>
</header>

<main>
<section class="stage" id="stage" aria-labelledby="home-heading">
  <div class="worldFrame" id="worldFrame" data-open data-entering>
    <div class="worldView" aria-hidden="true">
      <div class="skyLayer" data-depth="10">
        <img src="https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/sky.webp"
             srcset="https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/sky-sm.webp 960w, https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/sky.webp 1672w"
             sizes="105vw" alt="A hand-painted sky at golden hour" fetchpriority="high">
      </div>
      <div class="petalsBack" data-depth="22" id="petalsBack"></div>
    </div>

    <div class="wallWrap">
      <p class="loaderBrand" id="loaderBrand" aria-hidden="true"></p>
      <div class="galleryLoader" id="galleryLoader">
        <div class="loaderStatus">
          <p class="loaderCaption" id="loaderCaption" aria-hidden="true"> </p>
          <div class="loaderCount" id="loaderCount" role="progressbar" aria-label="Preparing gallery" aria-valuemin="0" aria-valuemax="100" aria-valuenow="0">
            <span class="loaderMask"><span class="countRise" id="odometer"></span></span>
            <span class="loaderPercent">%</span>
          </div>
        </div>
        <button type="button" class="skipBtn" id="skipBtn" aria-label="Skip entrance animation" title="Skip animation">↗</button>
      </div>
      <div class="wall" id="wall" data-entering role="region" aria-label="Halo’s gallery">
        <div class="wallLens" id="wallLens"><div class="wallCenter" id="wallCenter"></div></div>
      </div>
    </div>

    <div class="worldView worldFront" aria-hidden="true">
      <div class="cloudLayer cloudRise" data-depth="34">
        <img src="https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/clouds-front.webp" alt="A bank of painted clouds">
      </div>
      <div class="petalsFront" data-depth="48" id="petalsFront"></div>
      <div class="motes" data-depth="40" id="motes"></div>
    </div>
  </div>

  <div class="below" id="below">
    <div class="sentenceWrap"><h1 class="sentence" id="home-heading"></h1></div>
    <div class="metaWrap" id="metaWrap">
      <dl class="meta">
        <div><dt>Sites live</dt><dd data-count="120+">120+</dd></div>
        <div><dt>Clients</dt><dd data-count="46">46</dd></div>
        <div><dt>AI prompts</dt><dd data-count="3.2K">3.2K</dd></div>
      </dl>
    </div>
  </div>
</section>
</main>

<div class="previewAnchor" id="previewAnchor" aria-hidden="true">
  <div class="preview" id="preview">
    <div class="previewMedia" id="previewMedia"></div>
    <p class="previewAsk" id="previewAsk" hidden>What should your site say?<span class="caret"></span></p>
    <p class="previewMeta" id="previewMeta"></p>
  </div>
</div>

<script>
(() => {
'use strict';

/* ================= data ================= */
const WORLDS = {
  tech:   { label: 'PORTFOLIO', color: '#2F55FF', on: '#FFFFFF' },
  studio: { label: 'AI SITES', color: '#EF3B2D', on: '#FFFFFF' },
  learn:  { label: 'TEMPLATES', color: '#FFC81A', on: '#111111' }
};
// IMAGE SLOTS: 10 photos hosted on R2, repeated to fill 30 slots (slot i uses photo i % 10).
// Replace any URL to swap a picture. An empty string keeps the gray shape. Portrait photos (~270x480 ratio) fit best.
const IMG_BASE = 'https://pub-c061447f29f940ff863bb50e9a78d984.r2.dev/halo-carousel/';
const PHOTOS = Array.from({ length: 10 }, (_, i) => IMG_BASE + 'photo-' + String(i + 1).padStart(2, '0') + '.jpg');
const IMAGES = Array.from({ length: 30 }, (_, i) => PHOTOS[i % 10]);
const TONES = ['#cfcfca', '#c6c6c1', '#d7d7d2', '#bfbfba'];
const WORLD_KEYS = ['tech', 'studio', 'learn'];
const SLIDES = IMAGES.map((image, i) => ({ world: WORLD_KEYS[i % 3], tone: TONES[i % TONES.length], image, title: 'Image ' + (i + 1) }));

const PETALS = [ // [left%, top%, width px, duration s, delay s, color, layer]
  [4,14,9,29,0,'var(--color-studio)','back'],[14,4,7,34,-12,'var(--color-learn)','back'],[24,26,8,31,-21,'var(--color-studio)','back'],
  [36,10,6,37,-5,'var(--color-learn)','back'],[46,30,9,27,-16,'var(--color-studio)','back'],[56,6,7,33,-27,'var(--color-learn)','back'],
  [66,20,8,30,-9,'var(--color-studio)','back'],[76,2,6,36,-24,'var(--color-learn)','back'],[86,24,9,28,-3,'var(--color-studio)','back'],
  [94,12,7,35,-18,'var(--color-learn)','back'],[30,44,6,39,-31,'var(--color-studio)','back'],[60,40,7,32,-13,'var(--color-learn)','back'],
  [10,50,14,23,-6,'var(--color-learn)','front'],[40,62,12,26,-19,'var(--color-studio)','front'],[70,56,16,21,-11,'var(--color-studio)','front'],
  [88,46,13,24,-2,'var(--color-learn)','front'],[52,70,11,28,-23,'var(--color-learn)','front']
];
const MOTES = [ // [left%, top%, size px, duration s, delay s]
  [8,62,10,14,-2],[18,40,6,18,-9],[27,70,14,16,-5],[36,52,8,20,-13],[44,30,5,17,-3],[53,66,12,15,-11],[61,44,7,19,-7],
  [69,74,16,14,-1],[77,36,6,21,-15],[85,58,11,16,-8],[92,46,7,18,-4],[13,82,9,22,-17],[48,84,13,17,-6],[72,22,5,23,-19]
];
const PREVIEW_META = {
  tech: '120+ sites live · Case studies, galleries and contact pages',
  studio: '3.2K prompts shipped · Chat widgets, smart search and auto-copy',
  learn: '46 clients served · Free starter kits, updated monthly'
};

/* ================= helpers ================= */
const $ = (id) => document.getElementById(id);
const clamp = (v, lo = 0, hi = 1) => Math.min(hi, Math.max(lo, v));
const reduced = matchMedia('(prefers-reduced-motion: reduce)');
const finePointer = matchMedia('(hover: hover) and (pointer: fine)');

function bezier(x1, y1, x2, y2) {
  const cx = 3 * x1, bx = 3 * (x2 - x1) - cx, ax = 1 - cx - bx;
  const cy = 3 * y1, by = 3 * (y2 - y1) - cy, ay = 1 - cy - by;
  const sx = (t) => ((ax * t + bx) * t + cx) * t;
  const sy = (t) => ((ay * t + by) * t + cy) * t;
  const dx = (t) => (3 * ax * t + 2 * bx) * t + cx;
  return (x) => {
    if (x <= 0) return 0; if (x >= 1) return 1;
    let t = x;
    for (let i = 0; i < 8; i++) { const e = sx(t) - x; if (Math.abs(e) < 1e-6) break; const d = dx(t); if (Math.abs(d) < 1e-6) break; t -= e / d; }
    let lo = 0, hi = 1; for (let i = 0; i < 20 && Math.abs(sx(t) - x) > 1e-6; i++) { if (sx(t) < x) lo = t; else hi = t; t = (lo + hi) / 2; }
    return sy(t);
  };
}
const EASE_OUT = bezier(0.22, 1, 0.36, 1);
const linear = (t) => t;

// Time-based tween. Returns { stop }.
function tween({ from, to, duration, delay = 0, ease = EASE_OUT, onUpdate, onComplete }) {
  let raf = 0, stopped = false; const t0 = performance.now() + delay * 1000;
  if (duration <= 0 && delay <= 0) { onUpdate && onUpdate(to); onComplete && onComplete(); return { stop() {} }; }
  const step = (now) => {
    if (stopped) return;
    const p = duration <= 0 ? (now >= t0 ? 1 : 0) : clamp((now - t0) / (duration * 1000));
    if (now >= t0) onUpdate && onUpdate(from + (to - from) * ease(p));
    if (p >= 1) { onComplete && onComplete(); return; }
    raf = requestAnimationFrame(step);
  };
  raf = requestAnimationFrame(step);
  return { stop() { stopped = true; cancelAnimationFrame(raf); } };
}

// Damped spring follower (stiffness / damping / mass), stepped from the main loop.
class Spring {
  constructor(v, { stiffness = 100, damping = 10, mass = 1 } = {}) { this.x = v; this.v = 0; this.target = v; this.k = stiffness; this.c = damping; this.m = mass; }
  set(t) { this.target = t; }
  jump(t) { this.x = this.target = t; this.v = 0; }
  get() { return this.x; }
  step(dt) {
    const n = Math.max(1, Math.ceil(dt / 0.004)), h = dt / n;
    for (let i = 0; i < n; i++) { const a = (-this.k * (this.x - this.target) - this.c * this.v) / this.m; this.v += a * h; this.x += this.v * h; }
    if (Math.abs(this.x - this.target) < 1e-4 && Math.abs(this.v) < 1e-4) { this.x = this.target; this.v = 0; }
    return this.x;
  }
}
const springs = new Set();
const mkSpring = (v, o) => { const s = new Spring(v, o); springs.add(s); return s; };

/* ================= entrance state ================= */
const header = $('siteHeader'), stage = $('stage'), frameEl = $('worldFrame'), below = $('below'), metaWrap = $('metaWrap');
const sentence = $('home-heading');
let entrance = false, immediate = false;
function setEntrance(on, imm = false) {
  entrance = on; immediate = imm;
  [header, frameEl, below, metaWrap, sentence].forEach((el) => el.toggleAttribute('data-immediate', imm));
  if (!on) return;
  header.setAttribute('data-in', '');
  frameEl.removeAttribute('data-open');
  frameEl.removeAttribute('data-entering');
  below.setAttribute('data-in', '');
  metaWrap.setAttribute('data-in', '');
  sentence.setAttribute('data-ready', '');
  runCounters(imm);
  onEntranceForBrand();
}

/* ================= world frame measurement ================= */
function measureFrame() {
  const r = frameEl.getBoundingClientRect();
  frameEl.style.setProperty('--ft', `${r.top + scrollY}px`);
  frameEl.style.setProperty('--fl', `${r.left}px`);
  frameEl.style.setProperty('--fr', `${innerWidth - r.right}px`);
  frameEl.style.setProperty('--fb', `${innerHeight - r.bottom - scrollY}px`);
}
measureFrame();
new ResizeObserver(measureFrame).observe(frameEl);
addEventListener('resize', measureFrame);

/* ================= petals + motes ================= */
function buildPetals(layer, host) {
  PETALS.filter((p) => p[6] === layer).forEach(([l, t, w, dur, del, col], i) => {
    const s = document.createElement('span');
    s.className = 'petal';
    if (i % 3 === 0) s.setAttribute('data-flip', '');
    Object.assign(s.style, { left: l + '%', top: t + '%', width: w + 'px', height: 0.62 * w + 'px', background: col, animationDuration: dur + 's', animationDelay: del + 's' });
    host.appendChild(s);
  });
}
buildPetals('back', $('petalsBack'));
buildPetals('front', $('petalsFront'));
MOTES.forEach(([l, t, sz, dur, del]) => {
  const s = document.createElement('span');
  s.className = 'mote';
  Object.assign(s.style, { left: l + '%', top: t + '%', width: sz + 'px', height: sz + 'px', animationDuration: dur + 's', animationDelay: del + 's' });
  $('motes').appendChild(s);
});

/* ================= pointer parallax ================= */
const px = mkSpring(0, { stiffness: 40, damping: 18, mass: 1 });
const py = mkSpring(0, { stiffness: 40, damping: 18, mass: 1 });
const parallaxLayers = [...document.querySelectorAll('[data-depth]')].map((el) => ({ el, d: +el.dataset.depth }));
addEventListener('pointermove', (e) => {
  if (!finePointer.matches || reduced.matches) return;
  px.set((e.clientX / innerWidth) * 2 - 1);
  py.set((e.clientY / innerHeight) * 2 - 1);
}, { passive: true });

/* ================= sentence ================= */
let wordIndex = 0;
function wordSpan(text) {
  const m = document.createElement('span'); m.className = 'wordMask';
  const s = document.createElement('span'); s.textContent = text;
  s.style.setProperty('--d', `${0.05 + 0.035 * wordIndex++}s`);
  m.appendChild(s); return m;
}
function plain(parent, text) {
  text.split(/(\s+)/).forEach((part) => { if (part.trim()) parent.appendChild(wordSpan(part)); else if (part) parent.appendChild(document.createTextNode(part)); });
}
const choices = [];
function choice(parent, world, label, num) {
  const isAi = world === 'ai';
  const el = document.createElement(isAi ? 'button' : 'a');
  el.className = 'choice';
  if (isAi) el.type = 'button'; else el.href = '#';
  el.dataset.choice = world;
  el.style.setProperty('--world', isAi ? 'var(--color-ink)' : WORLDS[world].color);
  el.style.setProperty('--on', isAi ? 'var(--color-paper)' : WORLDS[world].on);
  const lab = document.createElement('span'); lab.className = 'choiceLabel';
  const mk = document.createElement('span'); mk.className = 'marker'; mk.setAttribute('aria-hidden', 'true'); lab.appendChild(mk);
  label.split(' ').forEach((w, i) => { const sp = document.createElement('span'); if (i > 0) sp.appendChild(document.createTextNode(' ')); sp.appendChild(wordSpan(w)); lab.appendChild(sp); });
  const ul = document.createElement('span'); ul.className = 'underline'; ul.setAttribute('aria-hidden', 'true');
  ul.style.setProperty('--d', `${0.9 + 0.08 * Number(num)}s`); lab.appendChild(ul);
  const sup = document.createElement('sup'); sup.setAttribute('aria-hidden', 'true'); sup.textContent = num;
  sup.style.setProperty('--d', `${1 + 0.08 * Number(num)}s`);
  el.append(lab, sup);
  el.addEventListener('click', (e) => e.preventDefault());
  parent.appendChild(el); choices.push(el);
}
function nowrap(fn) { const s = document.createElement('span'); s.className = 'nowrap'; fn(s); sentence.appendChild(s); }
const sp = () => sentence.appendChild(document.createTextNode(' '));
plain(sentence, 'I build portfolios for'); sp();
nowrap((s) => { choice(s, 'tech', 'designers', '01'); plain(s, ','); }); sp();
plain(sentence, 'sites powered by'); sp();
choice(sentence, 'studio', 'AI', '02'); sp();
plain(sentence, 'and free templates for'); sp();
nowrap((s) => { choice(s, 'learn', 'freelancers', '03'); plain(s, '.'); }); sp();
plain(sentence, 'Or simply'); sp();
nowrap((s) => { choice(s, 'ai', 'ask my AI', '04'); plain(s, '.'); });

/* ---- choice hover: highlight, dim cards, floating preview ---- */
const anchor = $('previewAnchor'), preview = $('preview');
const ax = mkSpring(0, { stiffness: 260, damping: 28, mass: 0.6 });
const ay = mkSpring(0, { stiffness: 260, damping: 28, mass: 0.6 });
let activeChoice = null;
const featured = {}; Object.keys(WORLDS).forEach((w) => { featured[w] = SLIDES.find((r) => r.world === w); });
function placeAnchor(x, y, jump) {
  y = Math.min(y, sentence.getBoundingClientRect().top + 12);
  ax.set(x); ay.set(y); if (jump) { ax.jump(x); ay.jump(y); }
}
function setActive(world) {
  activeChoice = world;
  choices.forEach((c) => c.toggleAttribute('data-active', c.dataset.choice === world));
  sentence.toggleAttribute('data-hovering', !!world);
  setFocusWorld(world && world !== 'ai' ? world : null);
  if (!world) { preview.removeAttribute('data-on'); return; }
  const ai = world === 'ai';
  preview.toggleAttribute('data-ai', ai);
  preview.style.setProperty('--world', ai ? '#1a1a1c' : WORLDS[world].color);
  $('previewMedia').hidden = ai; $('previewAsk').hidden = !ai;
  const meta = $('previewMeta'); meta.textContent = '';
  if (ai) meta.textContent = 'Halo’s AI · ask about a portfolio, a template or how to add AI to your site';
  else {
    const f = featured[world];
    $('previewMedia').style.setProperty('--tone', f ? f.tone : '#cfcfca');
    const b = document.createElement('b'); b.append(document.createElement('i'), WORLDS[world].label);
    meta.append(b, ' ' + PREVIEW_META[world]);
  }
  preview.setAttribute('data-on', '');
}
if (finePointer.matches) {
  choices.forEach((c) => {
    c.addEventListener('pointerenter', (e) => { if (e.pointerType !== 'mouse') return; placeAnchor(e.clientX, e.clientY, activeChoice === null); setActive(c.dataset.choice); });
    c.addEventListener('pointermove', (e) => { if (e.pointerType === 'mouse') placeAnchor(e.clientX, e.clientY); });
    c.addEventListener('pointerleave', () => setActive(null));
    c.addEventListener('focus', () => { if (!c.matches(':focus-visible')) return; const r = c.getBoundingClientRect(); placeAnchor(r.left + r.width / 2, r.top, true); setActive(c.dataset.choice); });
    c.addEventListener('blur', () => setActive(null));
  });
} else anchor.hidden = true;
function setFocusWorld(w) { if (w) stage.dataset.focus = w; else delete stage.dataset.focus; }
document.querySelector('.site-mark').addEventListener('click', (e) => e.preventDefault());

/* ---- stat counters ---- */
function runCounters(imm) {
  document.querySelectorAll('[data-count]').forEach((dd, i) => {
    const v = dd.dataset.count, m = v.match(/^([\d.]+)(.*)$/);
    if (!m) return;
    if (imm || reduced.matches) { dd.textContent = v; return; }
    const num = Number(m[1]), dec = m[1].includes('.') ? 1 : 0, suf = m[2];
    dd.textContent = `0${suf}`;
    tween({ from: 0, to: num, duration: 1.6, delay: 0.8 + 0.1 * i, onUpdate: (x) => { dd.textContent = `${x.toFixed(dec)}${suf}`; } });
  });
}

/* ================= image wall (ring) ================= */
const wall = $('wall'), lens = $('wallLens'), ring = $('wallCenter');
const SLOTS = Math.min(16, SLIDES.length);
const slotAngle = (i, rot, n = 16) => (((((360 * i) / n + rot + 180) % 360) + 360) % 360) - 180;
const slotIndex = (i, rot, len, n = 16) => { const a = Math.floor(((360 * i) / n + rot + 180) / 360); return (((i - a * n + Math.min(3, len - 1)) % len) + len) % len; };

const cards = [];
function fillCard(c, idx) {
  const r = SLIDES[idx];
  c.slide = idx;
  c.el.dataset.world = r.world;
  c.face.style.setProperty('--tone', r.tone);
  c.el.setAttribute('aria-label', r.title);
  if (r.image) { c.img.src = r.image; c.img.alt = r.title; c.img.hidden = false; } else { c.img.removeAttribute('src'); c.img.hidden = true; }
}
for (let t = 0; t < SLOTS; t++) {
  const el = document.createElement('a');
  el.className = 'card'; el.href = '#'; el.draggable = false; el.dataset.card = '';
  el.style.opacity = '0';
  el.style.transform = `rotateY(${slotAngle(t, 0, SLOTS)}deg) translateZ(-1800px) scale(2.3)`;
  el.innerHTML = '<span class="tilt"><span class="frame" aria-hidden="true"></span><span class="face"><img class="slideImage" alt="" draggable="false" hidden></span></span>';
  const c = {
    el, tilt: el.querySelector('.tilt'), face: el.querySelector('.face'), img: el.querySelector('.slideImage'),
    rx: mkSpring(0, { stiffness: 180, damping: 24 }), ry: mkSpring(0, { stiffness: 180, damping: 24 }), rz: mkSpring(0, { stiffness: 180, damping: 24 }),
    slide: -1, vis: '', op: ''
  };
  fillCard(c, slotIndex(t, 0, SLIDES.length, SLOTS));
  el.addEventListener('dragstart', (e) => e.preventDefault());
  el.addEventListener('click', (e) => { e.preventDefault(); });
  el.addEventListener('pointermove', (e) => {
    if (e.pointerType !== 'mouse' || reduced.matches) return;
    const r = el.getBoundingClientRect();
    const nx = clamp((e.clientX - r.left) / r.width - 0.5, -0.5, 0.5), ny = clamp((e.clientY - r.top) / r.height - 0.5, -0.5, 0.5);
    c.rx.set(-(20 * ny)); c.ry.set(24 * nx); c.rz.set(45);
  });
  const reset = () => { c.rx.set(0); c.ry.set(0); c.rz.set(0); };
  el.addEventListener('pointerleave', reset); el.addEventListener('blur', reset);
  ring.appendChild(el); cards.push(c);
}

// intro / interaction state
let formation = 0, expansion = 0, entered = false;
const rot = { m: 0 };
const rotSpring = mkSpring(0, { stiffness: 150, damping: 27, mass: 1 });
let offA = 0, sizeO = 1, wallScale = 1;
function measureWall() {
  const r = wall.getBoundingClientRect();
  offA = 0.44 * innerHeight - (r.top + r.height / 2);
  wallScale = parseFloat(getComputedStyle(ring).getPropertyValue('--wall-scale')) || 1;
  sizeO = Math.min(1.05, (innerWidth - 32) / 1240, (0.66 * innerHeight) / 800) / wallScale;
}
measureWall(); addEventListener('resize', measureWall);

function ringGeometry(e, d = 450) {
  const tilt = -22 * (1 - e), radius = 460 + 1340 * e;
  return {
    radius,
    anchor: -radius * Math.sin((tilt * Math.PI) / 180) + 460 * Math.sin((-22 * Math.PI) / 180) * (1 - e),
    perspective: 1000 + (d - 1000) * e,
    tilt, roll: -20 * (1 - e), scale: 0.52 + (2.3 - 0.52) * e * e
  };
}
function placeCard(angle, form, g) {
  const n = (((angle + 180) % 360) + 360) % 360, a = n / 360;
  const i = clamp((form - 0.7 * a) / 0.3), s = 1 - (1 - i) ** 3;
  const yaw = -180 + n * s, rad = (yaw * Math.PI) / 180, R = g.radius - (1 - s) * a * 24;
  const x = -Math.sin(rad) * R, z = -Math.cos(rad) * R;
  const depth = z * Math.cos((g.tilt * Math.PI) / 180);
  return { x, z, yaw, scale: g.scale, appear: Math.min(1, 2.5 * i), behind: depth >= g.perspective - 70 };
}

// wheel / drag / touch
let hoverIdx = -1, slow = 1, drag = null, touch = null, wasDragged = false, frameCount = 0;
const mouse = { x: -1e4, y: -1e4, inWall: false };
const dominant = (x, y) => (Math.abs(x) >= Math.abs(y) ? x : y);
addEventListener('wheel', (e) => {
  if (e.ctrlKey || e.metaKey) return;
  e.preventDefault();
  if (!entered) return;
  const k = e.deltaMode === 1 ? 16 : e.deltaMode === 2 ? innerHeight : 1;
  const d = dominant(e.deltaY, e.deltaX) * k;
  rot.m += 0.5 * clamp(d, -480, 480);
  if (reduced.matches) rotSpring.jump(rot.m);
}, { passive: false });
wall.addEventListener('pointerdown', (e) => { if (e.button !== 0 || !entered) return; drag = { x: e.clientX, y: e.clientY, a: rot.m, moved: false }; wall.style.cursor = 'grabbing'; });
wall.addEventListener('pointerenter', (e) => { mouse.inWall = e.pointerType === 'mouse'; });
wall.addEventListener('pointerleave', () => { mouse.inWall = false; });
addEventListener('pointermove', (e) => {
  mouse.x = e.clientX; mouse.y = e.clientY;
  if (!drag) return;
  const dx = e.clientX - drag.x, dy = e.clientY - drag.y;
  if (Math.max(Math.abs(dx), Math.abs(dy)) > 4) drag.moved = true;
  rot.m = drag.a + 0.2 * dominant(dx, dy);
  if (reduced.matches) rotSpring.jump(rot.m);
});
const endDrag = () => { if (!drag) return; wasDragged = drag.moved; drag = null; wall.style.cursor = ''; setTimeout(() => { wasDragged = false; }, 50); };
addEventListener('pointerup', endDrag); addEventListener('pointercancel', endDrag);
wall.addEventListener('click', (e) => { if (wasDragged) e.preventDefault(); }, true);
addEventListener('touchstart', (e) => {
  touch = null;
  if (e.touches.length !== 1 || !entered) return;
  if (e.target instanceof Element && e.target.closest('[data-card]')) return;
  touch = { x: e.touches[0].clientX, y: e.touches[0].clientY, a: rot.m };
}, { passive: true });
addEventListener('touchmove', (e) => {
  if (!touch || e.touches.length !== 1) return;
  rot.m = touch.a + 0.2 * dominant(e.touches[0].clientX - touch.x, e.touches[0].clientY - touch.y);
  if (reduced.matches) rotSpring.jump(rot.m);
}, { passive: true });
addEventListener('touchend', () => { touch = null; }); addEventListener('touchcancel', () => { touch = null; });

function updateWall(delta) {
  const s = Math.min(delta / (1000 / 60), 3);
  if (mouse.inWall && !drag && entered) {
    if (frameCount++ % 3 === 0) { const hit = document.elementFromPoint(mouse.x, mouse.y)?.closest('[data-card]'); hoverIdx = hit ? cards.findIndex((c) => c.el === hit) : -1; }
  } else hoverIdx = -1;

  const f = expansion, g = ringGeometry(f, 450), A = wallScale * (sizeO + (1 - sizeO) * f);
  ring.style.setProperty('--intro-offset', `${offA * (1 - f) + g.anchor * A}px`);
  lens.style.perspective = `${g.perspective * A}px`;
  lens.style.perspectiveOrigin = `50% calc(50% + ${offA * (1 - f)}px)`;
  ring.style.setProperty('--intro-tilt', `${g.tilt}deg`);
  ring.style.setProperty('--intro-roll', `${g.roll}deg`);
  ring.style.setProperty('--intro-x', `${160 * Math.sin((g.roll * Math.PI) / 180) * A}px`);
  ring.style.setProperty('--intro-sz', String(A));
  ring.style.setProperty('--intro-scale', String(sizeO + (1 - sizeO) * f));
  if (entered && wall.hasAttribute('data-entering')) wall.removeAttribute('data-entering');

  slow += ((hoverIdx >= 0 ? 0.2 : 1) - slow) * Math.min(1, 0.08 * s);
  if (!drag && !wall.contains(document.activeElement) && !reduced.matches) {
    const speed = entered ? 0.3 : 0.3 * (0.5 + 0.5 * f) * clamp((78 - rot.m) / 20);
    rot.m += speed * slow * s;
  }
  rotSpring.set(rot.m);
  const C = rotSpring.get();

  for (let t = 0; t < cards.length; t++) {
    const c = cards[t], angle = slotAngle(t, C, SLOTS), abs = Math.abs(angle);
    const idx = slotIndex(t, entered ? C : 0, SLIDES.length, SLOTS);
    if (idx !== c.slide) fillCard(c, idx);
    const setVis = (v) => { if (c.vis !== v) { c.vis = v; c.el.style.visibility = v; } };
    if (abs > 96 && entered) { setVis('hidden'); continue; }
    setVis('visible');
    const p = placeCard(angle, formation, g);
    if (p.behind) { setVis('hidden'); continue; }
    c.el.style.transform = `translate3d(${p.x}px,0px,${p.z}px) rotateY(${p.yaw}deg) rotateZ(0deg) scale(${p.scale})`;
    const outside = p.z > (g.radius * g.radius) / g.perspective;
    if (outside !== c.el.hasAttribute('data-outside')) c.el.toggleAttribute('data-outside', outside);
    const op = String(Math.round(1000 * p.appear) / 1000);
    if (c.op !== op) { c.op = op; c.el.style.opacity = op; }
    c.tilt.style.transform = `translateZ(${c.rz.get()}px) rotateX(${c.rx.get()}deg) rotateY(${c.ry.get()}deg)`;
  }
}

/* ================= loader ================= */
const loader = $('galleryLoader'), brand = $('loaderBrand'), caption = $('loaderCaption'), odo = $('odometer'), countEl = $('loaderCount');
'Halo.'.split('').forEach((ch, i) => {
  const m = document.createElement('span'); m.className = 'loaderMask';
  const s = document.createElement('span'); s.textContent = ch; s.style.animationDelay = `${0.1 + 0.08 * i}s`;
  m.appendChild(s); brand.appendChild(m);
});
const digitCols = [0, 1, 2].map(() => {
  const d = document.createElement('span'); d.className = 'odometerDigit';
  const col = document.createElement('span');
  for (let i = 0; i < 10; i++) { const n = document.createElement('span'); n.textContent = i; col.appendChild(n); }
  d.appendChild(col); odo.appendChild(d); return col;
});
function setOdometer(v) {
  String(clamp(Math.round(v), 0, 999)).padStart(3, '0').split('').map(Number).forEach((d, i) => { digitCols[i].style.transform = `translateY(${-(10 * d)}%)`; });
}
setOdometer(0);
// caption scramble: 45 ms per resolved char, 6 random trailing chars, starts after 350 ms
(() => {
  const text = 'Loading Gallery', pool = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789#%&*+=';
  const start = performance.now() + 350; let raf;
  const step = () => {
    const n = performance.now() - start;
    if (n >= 0) {
      const k = Math.floor(n / 45);
      caption.textContent = [...text].map((ch, i) => (ch === ' ' || i < k ? ch : i < k + 6 ? pool[Math.floor(Math.random() * pool.length)] : '')).join('') || ' ';
      if (k >= text.length) return;
    }
    raf = requestAnimationFrame(step);
  };
  raf = requestAnimationFrame(step);
})();

// Simulated preview loading: 19 front items settle one by one (no media to fetch while cards are gray placeholders).
const FRONT_TOTAL = 19;
let ready = 0, loading = true, countTween = null, countVal = 0;
const loadStart = performance.now();
const loadTimer = setInterval(() => {
  // one item settles every 85 ms, derived from elapsed time so throttled timers catch up
  const next = Math.min(FRONT_TOTAL, Math.floor((performance.now() - loadStart) / 85));
  if (next === ready) return;
  ready = next;
  countEl.setAttribute('aria-valuenow', String(Math.round((ready / FRONT_TOTAL) * 100)));
  if (countTween) countTween.stop();
  countTween = tween({ from: countVal, to: (ready / FRONT_TOTAL) * 100, duration: 0.65, onUpdate: (v) => { countVal = v; setOdometer(v); } });
  if (ready >= FRONT_TOTAL) { clearInterval(loadTimer); loading = false; tryReveal(); }
}, 85);

let formed = false, revealing = false, done = false, completed = false, skipped = false, loaderGone = false;
const anims = [];
function complete(imm = false) { if (completed) return; completed = true; entered = true; if (imm) setEntrance(true, true); }
function reveal() {
  frameEl.setAttribute('data-revealed', '');
  loader.setAttribute('data-exit', '');
  setTimeout(() => { loader.style.display = 'none'; }, 900);
  loaderGone = true; flyBrand();
}
function tryReveal() {
  if (!formed || loading || revealing || done) return;
  revealing = true; reveal();
  anims.push(tween({ from: 0, to: 1, duration: 1.9, ease: bezier(0.5, 0, 0.15, 1), onUpdate: (v) => { expansion = v; }, onComplete: () => complete() }));
  anims.push(tween({ from: 0, to: 1, duration: 1.05, ease: linear, onComplete: () => setEntrance(true) }));
}
function skip() {
  if (done || completed) return;
  skipped = true; revealing = true;
  anims.forEach((a) => a.stop()); clearInterval(loadTimer);
  loader.style.display = 'none'; brand.remove();
  formation = 1; expansion = 1;
  frameEl.setAttribute('data-revealed', '');
  complete(true);
}
$('skipBtn').addEventListener('click', skip);
addEventListener('keydown', (e) => { if (e.key === 'Escape' || e.key === 'Tab') skip(); });
reduced.addEventListener('change', () => { if (reduced.matches) skip(); });
anims.push(tween({
  from: 0, to: 1, duration: reduced.matches ? 0 : 2.6, ease: linear,
  onUpdate: (v) => { formation = v; },
  onComplete: () => { if (reduced.matches || skipped) return skip(); formed = true; tryReveal(); }
}));

// "Halo." flies into the header wordmark once the loader starts leaving.
function flyBrand() {
  if (skipped || !brand.isConnected) return;
  const target = document.querySelector('.wordmark-first');
  const r = brand.getBoundingClientRect(), n = target.getBoundingClientRect();
  const sc = parseFloat(getComputedStyle(target).fontSize) / parseFloat(getComputedStyle(brand).fontSize);
  const ease = bezier(0.65, 0, 0.35, 1), dx = n.left - r.left, dy = n.top - r.top;
  tween({ from: 0, to: 1, duration: 1.3, ease, onUpdate: (p) => { brand.style.transform = `translate(${dx * p}px, ${dy * p}px) scale(${1 + (sc - 1) * p})`; } });
  const dot = brand.lastElementChild;
  tween({ from: 1, to: 0, duration: 0.5, ease: bezier(0, 0, 0.58, 1), onUpdate: (o) => { dot.style.opacity = o; } });
}
function onEntranceForBrand() { if (brand.isConnected) setTimeout(() => brand.remove(), immediate ? 0 : 900); }

/* ================= main loop ================= */
let last = performance.now();
function loop(now) {
  const delta = Math.min(100, now - last); last = now;
  springs.forEach((s) => s.step(delta / 1000));
  updateWall(delta);
  parallaxLayers.forEach(({ el, d }) => { el.style.transform = `translate(${-(px.get() * d)}px, ${-(py.get() * d * 0.5)}px)`; });
  anchor.style.transform = `translate(${ax.get()}px, ${ay.get()}px)`;
  requestAnimationFrame(loop);
}
requestAnimationFrame(loop);
})();
</script>
</body>
</html>
```
