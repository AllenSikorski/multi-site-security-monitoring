# UniFi Network Connections

This section shows the **live gateway status** for each site and lets you **reboot a UDM/UDM-Pro** with one tap (with a confirmation).  
It’s designed for quick, safe recovery when a site drops offline.

<img width="435" height="185" alt="image" src="https://github.com/user-attachments/assets/135c2d46-f12f-4173-a5e7-2cf70ab8c2be" />
---

## Lovelace Card (YAML)

```yaml
type: custom:button-card
name: SV
entity: sensor.south_valley_udm_pro_state
icon: mdi:lan
show_state: false
size: 50%
tap_action:
  action: call-service
  service: button.press
  target:
    entity_id: button.south_valley_udm_pro_restart
  confirmation:
    text: Reboot gateway now?
state:
  - value: connected
    color: green
    icon: mdi:lan-connect
  - operator: default
    color: red
    icon: mdi:lan-disconnect
```
`sensor.south_valley_udm_pro_state` is a **connectivity/state sensor** for the site’s gateway.

Tapping the tile calls `button.south_valley_udm_pro_restart`, which is the **Restart** entity exposed by the UniFi integration for that gateway.

> 💡 **Tip:** The confirmation prompt prevents accidental reboots.

---

## What You Need (Per Site)

1. **UniFi UDM / UDM-Pro** (or any UniFi OS gateway) at each location.  
2. **Home Assistant** running locally at that site (remote node).  
3. **UniFi Network integration** added on that remote Home Assistant node:

   - Create a **local UniFi user** (*Settings → Admins*) or an **API Token**  
     (*Settings → System → API Access → Create new token*).  
   - **Minimum scopes** if using a token:  
     - `Device write` (to allow reboots)  
     - `Read`
   - In Home Assistant:  
     **Settings → Devices & Services → Add Integration → Ubiquiti UniFi Network**  
   - Enter your UniFi OS URL (e.g., `https://<udm_ip_or_dns>`) and authenticate using the local user or API token.  
   - Select the appropriate **site** when prompted.

---

### Entities Created

- `button.<gateway_name>_restart` — used to trigger a reboot  
- `sensor.<gateway_name>_...` / `binary_sensor.<gateway_name>_...` — link state, WAN status, uptime, etc.

> 💡 **Tip:**  
> If a dedicated *state* sensor is not available, use a WAN `binary_sensor` or create a **template sensor** that maps `on/off` to `connected/disconnected` for the card.


---
## Cross-Site (HQ) View

All remote nodes are linked to HQ via **Remote Home Assistant (HACS)**.  
Once each remote site has the UniFi integration configured, those entities are mirrored to HQ and can be added directly to this dashboard.

See [`remote-nodes.md`](./remote-nodes.md) for the **Remote Home Assistant** setup and entity sharing details.

---

### Behavior

- 🟩 **Green / Connected** — Gateway is reachable by Home Assistant; tile shows `mdi:lan-connect`.  
- 🟥 **Red / Disconnected** — Any other state; tile shows `mdi:lan-disconnect`.  
- 🔁 **Tap to Reboot** — Sends a reboot to the site’s UDM/UDM-Pro via the UniFi integration’s `button.press`.

> 💡 **Why reboot instead of power-cycle?**  
> A clean software reboot avoids filesystem issues and minimizes risk to UniFi OS.

---

### Hardening & Tips

- 🔐 Use a **site-scoped local account or API token** with limited scopes (no need for full admin).  
- ⏱️ Tokens can expire — document rotation and include an **Integration Health** tile.  
- 📜 If your UniFi OS uses a self-signed certificate, enable **Ignore SSL** during integration or install a trusted cert.  
- ⚙️ Keep a **manual WAN-outage playbook** — this control is intentionally **operator-driven**, not automated.

---

### Troubleshooting

- ⚠️ **Entities missing:** Reopen the UniFi integration and re-authenticate (token expired or password changed).  
- 🔁 **Button press fails:** Ensure the token/user has `Device write` and HA can reach `https://<udm>`.  
- 🧭 **State stuck:** Use a WAN binary sensor (e.g., `*_wan_status`) or create a template mapping to `connected` / `disconnected`.

---

### Why It Matters

- ⚡ **Fast recovery** — Reboot a site gateway safely with one tap.  
- 🧭 **At-a-glance health** — Clear green/red status across all locations.  
- 🌐 **Multi-site ready** — Works seamlessly with Remote Home Assistant.  
- 🔒 **Safe by design** — Confirmation prompts + least-privilege API access.


