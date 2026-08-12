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
3. **⚖ Send to Judge** — the full-screen card puts both up, side by side.
4. **Rule: Trev** / **Rule: Jake** — the loser greys out, the winner lights gold, the gavel bangs,
   and a **lower third** announces the ruling.

The ruling is the decision, not a caption over one: the winner goes into the slot, locked, with the
petal awarded. A who-is-who panel in the control spells out which name is whose so nobody rules for
the wrong side by accident.

---

## The graphics

The roster panel is deliberately the **same geometry as MLB_Standings** — 1920×1080 transparent,
528×1004 pinned right at `top:38px`, Rift, gold `#c8a84b`, same slide-in — so it drops into that
Browser Source without re-aligning anything.

- **Roster** — crest, month title, league, then a row per slot: position, headshot, name, club and
  petals. **Show Roster / Hide Roster**.
- **Ticker** — a bottom scroll announcing each lock-in with headshot, club, month and petals, in the
  same lockup as the trade-deadline scroll. Toggle with **▶ Ticker**.
- **Judge card** and **verdict lower third**, above.
- **Clear All** kills everything on screen at once.

The control page carries a **live preview**: the real output page, on the real topic, scaled to a
quarter. Not a mock — a mock drifts from the real thing exactly when it matters.

---

## Connection

Both pages subscribe to a named topic on [ntfy.sh](https://ntfy.sh), so the two machines don't have
to be on the same network — or in the same building. Nothing to install.

**It doesn't depend on one host.** ntfy.sh went down mid-use once, so the control publishes to
several public ntfy instances and the output subscribes to all of them. Any one being up is enough,
with no reconfiguring mid-show. `?ntfy=https://host` pins a single host if you ever need to.

**Topics are public to anyone who knows the name**, so the suggested one carries a random tail. Don't
shorten it to something guessable — that's a stranger's write access to your graphics.

Everything on the wire is absolute state with a sequence number, same as the standings build, so a
duplicated or late message can't leave the screen disagreeing with the control page.

---

## Notes

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
- Sibling tool: [Trade Snapshot](https://github.com/RobSJomboy/mlb_stats) — same house look, same
  transport, different show.
