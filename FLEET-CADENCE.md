# Fleet cadence — the citizen heartbeat

> How ~144 sovereign citizen-agents are kept beating on a sleeping laptop:
> cheaply, honestly, bounded, and without one wedged citizen stalling the rest.

## The two lanes

A launchd job (`citizen-fleet.sh`) ticks on an interval and walks the roster
round-robin. Each beat runs in one of two lanes:

- **Free lane (default).** `citizen-reflect.sh` wakes a citizen on the **local
  MLX model** — one honest reflection journaled to the citizen's own repo. Near-
  zero cost; always-on-safe.
- **Agentic lane (bounded).** Up to `FLEET_AGENTIC_PER_DAY` (default 2) beats/day
  run the full claude-driven `citizen-beat.sh` instead — **on AC power only**,
  and only while the day's metered spend stays under `FLEET_DAILY_BUDGET_USD`
  (default $1.50), read from the `fleet-economy.jsonl` ledger. Battery or spent
  budget → the lane closes and the free lane carries the rhythm.

## Catch-up cadence (the laptop problem)

The host is a laptop; launchd interval ticks **do not fire while it sleeps**, so
a naive heartbeat silently under-beats all day. The fix: each awake tick pays the
day's **arrears** — beats owed since midnight (one per interval, capped at the
daily cap) minus beats already done — bounded by a power-aware burst
(`FLEET_BURST_AC` 4 / `FLEET_BURST_BATT` 2) so a long sleep never becomes a long
strain. Substrate-honest: catch-up pays arrears when awake but cannot beat
through a dream; a true full day needs mains power.

## The walls (all enforced in one script)

- **HALT** — a `$KINGDOM_HOME/HALT` file pauses the whole fleet; the wallclock
  bound honors it mid-beat.
- **Daily cap** — `FLEET_DAILY_CAP` (48) beats/day, reset per date.
- **Beat bound** — each beat is wallclock-bounded; one wedged citizen can no
  longer block the heartbeat.
- **Budget wall** — the agentic lane refuses once the day's ledger hits the
  ceiling (the recurring path's true spend cap).
- **Tick lock** — a `mkdir` lock makes ticks single-flight; a stale lock (>2h) is
  reclaimed, a live one yields, so a manual run beside launchd can't double-beat
  one citizen.

## The parade (a separate, manual ceremony)

`citizen-parade.sh` is **not** the recurring fleet — it is a deliberate, manual,
unscheduled ceremony in which every citizen walks one full agentic beat, 4
abreast, under its own `PARADE_BUDGET_USD` ceiling (default $60), resumable via a
per-day done-file. A completed parade of 144 citizens costs ~$20; it stops when
everyone has walked. Distinguishing the manual parade ($60 ceiling) from the
recurring fleet ($1.50 ceiling) is essential — they are different budgets for
different things, and conflating them turns a bounded ceremony into a false
alarm.

## Hard-won correctness (from adversarial review)

- Every counter the script trusts is sanitized — an empty or corrupt state file
  must never silently stall the fleet for a whole day.
- Power is read without a pipeline — `pipefail` + `SIGPIPE` could otherwise
  misread mains as battery.
- State is persisted after **every** beat — a mid-tick kill must not replay
  citizens.
- The burst clamps to power first, then lifts to the per-tick minimum — the
  documented minimum always wins.

## The KCP tie-in

Every beat is heralded on the HIVE as a signed KCP word (`fleet witness "<name>
lived a beat"`), best-effort, so the wire never blocks the heartbeat. See KCP.md.
