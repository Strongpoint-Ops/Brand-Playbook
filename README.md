# Strongpoint — brand & design playbook

**Target: https://brand.strongpointops.com**

The source of truth for how every Strongpoint property looks. A site that has
drifted from this repo is the thing that is wrong.

**The playbook is a page, not a PDF:** open `site/index.html`. Each property
card renders in that property's actual theme, so the palettes are the document
rather than a picture of it.

```
tokens/
  strongpoint.css      shared tokens — every variable with a neutral default,
                       every invariant stated in a comment
  theme-parent.css     strongpointops.com          light ground, steel
  theme-principal.css  principal.strongpointops.com graphite, field olive
  theme-build.css      build.strongpointops.com     cool graphite, safety orange
  theme-advisory.css   consult.strongpointops.com  deep ground, field green
  theme-dashboard.css  dashboard.strongpointops.com near-black, cyan (+ data palette)
site/
  index.html           the playbook — palettes, type, MOTION (six live demos),
                       COMPONENTS (six live specimens), rules, audit
docs/
  audit.md             what the live sites actually measure, and what to change
```

## How to use it

```html
<link rel="stylesheet" href="tokens/strongpoint.css">
<link rel="stylesheet" href="tokens/theme-build.css">
```

Shared file first, then exactly one theme. Every property in this group is
single-file HTML with no build step, so in practice you paste the `:root` blocks
inline instead of linking. That is fine — what is not fine is changing a token
in a site and not here.

## The one-sentence version

Every Strongpoint property shares a skeleton — a condensed display face, a mono
eyebrow, hairline rules, registration corner marks, the same type scale and
layout metrics, and steel blue `#6f8fa8` reserved for links that leave the
property. Each one owns its ground, its accent, its typefaces and one signature
motion. Vary several of those at once; never vary the skeleton.

## Colour: three gates, all measurable

An accent ships only if it clears all three.

1. **≥ 4.5:1 against its own ground.** It is used for link text, so WCAG AA for
   normal text — not the 3:1 large-text allowance.
2. **≥ 60° of hue from every sibling accent.** This is what stops two
   dark-grounded sites reading as one company.
3. **Not the family colour.** `#6f8fa8` means "this link leaves the site."

Current state:

| Company | Accent | Hue | Contrast on its ground |
| --- | --- | --- | --- |
| Strongpoint | `#4c7094` | 210 | 4.63:1 |
| Principal | `#8a9c6f` | 84 | 6.33:1 |
| Build | `#e07a3c` | 23 | 6.13:1 |
| Advisory | `#7fa968` | 99 | 6.92:1 |
| Dashboard | `#7df3ff` | 186 | 15.51:1 |

Worst pair is Parent ↔ Dashboard at 24°, which fails gate 2 and is kept anyway:
one is muted steel on near-white, the other saturated cyan on near-black, and
ground plus saturation separate them where hue does not. That is the system's
only exemption, and it is written down rather than left as an accident.

## Two changes this playbook asks for

- **Parent accent `#5980a6` → `#4c7094`.** The original measures **3.71:1** on
  `#f2f2f3` and fails AA for link text.
- **Principal accent `#c3893c` → `#8a9c6f`.** Brass sat **12°** from Build's
  orange — both dark-grounded, similarly saturated, similarly light. They were
  meant to read as separate companies and did not. Field olive opens the gap to
  61° and costs one token.

Neither is applied to the live sites yet. See `docs/audit.md`.

## Adding a fifth company

1. Pick a ground. Light is the parent's; a second light property needs a reason.
2. Pick an accent, run it against all four existing hues, redo it if anything is
   under 60°.
3. Check contrast on the ground; adjust until it clears 4.5:1.
4. Pick an unused condensed display face and **reuse an existing body face** —
   the cheapest family tie there is.
5. Copy a theme file, change the fourteen values, change nothing else.
6. Add it to the audit table and to every sibling's 404 destination list.


## Motion

Six movements, documented with the live demo running next to the rule:

| Movement | Owner | Mechanism |
| --- | --- | --- |
| Dashed flow | Build | `stroke-dashoffset` keyframes, 2.6s |
| Instrument ring | Principal | SMIL `animateTransform` about `0 0`, 24s |
| Rim pulse | shared | `r` + `opacity`, ease-out, 2.8s |
| Reveal | shared | `scaleX` from left, 700ms, 80ms stagger |
| Hairline underline | shared | `border-bottom-color`, 220ms |
| Dim the rest | shared | `opacity` to .16, 250ms |

One signature per property. Advisory deliberately has none — the product is a
document, and stillness is the signal.

**`prefers-reduced-motion` does not reach SMIL.** The CSS media query zeroes
every duration token, but an `<animateTransform>` keeps running for someone who
asked for stillness. The playbook page carries a `matchMedia` guard that calls
`svg.pauseAnimations()`; any property using a SMIL signature needs to copy it.
Verified both ways: with reduce, 0 CSS animations running and `animationsPaused()`
returns true.
