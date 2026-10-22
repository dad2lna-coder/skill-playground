# skill-playground

Agent skills for this account. One directory under `skills/` per skill, using the [Agent Skills](https://agentskills.io/specification) layout.

```text
skills/<name>/
  SKILL.md          # required; frontmatter name matches <name>
  references/       # long detail, loaded only when the skill says so
  assets/           # templates and fill-in facts
  scripts/          # optional executables
```

An agent working in another repo should read the matching `SKILL.md` before editing. `AGENTS.md` is the load order.

## Index

| Skill | Use when |
| --- | --- |
| [moonfin-android](skills/moonfin-android/SKILL.md) | Custom Moonfin APKs: Flutter flavors, signing, sideload |
| [moonbase](skills/moonbase/SKILL.md) | Moonfin's Jellyfin/Emby server plugin |
| [jellyfin-playback](skills/jellyfin-playback/SKILL.md) | Direct play vs transcode, device profiles, PlaybackInfo |
| [jellyfin-server](skills/jellyfin-server/SKILL.md) | This server. Fill the card before changing it |

## Add a skill

1. Create `skills/<name>/SKILL.md`.
2. `name` equals the directory name: lowercase letters, numbers, hyphens; no leading, trailing, or doubled hyphen; 64 characters max.
3. `description` says what the skill does and when to use it, with the words that should trigger it. 1024 characters max.
4. Keep `SKILL.md` short. Put command lists and tables in `references/`.
5. Add a row to the index above.

## Rules

- Do not vendor Moonfin or Jellyfin source in this repo. Moonfin-Core is GPL-2.0; a distributed modified APK needs its source published with it.
- Server facts live only in [skills/jellyfin-server/assets/server-card.md](skills/jellyfin-server/assets/server-card.md). Other skills read that card. They do not invent a Docker path, GPU, or version.
- If the environment has no Flutter, Android SDK, or .NET SDK, give the exact command and stop. Do not claim the APK or plugin was built.
