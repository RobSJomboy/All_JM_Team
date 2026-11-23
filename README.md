# All-JM Team

The monthly All-JM episode, on screen. Build the AL and NL boards, lock in the month's winners,
and put the roster, the ticker and the Silver Daisy on air.

One HTML file. No build step, no npm, no API key. Player names and headshots come from the public
MLB StatsAPI; the league crests are embedded.

---

## Running it

**Control** — open on the laptop you're driving from:

```
https://robsjomboy.github.io/All_JM_Team/?topic=your-topic-name
```

**OBS** — two Browser Sources, both 1920×1080, on the machine running the show:

```
Graphics: https://robsjomboy.github.io/All_JM_Team/?output=1&layer=main&topic=your-topic-name
Ticker:   https://robsjomboy.github.io/All_JM_Team/?output=1&layer=ticker&topic=your-topic-name
```

Put the **Ticker** source at the **top of the scene's source list** so the scroll sits above the
title bar and every other bar. Both must carry the **same topic**. Hit **Connect**, then **Copy
Graphics URL** / **Copy Ticker URL** — don't type them by hand. An old `?output=1` link with no
`layer` still shows everything in one source.

Leave "Shutdown source when not visible" **unchecked** so the graphic doesn't drop when you switch
scenes.

---

## The board

Slots follow the All-JM sheet: **C, 1B, 2B, SS, 3B, OF, CF, OF, DH, SP**, then the three **UTIL**
spots for the community vote. **RP is a toggle** — off by default.

Search fills a slot. **Lock In** puts that player on the board for the month.

**The board ships inside the page.** Every decided month is baked into `BAKED` in `index.html`
(with player ids), so it's there in any browser, on any computer, before anything is clicked.
Edits on top of that are saved locally **and** to the relay room `alljm-board`, and every page load
reads that room back — lock someone in on one laptop and the other laptop has it. Each
league-month carries an edit time `t`; when two copies disagree the newer one wins (baked = 0).

A browser still holding the old `alljm-store` save is folded in on first open: months the file
doesn't have come over whole; months it does have only get their empty slots filled, so a
half-entered month can't wipe a full one. Then it's pushed to the relay.

**SEPTEMBER is the Final All-JM.** It's stored as `SEPTEMBER`, but the menu, the panel and the
ticker call it **Final All-JM** and give it gold foil, a shimmer and a glow.

---

## Petals and the Silver Daisy

Petals are read straight off the board — one per month the player is locked in on. There's no
separate history to drift out of step, so unlocking or clearing a slot takes the petal back on its
own.

Be on **every month taped so far** (at least two) and the flower's centre lights gold and it
turns: **The Silver Daisy**. Daisy winners' **names go gold** on the panel and the ticker.

When a lock **completes** a sweep, the **Silver Daisy celebration** goes up on its own (switchable):
"2026 SILVER DAISY WINNER", the name in gold foil, the full flower blooming, and a few small
gold-and-silver fireworks. It clears after 12s. **✿ Daisy** on any daisy holder's slot, or **✿
Show** in the Silver Daisy list, puts it up by hand; **✕ Hide Daisy** takes it down.

Judge Jomboy was removed in November 2026.

---

## The graphics

The roster panel is deliberately the **same geometry as MLB_Standings** — 1920×1080 transparent,
528×1004 pinned right at `top:38px`, Rift, gold `#c8a84b`, same slide-in — so it drops into that
Browser Source without re-aligning anything.

- **Roster** — crest, month title, league, then a row per slot: position, headshot, name, club and
  petals. **Show Roster / Hide Roster**.
- **Ticker** — the selected month's whole board, both leagues, behind a gold label
  ("2026 AUGUST ALL-JM"). **Above title bar** sets its bottom edge at y 988 (just over the TB title
  bar), **Bottom edge** at the frame edge, and **Nudge px** fine-tunes either. Toggle with **▶ Ticker**.
- **Silver Daisy** — centred card sitting just above the ticker, fireworks on a canvas behind it.
- **Clear All** kills everything on screen at once.

The control page carries a **live preview**: the real output page, on the real topic, scaled to a
quarter. Not a mock — a mock drifts from the real thing exactly when it matters.

---

## Connection

Both pages subscribe to a named topic on [ntfy.sh](https://ntfy.sh), so the two machines don't have
to be on the same network — or in the same building. Nothing to install.

**Better: use your own relay.** The public ntfy hosts keep going down. The
[Trade Snapshot repo](https://github.com/RobSJomboy/mlb_stats) carries a `relay/` folder — a small
Cloudflare Worker, free plan, no card. Deploy it once (`npx wrangler login && npx wrangler deploy`),
paste the `https://…workers.dev` URL into the **Relay** box and hit **Use Relay**. The output holds
a WebSocket to it, so pushes are instant and a refreshed Browser Source is caught up the moment it
connects. The relay URL rides along in the copied links, so the other machine inherits it.

**ntfy stays wired as an automatic fallback.** With no relay set — or if it can't be reached —
publishing goes to **five** public ntfy instances and the output subscribes to all of them, so it
takes five simultaneous outages to lose the show. The Connection pill names whichever path carried
it. `?ntfy=https://host` pins a single host.

**ntfy.sh is deliberately last in that list.** It is the busiest instance and the quickest to answer
`429 limit reached` for an address; listed first, the host most likely to be rate limited was also
the host most likely to be believed. The other four — envs.net, adminforge.de, mzte.de, hostux.net —
were each checked for a POST/poll round trip and for `access-control-allow-origin: *`, without which
a browser can't read the reply at all.

**Rate limiting is the failure to expect, not downtime.** A 429 is a perfectly well-formed HTTP
response, so code that only checks "did the request come back" reads it as delivery and shows a
green light while nothing reaches the screen. Both sides check the status now, and the pill says
**rate limited** rather than "not reaching topic" — different problem, different fix. If you see it:
wait a minute, or deploy the relay.

The output used to poll every host every 10 seconds on top of its streams — 18 requests a minute,
forever, whether or not anyone touched a button, which is roughly 26,000 a day per Browser Source
and is what earned the 429s. The streams now carry the show and the poll is a fallback: it runs only
when every stream is down, one host per tick, round-robin.

**Topics are public to anyone who knows the name**, so the suggested one carries a random tail. Don't
shorten it to something guessable — that's a stranger's write access to your graphics.

Everything on the wire is absolute state with a sequence number, same as the standings build, so a
duplicated or late message can't leave the screen disagreeing with the control page.

The sequence is **seeded from the clock**, not from zero. It used to start at 1 on every load, so a
control page reloaded mid-show was ignored until it had climbed back past wherever it left off —
dozens of presses doing nothing, looking exactly like a dead transport. The clock only goes one way,
so a reload always resumes above the last number the output saw.

---

## Notes

- **ntfy proves a quiet stream is alive with a keepalive every 45 seconds, and it arrives as a
  *named* SSE event.** `es.onmessage` only ever fires for `message`, so watching it alone makes a
  perfectly healthy stream on a quiet show look dead. Any staleness check has to listen for
  `keepalive` and `open` as well, and sit past two of them — the watchdog here is 100s. A 20s one
  tears every stream down and reopens it three times a minute for as long as the show is up.
- **Never let two reconnect engines run at once.** `EventSource` retries on its own timetable, which
  can't be read or changed from JS, so leaving a failed stream open *and* reopening it from a
  watchdog gives you both. Close on error, then back off 3s→60s with jitter.

- **The board is baked into the page and synced through the relay room `alljm-board`** — it no
  longer lives in one browser. When a month is finished, copy it into `BAKED` so it survives even
  if the relay room is ever cleared.
- **Month labels must match the sheet exactly.** A month spelled differently is invisible to the
  sweep check, which silently hands out a Silver Daisy on an incomplete run. That bug was real:
  `MAR/APR` vs `APRIL` gave Cody Bellinger a daisy for two months out of three.
- **The player index carries only `currentTeam.id`** — no league, no abbreviation — so clubs are
  joined from the teams list. Without that the AL/NL filter matches everyone and the club under a
  name comes out blank.
- **Two slots both read "OF"**, so slots are keyed by id and labelled separately. Keying by label
  would collapse them into one.
- **`histKey` is gone.** Petals used to live in a separate history keyed by a seeded row's
  `histKey`, and a key left behind after a slot changed hands filed one player's petal on another's
  record. Petals are now counted off the board itself, so there's nothing to drift.
- Sibling tool: [Trade Snapshot](https://github.com/RobSJomboy/mlb_stats) — same house look, same
  transport, different show.
