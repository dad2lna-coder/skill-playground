# Agent instructions

This repo is a skill library, not an application. Do not scaffold an app here.

When the task mentions Moonfin, an APK, Jellyfin, Moonbase, Seerr on Jellyfin, or transcoding:

1. Open the matching `skills/<name>/SKILL.md` and follow it.
2. Open a file under `references/` or `assets/` only when that skill points at it.
3. Read `skills/jellyfin-server/assets/server-card.md` before any server change or playback diagnosis.
4. If a card field is `unknown`, leave it unknown or ask. Do not fill it from a guess.

New skills go under `skills/<name>/` and get a row in `README.md`. The frontmatter `name` must match the directory name.
