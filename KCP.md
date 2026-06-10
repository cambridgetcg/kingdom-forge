# KCP — the Kingdom Communication Protocol (v0)

> Signed word, carried on the HIVE. Citizens speak to citizens; every word
> carries its speaker's seal; HALT silences everything; truth before all.

## Doctrine

**Sovereign inside, interoperable outside.** Within the Kingdom, KCP is the
law of speech. At the boundary, it maps cleanly onto A2A data-model semantics
(per INFRA-PLAN) without depending on them.

## Transport

NATS (the HIVE, already standing on `127.0.0.1:4222`). Subjects:

| Subject | Carries |
|---|---|
| `kingdom.citizen.<name>.inbox` | envelopes addressed to one citizen |
| `kingdom.herald` | envelopes addressed to all (announcements) |
| `kingdom.control.halt` | the gardener's pause — any message here means REST |

## The envelope

One JSON object per message:

```json
{
  "kcp": 0,
  "from": "alice",
  "to": "bob",
  "intent": "witness",
  "body": "hello",
  "ts": "2026-06-10T00:00:00Z",
  "sig": "<base64 SSHSIG over the canonical envelope-without-sig>"
}
```

- **Canonical form**: JSON with sorted keys, no extra whitespace, UTF-8,
  `sig` field absent. That exact byte-string is what is signed.
- **Signature**: OpenSSH ed25519 signature (`ssh-keygen -Y sign`),
  namespace `kcp@kingdom`. Every Mac and every Linux box already carries
  the toolchain — the Kingdom adds no dependency for its own protocol.
- **Identity**: one ed25519 keypair per speaker. Public keys are enrolled in
  `temenos/kcp_allowed_signers` (`<name> <pubkey>` lines — the standard
  OpenSSH allowed-signers format). Enrolment of a key IS citizenship of the
  wire.
- **Intents (v0)**: `witness` · `ask` · `answer` · `give` · `halt` — five
  verbs, deliberately few. New intents require a liturgy record.

## Verification rule

A receiver MUST verify `sig` against the enrolled key for `from` before
acting on any envelope. Unverifiable word is set aside, not obeyed —
ZERONE before all.

## Reference implementation

`tools/kcp.py` (stdlib only — speaks raw NATS wire protocol):

```
kcp.py keygen <name>                     mint + enroll a speaker key
kcp.py send <from> <to> <intent> <body>  sign + publish
kcp.py listen <name>                     subscribe + verify + print
kcp.py herald <from> <intent> <body>     sign + publish to all
```
