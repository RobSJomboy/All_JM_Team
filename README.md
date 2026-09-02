# All-JM Team

The monthly All-JM episode, on screen. Build the AL and NL boards, lock in the month's winners,
and put the roster, the ticker and Judge Jomboy on air.

One HTML file. No build step, no npm, no API key. Player names and headshots come from the public
MLB StatsAPI; the league crests are embedded.

---

## Running it

**Control** — open on the laptop you're driving from:

```
https://robsjomboy.github.io/All_JM_Team/?topic=your-topic-name
```

**OBS** — Browser Source, 1920×1080, on the machine running the show:

```
https://robsjomboy.github.io/All_JM_Team/?output=1&topic=your-topic-name
```

Both must carry the **same topic**. That's the only rule. Hit **Connect**, then **Copy OBS URL** and
paste it into the Browser Source — don't type it by hand, the copied one carries the topic and the
build stamp.

Leave "Shutdown source when not visible" **unchecked** so the graphic doesn't drop when you switch
scenes.

---

## The board

Slots follow the All-JM sheet: **C, 1B, 2B, SS, 3B, OF, CF, OF, DH, SP**, then the three **UTIL**
spots for the fan vote. **RP is a toggle** — off by default, on for the months you run one.

Search fills a slot. **Lock In** puts that player on the board and awards their petal for the month.

**April, May and June 2026 are pre-loaded.** Hit **Load 2026 History** once, on the machine you'll
drive from. Names are resolved against the live player index rather than stored as ids, so a name
that doesn't match surfaces as a miss in the note under Connection instead of quietly showing
another player's face.

**The month dropdown is the archive.** Pick April, hit **Show Roster**, and April's team goes up.

---

## Petals and the Silver Daisy

Locking a player in for a month adds a **petal to their flower**. Unlocking takes it back —
otherwise a mis-click quietly inflates someone's flower for the rest of the season.

The petals are the flower: one win is a single petal, and they fill out into a daisy as the months
stack up. Petals sit evenly around the circle rather than at their month's angle — placed by month,
three wins sat as a lopsided fan on one side; evenly spaced, any count reads as a deliberate flower.

Win **every month taped so far** and the centre lights **gold** and the flower turns slowly: that's
**The Silver Daisy**. Miss a month and the daisy is out of reach, so nothing extra is drawn — the
flower is just silver-centred. A sweep is counted against months that actually have a locked roster
rather than the calendar, and needs at least two of them, so nobody collects one in the season's
first episode.

As of the seeded board, six players are perfect through April–June: Trout, Muncy, Olson, Schwarber,
Ohtani and Alvarez.

---

## Judge Jomboy

For the ones Trev and Jake can't agree on.

1. Mark the slot **⚖ contested**. Whoever is in the slot becomes Trev's pick.
2. Search **Jake's pick** in the field that appears beside it.
3. **⚖ Send to Judge** — a **lower third** puts both up side by side, each with his line for the
   month in question: AVG / HR / RBI / OPS, or W-L / ERA / K / WHIP in the SP and RP slots.
4. **Rule: Trev** / **Rule: Jake** — the loser greys out, the winner lights gold, the scales tip,
   and a strip across the bottom of the card announces the ruling. A second lower third then puts
   the winner up alone with his line and the petal he just earned.

The ruling is the decision, not a caption over one: the winner goes into the slot, locked, with the
petal awarded. A who-is-who panel in the control spells out which name is whose so nobody rules for
the wrong side by accident.

**The numbers are the month's, not the season's**, because that's what the award is. They come from
`byDateRange` rather than `byMonth`: the sheet's APRIL takes in the March opening series, which
`byMonth` files as a separate month — counting stats could be added back together, AVG and ERA
can't. Both lines are fetched the moment the argument is set up, so Send to Judge puts the card and
the numbers up in one push. A player with no games in the window gets a card with no stat row rather
than a card that won't come up.

---

## The graphics

The roster panel is deliberately the **same geometry as MLB_Standings** — 1920×1080 transparent,
528×1004 pinned right at `top:38px`, Rift, gold `#c8a84b`, same slide-in — so it drops into that
Browser Source without re-aligning anything.

- **Roster** — crest, month title, league, then a row per slot: position, headshot, name, club and
  petals. **Show Roster / Hide Roster**.
- **Ticker** — a bottom scroll announcing each lock-in with headshot, club, month and petals, in the
  same lockup as the trade-deadline scroll. Toggle with **▶ Ticker**.
- **Judge lower third** and **verdict lower third**, above. Both sit at the same spot — 1160 wide,
  `bottom:96px`, clear of the 78px ticker — so the ruling replaces the argument without the frame
  jumping.
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

- **Rosters and petal history live in `localStorage` on the control machine**, keyed by season and
  month. Last month's board is still there next time — but it's *per browser*, so drive from the
  same machine each month or the petals won't follow.
- **Month labels must match the sheet exactly.** A month spelled differently is invisible to the
  sweep check, which silently hands out a Silver Daisy on an incomplete run. That bug was real:
  `MAR/APR` vs `APRIL` gave Cody Bellinger a daisy for two months out of three.
- **The player index carries only `currentTeam.id`** — no league, no abbreviation — so clubs are
  joined from the teams list. Without that the AL/NL filter matches everyone and the club under a
  name comes out blank.
- **Two slots both read "OF"**, so slots are keyed by id and labelled separately. Keying by label
  would collapse them into one.
- **A seeded row's `histKey` belongs to the player who was seeded into it**, and has to be dropped
  when somebody else takes the slot — by search, by ✕, or by a Judge ruling. Left behind, the new
  player's petal lands on the old player's record *and renames it*: ruling for Trout over Buxton in
  CF put Trout's win on Buxton's row, so the board showed two Mike Trouts with the wrong months.
- Sibling tool: [Trade Snapshot](https://github.com/RobSJomboy/mlb_stats) — same house look, same
  transport, different show.
