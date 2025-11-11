## Siren Control (On/Off)

This dashboard feature enables direct **activation or deactivation** of all sirens and visual alarms at a given site.  
With one tap, operators can **trigger or silence** all connected sirens across interior and exterior zones — an essential capability for emergency response and routine testing.

<img width="441" height="203" alt="image" src="https://github.com/user-attachments/assets/bc3e509a-9bf1-4fc3-a478-040ae3d815a6" />

---

## Purpose

These cards control **audible sirens and LED alarms** remotely via the main Home Assistant dashboard:

- 🔊 **Orange / ON** — All configured alarms and sirens are active.  
- 🔇 **Blue / OFF** — Sirens are disabled.  

This enables fast, site-wide alerting or silencing without entering Node-RED.

---

## Lovelace Card (YAML)

```yaml
type: custom:button-card
entity: input_boolean.sv_sirens_boolean
name: SV
icon: mdi:volume-equal
size: 50%
show_state: true
state:
  - value: "on"
    color: orange
    icon: mdi:volume-high
  - operator: default
    color: blue
    icon: mdi:volume-mute
```

Each button represents a physical site (e.g., SV, JF, SF715).
Changing the entity name in entity: and name: duplicates the control for each location.

---

## Entity Definition (Remote Site Configuration.yaml)

The siren Boolean exists on **each remote Home Assistant instance**.  
All remote sites are connected to the HQ node via the **HACS Remote Home Assistant integration**  
(see [`remote-nodes.md`](./remote-nodes.md) for connection setup).

```yaml
input_boolean:
  sv_sirens_boolean:
    name: SV Sirens Boolean
    initial: false
```
`initial: false` ensures the sirens remain **off** after a system restart.  
When toggled **on**, the HQ dashboard immediately updates the remote entity through the **Remote Home Assistant integration**, allowing synchronized siren control across all connected sites.

---

## Node-RED Logic

When the button is pressed, **Node-RED listens for changes** to the siren Boolean and reacts accordingly.

---

### Sirens ON Flow

<img width="466" height="76" alt="image" src="https://github.com/user-attachments/assets/d15c730d-fc26-49f8-b002-3d30143d98f7" /><br>

<img width="492" height="575" alt="image" src="https://github.com/user-attachments/assets/eb7dc902-9155-41ad-b42d-52934382a75e" />

The **event: state** node monitors `SV Sirens Boolean`.  
When its state changes to `"on"`, Node-RED calls the **Turn On All Alarms** action node, which triggers every linked siren entity.

<img width="490" height="567" alt="image" src="https://github.com/user-attachments/assets/6576bf7e-1afd-4265-a59d-87fd88a4d202" />

This action node executes the `switch.turn_on` service on:

- `SV Alarm East Interior`  
- `SV Alarm West Interior`  
- `SV Alarm East Exterior`  

**Result:** all sirens and lights activate simultaneously.

### Sirens OFF Flow

<img width="777" height="124" alt="image" src="https://github.com/user-attachments/assets/9862a10d-4948-4b26-aa35-685ec424ea7f" />

Another **event: state** node monitors when the Boolean changes to `"off"`.  
This triggers two sequential actions:

1. **Turn Off Interior Alarms** — executes the `switch.turn_off` service to deactivate all siren and light entities.  
2. **Turn Off Siren Boolean** — resets the siren Boolean (`input_boolean.turn_off`), ensuring the dashboard state remains synchronized with the Node-RED logic.

**Result:** all sirens and visual indicators power down in unison, and the dashboard correctly reflects the “off” state system-wide.

---

## Why It Matters

⚡ **Instant control** — Trigger or silence every siren at a building with one tap.  
🌐 **Remote integration** — Uses *Remote Home Assistant* for seamless cross-site synchronization.  
🧠 **Automation-ready** — Node-RED handles timing and multi-device control automatically.  
🔒 **Fail-safe** — Remote booleans isolate site logic, ensuring each building operates independently and safely.
