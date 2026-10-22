---
name: jellyfin-playback
description: >
  Diagnose Jellyfin playback for Moonfin and other clients. Use when video
  stutters, transcodes unexpectedly, direct play fails, audio passes through
  or does not, subtitles burn in, or a device profile looks wrong. Read the
  server card first. Do not edit the client player until PlaybackInfo is known.
---

# Jellyfin playback

Read `skills/jellyfin-server/assets/server-card.md` first. Hardware accel, the install type, and the client list are unknown until that card says otherwise.

Moonfin on Android uses Media3 / ExoPlayer and tells the server which decoders that device actually has. The server should transcode only what that report cannot direct-play. A transcode of a file the device can play is a profile or settings bug, not a reason to re-encode the library.

## Order of operations

1. Name the client, the item, and the audio/subtitle/video codecs. If any of those are missing, ask. Do not guess HDR, TrueHD, or ASS.
2. Get `PlaybackInfo` for that item on that device (Dashboard session, or the client's playback report). Note the play method: direct play, direct stream, or transcode, and the reason string.
3. Only then change one thing.

## What each method means

| Method | Meaning |
| --- | --- |
| Direct play | File reaches the device as stored. Best case. |
| Direct stream | Container remux, codecs unchanged. Still cheap. |
| Transcode | Server decodes and re-encodes. Costs CPU or the GPU named on the card. |

A transcode reason that says the client refused the codec belongs to the client profile or the player. A reason that says the bitrate cap or a user setting forced it belongs to Jellyfin playback settings. Do not "fix" the second kind inside Moonfin.

## Hardware

Use the card's `hwaccel` value. Do not turn on VAAPI, QSV, NVENC, or AMF because a forum post said so. If `hwaccel` is `unknown`, say the server is software-transcoding unless the dashboard shows otherwise, and ask.

Tone-mapping and subtitle burn-in can force a software path even when a GPU is configured. Check that before declaring the GPU unused.

## Moonfin-specific

- Android TV direct-play notes from the Moonfin playback wiki include Dolby Vision Profile 7 and audio passthrough on Android TV. Treat those as device-dependent. A phone build is not a Shield.
- Phone flavor and `androidTv` flavor are different clients. A log from one does not explain the other.
- If Moonbase is not installed, Moonfin still plays. Do not install the plugin to fix a transcode.
