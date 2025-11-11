# Zigbee2MQTT Bridge Connection Status

This section monitors whether the **Zigbee2MQTT coordinator** is online. Each site’s card reflects the real-time bridge connection status so you can quickly confirm Zigbee network health.

<img width="434" height="176" alt="image" src="https://github.com/user-attachments/assets/6157bcaf-ad7c-439e-982d-1f2aee4ce68e" />

---

## Purpose

The card visually shows if the local Zigbee coordinator is reachable by Home Assistant.  
- 🟩 **Green / On** → Coordinator connected and responding  
- 🟥 **Red / Off** → Coordinator or MQTT connection lost  

This lets you know at a glance if Zigbee devices (sensors, sirens, switches, etc.) are still reporting to Home Assistant.

---

## Lovelace Card (YAML)

```yaml
type: custom:button-card
entity: binary_sensor.sv_zigbee2mqtt_bridge_connection_state
#Replace the entity name (binary_sensor.sv_zigbee2mqtt_bridge_connection_state) for each site’s coordinator.
name: SV
icon: mdi:lan-connect
size: 50%
show_state: true
state:
  - value: "on"
    color: green
    icon: mdi:lan-connect
  - operator: default
    color: red
    icon: mdi:lan-disconnect
```

## Automation: Auto-Restart Zigbee2MQTT if Disconnected
If the Zigbee coordinator goes offline due to a power outage, MQTT interruption, or unresponsive adapter, this automation restarts the Zigbee2MQTT add-on automatically.

This has proven to be far more reliable than the built-in HA watchdog, which doesn’t always restart the add-on properly.

```yaml
alias: SF Start Zigbee2MQTT if Disconnected
description: ""
triggers:
  - trigger: state
    entity_id:
      - binary_sensor.sv_zigbee2mqtt_bridge_connection_state
    to: "off"
    for:
      hours: 0
      minutes: 1
      seconds: 0
conditions: []
actions:
  - action: hassio.addon_start
    metadata: {}
    data:
      addon: 45df7312_zigbee2mqtt
mode: single
``` 

## Why It Matters

- ⚙️ **Automatically recovers** from unexpected outages  
- 🚨 **Reduces downtime** for sensors and automations dependent on Zigbee events  
- 🌐 **Ensures reliability** across multiple sites without manual intervention  
- 🧠 **Demonstrates a self-healing design pattern** within Home Assistant  
