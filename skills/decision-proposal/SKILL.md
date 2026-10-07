---
name: decision-proposal
description: Lay out a feature or system as one visual, self-contained HTML proposal — the model, every screen and state as a mockup, edge cases as their own frames, a states-and-copy table, a survey of what it touches, and numbered questions each with a recommended answer — then fold the answers back in as a decision register and turn it into a one-page spec. Use when the user asks for a proposal, a design for something with no mockup, "lay out the system", "what are the edge cases", or wants decisions made before building.
---

# Decision proposal

Three phases, one feature: **propose → decide → spec**. The proposal is a page the decider can read in ten minutes and answer in one message ("1 yes, 2 no — keep it, rest as recommended"). The spec is what gets built.

Files beside this one:
- `template.html` — the blank page: the shell, every block below with placeholders, and the visual style. Copy it; don't restyle it from scratch.
- `spec-template.md` — the blank phase-3 shape.
- `examples/room-booking.html` — a filled proposal at phase 2 (answers folded in, one frame added after them, follow-up questions open). Read it before writing your first one: it shows the density and tone to aim for.
- `examples/room-booking-spec.md` — the spec that proposal became.

## Before writing anything: ground it

Read what exists first — the code on main, prior specs and proposals, design references, the data model. The proposal must describe the system as it really is, so:
- name the real routes, tables, functions, permissions and settings it touches;
- note what it builds on and what has no design yet;
- if the project keeps a design-tokens file, put its values in the template's `:root` and make mockups match the built app's shell (sidebar, top bar, tables) so frames look like the product, not a wireframe;
- if the project has a proposals folder and index, follow it (add a row with status). Otherwise use `docs/proposals/<slug>.html`.

## Phase 1 — the proposal (one HTML file, no build step)

In this order:

1. **Header comment** (in `<head>`): status + date, what it builds on (specs, PRs, files with line numbers), what tier/style rules the frames follow. This is for the next agent, not the reader.
2. **Title + lede.** One paragraph that states the whole idea in plain words. Bold the nouns the reader must hold on to. Say what it is *not* as well as what it is.
3. **Flow strip.** 3–5 cards, each one line: the model at a glance (where it applies → how it runs → who decides → what's kept).
4. **Numbered frames**, one `h2.sec` per area (`1 · Find a room on the day view`, `2 · Find me a room`, …). Each has:
   - a short paragraph above saying *why* this frame is shaped this way;
   - a real mockup with realistic data (names, times, counts — never "Lorem" or "User 1");
   - a caption below carrying the technical detail (frame id `1a`, route, permission/capability, defaults), so the frame itself stays visual.
   **Edge cases get their own frames, not footnotes**: nothing found / several found, empty, refused and why, partial success, the person who can't, the device with no operator, the undo. If a reviewer would ask "what happens when…", draw it.
5. **Tables**, where they apply (all use `table.copy`):
   - *resolution order* — when several rules can decide, the order they win in;
   - *states and copy* — When · What the user sees · What the operator sees, with exact wording in `<q>`;
   - *kept / never kept* — for anything touching personal or sensitive data: each field, kept?, why;
   - *what this touches* — Area · Today · With this — a survey of the real code, dated. This is where hidden work and cross-feature edge cases surface.
6. **Notes panel** (`section.notes`, always last):
   - `How it works` — 3–5 numbered principles: the model, where the trust boundary is (what the server re-checks), what it deliberately is *not*, and "no new X" where existing permissions/settings suffice.
   - `Questions (recommendations in italics)` — numbered. Each: the question in bold, the tension in one sentence, then *the recommended answer* and why. Never a question without a recommendation. Put the riskiest or most expensive-to-reverse questions first.
   - `Build` — slices, one PR each, in order; each says what it ships and what review/tests gate it.

Style: plain words in the product's own vocabulary; short sentences; say "unsafe" rather than jargon; disagree in the page if a requested shape has a real problem, and propose the fix. No marketing tone.

Then show it to the user (open the file in a browser or publish it) and stop. Nothing is built until it's answered.

## Phase 2 — fold in the answers (same file)

When the decider answers:
- Rename `Questions` → `Decisions (<who>, <date>)`. One numbered line per answer, in decided words ("**Auto-release**: yes, at start + 10 minutes. As recommended."). Keep the numbering, so later text can cite "decision 6".
- Unanswered questions: take the recommendation and say so ("Unanswered 3 taken as recommended").
- Answers that add scope become new or changed frames, marked in their `small` tag: `· new` or `· added after the answers`.
- Questions the answers raise go in `New questions from the revision`, numbered `N1, N2…`, same format (recommendation in italics).
- Update the header comment and add a dated `p.note` under the flow strip: what changed and which frames need a look.
- Never silently change something already decided. If it must change, say so in the note and re-ask.

Repeat until nothing is open, then mark the proposal approved (header comment, index row: "Approved with changes <date> — <one-line summary of the changes>").

## Phase 3 — the spec (`spec-template.md`)

One page of markdown in the project's specs folder, built from the approved proposal:
- **Status line** — approved date, link to the proposal, "decisions in its notes panel", what it extends.
- **The rule** — the invariants in prose: what's per-X vs per-Y, limits as named constants, uniqueness, what is never shown. Cite decisions by number.
- **Numbered sections, one per slice/PR** — what changes, the function signatures and error codes, migrations (and what they refuse to do), tests that prove it. Mark which need a security review.
- **Not in this work** — the deferred list, explicitly.
- **Docs** — which user-facing pages each PR updates.

As slices land, append an **As built** paragraph to that slice's section with anything that differs from the plan and why — the spec stays true.

## Using it without the skill

The steps above are self-contained: paste this file's body (below the front matter) into any session as the prompt, and attach `template.html` (and the example, if the session can take it).
