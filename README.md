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

## The sample: booking meeting rooms

A fictional company, Harbor & Pine, wants people to book meeting rooms for a block of time. The sample is the proposal for that after its first round of answers. It's here to show the method end to end, and as a starting point if you're designing a similar system: reserving something rather than someone (a room, a space, a piece of equipment).

**The idea and the main screen.** One paragraph says what it is and isn't. Four cards give the model. Then the day view: every room on a floor, hour by hour, with each kind of booking drawn, and a legend saying what each one means. That's the screenshot at the top of this page.

**Edge cases, each drawn.** Someone else booked the room while you were filling in the form. A weekly booking where 3 of 24 dates clash. A room that needs approval. Facilities taking a room out of service when 7 bookings are already in it. Each frame ends with something to do next. The caption under each one carries the technical detail and the decision it came from.

![Four edge-case frames: a clash, a repeat with clashes, an approval request, and taking a room out of service](docs/screenshots/example-edge-cases.png)

**The physical side.** The tablet by the door, before check-in and after a no-show releases the room, and the email the organiser gets.

![Two room tablets and a release email](docs/screenshots/example-arrive.png)

**After the tables, the decision register.** These come first: which rule wins, states and wording, who sees what, and what it touches in the code. Then the panel at the bottom: the model; the decisions, with "Changed" where the answer differed from the recommendation; follow-up questions N1–N3; and the build slices.

![The notes panel: the model, decisions, new questions and build slices](docs/screenshots/example-decisions.png)

The [spec it became](skills/decision-proposal/examples/room-booking-spec.md) cites those decisions by number.

## The blank page

What Claude starts from: every block, with placeholders saying what goes there.

![The blank template: placeholder title, card strip, an app-shell mockup and edge-case frames](docs/screenshots/blank-top.png)

## Install

**As a Claude Code plugin** (updates when the repo does):

```bash
claude plugin marketplace add doncampbell-hash/decision-proposal
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
