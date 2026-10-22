# HAOS card

Fill this in. Leave unknown fields as `unknown`. Do not guess.

Never put the long-lived access token, a camera password, or any other secret in this file. The token stays in Moonbase plugin config.

```yaml
ha_url: unknown                 # LAN base the Jellyfin host can reach, no ingress path
frigate_url: unknown            # LAN base for the Frigate HTTP API, usually :5000
frigate_rtsp: unknown           # host:8554 the TV can open, or unreachable
frigate_install: unknown        # ha-addon | separate
clients_on_lan: unknown         # yes | no | mixed
emby: no
climate: []                     # climate.downstairs, ...
cameras: []                     # Frigate camera names
camera_entities: []             # camera.front, only if frigate_url is unknown
ha_token_stored: unknown        # yes once it is in plugin config, never the token
notes: ""
```
