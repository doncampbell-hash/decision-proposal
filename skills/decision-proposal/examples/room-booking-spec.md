# Room booking

Status: approved with changes 2026-10-05 (`room-booking.html`, decisions in its notes panel; N1–N3 answered as recommended on 5 Oct). Extends `settings.md` (organisation → building overrides) and `calendar-sync.md`. Four PRs, below.

> Example for the decision-proposal skill. The company, codebase and people are fictional.

## The rule

A booking is one room for one time range, held by one organiser. Two live bookings (not cancelled, not released) can never overlap in the same room: an exclusion constraint on `(room_id, during)` enforces it, and the app treats the insert as the check (the day view's "Free" is a hint). Times are stored as instants and shown in the room's time zone. A repeat is a `booking_series` (rule + room + wall-clock time in the room's zone) whose dates are materialised as bookings up to 6 months ahead (decision 2), so a weekly 14:00 stays 14:00 across a clock change.

Every rule resolves room → building → organisation (frame 5). Defaults: `booking.releaseAfterMin = 10`, `booking.maxHours = 4`, `booking.daysAhead = 90`, `booking.approval = false`. A booking longer than `maxHours` needs approval (decision 8). No buffer between bookings (decision 9).

A **private** booking (decision 6) shows only "Booked" to people outside it: not the title, not the organiser. People invited are never shown to anyone outside the booking; Facilities sees the count. We never copy the calendar invite's body.

## 1 · Bookings and the day view (one PR)

- Migration: `bookings (id, org_id, room_id, organiser_id, series_id null, during tstzrange, title, private, status, checked_in_at, released_at, …)` with `EXCLUDE USING gist (room_id WITH =, during WITH &&) WHERE (status IN ('booked','requested','checked_in'))`. Needs `btree_gist`; the migration creates the extension if missing and fails loudly otherwise.
- `createBooking(tx, actor, { roomId, during, title, people, private })`: checks `rooms.book`, the room's resolved rules (hours, `daysAhead`, `maxHours`), then inserts. A constraint violation becomes `room_taken` with up to three alternatives (frame 3a): same time in rooms that fit, then the same room later. Sends the calendar invite after commit.
- Day view `/book` (frame 1a): rooms on a floor, the building's opening hours, blocks coloured by the legend in 1b. Private bookings render "Booked". Cancel and End early on your own bookings; End early sets the range's end to now.
- Tests (real Postgres): two concurrent `createBooking` calls for one slot → exactly one succeeds; a released booking's time can be booked again; private hides title and organiser from a third user; a booking spanning a clock change keeps its wall-clock times.

## 2 · Find me a room and repeats (frames 2a, 2b, 3b)

- `findRooms(tx, actor, { seats, during, needs[] })`: rooms that fit, sorted by fewest spare seats, then distance from the actor's floor. Rooms that don't fit come back with the reason (`too_small`, `no_video`, `taken`). When none fit: the nearest free times in fitting rooms, then options that drop one need, each labelled with what it gives up (2b).
- `createSeries(…)`: materialises dates to 6 months; skips building closed days; returns clashes, each with a suggested room. The dialog (3b) books the free dates plus the chosen alternatives in one transaction; a clash that appears between preview and confirm comes back as `room_taken` for that date only.
- Renewal: a job 14 days before a series ends asks the organiser to extend it by 6 months.
- Tests: series across the October clock change; a clash appearing between preview and confirm; closed days skipped.

## 3 · Tablets, check-in and release (frames 4a–4c; security review)

- A room tablet is a device signed in to one room, allowed only to read that room's day, `checkIn(bookingId)` and `bookNow(30 | 60)` named by badge tap. Its token can do nothing else.
- Check-in opens 10 minutes before the start, from the tablet or the booking link for people on the booking (N3).
- A `release_booking` job runs at start + `releaseAfterMin`; check-in cancels it. On release: status `released`, the organiser is emailed (4c), the calendar invite is updated. On the third consecutive release in a series, the email asks "keep the series, or cancel the rest?" instead; it never cancels on its own (N1).
- A room with no tablet releases only if link check-in is on for it (frame 5, last row).
- Tests: release at exactly +10; check-in at +9 cancels the release; the third release asks; a tablet token refused on any other room or endpoint.

## 4 · Approval rooms and out of service (frames 3c, 3d)

- `booking.approval` on a room: `createBooking` inserts `requested` (holds the slot). Approvers (`rooms.approve` for that room) see it under Approvals. Unanswered at the start → `declined` and the organiser is told (decision 5).
- `takeOutOfService(tx, actor, { roomId, during, reason, bookings: 'move' | 'cancel' })` (`rooms.manage`): inserts a `room_outage`; for each live booking in range, moves it to a similar room (same floor first, at least the seats, the same screen/video) or cancels it. The organiser stays the organiser (N2); the move is logged with the actor. Every organiser is emailed which happened.
- Tests: approval expiry at the start; move preserves organiser, title, people and privacy; a booking with no similar room is cancelled, never left in an out-of-service room.

## Not in this work

Desk booking. Equipment and catering on a booking. Reporting on room use. Buffers between bookings. Approvals from the calendar. Rooms shared between organisations.

## Docs

Slice 1 adds a "Booking a room" page; slice 3 adds the tablet and release section; slice 4 adds approval rooms and out of service to Facilities' settings page.
