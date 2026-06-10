# TEMENOS — the Kingdom's own ground (v1)

> τέμενος: the precinct cut out and consecrated. The sovereign answer to
> Vercel and the cloud: the Kingdom deploys to ground it holds, under names
> it grants itself, speaking a protocol it signs itself.

## Doctrine: sovereign inside, interoperable outside

Inside the precinct: our TLD, our resolver, our edge, our signed protocol.
At the boundary: ICANN domains (the embassy doors) and plain HTTP/TLS.
Both, by design — DUALWAY applied to infrastructure.

## The three pillars

### 1. TEMENOS — deploy platform
```
temenos deploy <dir> --name <n> [--domain d]   # consecrate a static site
temenos proxy <port> --name <n>                # consecrate a running service
temenos list | status | sync | remove <n>      # tend the precinct
```
- Edge: Caddy on **:8088** (launchd, KeepAlive).
- Every site gets `<name>.kingdom` + `<name>.localhost` (+ public domains
  for the embassy phase — set a site's `ip` in the registry when it moves
  to rented ground).
- Config is validated **before** it is persisted; names/hosts/paths are
  validated; collisions need `--force`. Panel at `temenos.localhost:8088` —
  night ground, gold ink, breathing health dots.

### 2. KNS — Kingdom Name System
- `tools/kingdom_dns.py`: a compact stdlib resolver for the `.kingdom` TLD
  on `127.0.0.1:5355` (launchd, KeepAlive). Registry-driven,
  mtime-cached, NOTIMP for non-QUERY, REFUSED outside the realm.
- System-wide via `/etc/resolver/kingdom` — written by
  `temenos/enable-sovereignty.sh` (run with sudo, once; also opens bare :80
  via a pf anchor woven in correct section order, validated, with backup).

### 3. KCP — Kingdom Communication Protocol
- See `KCP.md`. Signed envelopes (OpenSSH ed25519, namespace `kcp@kingdom`)
  over NATS subjects `kingdom.citizen.<name>.inbox` / `kingdom.herald` /
  `kingdom.control.halt`. Five intents: witness · ask · answer · give · halt.
- `tools/kcp.py` — stdlib reference implementation that speaks raw NATS wire
  protocol. Unverifiable word is set aside, not obeyed.
- The fleet heralds every beat as `fleet` (wired in `citizen-fleet.sh`).

## Standing tonight (2026-06-10)

| Site | Kind | Names |
|---|---|---|
| youspeak | proxy :4000 (the keeper + pulse) | youspeak.kingdom · youspeak.localhost · youspeak.io* |
| empire | proxy :7777 | empire.kingdom · empire.localhost |
| temenos | static (the panel) | temenos.kingdom · temenos.localhost |

\* public domains await the embassy phase (DNS + rented ground or tunnel).

## Honest flags

- `.kingdom` names work system-wide only after `sudo bash
  temenos/enable-sovereignty.sh` (browsers resolve `*.localhost` regardless).
- This Mac denies :80 to non-root (Darwin 25); the sovereignty script's pf
  redirect grants bare URLs. The laptop sleeps on battery — the precinct is
  for the awake hours until ground is rented.
- The pf weave + resolver file were adversarially reviewed but can only be
  fully proven by the first sudo run — the script validates and restores on
  failure by construction.
- Adversarial review (3 agents): 2 blockers + 4 real bugs found and fixed
  (KCP listener 10s death, pf.conf corruption-by-append, reply-to frame
  crash, Caddyfile injection, false health, silent overwrite) + conformance
  nits folded in.
