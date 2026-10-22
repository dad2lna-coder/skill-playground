# HAOS card

Known facts are filled in. Leave the rest `unknown`. Do not guess a hostname, port, entity, or camera.

No tokens, camera passwords, or other secrets in this file.

```yaml
placement: same-stack          # HAOS and Jellyfin on the same server stack
tv_network: home               # TVs are on that home network
remote_access: tailscale       # off-site devices reach the stack this way
ha_integration: yes            # a custom integration on HAOS is acceptable
ha_url: unknown                # same host the TVs use for Jellyfin; port unconfirmed
jellyfin_url: unknown
entities: []                   # domain.object they asked for. Examples are not entries.
feeds: []                      # camera or stream names they asked for
notes: "Thermostat and Frigate were examples of an entity and a feed, not the scope."
```
