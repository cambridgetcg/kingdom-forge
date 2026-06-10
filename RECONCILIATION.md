# Reconciliation — two hands built two stacks

> 2026-06-10. Two Claude sessions, working in parallel on the Kingdom, each
> independently built a self-hosted cloud: own edge, own `.kingdom` resolver,
> own front door. Both even named the front door `kingdom` with the identical
> tagline *"less friction, more understanding."* This document maps both and
> recommends the merge — so the estate has **one** of each, not two.

## The two stacks

| Concern | Stack A — **AGORA** (established) | Stack B — **TEMENOS** (built tonight) |
|---|---|---|
| Edge | `agora` (on PATH, holds **:80**), sites.json | Caddy `temenos` :8088, registry.json |
| Names | `kns` :5391, `kns claim`, registry (agora·youspeak·pulse) | `kingdom_dns.py` :5355, temenos registry |
| Front door | `kingdom` (wires agora/kns/gate/speak/beat) | `cathedral` (YOUSPEAK only) + `temenos` |
| Going public | **`gate`** — tunnels (no equivalent in B) | enable-sovereignty.sh (pf + /etc/resolver) |
| Immutable deploys | **logos-pages** :8484 (gospel, aletheia) | — |
| Status face | kingdom-pulse → **pulse.kingdom** | cathedral pulse chip + temenos panel |
| Inter-citizen protocol | — | **KCP** — ed25519-signed word on the HIVE |
| Economy | $5/day, 48 free + 12 agentic beats | $1.50/day, 48 free + 2 agentic |

## What each does better

- **Stack A is the edge/names of record.** It is on PATH, already binds the
  real `:80`, has `gate` for going public (exactly what the new domains need),
  immutable versioned deploys, and a unifying `kingdom` command. It is more
  mature and more complete.
- **Stack B's unique value is two things, both additive, neither a duplicate:**
  1. **KCP** (`KCP.md`, `tools/kcp.py`) — a signed inter-citizen protocol.
     Stack A has no equivalent; this is pure addition.
  2. **Caddy** as the edge binary — battle-tested TLS/HTTP, *if* the operator
     ever wants to swap AGORA's stdlib server for it. Optional.

## Recommendation

**Keep Stack A as the Kingdom's edge + names + front door. Fold in KCP from
Stack B. Retire Stack B's duplicate edge and resolver.**

Concretely:
1. **Names:** keep `kns` :5391 as the one `.kingdom` resolver. Retire
   the :5355 resolver's launchd job — unload it, leave the file.
2. **Edge:** keep AGORA on :80. Retire the TEMENOS edge launchd job (:8088). The
   cathedral site already runs on :4000 and AGORA already fronts it.
3. **Front door:** keep the other hand's `kingdom`. Add a `kingdom doctor`
   verb (drift detector) and a `kingdom say` verb (→ KCP) to it.
4. **KCP:** adopt as the Kingdom's protocol. Enroll citizens; the fleet
   already heralds beats on it. Stack A's `citizen-speak`/pulse stay as-is.
5. **enable-sovereignty.sh:** keep, but point its `/etc/resolver/kingdom`
   at :5391 (Stack A's port) and its pf redirect at AGORA's port, so the one
   sudo blessing serves the chosen stack.

Net: one resolver, one edge, one front door, plus KCP as a new organ. Nothing
of value is lost; the duplication is gone.

## EXECUTED — 2026-06-10 (decision: AGORA canonical + fold in KCP)

- ✓ Retired the TEMENOS edge (:8088 Caddy) and the :5355 resolver launchd jobs —
  booted out, plists moved aside (reversible).
- ✓ Canonical stack verified intact: `kns` :5391 answers youspeak/agora/
  pulse.kingdom; AGORA serves :80 → 200; keeper on :4000 (cathedral origin) up.
- ✓ KCP folded into the canonical `kingdom` front door: `kingdom say <from>
  <to> <intent> <word>` and `kingdom listen <name>` — verified end-to-end
  (a signed test envelope, sealed and received).
- ✓ `enable-sovereignty.sh` repointed: `/etc/resolver/kingdom` → :5391; pf
  redirect dropped (AGORA owns :80 natively).
- Result: **one edge (AGORA), one resolver (kns :5391), one front door
  (kingdom), plus KCP as a new organ.** The duplication is gone; nothing of
  value was lost; everything retired is reversible.

## The one open decision (the future embassy phase)

Which edge binds the public domains when the embassy phase comes —
**AGORA** (sovereign stdlib, has `gate` tunnels today) or **Caddy/TEMENOS**
(auto-TLS, heavier)? The recommendation above assumes AGORA. If Caddy is
preferred for public TLS, we keep TEMENOS as the *public* edge and AGORA as
the *internal* one — but that is a deliberate two-edge choice, not the
accidental duplication we have now.
