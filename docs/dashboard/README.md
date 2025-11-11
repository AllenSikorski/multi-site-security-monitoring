# 🖥️ Multi-Site Dashboard Overview

This section documents the **Home Assistant dashboard** that unifies multiple remote sites into a single, centralized view.  
It includes the **Lovelace YAML**, **supporting automations**, and **Node-RED logic** directly connected to dashboard controls — everything that powers the visual and interactive layer of the system.

---

## 📂 Dashboard Modules

| File | Description |
|:--|:--|
| [ai-status.md](./ai-status.md) | 24-hour AI detection summaries for each site, pulled from motion and person-detection sensors. |
| [alarm-arming.md](./alarm-arming.md) | One-tap site arming/disarming for after-hours automation logic. |
| [sirens-control.md](./sirens-control.md) | Direct control of audible and visual sirens across interior/exterior zones. |
| [unifi-network.md](./unifi-network.md) | Monitors UniFi gateway connectivity with one-tap reboot functionality. |
| [zigbee-status.md](./zigbee-status.md) | Tracks Zigbee coordinator health and triggers auto-recovery if offline. |
| [remote-nodes.md](./remote-nodes.md) | Explains how Remote Home Assistant links all site entities to HQ through HACS integration. |

---

## 🧩 What’s Included

Each markdown file contains:
- ✅ **Lovelace YAML** — the actual button-card or dashboard configuration used in Home Assistant.  
- ⚙️ **Automation snippets** — Home Assistant or Node-RED logic directly tied to that card’s operation.  
- 🧠 **Visual references** — screenshots of cards, Node-RED flows, and interface layouts for clarity.  
- 🗒️ **Explanations** — detailed purpose, behavior, and how each piece connects to the overall system.  
- 🌐 **Integration details** — configuration notes for **UniFi systems**, **HACS integrations**, and **Remote Home Assistant** nodes, showing how each site communicates with the HQ instance.

---

## 🌐 Dashboard Scope

This dashboard provides an at-a-glance view of all critical systems:

- **AI Detection:** real-time event tracking and daily summaries.  
- **Security Control:** alarm arming, siren activation, and recovery.  
- **Network Health:** UDM/UDM-Pro status and reboot management.  
- **Device Connectivity:** Zigbee coordinator and sensor uptime.  
- **Cross-Site Integration:** seamless communication through Remote Home Assistant.

Each card on the dashboard represents a real site and pulls its entity data from a **remote Home Assistant node** linked to HQ.

---

## 🧠 Purpose

- Centralize visibility across all operational sites.  
- Allow rapid response to alarms, connectivity loss, or system alerts.  
- Bridge remote nodes into one unified interface without compromising autonomy.  
- Keep visual design modular — each `.md` file corresponds to one card or system view.

---

> 💡 **Note:**  
> This documentation focuses on the **dashboard and its connected automations**.  
> Broader Node-RED logic, cloud integrations (Twilio, MQTT, etc.), and backend systems will be covered separately in a future `/docs/automations/` section.
