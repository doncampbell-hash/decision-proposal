# decision-proposal

A [Claude Code](https://claude.com/claude-code) skill for deciding a feature before you build it.

Claude lays the feature out as **one visual HTML page**: the model in a paragraph, every screen and state as a mockup, the edge cases each drawn as their own frame, the exact wording for each state, a survey of what it touches in your real code, and **numbered questions, each with a recommended answer**. You answer in one message ("1 yes, 2 no, rest as recommended"). Claude folds the answers back into the page as a **decision register**, draws anything the answers added, asks the follow-up questions, and then writes a **one-page spec** that cites the decisions by number.

**propose → decide → spec**

![The sample proposal: a summary paragraph, a strip of four cards, and a day view of meeting rooms](docs/screenshots/example-top.png)

## What's in the repo

| | |
|---|---|
| [`skills/decision-proposal/SKILL.md`](skills/decision-proposal/SKILL.md) | The method: what goes on the page, in what order, and how answers become decisions and then a spec. |
| [`template.html`](skills/decision-proposal/template.html) | **The blank page.** Every block with placeholders, in the visual style. Claude copies this. |
| [`spec-template.md`](skills/decision-proposal/spec-template.md) | The blank spec. |
| [`examples/room-booking.html`](skills/decision-proposal/examples/room-booking.html) | **A filled sample**: booking meeting rooms at a fictional company. It's shown after the decider answered, so you can see the decision register, a frame added after the answers, and follow-up questions. |
| [`examples/room-booking-spec.md`](skills/decision-proposal/examples/room-booking-spec.md) | The spec that sample became. |

GitHub shows the HTML files as source code. To see them as pages, download the file and open it in a browser.

The blank page:

![The blank template: placeholder title, card strip, an app-shell mockup and edge-case frames](docs/screenshots/blank-top.png)

The bottom of the sample: the model, the decisions (with what changed from the recommendation), the new questions and the build slices:

![The notes panel of the sample: the model, decisions, new questions and build slices](docs/screenshots/example-decisions.png)

## Install

**As a Claude Code plugin** (updates when the repo does):

```bash
claude plugin marketplace add OWNER/decision-proposal
```

```bash
claude plugin install decision-proposal@decision-proposal
```

**Or as a plain skill**: copy `skills/decision-proposal/` into `~/.claude/skills/` (every project on your machine), or into a repo's `.claude/skills/` (everyone working in that repo).

**Without Claude Code** (Claude chat, or another assistant): paste the body of `SKILL.md` as your prompt and attach `template.html`, plus the sample if you can.

## Use

Ask in plain words, in the project you're working on:

> Write a proposal for recurring bookings, with the edge cases and the questions you need answered.

Or invoke it by name: `/decision-proposal` (plain skill), or `/decision-proposal:decision-proposal` (plugin).

Then:
1. **Read the page** and answer the questions by number. Anything you skip is taken as recommended, and the page says so.
2. Claude **revises the page**: the questions become decisions, frames the answers added are marked "new", and follow-up questions appear as N1, N2… Repeat until nothing's open.
3. Ask for **the spec**. Build from it. As each piece ships, Claude adds an "As built" note to its section, so the spec stays true.

## What makes it work

- **It reads your code first.** The "what this touches" table names real tables, routes and permissions, which is where hidden work turns up.
- **Edge cases are drawn, not footnoted.** Two people booking at once, a repeat that clashes, nobody turning up: each gets its own mockup, ending in something the user can do next.
- **Every question comes with a recommendation.** You decide by agreeing or saying no, not by writing an essay.
- **Decisions keep their numbers.** The spec says "decision 6", and anyone can find out why.
- **Mockups look like your product.** If the project has design tokens, Claude puts them in the page so the frames match your app.

## Licence

[MIT](LICENSE)
