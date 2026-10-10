# Audit — the live sites against the playbook

Measured from the deployed stylesheets on 2026-09-17, not from a brief. Contrast
is WCAG 2.x relative luminance; hue is HSV degrees.

## Summary

| Property | Accent | Accent contrast | Body contrast | Display face | Mono eyebrow | Family link colour |
| --- | --- | --- | --- | --- | --- | --- |
| Strongpoint (parent) | `#5980a6` | **3.71:1 — fails AA** | 14.79:1 | Barlow Condensed ✓ | partial | none |
| Principal | `#c3893c` | 6.23:1 ✓ | 15.44:1 | Saira Condensed ✓ | ✓ | ✓ |
| Build | `#e07a3c` | 6.13:1 ✓ | 15.07:1 | Oswald ✓ | ✓ | ✓ |
| Advisory | `#7fa968` | 6.92:1 ✓ | 15.35:1 | Archivo Narrow ✓ | ✓ | ✓ |
| Dashboard (AIOS) | `#7df3ff` | 15.51:1 ✓ | 16.34:1 | Rajdhani (semi-condensed) | ✓ | ✓ |

Body contrast is excellent everywhere — 14.8:1 to 16.3:1. Nothing to do there.

## Findings, worst first

### 1. Principal and Build accents are 12° apart

`#c3893c` (hue 34) and `#e07a3c` (hue 23). Both sit on a dark graphite ground,
at similar saturation and similar value. They were built to read as two separate
companies and they do not — at a glance the two sites are the same brand in
slightly different light.

**Fix:** move Principal to field olive `#8a9c6f` (hue 84, 6.33:1). Gap becomes
61°, and olive suits an advisory practice built on land and stewardship better
than brass did. One token.

Hue separation before and after:

```
before                                  after
Principal  <-> Build          12  FAIL   Build      <-> Principal   61  ok
Parent     <-> Dashboard      24  FAIL   Parent     <-> Dashboard   24  accepted
Principal  <-> Dashboard     151  ok     Principal  <-> Dashboard  102  ok
Build      <-> Dashboard     163  ok     Build      <-> Dashboard  163  ok
Parent     <-> Principal     175  ok     Parent     <-> Principal  126  ok
Parent     <-> Build         173  ok     Parent     <-> Build      173  ok
```

### 2. The parent's accent fails AA

`#5980a6` on `#f2f2f3` is **3.71:1**. It is used for link text, which needs
4.5:1; the 3:1 allowance only covers large text. Darkening to `#486c8f` gives
4.92:1 at the same hue, so nothing about the brand changes.

### 3. The parent has no cross-property link treatment

Every other site marks outbound links with the family colour and a `↗`. The
parent — the one page whose whole job is routing people to the subsidiaries —
has neither.

### 4. Dashboard uses its display face for body text

`--font-display` and the body face are both Rajdhani. It works at console
density and short strings, but any long-form page there will read poorly.
Split the slots: keep Rajdhani for headings, add a neutral body face.

### 5. Accepted exemption: parent ↔ dashboard, 24°

Kept deliberately. Muted steel on near-white versus saturated cyan on
near-black: ground and saturation carry the separation. Recorded here so it
reads as a decision rather than an oversight.

## What has already been fixed

These were found while building the sites and are in the live repos:

- A `<button>` centres its content vertically even at `display:block`, so a
  one-line card sat 11px below its neighbours. Fixed on Principal and Build.
- `.bio` and `.struct` on Principal had no breakpoints — the page rendered
  679px wide inside a 390px viewport. Fixed.
- Principal's `.samples` list titles and summaries ran inline instead of
  stacking. Fixed.

## Not yet applied

The two accent changes above are **documented here but not in the live sites.**
Applying them is one token per site plus a re-verify pass. They are held back so
the change is a decision rather than a surprise.


## Update — 2026-10-08

Advisory added as the fifth property and built to the gates from the start:
hue 99, 6.92:1 on its own ground, 65° minimum separation from all four
siblings. It is the first property designed against this document rather than
audited into it.

Finding 1 (Principal `#c3893c` 34° vs Build `#e07a3c` 23°, **11° apart**)
is still open. Both ship live on dark grounds at similar saturation and value.
The fix remains one token on Principal.

New finding: **`prefers-reduced-motion` does not reach SMIL.** Principal's
instrument ring is a SMIL `animateTransform`, so it keeps spinning for a user
who has asked for reduced motion — the CSS media query cannot stop it. The
playbook carries a `matchMedia` guard calling `svg.pauseAnimations()`.
Principal needs the same guard; it does not have one today.
