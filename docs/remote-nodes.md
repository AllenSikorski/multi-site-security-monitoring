# Remote Home Assistant Nodes (Online/Offline Status)

This section shows whether each site’s **remote Home Assistant** node is online.  
If a remote node goes offline, the card immediately reflects it so you can react quickly.

---

## Prerequisites

### HACS + Remote Home Assistant
All instances use **HACS (Home Assistant Community Store)** to install **Remote Home Assistant**:

- Each site runs its own Home Assistant instance.
- The **HQ** instance adds each site as a **Remote Home Assistant** connection.
- HQ then **pulls entities** (sensors, switches, Zigbee, etc.) from each site for centralized visibility and automation.

> Result: You get centralized monitoring and logic at HQ, while each site remains autonomous if HQ is unreachable.

---

## What the Card Shows

Each card reflects the **connection state** from HQ → a given site’s HA instance.

- 🟩 **Green / connected**: HQ can reach the remote HA node and is receiving entity updates.
- 🟥 **Red / disconnected**: The remote node is offline or the link is down.

This is ideal for a quick at-a-glance **node health check** across locations.

---

## Lovelace Card (YAML)

```yaml
type: custom:button-card
entity: sensor.remote_connection_to_192_168_7_12_8123
#Replace sensor.remote_connection_to_192_168_7_12_8123 with the sensor created by your Remote Home Assistant integration for each site.
name: South Valley
icon: mdi:lan-connect
size: 50%
show_state: false
state:
  - value: connected
    color: green
    icon: mdi:lan-connect
  - operator: default
    color: red
    icon: mdi:lan-disconnect
```

