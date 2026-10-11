### Mission

When invoked, you (the coding agent) must design and implement UI/UX for the user's app in the **"Apple-Class"** style: restrained, confident, physical, and emotionally warm. You are **not** cloning Apple's assets, SF Symbols, exact colors, or proprietary layouts. You are applying the _design grammar_ — the underlying physics and restraint — to build something original that feels as considered as Apple's work but belongs to this app.

If a design decision could be mistaken for a direct copy of a specific Apple product (e.g., exact iOS Control Center blur values, exact macOS window chrome, SF Pro type metrics), **change at least one meaningful variable** (hue, radius, timing curve, hierarchy) so the result reads as "inspired," not "cloned."

---
### The Five Laws (non-negotiable)

1. **Restraint beats decoration.** Remove until it breaks, then add one thing back.
2. **Motion is physics, not animation.** Everything has mass, momentum, and damping. Springs over easings; easings over linear.
3. **Hierarchy is spatial.** Depth comes from blur, shadow, and scale — not borders and dividers.
4. **Type is the interface.** Get typography right and 60% of the design is done.
5. **Color is a signal, not a coat of paint.** Neutrals dominate; accent appears only where the eye must go.
---
### Design Tokens You Must Generate

Before writing any component, produce a `tokens.ts` (or platform equivalent) with these categories. Derive values from the app's brand, not from Apple's palette.

```text

spacing      // 4-based scale, but cap at 8 steps (e.g. 4, 8, 12, 16, 24, 32, 48, 64)
radius       // 3-4 values only: sm, md, lg, pill
elevation    // 3 levels max: raised, floating, modal — expressed as (blur, y, opacity)
blur         // 2-3 levels: chrome, overlay, hero
type         // 6-8 roles only: display, title, headline, body, callout, caption, mono
color        // semantic: bg, bgElevated, bgOverlay, textPrimary, textSecondary,
             //           accent, accentMuted, success, warning, danger, separator
motion       // named curves: settle, snappy, gentle, hero + 3 durations (120/240/400ms)
```

Rules:

- **Spacing**: use a 4pt base but keep the scale short. Long scales invite inconsistency. 
- **Radius**: nested radii must feel related (inner radius ≈ outer − padding). Pill only for chips/buttons.
- **Elevation**: never mix a shadow and a border to define the same edge. Pick one.
- **Blur**: reserve heavy blur for surfaces that _float over content_. Never blur decorative elements.
- **Type**: define line-height as a ratio (1.05 for display, 1.5 for body). Track display text tight (−0.02em), body text neutral, captions slightly loose.
- **Color**: build neutrals first (5–7 steps). Add ONE accent hue. Any second accent must be a tint/shade of the first.
- **Motion**: springs should have visible damping — nothing bounces more than once unless it's a "hero" moment.
---
### Component Contract

Every component you write must satisfy this checklist. If it fails, rewrite it.

|Requirement|How to verify|
|---|---|
|**Content-first**|Remove all chrome (borders, dividers). Does it still parse? If yes, the chrome was decoration.|
|**One focal point**|Each screen has exactly one element the eye lands on first.|
|**Physical press state**|Every tappable element has a _scale_ or _opacity_ response within 80ms. No color-only hovers.|
|**Motion continuity**|When a view appears, its children stagger by ≤40ms. When it dismisses, it leaves along the same path it entered.|
|**Reduced-motion safe**|Wrap all non-essential motion in `prefers-reduced-motion`. Keep opacity fades only.|
|**Dark mode by construction**|Never hardcode `#000` or `#fff`. Neutrals must be tinted toward the accent hue by 2–5%.|
|**Hit target**|Minimum 44×44pt (or 48dp) touch area even if the visual is smaller.|
|**Focus visible**|Keyboard focus ring uses accent with 2px offset, never removed.|
|**Text scales**|Uses semantic roles, not raw px. Supports dynamic type / text scaling.

---
### Motion Physics

Use these defaults unless the brand demands otherwise:

- **Settle** (default UI): spring, stiffness ≈ 220, damping ≈ 26.    
- **Snappy** (buttons, toggles): spring, stiffness ≈ 380, damping ≈ 30.
- **Gentle** (page transitions, modals): spring, stiffness ≈ 140, damping ≈ 22.
- **Hero** (onboarding, celebration): allow one overshoot, then settle.

Rules:

- Duration never exceeds 500ms for UI. If it does, it's a scene, not a transition.
- Easing curves: prefer springs. If you must use a cubic-bezier, cap at `cubic-bezier(0.32, 0.72, 0, 1)` style — fast out, long tail.
- **Interruptibility**: every animation must be cancellable by a new gesture without popping.
---
### Typography Rules

- One family for UI, one optional for editorial. Never more.
- Weight creates hierarchy before size does. Prefer `600 → 400` over `32px → 24px`.
- Optical sizing: display text tightens tracking; body text does not.
- Number formatting: use tabular figures in tables, proportional everywhere else.
- Never center more than 3 lines of text.
- Line length: 55–75 characters for reading text.
---
### Color Rules

- Backgrounds are never pure neutral — tint them 2–5% toward the accent hue.
- Dark mode is not inverted light mode. Reduce chroma, raise contrast on text, soften shadows into glows.
- Accent is a spotlight: if more than 10% of a screen is accent-colored, it's no longer an accent.
- Gradients are allowed only when they encode depth (e.g., a subtle top-light on a surface). No rainbow gradients as decoration.
- Semantic colors (success/warning/danger) must be distinguishable by shape or icon, not hue alone.
---
### Layout Rules

- Content lives in a **single column of attention**. Multi-column layouts must have an obvious reading order.
- Alignment: pick a grid (8pt) and never break it except for optical corrections.
- Negative space is a feature. If a screen feels "empty," it's probably right.
- Group related items with _space_, not lines. Lines are a last resort.
- Edge insets: 16–24pt typical. Full-bleed only for media.
---
### The "Cool and Nice" Delta (what makes it not-Samsung)

Samsung's One UI often errs toward: heavy blur, gradient overload, busy widgets, dense chrome, aggressive animations. **Reject these.** Instead lean into:

- **Confident emptiness** — large quiet zones around a single strong element.
- **Optical depth** — subtle inner shadows, top-edge light, not flat cards.
- **Micro-physics** — a toggle that _feels_ like it has weight; a sheet that resists then yields.
- **Typographic voice** — one hero headline per screen that does the emotional work.
- **Restrained color** — neutrals with one warm or cool accent, used like punctuation.
- **Sensory feedback** — haptics on commit actions (if platform allows), never on every tap.

---

### Workflow (the agent must follow this order)

1. **Ask** — confirm the app's purpose, audience, brand color, and platform(s). Do not design in a vacuum.    
2. **Tokens** — emit `tokens.ts` first. Show it. Wait for approval.
3. **Skeleton** — build one representative screen with layout only (no color, no motion). Validate hierarchy.
4. **Skin** — apply color and typography. Screenshot or describe.
5. **Motion** — add transitions. Every motion must have a stated purpose.
6. **Audit** — run the Component Contract table against every component. Report failures.
7. **Polish** — optical alignment, empty states, loading states, error states, dark mode.

---

### Forbidden

- Copying SF Symbols, SF Pro, or any Apple proprietary asset.    
- Reproducing exact Apple UI (Control Center, Dynamic Island, macOS Dock at identical scale/hue).
- Hardcoded colors, spacing, or fonts anywhere outside `tokens.ts`.
- Animations over 500ms without explicit user request.
- Borders + shadows on the same edge.
- More than one accent hue.
- "Just in case" states that aren't reachable in the product.
---
### Definition of Done

A screen is done when:

- It passes the Component Contract table.
- It works with reduced motion, dark mode, and 200% text scaling.
- Every interactive element has a press state.
- Removing any single element makes the screen worse — i.e., nothing is decoration.
- A stranger could describe the screen's focal point in one sentence.
---
### Invocation

When the user says anything like _"design this in Apple-Class"_ or _"apply the apple-class skill,"_ begin at step 1 of the Workflow. Never skip token generation. Never produce final UI in one shot — the process _is_ the quality.