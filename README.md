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
| [moonfin-haos](skills/moonfin-haos/SKILL.md) | Any HAOS entity or feed inside Moonfin |
| power-platform/* | Microsoft Power Platform skills (Power Pages, model apps, canvas apps, Power Automate, PCF, MCP apps, mobile, etc.) copied from microsoft/power-platform-skills. See `skills/power-platform/` |

## Add a skill

1. Create `skills/<name>/SKILL.md`.
2. `name` equals the directory name: lowercase letters, numbers, hyphens; no leading, trailing, or doubled hyphen; 64 characters max.
3. `description` says what the skill does and when to use it, with the words that should trigger it. 1024 characters max.
4. Keep `SKILL.md` short. Put command lists and tables in `references/`.
5. Add a row to the index above.

## Rules

- Do not vendor Moonfin or Jellyfin source in this repo. Moonfin-Core is GPL-2.0; a distributed modified APK needs its source published with it.
- Jellyfin server facts live only in [skills/jellyfin-server/assets/server-card.md](skills/jellyfin-server/assets/server-card.md). HAOS facts live only in [skills/moonfin-haos/assets/haos-card.md](skills/moonfin-haos/assets/haos-card.md). Do not invent a URL, entity, GPU, or version.
- If the environment has no Flutter, Android SDK, or .NET SDK, give the exact command and stop. Do not claim the APK or plugin was built.

## Power Platform skills

The `skills/power-platform/` tree contains SKILL.md files from [microsoft/power-platform-skills](https://github.com/microsoft/power-platform-skills), organized by plugin. These are the open Agent Skills format and can be used with Gemini, ChatGPT/Codex, Claude, Copilot, etc.

Note: many of these skills assume Power Platform CLI (`pac`), MCP servers, and local tooling that is available in GitHub Copilot CLI / Claude Code plugin environments. In pure chat interfaces they primarily provide strong instructional workflows.
