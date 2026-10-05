# Build Prompt: Skyler (Yicen) Xu — Application Profile Website (for BMO Customer Service Representative, part-time)

> Paste this whole file into Claude Code in an empty project folder. Build in the phases listed at the end, and stop after each phase so I can review it locally in the browser.

---

## 0. What this is

A single-page, scroll-driven personal website that supplements my job application to BMO (part-time Customer Service Representative, Vancouver). It is a sequence of full-screen "scenes". Each scene has one talking-head video of me (transparent background, only my figure), and the scene's video **auto-plays when the scene scrolls into view and stops when it leaves**. The mood is minimal, calm, trustworthy, **blue-toned**, with Apple-product-page-quality motion.

Tech and delivery:
- Vite + vanilla JavaScript + CSS (no heavy framework). Use **GSAP + ScrollTrigger** for scroll-linked animation. Lenis smooth scroll is optional.
- Runs locally with `npm run dev`. Code is saved on GitHub and deployed from GitHub to **Vercel** (static build, `npm run build` → `dist`). Add a `README.md` with these steps.
- Desktop-first (recruiters will most likely watch on a laptop). Provide a simplified but still working layout below 900px width.
- All text content, timings, dates and asset paths live in **one file: `src/config.js`**, so I can edit without touching the code.
- **Do not invent facts about me.** Anywhere I have not provided text, use an obvious placeholder like `[caption line 1]`.

---

## 1. Visual style (derived from my four reference files)

1. **`风格参考_蓝色.pdf`** (my Canva portfolio — palette and UI language)
   - White / very light grey canvas with **macOS-style pale-blue gradient** elements (folder blue: light `#A9CBF0` → deeper `#3F6FAF`), a macOS-blue accent (`#0A74E8`), and a deep navy (`#0B2A5B`) for text and dark transitions.
   - Photos appear as **slightly tilted white-bordered cards with rounded corners and soft shadows**.
   - Frosted-glass rounded panels (like a macOS menu), and a **light-blue gradient bar** along the bottom with my name.
   - An optional macOS window frame (three traffic-light dots) — use it **only** for the Schedule scene.
2. **`首页排版_文字在左_人物在右.png`** (hero layout)
   - Cover: **huge, tight-tracked grotesque headline on the left**, my cut-out portrait on the right slightly overlapping the end of the text, small parenthesised labels like `( VANCOUVER )`, and a small pill button.
3. **`人物在中间_照片文字等等在两边_创造视觉中心.png`** (center-figure layout)
   - Video scenes: **my figure in the exact center**, with tilted rounded photo cards and short labels floating on both sides, creating a strong visual center.
4. **`极简画面设计.png`** (minimal Apple-style)
   - Lots of white space, a centered bold headline, one small colored accent, one pill button. Nothing decorative that does not carry meaning.

Typography: a bold, tightly tracked grotesque (use **Inter Tight** via `@fontsource`, fallback Helvetica Neue / Arial). Headlines mix weights in one line (e.g. **bold** + light), uppercase for the cover name, `letter-spacing: -0.04em` on large type. Body text small, medium grey-navy.

Color tokens (CSS variables): `--bg:#FFFFFF; --bg-soft:#F5F7FA; --navy:#0B2A5B; --blue:#0A74E8; --blue-light:#A9CBF0; --blue-mid:#3F6FAF; --text:#0B1B33; --muted:#6B7A90;`

### Motion style (reference: https://www.apple.com/imac/)
- **Scroll-linked and scrubbed**: each scene is pinned (sticky) while its elements animate with scroll progress; large type fades/scales in; images drift with subtle parallax; elements enter with a slight scale (0.96→1), blur-to-sharp and upward translate.
- Easing: `cubic-bezier(0.22, 1, 0.36, 1)` for entrances, long gentle durations (0.8–1.4s). No bouncy or cartoonish motion.
- Scene-to-scene transitions feel like a keynote: crossfade through white, or briefly through deep navy. Use `scroll-snap-type: y proximity` so each scene settles.
- Respect `prefers-reduced-motion` (fall back to simple fades).

---

## 2. Global behaviors

### 2.1 Start gate (required for sound)
Browsers block audio autoplay, so the first screen is a minimal full-screen **"Click to start"** gate (white, my name small, one blue pill button `Start`). Clicking it unlocks audio, fades the gate out, and reveals the Cover. After that, videos autoplay **with sound**.

### 2.2 Auto-play / auto-stop logic (critical)
- Use `IntersectionObserver` (threshold ≈ 0.6) or ScrollTrigger to determine the **single active scene**.
- When a scene becomes active: **pause and reset (currentTime = 0) every other scene's video**, then play this scene's video **from 0** with sound.
- When it leaves: pause immediately. Scrolling back replays from the start.
- Preload metadata of the next scene's video. Use `playsinline`, `preload="metadata"`.
- Provide a small, quiet **mute/unmute** toggle and a **captions (CC) toggle** fixed at the top right, next to a **"Download résumé"** button.

### 2.3 Subtitles (required)
- Custom caption overlay (bottom center, white text on soft navy translucent pill), driven by the video's `currentTime`.
- Cues are in `config.js` per scene: `captions: [{start: 0.0, end: 2.5, text: "[caption line 1]"}, ...]`. Leave placeholders; I will paste my transcript later.
- Captions on by default, toggleable.

### 2.4 Transparent-video placeholder system
My videos will be background-removed (only my figure). I will make them myself.
- Per scene, support two sources: `.webm` (VP9 with alpha — Chrome/Firefox/Edge) and `.mov` (HEVC with alpha — Safari). Use `<source>` with correct `type`/codecs, WebM first, and pick HEVC on Safari.
- **Until real files exist**, render a clean placeholder (soft blue gradient silhouette of a standing person in the correct position/size, label "VIDEO PLACEHOLDER — 19 s") and run a **virtual clock** for the scene's configured duration, so all timed cues (captions, timeline, ping-pong, hand pointing) can be developed and tested now. Switching to the real video must require **only dropping files into `public/assets/video/`** (the code detects the file and switches automatically; `USE_PLACEHOLDER_VIDEO` flag in config).
- All timed effects must read time from one abstraction (`getSceneTime()`) that works for both the real video and the virtual clock.

---

## 3. Scenes (in scroll order)

### Scene 0 — Start gate
As in 2.1.

### Scene 1 — Cover (no video)
Layout follows reference 2 (text left, person right).
- Left: giant headline **SKYLER (YICEN) XU** (three lines or two, tight tracking), under it small text `UBC Sauder · BCom · Year 2`, plus a one-line placeholder intro `[one-line intro]` and `( VANCOUVER, BC )`.
- Right: my cut-out portrait `public/assets/img/portrait-cutout.png` (placeholder if missing), overlapping the end of the headline slightly, with a subtle float on scroll.
- **Reserve a free area** (a clearly marked empty slot in the layout, configurable via `coverExtraSlot`) where I may add extra content later (e.g., a short line or small badges).
- Small blue pill: `Scroll ↓` and the fixed top-right "Download résumé".
- Scroll hint animates gently. On scroll, the cover dissolves into Scene 2 through white.

### Scene 2 — "My link with BMO" (video, **19 s**)
Eyebrow label: `MY LINK WITH BMO`. This scene has two stages.

**Stage A — Entrance (before the video plays)**
1. A large **frosted-glass emblem** is shown in front: a stylized **"M" with a single horizontal bar beneath it**, rendered as grey, translucent, blurred glass (Apple-style `backdrop-filter: blur(30px) saturate(1.4)`, soft inner highlight and shadow, subtle parallax with the pointer). It is a simplified stylized shape, **not** the official logo artwork; build it as inline SVG.
2. Behind the glass is my photo `bmo-pose-full.jpg`: several people arranged to form the BMO-logo shape, with **me at the bottom with both arms extended horizontally (180°, like the bar under the M)**.
3. The glass emblem fades/blurs away, revealing the photo.
4. **Natural photo→video transition**: I will supply `bmo-pose-skyler-cutout.png` (just me, aligned pixel-perfect over the full photo). Fade out the full photo, all other people and the background (opacity → 0 over ~1s), leaving only my cut-out. Then **morph my cut-out into the video figure**: crossfade + scale + slight upward translate + a tiny motion blur, so it reads like "the person stands up and steps forward" into the live video. Sizes may not match, so animate scale/translate from the photo person's box (`bmoPhoto.personBox`, normalized x/y/w/h in config) to the video figure's box. The video figure ends **centered**.
5. Only when the transition completes does the video start (or: start the video at the moment the crossfade begins; make this a config option `bmo.videoStartsAt`).

**Stage B — During the video**
- 0–3 s: I speak, figure **centered**.
- From **3 s**: I start walking; the video frame slides smoothly to the **far left** of the screen (my figure walking sideways toward the left, keeping the body in frame).
- On the **right side** appears a **horizontally scrolling timeline** showing my journey with BMO: **photo cards move continuously from right to left**, synchronized with the video from 3 s to the end.
  - **4 photo slots, in this order:** (1) doing research, (2) business proposal, (3) rehearsal, (4) offline presentation — winning the championship. Add **2 extra optional empty slots** after them (config `timeline.extraSlots`) in case I add more.
  - Each card: rounded corners, white border, slight alternating tilt (±2–4°), soft shadow. **No captions on the photos.**
  - **Under each card a small text box that travels with it** (same movement) for its date, defaulting to `[date]`. Dates are editable in `config.js`.
  - Images load from `public/assets/img/timeline-1-research.jpg`, `timeline-2-proposal.jpg`, `timeline-3-rehearsal.jpg`, `timeline-4-champion.jpg`; use clearly marked placeholders if missing.
- A thin blue progress line under the timeline fills as it moves.

### Scene 3 — "Bank of China · Similar job experience" (video, **18 s**)
Eyebrow label: `BANK OF CHINA · SIMILAR EXPERIENCE`.
- Entrance uses the same technique as Scene 2 Stage A, but with my **front-desk work photo**: `boc-frontdesk-full.jpg` + `boc-frontdesk-skyler-cutout.png`. Background and other people fade out; my cut-out transitions naturally into the video figure, **centered**. (No glass emblem here.)
- Layout follows the center-figure reference: video figure in the middle, tilted rounded photo cards and short labels at the sides (labels can be short role keywords I will fill in via config; default empty).
- **Reserve a photo slot** (rounded tilted card, to one side of the figure) for a photo of **my high-school teacher whom I ran into at the bank**. It appears at a configurable video time `bocTeacherPhoto.showAt` (default `[seconds TBD]`) with a gentle scale/fade-in, and fades out at `hideAt`. Image path: `public/assets/img/teacher.jpg`.

### Scene 4 — "Strength · A very focused person" (video, **16 s**)
Eyebrow label: `MY STRENGTH`; headline `A very focused person` (bold + light weight mix).
- My cut-out figure **centered**.
- **Ping-pong animation** tied to my story about watching a friend's table tennis match, with my eyes "following the ball":
  - Two minimal table-tennis paddles, one at the **left** and one at the **right** of the figure (flat blue / navy vector style).
  - A white ball flies **between the paddles along a semicircular arc above my head**, back and forth.
  - **Exact timing (seconds in this scene's video):** ball at the **left** paddle at **2.69 s** → at the **right** paddle at **4.32–4.36 s** → back at the **left** at **5.61 s** → then **disappears** (quick fade/scale-out). The ball is invisible before ~2.0 s (fade in just before 2.69).
  - Implement as a time-driven path (not a free-running loop): ball position is a pure function of `getSceneTime()`, interpolating along the arc between those keyframes; paddles do a small "hit" nudge at each contact. Keyframes live in `config.js` (`pingpong.keyframes`) so I can tweak them.
  - A subtle soft shadow line below the arc is fine; keep it minimal.

### Scene 5 — "Schedule" (video, **6 s**)
Eyebrow label: `MY SCHEDULE`. Purpose: show clearly that I have a lot of availability for part-time work.
- My figure centered. In the video I point my **hand to the left, then to the right**.
- Place **two large image frames** (macOS-window style with traffic-light dots, rounded, soft shadow) — **left** and **right** of me — each holding a screenshot of my **class timetable**:
  - Left frame label: **`Sep – Dec 2026`** → `schedule-2026-fall.png`
  - Right frame label: **`Jan – Apr 2027`** → `schedule-2027-winter.png`
  - Frames should be big enough that the timetable text is clearly readable. Reserve the space even when images are missing (placeholder "Drop timetable here").
  - If I point right first in the video, swap via `schedule.leftIsFirst` in config.
- Beside/under the frames, a short line: **"Class times only — all other times are available for work."** (use this exact wording; it can be edited in config).
- Frames reveal in sync with the hand-point moments (configurable `schedule.leftRevealAt`, `schedule.rightRevealAt`, defaults `[TBD]`; if unset, reveal left at 1.5 s and right at 3.5 s).

### Scene 6 — Closing (no video)
- Minimal, centered, mostly white with a soft blue gradient bar at the bottom (like the PDF reference).
- Headline: **Thank you for watching.**
- Subline: **I would love to share more in an interview.**
- Buttons: blue pill `Download résumé` (file: `public/assets/docs/Skyler_Xu_Resume.pdf`), and an optional contact line (email/phone, controlled by `contact.show`, default `false`).
- Footer: `@Skyler (Yicen) Xu`.

---

## 4. Required assets (create folders and placeholders; I will drop in real files)

```
public/assets/
  video/   bmo.webm bmo.mov  boc.webm boc.mov  strength.webm strength.mov  schedule.webm schedule.mov
  img/     portrait-cutout.png
           bmo-pose-full.jpg  bmo-pose-skyler-cutout.png
           boc-frontdesk-full.jpg  boc-frontdesk-skyler-cutout.png
           teacher.jpg
           timeline-1-research.jpg  timeline-2-proposal.jpg  timeline-3-rehearsal.jpg  timeline-4-champion.jpg
           schedule-2026-fall.png  schedule-2027-winter.png
  docs/    Skyler_Xu_Resume.pdf
```
Durations: bmo 19 s, boc 18 s, strength 16 s, schedule 6 s. Cover and closing have no video.

---

## 5. `src/config.js` must expose (at minimum)

`USE_PLACEHOLDER_VIDEO`, per-scene `{ duration, sources, captions[] }`, `cover` text and `coverExtraSlot`, `bmo.videoStartsAt`, `bmo.walkStartsAt: 3`, `bmoPhoto.personBox`, `timeline.items[{ image, date }]` and `timeline.extraSlots`, `bocTeacherPhoto{showAt,hideAt}`, `pingpong.keyframes`, `schedule{leftIsFirst,leftRevealAt,rightRevealAt,text}`, `contact{show,email,phone}`, `resumePath`.

---

## 6. Quality bar / acceptance checklist

- [ ] Click-to-start gate works; sound plays after it.
- [ ] Scrolling into a scene plays its video from 0; leaving pauses it; no two videos ever play at once.
- [ ] Captions sync to video time and can be toggled.
- [ ] Placeholder mode with the virtual clock lets every timed effect be tested without real videos.
- [ ] Scene 2: glass emblem → photo → natural morph into the video figure; others fade out; at 3 s the figure moves left and the timeline (4 photos + date boxes) scrolls right-to-left.
- [ ] Scene 3: photo → video transition, teacher-photo slot appears at the configured time.
- [ ] Scene 4: ball hits exactly at 2.69 s (left), 4.32–4.36 s (right), 5.61 s (left), then disappears.
- [ ] Scene 5: two large, readable timetable frames labeled `Sep – Dec 2026` and `Jan – Apr 2027`.
- [ ] Transitions feel like a keynote; no jank at 60 fps on a normal laptop; page weight reasonable (lazy-load images, preload only the next video's metadata).
- [ ] Download résumé button works. Visual language stays minimal and blue throughout.
- [ ] `README.md` documents: how to replace assets, how to export transparent videos (WebM VP9 alpha + HEVC alpha for Safari), how to edit `config.js`, and GitHub → Vercel deployment.

---

## 7. Build phases (stop after each for my review)

1. **Foundation:** Vite project, design tokens, fonts, `config.js`, start gate, scene scaffolding, scroll/active-scene manager, placeholder video + virtual clock, caption overlay, top-right controls.
2. **Cover + Closing** scenes with final styling.
3. **Scene 2 (BMO):** glass emblem, photo→video morph, walk-left, timeline with date boxes.
4. **Scene 3 (Bank of China)** incl. teacher-photo slot.
5. **Scene 4 (Strength)** ping-pong, time-driven.
6. **Scene 5 (Schedule)** frames and reveal timing.
7. **Polish:** keynote-style transitions between scenes, reduced-motion, mobile fallback, performance, README, Vercel deploy notes.
