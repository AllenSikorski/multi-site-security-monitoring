# Alarm Arming (On/Off)

This dashboard control allows you to **arm or disarm the entire alarm system** for a given site with a single tap.  
Behind this simple button is a coordinated automation flow that governs the behavior of the site’s alarms, sirens, and notification logic.

---

## Purpose

This button determines whether a site’s alarm system is **active** or **inactive**:

- 🟩 **Green / Armed (ON)** — The alarm logic is enabled. After-hours AI or motion detections will trigger full alarm sequences and notifications.  
- 🟥 **Red / Disarmed (OFF)** — The alarm system is disabled. No motion or AI events will trigger alarms, useful for authorized after-hours access or maintenance.

This control ensures site managers can securely toggle alarm behavior without diving into Node-RED or configuration menus.

---

## Lovelace Card (YAML)

```yaml
type: custom:button-card
entity: input_boolean.sf715_alarm_boolean
name: SF715
icon: mdi:alarm-light-off
size: 50%
show_state: true
state:
  - value: "on"
    color: green
    icon: mdi:alarm-light
  - operator: default
    color: red
    icon: mdi:alarm-light-off
```
Each card corresponds to a single site.
You can duplicate this format across all locations by changing the entity name and site label.

---
## Entity Definition (Remote Site Configuration.yaml)
This input boolean must be created on the remote site’s Home Assistant, not on the HQ instance.
The Node-RED flow at each site uses this boolean to determine if its alarm logic should be active or skipped.

```yaml
input_boolean:
  sf715_alarm_boolean:
    name: SF715 Alarm Boolean
    initial: true
```
 - initial: true ensures the alarm system defaults to armed after a restart, maintaining after-hours protection.
 - Setting it to false leaves the site disarmed until manually re-enabled.

---

## Functionality Overview

When the boolean is **true**, the site’s Node-RED flow activates automatically after hours (for example, **20:00–06:00**).  
If any AI or motion detection occurs during that window, the system will:

- 🚨 **Activate sirens and alarms**  
- 📞 **Send Twilio phone and SMS notifications**  
- 🧾 **Log the event** for audit visibility  

When **false**, these automations are **bypassed entirely**, providing a safe mode for maintenance crews or authorized after-hours access.

<img width="1827" height="372" alt="image" src="https://github.com/user-attachments/assets/6ee912fb-39ad-438d-acf2-ae47a729037b" />

A detailed breakdown of the Node-RED logic is available in the [`Node-RED.md`](./Node-RED.md) section.

---

## Why It Matters

- 🔐 **Centralized control** — One tap arms or disarms an entire site’s alarm logic  
- 🧠 **Local autonomy** — Each remote node independently manages its alarm logic even if HQ is unreachable  
- 🕒 **Operational flexibility** — Allows for scheduled work or maintenance without false alarms  
- ⚙️ **Clean integration** — Seamlessly ties into Node-RED without additional scripts or manual triggers  
