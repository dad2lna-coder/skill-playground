---
name: jellyfin-server
description: >
  Facts about this household's Jellyfin server. Use before any server change,
  plugin install, transcode setting, path, Docker edit, or hardware-accel
  advice. If the server card field is unknown, do not invent it.
---

# This Jellyfin server

The only source of truth is [assets/server-card.md](assets/server-card.md).

## Rules

- Read the card before recommending a path, a restart command, a GPU setting, or a Moonbase install step.
- `unknown` means unknown. Ask, or say you do not know. Never replace it with a common default and present that default as this server.
- Candidate paths for Linux package, Docker, and Windows live in the Moonbase build note. Quote one only when `install` on the card matches that row.
- Do not put secrets on the card: no passwords, API keys, or session tokens. A URL without credentials is fine.
- When the user corrects a fact, edit the card in the same change. Do not leave the correction only in chat.
- Moonfin client work does not belong in this skill. Playback decisions belong in `jellyfin-playback`, which must read this card first.
