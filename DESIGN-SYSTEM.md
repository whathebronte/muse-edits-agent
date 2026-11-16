# Design system

A calm, light design language for personal boards and dashboards: trackers, invites, pipelines, lists, planners. White rounded cards float on a pale slate page, colour is used for status and never for decoration, and everything is built phone first.

**For the agent applying this:** follow the tokens and rules here exactly. Where this file and an older style in the target app disagree, this file wins. Keep the target app's own content, features and data; change only how it looks. Never use em dashes in UI copy.

---

## 1. Character in one paragraph

Calm, light and tidy, with a few strong colours that each carry one meaning. White rounded cards float on a pale slate page with soft shadows. Type is Inter, with confident bold headings and plain, friendly copy. Colour is used for status, never as decoration, so anyone can scan the screen and know what needs attention. It feels like a well-made personal tool: precise and quietly polished, not flashy.

**Mode:** an "operate" interface. The person is getting a task done, so scanability, consistency and familiar patterns beat expression. The personality lives in the details: the lowercase wordmark with a coloured dot, the rounded tiles, and the snappy press feedback.

---

## 2. Colour

### 2.1 The rule that matters most

**One colour = one meaning, everywhere.** A colour that marks "confirmed" in a list must mean "confirmed" in a chip, a tile, a legend and a stat. Never reuse a status colour for decoration, and never use two colours for the same meaning. Always show a legend (colour key) when colours carry status.

### 2.2 Tokens

Copy these as CSS custom properties on `html`. Names are by role, so they fit any app.

```css
html {
  color-scheme: light;

  /* Page and surfaces */
  --ds-bg: #f8fafc;          /* page background (slate 50) */
  --ds-surface: #ffffff;     /* cards, panels, menus */
  --ds-border: #e2e8f0;      /* hairlines, tile borders (slate 200) */
  --ds-outline: #cbd5e1;     /* stronger outlines, e.g. toggle borders (slate 300) */

  /* Text */
  --ds-text: #1e293b;        /* body and headings (slate 800) */
  --ds-muted: #64748b;       /* secondary text, labels (slate 500) */

  /* Status roles: all fills are the 600 weight so they look like one family */
  --ds-blue: #2563eb;        /* fixed and given: scheduled, locked in. Also the primary/action colour */
  --ds-blue-soft: #eff6ff;   /* hover wash on interactive tiles */
  --ds-blue-tint: #dbeafe;   /* selected state of toggles / segmented buttons */
  --ds-on-blue-tint: #1e3a8a;

  --ds-green: #059669;       /* a commitment made: accepted, confirmed, positive */
  --ds-green-dark: #047857;  /* green text on white, green borders */
  --ds-green-tint: #ecfdf5;  /* soft green backgrounds, count pills */
  --ds-green-icon-bg: #d1fae5;

  --ds-red: #dc2626;         /* blocked or negative: declined, clash, overdue */
  --ds-red-dark: #b91c1c;    /* red text, red borders, error messages */
  --ds-red-tint: #fef2f2;
  --ds-red-icon-bg: #fee2e2;

  --ds-yellow: #facc15;      /* suggested: an opportunity, needs a look */
  --ds-yellow-border: #eab308;
  --ds-yellow-text: #422006; /* ALWAYS dark text on yellow, never white */
  --ds-yellow-note-bg: #fefce8;     /* soft notice background */
  --ds-yellow-note-border: #fde68a;
  --ds-yellow-note-icon: #a16207;

  --ds-neutral: #f1f5f9;      /* inactive: past, archived, not actionable */
  --ds-neutral-text: #5f6f86;

  /* Shape and depth */
  --ds-radius: 16px;          /* cards and panels */
  --ds-radius-tile: 10px;     /* small tiles (day cells, chips that are tiles) */
  --ds-radius-control: 14px;  /* large tappable controls in the bottom bar */
  --ds-shadow: 0 1px 3px rgb(15 23 42 / 0.06), 0 6px 16px rgb(15 23 42 / 0.06);
}

::selection {
  background: #bfdbfe;
  color: var(--ds-text);
}
```

### 2.3 Suggested status mapping for a tracker or dashboard

Keep the logic ("what kind of thing is it?") rather than the exact labels, and confirm with the owner if a status does not fit.

| Role | Kind of thing | Suggested dashboard meaning |
|---|---|---|
| Yellow | An opportunity, needs a look | **New item awaiting your reply.** Needs attention |
| Green | A commitment you made | **Accepted / confirmed / delivered / paid.** Your yes |
| Blue | Fixed, given to you | **Scheduled and fixed** (date locked, nothing to do), plus all buttons and links |
| Red | Blocked, cannot proceed | **Declined, clash, overdue or at risk** |
| Neutral grey | Not actionable | **Past, archived or not relevant** |
| Half green / half white | Partly done | **Partly done**, e.g. a deliverable half complete or a tentative yes |

Keep the count of status colours to these five. If a sixth state seems needed, show it with a label or icon on one of the existing colours instead of inventing a new colour.

### 2.4 Text on colour (contrast)

- White text on blue, green and red fills. Dark text (`--ds-yellow-text`) on yellow. Muted text (`--ds-neutral-text`) on neutral grey.
- Body text must reach 4.5:1 contrast, and large text (24px+, or 19px+ bold) 3:1. The pairs above pass. White on green is 3.8:1, so on green keep text **bold** and short (dates, numbers, one-word tags).
- Secondary text on a coloured surface uses a darker tint of that colour, never a random grey.
- Pills are a tint background with dark text of the same colour. Dark text on yellow, never white.
- Non-link text is never blue: body text, headings and bullet points or markers stay in the normal text colour. Blue is only for actual hyperlinks, buttons, and the blue status meaning. Watch for this trap: a card header wrapped in a button element does not inherit the body colour by default, so its text falls back to browser blue. Set the colour explicitly on button elements and check the rendered text, not just the bullet dots.

---

## 3. Typography

- **Font:** Inter (Google Fonts, weights 400, 500, 600, 700, 800), fallback `system-ui, sans-serif`. Turn on `-webkit-font-smoothing: antialiased`.
- **Numbers:** any number that counts, changes live or lines up in a column uses `font-variant-numeric: tabular-nums`, so it does not jitter.
- **Case:** sentence case everywhere. No ALL CAPS headings. The only uppercase is tiny status tags inside tiles.

| Use | Size | Weight | Notes |
|---|---|---|---|
| Wordmark (app name) | `clamp(1.5rem, 5vw, 2.25rem)` | 700 | Lowercase, `letter-spacing: -0.035em`, ends in a green dot (see 5.1) |
| Key number (one per card) | 2.75rem | 800 | `letter-spacing: -0.02em`, line-height 1.1, coloured by status |
| Section title | 1.15rem | 700 | Text colour |
| Card label | 1rem | 600 | Muted colour |
| Body / list rows | 0.9rem | 400, first column 600 | |
| Sub text under a number | 0.85rem | 400 | Muted |
| Small meta (status line, legend, pills) | 0.75rem | 500 to 600 | |
| Column headers | 0.7rem | 700 | Muted |
| Hint text in controls | 0.65rem | 400 | Muted |
| Tags inside tiles | 0.55rem | 700 | Uppercase, `letter-spacing: 0.03em` |

---

## 4. Layout and spacing

- **Page:** max width 1200px, centred, padding 16px on every side, and 120px at the bottom when there is a fixed bottom bar, so nothing hides behind it.
- **Rhythm:** 16px between cards and sections. Inside groups use 4px (tile grids), 8 to 12px (controls), or 2px (title and its subtitle). Tight within groups, generous between them.
- **Grid of repeated cards:** 1 column on phones, 2 from 640px, 3 from 1024px.
- **Overview row:** on 768px and wider, two cards side by side at 1fr / 2fr (summary card plus filters-and-legend card). Stacked on phones.
- **Breakpoints to test:** 1440, 1024, 768 and 390 px wide. Nothing may scroll sideways.
- Content inside a card that lists things reads straight across: label on the left with the detail right beside it (grid `minmax(9rem, max-content) 1fr`), not pushed to opposite edges. Below 480px it stacks.
- The colour key (legend) sits at the very bottom of the page: coloured dots and short labels only, no heading, no long explanatory text.
- No big number tiles competing for attention. At most one key number card per screen, and only when a single number genuinely matters (a countdown, a balance).

---

## 5. Components

### 5.1 Header and wordmark
A card at the top: wordmark on the left, actions on the right (one primary pill button plus a "more" icon menu). The wordmark is the app name in lowercase, run together, followed by a green full stop, for example `invites.` or `brandcollabs.`. Under it sits a tiny status line with an icon (e.g. "All changes saved" in dark green, errors in dark red). On phones, shorten long button labels ("Sign in with Google" becomes "Sign in") so the header stays on one row.

### 5.2 Card (`.ds-card`)
```css
.ds-card {
  background: var(--ds-surface);
  border-radius: var(--ds-radius);
  box-shadow: var(--ds-shadow);
  padding: 16px;
}
```
No borders on cards; the soft shadow separates them. Never put a card inside a card.

### 5.3 Key-number card
One label (muted, 600) with an optional small text button on the right ("Edit" with a pencil icon). Then one big number (2.75rem, 800) coloured by meaning: green when healthy, red when at or below zero. Under it a muted line of context, then a thin progress bar. Use this pattern once per screen, for the single number that matters most. Do not build a row of identical big-number tiles.

### 5.4 Status tiles
The signature element. A 48px tall tile with a 10px radius, 1px border, centred number and an optional tiny uppercase tag underneath.

- **Plain:** white with a `--ds-border` border.
- **Status fills:** solid colour with a matching darker border (green uses `--ds-green-dark`, red uses `--ds-red-dark`, yellow uses `--ds-yellow-border`), and bold text.
- **Partial:** a left-to-right split, colour then white (`linear-gradient(to right, var(--ds-green) 50%, var(--ds-surface) 50%)`) with dark text.
- **Inactive:** neutral grey fill, muted text, not tappable.
- **Today / current item:** a ring with a gap: `box-shadow: 0 0 0 1px var(--ds-surface), 0 0 0 3px var(--ds-text)`.
- **Hover** (tappable only): blue wash `--ds-blue-soft` with a `#bfdbfe` border.
- **Press:** `transform: scale(0.92)` over 80ms. This snappy squish is part of the personality.
- **Focus:** `outline: 3px solid var(--ds-blue); outline-offset: 1px`.

The same tile works for days in a calendar, or as compact status squares in a list or grid.

### 5.5 Pills and chips
Rounded `999px`, 0.75rem, weight 600, padding `2px 8px`, a tint background with the dark text of the same colour (e.g. `--ds-green-tint` with `--ds-green-dark`). Use them for counts and small statuses next to titles.

### 5.6 Legend (colour key)
A wrapping row of 12px dots, each followed by a label: 0.75rem, weight 500, muted, with 6px gaps inside an item and `6px 14px` between items. Every status colour on screen must appear here. It sits at the very bottom of the page.

### 5.7 Segmented toggle
Two to four options in one pill-shaped group with an outline border. The selected option is `--ds-blue-tint` with `--ds-on-blue-tint` text. Good for view switches, or filters like "Invites / Collabs".

### 5.8 Collapsible summary panel
A card that folds open. Its header has two lines: a bold title and a muted one-line summary with numbers. Inside is a list of rows separated by 1px `--ds-border` lines: the first column bold (date or name), the detail beside it in muted text, with the key figure in bold. When empty, show one helpful sentence that says what to do next.

### 5.9 Bottom action bar (mode switch)
Fixed to the bottom with a 95% white background, an 8px blur, a top hairline and a soft upward shadow. It respects `env(safe-area-inset-bottom)`. It holds two to three large mode buttons (max 260px wide each): a 34px round icon on the left, then a bold 0.8rem title over a 0.65rem hint. The active mode gets that colour's tint background, a 2px border in the colour, and a coloured icon circle. Inactive modes stay fully readable, with a grey icon circle and muted title (never faded out). A small icon button such as Undo sits at the end.

### 5.10 Notices
For "heads up" messages, use a card with a soft yellow background (`--ds-yellow-note-bg`), a 1px `--ds-yellow-note-border` border, and an amber icon. Put the actions at the right: a text button ("Ignore") and a filled button ("Restore"). Errors use dark red text.

### 5.11 Buttons
- Primary: filled blue pill, white text, optional leading icon.
- Secondary: text button in blue.
- Icon only: round icon button, always with an `aria-label` and a tooltip.
- Button labels name the action ("Restore", "Start a new planner"), never "OK" or "Submit".

### 5.12 Full-page states
Loading, offline, not found, private and error states each get one centred card: a 40px muted icon, a short heading (1.2rem), one sentence of explanation (muted, max 420px wide), and one button with the way out. Never leave a blank screen or a bare spinner.

---

## 6. Icons

- **Material Symbols Outlined**, 20px, `vertical-align: middle`, 6px gap before a button label.
- One icon set and one stroke weight. No emoji or Unicode symbols as icons.
- Decorative icons next to text get `aria-hidden="true"`, so screen readers do not read the ligature word ("edit Edit").

---

## 7. Motion

Small and quick, only to confirm an action:
- Tile press: scale 0.92 over 80ms. Background colour changes over 150ms.
- Mode buttons: background and border colour over 150ms.
- Panels: the component's own expand/collapse.
- No decorative animation, no bounce, nothing that loops.

---

## 8. Accessibility checklist

- Contrast as in 2.4. Check the real rendered result, not just the hex values.
- Every tappable element has a visible focus ring (3px blue outline).
- Touch targets are at least 44px tall on phones.
- An element's `aria-label` contains its visible text (e.g. "Edit leave allowance" for an "Edit" button).
- Status is never shown by colour alone: tiles carry a tag or an accessible label.

---

## 9. Copy voice

- Plain, friendly, second person: "Your invites this week", "Tap a card to see the details."
- Short sentences. Explain what happened and what to do next.
- Numbers in words people use: "half a day", "23 to 31 May".
- Date ranges read "23 to 31 May" or "30 Dec to 3 Jan", not "23/05-31/05".
- No em dashes. Use a colon, a comma or a full stop instead.

---

## 10. Rules for boards and tracker spaces

These rules govern content boards built in this design language: invite trackers, work pipelines, idea boards, shopping lists, trip boards.

### Cards
- **Slim at a glance:** the card face shows only the essentials: the name, one short line of context, when it is, and any callouts. Everything else lives in the expanded card. When in doubt, move detail into the expansion.
- Two line clamp on card face descriptions.
- Tap anywhere on the face except the hide action expands the card inline. Only one card expanded at a time. Expanded cards stay inside the normal column and remain fully readable. Actions live inside the expanded card; hide stays outside.
- A key fact (for example "Paid") always shows in the same callout spot on the card, never in the title.
- Clash or conflict flags stay short and consistent: "Flag: Clashes with X". One wording everywhere.
- Hidden cards persist across refreshes in a hidden section at the bottom and can be brought back. Past items collapse into an "Already passed and closed" group above Hidden.
- Deduplicate by identity, not by reworded title, so the same item never reappears renamed.
- Long text inside an expanded card is never a paragraph block: break it into a label plus one short fact per line, evidence and asks one item per line. A long intro above a list collapses to one summary line with a show more / show less tap.
- In a tracker list, items already acted on sit above untouched items, in the order the owner values them.
- Sort orders are deliberate and documented per board (for example: paid work first with the highest fee at the top, then by date).

### Utility areas
- Search and filters stay compact: a single toolbar row by default, with the full controls in a panel that opens on demand. A utility card must not fill the first screen or push the actual content out of view.
- When filters are active, show a count and a one line summary, with a clear action.
- Filter pills are compact by default: small height, small text, tight padding and gaps, in a single horizontally scrollable row that never wraps into a block. Opening a page must show actual content in the first screen, never just filters.

### Declutter rules
- **No redundant subtext:** if a heading, stat or another element already says it, a sentence underneath repeating it comes out. A space does not display provenance or "last checked" footers to prove its data is current.
- **No duplicated affordances:** if colour already shows a link is a link, there is no link-out icon beside it. One signal per meaning, same as the colour rule.
- **No hint text for an action that just works:** cards expand by tapping anywhere on the face, with no "tap to open" label and no separate chevron button.
- **Edit in place where the content lives:** notes edit by tapping them on the expanded card, not by going to a separate setup or edit screen.

### Research and document spaces
- A space that is mostly a document to read still gets the scannable treatment: the default view is the summary only (verdict, key figures, blockers, one line takeaway per card), and dense detail sits behind a tap in an expandable card. Walls of text are a defect, not a style.
- Prices and costs are shown per person only, with no party or for-two totals, including in calculators (no travellers or party-size selector).

### Data persistence
- User data entered in a space (picks, selections, statuses, hidden state) must survive every UI refinement, restyle and rebuild pass. Losing it is a bug, not a side effect. Before presenting a refined build, verify that pre-existing user selections are still there.

### Exceptions
- A board may carry a deliberate alternate theme (a dark cinema look for a watchlist, a warm cream look for an event board) when the owner chooses it. The exception is that board only: its structure, card behaviour and verification rules still follow this file.

---

## 11. Finish details

- **Nested rounded corners:** where a rounded element sits inside another rounded element (tiles, pills and images inside cards), the inner radius equals the outer radius minus the padding between them. Mismatched radii are the tell of an unfinished build.
- **Alpha borders:** where a space uses borders, they are alpha colours (for example `rgb(0 0 0 / 0.12)`) rather than solid hex, so the line blends into whatever background sits behind it instead of reading as a hard outline.
- **Edge fades:** horizontally scrollable rows (filter pill rows, photo strips) fade at the edges with a mask gradient (transparent into solid over the outer 15%), so the row itself signals there is more to scroll. No arrows or extra icons.
- **Full height:** full-height sections use `100dvh`, never `100vh`, which breaks and jumps in mobile Safari.

---

## 12. Verification discipline

- Every build or edit is checked at phone width before it is shown to the owner: target viewport 402 x 874 CSS px, DPR 3 (a current large iPhone), with screenshots.
- The mobile render is the deciding evidence. A desktop screenshot, desktop preview, source inspection or aria-only check does not count as verification.
- The verification report must explicitly state the viewport used and what was inspected there. If it does not, treat the build as unverified and run another pass.
- Check for horizontal overflow, truncation, orphans, clipped text and tile alignment. Truncation is text cut off with dots; an orphan is a single word left alone on a line. Fix both before presenting.
- Inspect collapsed and expanded card faces, section headers, buttons, and every pill, chip and filter row at mobile width. A lone pill or short final item stranded on its own row, ragged wrapping, dead width at the edge of a control row, or a truncated title that hides which item it is all fail the check.
- Do a real second pass before presenting. A promised second pass is never grounds to call a build ready.
- Never claim a fix or a sync is done until the owner confirms it. Their confirmation is the finish line.
- Audit captures can lie: some sandboxes block web fonts, so icons can render as ligature words in screenshots while the published page is fine. Judge icons from the live page, not the audit screenshot. Self-hosting a small subset of the icon font inside the app removes the problem.

---

## 13. Process lessons

- **Mock first for major visual changes:** build a throwaway mock or face-off page with real content, keep the live space untouched, and apply to live only once the owner picks or approves the mock. Rejected directions die in the mock.
- When the owner asks "what do you recommend", answer with a clear opinion and a named favourite, then wait for their call before applying the change.
- Restyles are design only: the framework under a space cannot be swapped. Port the design language, keep features and data.
- Start visual options clean and simple; the owner dials back anything busy. Follow only their latest choice. Do not relitigate earlier versions.
- Present a finished build once, with a short update, instead of narrating every change.
- **Keep a learnings doc alongside this spec.** Every correction, preference or verdict the owner gives on a space gets a dated entry in it the same session: what they said, what changed, the rule going forward. Read it before building or editing any space. When a learning hardens into a standing rule, distill it into this spec and correct the superseded entry in place rather than piling up. This file stays the present-tense truth; the learnings doc is the history and reasoning behind it, and it is personal to the owner's spaces, so it is never shared as part of the spec.
- A polish asked for on one board in a family of boards applies to the whole family by default, because they are separate builds and a change does not flow through on its own. Only limit it when the owner says it is for one board.

---

## 14. Do and don't

**Do**
- Keep pages light: slate page, white cards, colour only for status.
- Show a legend wherever colour carries meaning.
- Use tabular numbers for anything that counts.
- Test at phone width before calling anything done.

**Don't**
- Add new accent colours, gradients, glows or glassy blur (the bottom bar's blur is the one exception).
- Put a thick coloured stripe down one side of cards or notices.
- Nest cards, or build the page from rows of identical icon-plus-text cards.
- Use small uppercase "eyebrow" labels above headings.
- Fade out inactive options until they are hard to read.
- Use white text on yellow.
