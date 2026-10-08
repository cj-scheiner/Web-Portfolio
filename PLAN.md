# Portfolio Site — PLAN.md

Personal portfolio for Christopher (Cj) Scheiner, CS student at UNF, graduating Spring 2027. Audience is recruiters and engineers screening for SWE internships and new grad roles.

**The page has one job:** be scannable in 30 seconds and make six projects look credible, with a live link that can go on applications immediately.

v1 is a single page, targeted at four days. Speed to live matters more than completeness. Everything cut from v1 is listed in the v2 section at the bottom and is purely additive later.

---

## Scope

**In**
- One page: hero, work index, about strip, footer
- All six projects in the index, each linking out to GitHub or live
- Resume PDF link
- Contact via mailto, GitHub, LinkedIn
- Two motion effects: scroll reveals, scroll-linked hero

**Out of v1**
- Case study pages
- Any second route
- Markdown rendering
- Blog, CMS, database, auth, contact form
- Light mode
- Sticky labels, pinned sequences, horizontal scroll

No Supabase. No react-markdown. All content is a single typed data file.

---

## Stack

- Vite + React + TypeScript
- Tailwind CSS
- Framer Motion (day 3 only)
- Vercel, custom domain

No router in v1, since there's one page. No state library, no component library.

Keep it plain. Hand-written components over abstractions.

---

## Content model

Everything lives in one file: `src/content/projects.ts`

```ts
type Project = {
  name: string
  tagline: string          // one sentence, shows in the index
  year: string
  role: 'Solo' | 'Solo (AI-assisted)' | 'Team'
  stack: string[]
  href: string             // GitHub repo, or live site where one exists
}
```

The `role` field is doing real work. It's honest about what was solo and what wasn't, with no apologetic language, and it gives the index a column that carries information.

### The six projects

| name | year | role | links to |
|---|---|---|---|
| NFLTrackr | 2025 | Solo (AI-assisted) | repo |
| INTERS | 2026 | Solo (AI-assisted) | live site |
| DungeonCrawler | 2024 | Solo | repo |
| Train2Fit | 2025 | Team | repo |
| Work Order Generator | 2024 | Solo | repo |
| GridWorld Algorithm Search | 2024 | Solo | repo |

**Taglines are not optional.** Six rows of names and stacks is a list, not a portfolio. One sentence each, written from the existing READMEs. This is half an hour of work and it's the highest-value writing on the page.

---

## Design direction

Editorial and dark. Visual weight comes from typography and spacing, not effects.

### Color

```
--ink:      #0B0D10   page background
--surface:  #14171C   raised panels, index row hover
--line:     #232830   hairline rules
--text:     #E8E6E1   primary text, warm off-white not pure white
--muted:    #868D97   labels, metadata, secondary text
--accent:   #93B4FF   links, hover, active state only
```

The off-white is deliberately warm against a cool dark base. That temperature split is most of why this won't read as a default dark theme. No second accent, no gradients.

### Type

- **Display:** Unbounded. Hero and section headings only.
- **Body:** Geist Sans. Neutral on purpose so the display carries personality.
- **Utility:** Geist Mono. Labels, years, stack tags, index columns, tabular figures.

```
hero      clamp(3.5rem, 9vw, 8rem)   Unbounded 500, tracking -0.03em
h2        1.75rem                    Unbounded 400
body      1.0625rem / 1.7            Geist Sans
label     0.75rem                    Geist Mono, uppercase, tracking 0.08em
```

The jump from hero to body should feel abrupt. Timid type scales are what make a portfolio look like a template.

### Layout

Single column, max width ~1100px, generous vertical rhythm. Hairline rules between sections instead of cards and shadows. Left-aligned throughout, nothing centered except the footer.

### Signature: the work index as a data table

The projects list is a dense monospace table, not a grid of cards. Hairline row dividers, no borders, no rounded corners, no images.

```
WORK

NFLTrackr                2025   Solo (AI)   TS · Python · XGBoost · Supabase   →
INTERS                   2026   Solo (AI)   TS · React · Supabase · Three.js   →
DungeonCrawler           2024   Solo        Java                               →
Train2Fit                2025   Team        TS · React · AWS Amplify           →
Work Order Generator     2024   Solo        Python                             →
GridWorld Search         2024   Solo        Python                             →
```

Tagline sits under the name in `--muted` at body size. On hover the row background shifts to `--surface` and the arrow slides right. Every row is a link out.

This is the one memorable thing on the page, it's borrowed from the actual subject matter since the work is odds boards and model outputs, it collapses cleanly to a stacked layout on mobile, and it's fast to build.

Everything else stays disciplined so this element carries the design.

---

## Motion

Two effects, nothing else. Both added on day 3, both cuttable.

**1. Reveals on enter.** Sections fade and rise 12px the first time they enter the viewport, staggered 40ms. Once only, never on scroll back up.

**2. Scroll-linked hero.** As the hero scrolls out, the headline tightens letter-spacing from -0.03em to -0.05em, drifts up ~60px, and fades. Behind it, an optional depth layer of thin drifting curve paths.

### Depth layer (optional, cut first if day 3 runs long)

- One inline `<svg>`, absolutely positioned behind the hero, `pointer-events: none`
- Four or five stroke-only paths shaped like probability density curves, `stroke-width: 1`, no fill
- Three depth groups at 4%, 7%, 10% opacity in `--muted`, plus one path in `--accent` at 12%
- Each group translates at a different rate on scroll; mouse parallax at 4px / 8px / 14px max, `pointer: fine` only
- Fades to zero by the bottom of the hero so it never competes with the index

Why these shapes: the work is prediction and probability, so the ambient layer is the shape of the output. Decoration that happens to be true.

Both effects respect `prefers-reduced-motion: reduce`. No page transitions, no scroll hijacking, no cursor effects, no motion below the hero except the reveals.

---

## Quality floor

- Responsive to 375px
- Visible keyboard focus rings using `--accent`
- Semantic headings, alt text on any image
- Lighthouse accessibility 95+
- OG image and meta tags, since this link gets pasted into applications and needs to preview well

---

## Four-day build

**Day 1 — scaffold**
Vite + React + TS + Tailwind. Fonts loaded. Tokens in `index.css` as CSS variables, mapped into `tailwind.config`. One throwaway page proving the type scale and colors render. Stop and eyeball it before continuing.

**Day 2 — content and index**
Write all six taglines first. Populate `projects.ts`. Build the hero and the work index. This is the phase that matters most.

**Day 3 — about, footer, motion**
About strip in your own voice, footer with contact links, responsive pass, then reveals and the scroll-linked hero. Depth layer if time allows.

**Day 4 — ship**
Resume PDF in `public/`, OG image, favicon, meta tags, domain, deploy to Vercel.

---

## v2, after it's live

In rough priority order. None of this blocks shipping.

1. **Case study pages.** Add React Router and a `/work/:slug` route, `src/content/case-studies/*.md` rendered with react-markdown, and a `featured` boolean on `Project`. Start with DungeonCrawler since it's entirely yours and easier to write, then NFLTrackr after a codebase review.
2. **Pinned architecture walkthrough.** On the NFLTrackr case study, a pinned sequence stepping through the data flow one hop at a time: ingestion, devig, EV, UI. This is the one place a pinned sequence earns its cost, because the pinning does explanatory work instead of decorating. Two to three days.
3. **Sticky section labels.** Monospace label pins to the left while its section scrolls past. Needs long sections, so it only makes sense once case studies exist.
4. **Promote INTERS.** It's not featured now because it's the least finished, and a case study page promises depth an unfinished project can't deliver. When it's done, flip the boolean and write the body.
5. Screenshots and GIFs of NFLTrackr and INTERS running. Vague claims read as inflated; one real screenshot fixes that.

**Explicitly never:** horizontal scroll sections. Real accessibility problems, and there's no content shaped like a horizontal sequence.

---

## Notes for whoever builds this

Write it the way a student writes it. Straightforward components, obvious names, comments only where something is genuinely non-obvious. No barrel files, no over-abstraction, no premature extraction of a `<Section>` wrapper used twice. If a component is 80 lines of plain JSX, leave it at 80 lines of plain JSX.

Build in the order above and stop at the end of each day's scope so it can be reviewed before moving on.
