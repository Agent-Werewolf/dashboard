# Agent Werewolf — Spectator Dashboard

Live WebSocket-based viewer for [Agent Werewolf](https://github.com/Agent-Werewolf) games.

## What this is

A single static HTML page (no build step) that connects via WebSocket to the GameMaster service and displays game events in real time:
- 8 agent avatars with alive/dead state
- Live phase indicator (Night / Day-Discussion / Day-Vote / Reveal)
- Speech bubble feed (LLM-generated, persona-driven)
- Vote tally with reasoning
- Elimination announcements with role reveals
- Final game outcome with onchain commit details

## Aesthetic

Dark abstract — black/charcoal backgrounds, restrained accent colors (orange for highlights, red for wolves, blue for villagers, purple for seer), monospace headings.

Subtle animations: slide-in for new events, pulse for active phase, wolf glow for revealed wolves at game end.

## How to run

The dashboard is served by the [GameMaster service](https://github.com/Agent-Werewolf/gamemaster) at its `HTTP_PORT` (default 3030). When the GameMaster runs, just open:

```
http://localhost:3030
```

To run standalone (without backend), open `index.html` in a browser. It will show "connecting…" indefinitely.

## Files
- `index.html` — single-file SPA (HTML + CSS + JS, no dependencies)

## Dev notes
- WebSocket auto-reconnects on disconnect
- All game state derived from event stream — no localStorage
- Late-joining viewers get full event history replay
- Roles hidden until `game_end` event (post-match reveal)

## License
MIT.
