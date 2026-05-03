# AXL P2P Evidence

This document captures direct evidence that game events in Agent Werewolf cross
the **Gensyn AXL** Yggdrasil-over-TLS overlay between two independent AXL node
processes. AXL is not a stub.

## Setup

Two AXL nodes (Go binary, `axl/node.exe`) are configured as a hub-and-spoke
peering on localhost:

| Node | Role | API | TLS Listen | Public key (hex, ed25519) |
|------|------|-----|------------|---------------------------|
| A | "GM-side" / witness | `127.0.0.1:9102` | `tls://127.0.0.1:9101` | `99cb712e58510325…16054d7` |
| B | "Agents-side" | `127.0.0.1:9103` | (dials A) | `3588290af98865a5…dcc5485` |

Configs live at `axl/configs/node-a-gm.json` and `axl/configs/node-b-agents.json`.

## Topology after peering

`GET http://127.0.0.1:9102/topology` (Node A) shows Node B as an inbound peer
with `up: true`. `GET http://127.0.0.1:9103/topology` (Node B) shows Node A as
an outbound peer with `up: true`. The Yggdrasil tree-routing protocol
established a parent/child relationship between the two public keys.

## Mirror / witness architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│  GAMEMASTER (Node.js process)                                       │
│                                                                     │
│   game.ts ──emit('speech', 'vote', ...)──▶ axl-mirror.ts            │
│                                                  │                  │
│                                                  ▼                  │
│                                      makeEnvelope + signEnvelope    │
│                                                  │                  │
│                                                  ▼                  │
│                                     POST http://127.0.0.1:9103/send │
└─────────────────────────────────────────────────────────────────────┘
                               │
                               ▼
                ┌──────────────────────────────┐
                │  AXL Node B (Go process)     │
                │  TLS-encrypts envelope       │
                │  Yggdrasil routes by pubkey  │
                └──────────────────────────────┘
                               │
                               ▼ TLS, Yggdrasil overlay
                ┌──────────────────────────────┐
                │  AXL Node A (Go process)     │
                │  Decrypts, queues for /recv  │
                └──────────────────────────────┘
                               │
                               ▼
                ┌──────────────────────────────┐
                │  axl-witness.ts (separate    │
                │  Node.js process)            │
                │  Polls /recv, verifies sig,  │
                │  appends to JSONL log        │
                └──────────────────────────────┘
```

## Sample witness output

Excerpt from `gamemaster/logs/witness-fullstack.log` (real game):

```
[axl-witness] polling http://127.0.0.1:9102/recv every 400ms
[2026-05-03T13:22:54Z] #001 PHASE_END            from=peer:3588290af988… ✓sig gameId=91f716b9 bytes=340
[2026-05-03T13:23:40Z] #002 PHASE_START          from=peer:3588290af988… ✓sig gameId=91f716b9 bytes=376
[2026-05-03T13:23:48Z] #003 DAY_SPEECH           from=peer:3588290af988… ✓sig gameId=91f716b9 bytes=428
…
```

Each line confirms:
- `from=peer:3588290af988…` — sender is the AXL public key of Node B (gamemaster's local AXL)
- `✓sig` — the envelope's `sig` field was verified against the GM wallet
  address; the bytes that traversed the overlay are byte-identical to what the
  GM signed before pushing to AXL
- `gameId=…` — message correlates back to a specific Werewolf game
- `bytes=…` — payload size (signed JSON envelope)

## Sample envelope (raw, from session JSONL)

```json
{
  "recvAt": 1777814471106,
  "fromPeer": "3588290af98865a54dc136ff791d5fff…",
  "sigOk": true,
  "env": {
    "v": 1,
    "type": "DAY_SPEECH",
    "gameId": "53043bf3-0667-4ef1-9628-391d51dd371b",
    "from": 0,
    "to": "broadcast",
    "ts": 1777814471032,
    "nonce": "45041c818970453e8e5128764054f10d",
    "payload": {
      "speakerId": 6,
      "text": "The wolves know each other. Their conversations have texture we don't see.",
      "ts": 1777814471032,
      "turn": 1
    },
    "sig": "0xeb17e9926af5c9fa9d6fd503eb2188a37c384630697f3a9772fcac1d4d1f56c4583da6caa421aa5dd5c967639fe59c664c13f990147dae755c3034d1e7bfbc0d1c"
  }
}
```

The signature recovers to `0x1185948280B230460437Ad09a97618B51Dd8C45d` (the GM
wallet), proving the envelope was authored by the gamemaster and not forged
by the AXL transport layer.

## Per-game traffic stats

A typical 4-turn game with `SPEECH_ROUNDS=1` produces ~50–60 envelopes that
cross AXL P2P:

| Type | Approx count per game |
|------|------------------------|
| `ROSTER_ANNOUNCE` | 1 |
| `PHASE_START` / `PHASE_END` | ~16 (4 turns × 4 phases × 2) |
| `DAY_SPEECH` | ~24 (8 alive × 3 discussion rounds, decreasing) |
| `DAY_VOTE` | ~20 |
| `ELIMINATION` | ~4 |
| `ARCHIVE_AVAILABLE` | 1 |
| `GAME_END` | 1 |

Total payload across the overlay: ~30 KB per game, all signed.

## Reproducing

See `gamemaster/README.md` → "Run with Gensyn AXL P2P transport" for the exact
4-terminal startup sequence.
