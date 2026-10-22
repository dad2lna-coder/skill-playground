# HA and Frigate calls

The plugin makes these. The APK does not. Send `Authorization: Bearer <token>` to HA only.

## Climate

```http
GET /api/states/climate.<id>
```

Use `attributes.current_temperature`, `attributes.temperature` (the setpoint; some devices use `target_temp_high` / `target_temp_low`), `state` (the mode: `heat`, `cool`, `heat_cool`, `off`, …), and `attributes.hvac_action` when present. Drop the rest. Do not return `attributes` whole.

```http
POST /api/services/climate/set_temperature
{"entity_id":"climate.<id>","temperature":72}

POST /api/services/climate/set_hvac_mode
{"entity_id":"climate.<id>","hvac_mode":"heat"}
```

`hvac_mode` must be one of that entity's `attributes.hvac_modes`. Reject anything else before the call.

Polling: the thermostat screen may poll the proxy every 15 seconds while it is open. No background poll. A websocket subscription is a later slice, not this one.

## Frigate

Camera name is the Frigate camera name, which is also the go2rtc stream name when they match.

```http
GET /api/<camera>/latest.jpg
GET /api/events?camera=<camera>&limit=20
```

Live, played by ExoPlayer on the device, not proxied:

```text
rtsp://<frigate-host>:8554/<camera>
```

That restream has no camera password. If the only URL on the card still has `user:pass@`, stop and say so. Do not embed those credentials in the client.

HA still fallback, only when `frigate_url` is unknown:

```http
GET /api/camera_proxy/camera.<id>
```

JPEG, low resolution. Not a live stream.

## Not these

- HA ingress (`/api/hassio_ingress/...`)
- go2rtc HLS `http://<host>:1984/api/stream.m3u8?src=<camera>`
- WebRTC port 8555
- jsmpeg detect stream
- Any HA domain other than `climate`, plus the camera still path above
