# Calls the HA integration makes

Moonfin does not make these. The integration on HAOS does. `Authorization: Bearer` is an HA admin token and stays inside HAOS.

## Entity

```http
GET /api/states/<entity_id>
POST /api/services/<domain>/<service>
{"entity_id":"<entity_id>", ...only the fields that service requires}
```

Example, a thermostat: `climate.set_temperature` with `temperature`, or `climate.set_hvac_mode` with a mode already in `attributes.hvac_modes`. Read `current_temperature`, the setpoint, `state`, and `hvac_action`. Drop the rest.

The same two calls fit a lock, a switch, a cover, or a sensor. The service name comes from the ask, not from this file. If they did not name a service, the slice is read-only.

Poll every 15 seconds while the screen is open. No background poll.

## Feed

Frigate is the example, not the only camera.

```http
GET /api/<camera>/latest.jpg
```

Playable URL, opened by ExoPlayer on the TV, home network or Tailscale:

```text
rtsp://<this-stack>:8554/<camera>
```

Use it when the camera name is also the go2rtc stream name. No camera password in the URL. If the only URL still has `user:pass@`, stop.

If there is no Frigate base on the card, a still can be HA `GET /api/camera_proxy/<entity_id>`. That image is low resolution. Say so. Do not invent a Frigate host.

Do not use HAOS ingress, go2rtc HLS (`:1984/api/stream.m3u8`), or the jsmpeg detect stream.
