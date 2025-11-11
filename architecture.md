# 🧱 System Architecture Documentation

## 🧭 Overview
This in-house multi-site security and automation system is built on **Home Assistant**, chosen for its broad hardware compatibility and seamless integration with third-party ecosystems.  
It enables unified automation across multiple subsystems — security, cameras, motion sensors, alarms, and communication — under a single configuration layer.

---

## 🏗️ Core Design

### Primary Hub
- Runs on an enterprise-grade server using **VMware ESXi** (or any compatible hypervisor).  
- Hosts the **main Home Assistant instance**, serving as the central control node.  

### Remote Nodes
- Each remote location operates on dedicated hardware (e.g., mini PCs or OptiPlex systems) running **Proxmox VE**.  
- Each host runs its own **Home Assistant virtual machine**, acting as a **remote node**.  
- All remote nodes connect to the HQ instance for data sharing and centralized logic.

### Remote Integration
Each Home Assistant node includes:
- **HACS** (Home Assistant Community Store) for community add-ons.
- **Remote Home Assistant Integration** for linking nodes together, enabling:
  - Centralized monitoring and automation logic.
  - Unified dashboards showing entities (sensors, Zigbee devices, cameras, etc.) across all sites.

---

## 🎥 Camera System Integration
Each site uses **AI-enabled IP cameras** managed locally (e.g., via UniFi Protect, Reolink, or similar).  
Camera integrations feed object detection events into Home Assistant.

<img width="1421" height="763" alt="image" src="https://github.com/user-attachments/assets/423381fd-0fb2-4273-91ea-e1fab7549694" />

- The automation layer only reacts to **“person detected”** events from the onboard AI models.  
- AI events trigger automation flows that control alarms and notifications.

---

## 🌐 Zigbee Network & MQTT Layer

### Zigbee2MQTT Setup
Each location includes:
- A **Zigbee coordinator** (e.g., SLZB-06P7 or CC2652-based adapter).  
- Configured via the Zigbee2MQTT add-on within Home Assistant.

All Zigbee devices — alarms, sensors, and smart plugs — communicate through an **MQTT broker** (Mosquitto add-on).  
This allows real-time messaging between automations and hardware devices.

### MQTT Broker
The broker acts as the communication backbone between Zigbee2MQTT and Home Assistant.

Typical ports:
- `1883` – MQTT (standard)
- `1884` – WebSocket access  

Used for reliable, low-latency communication between local and remote nodes.

---

## 🔔 Zigbee Devices & AI-Driven Alarm Logic

### Device Roles
- **Smart Plugs (e.g., Third Reality, SONOFF)** – used as alarm relays.  
  - **External alarms:** LED strobes + sirens for outdoor or perimeter zones.  
  - **Internal alarms:** Audible + LED sirens for interior notification.  
- **Zigbee Buttons/Switches** – manual test or override triggers.

### AI-Based Activation
Unlike traditional motion sensors, this system uses **AI-based human detection** before triggering alarms.

#### Sequence:
1. **AI Detection:** Cameras detect and classify objects.  
2. **Positive ID Filtering:** Only *person* events pass through.  
3. **Event Handling:** Node-RED receives the AI trigger via MQTT.  
4. **Zigbee Activation:** Smart plugs power the connected alarms/strobes.  
5. **System Logic:** Alarms remain active for ~5 minutes, then automatically reset.

This approach nearly eliminates false alarms (no triggers from animals, lighting, or weather).

---

## 🔄 Automation Logic (Node-RED)

### Flow Overview
The **Node-RED add-on** runs all automation logic.  
Input triggers include:
- Interior motion sensors  
- AI “Person Detected” events  
- Outdoor motion sensors  

#### Example Workflow:
1. **Input Trigger:** Motion or person detected.  
2. **Time Range Filter:** Only activates between 18:00 – 06:00 (after hours).  
3. **Action:**  
   - Turn sirens **ON**.  
   - Activate interior and exterior alarms.  
4. **Delay:** 5 minutes active, then auto-shutdown.  
5. **Notification:** Twilio sends phone call + SMS alerts.  
6. **Logging:** Event stored in Home Assistant history.

---

## 📞 Twilio Integration

The Twilio integration enables both automated alerts and remote text-based control.

### Notification Flow
When an after-hours trigger occurs:
- Twilio makes automated calls to designated personnel.
- SMS messages include event type and site location.
- All communications are logged in Home Assistant for auditing.

### Remote SMS Commands
Authorized users can arm/disarm alarms or control sirens via text.  
Commands are validated through an **allowlist** of trusted phone numbers.

#### Example Commands
| Command | Action |
|----------|--------|
| `ARM SITE1` | Enable automatic arming for that site. |
| `DISARM SITE1` | Disable all alarms for that site. |
| `SIRENS SITE1 ON/OFF` | Manually toggle sirens. |
| `COMMANDS` | Receive a list of all valid commands. |

Unauthorized senders automatically receive “Access denied.”

---

## 📊 Central Dashboard

A unified Home Assistant dashboard displays live status for all sites.

### Key Sections

#### AI Detection Status
- 🟩 **Green:** AI camera detected a person within the last 24 hours.  
- 🟥 **Red:** No detection (not an error, only inactivity).

#### Remote Connections
- 🟩 Online – remote node connected to HQ.  
- 🟥 Offline – connection lost.

#### Zigbee Network
- 🟩 Connected – coordinator operational.  
- 🟥 Disconnected – MQTT or Zigbee failure.

#### Alarm Arming
- 🟩 Armed – site set to auto-arm after hours.  
- 🟥 Disarmed – manual override for staff access.

#### Siren Control
- 🟧 On – sirens active.  
- 🔵 Off – sirens idle.

#### Network Health
- Displays gateway uptime, CPU, and memory metrics.  
- Icons link to reboot commands for quick remote recovery.

---

## ⚙️ Input Boolean Logic
Each alarm, siren, and AI status uses **input booleans** in Home Assistant:
- Tracks system states for dashboards and automations.  
- Automatically resets every 24 hours for a clean status window.

---

## 🧭 Operational Summary

| Step | Event | Action |
|------|--------|---------|
| 1 | AI or motion detected after hours | Trigger Node-RED automation |
| 2 | Time validation (18:00–06:00) | Confirm after-hours condition |
| 3 | Alarm activation | Lights and sirens ON |
| 4 | Twilio integration | Send SMS + phone call |
| 5 | Timer expiration | Auto-shutdown alarms |
| 6 | Event logging | Store entry for audit |

---

## 🔐 Security Practices
- SMS commands limited to allowlisted users.  
- VLAN separation for cameras, automation, and control networks.  
- Local automations continue even if cloud connectivity is lost.  
- Logs retained for audit visibility and troubleshooting.

---

## 🧩 Conclusion
This distributed automation system unifies AI-driven detection, smart devices, and remote communication into a resilient, site-independent architecture.

It delivers:
- Centralized monitoring  
- Site-level autonomy and redundancy  
- Real-time response via SMS and calls  
- Seamless integration of camera AI, Zigbee devices, and automation logic  

The platform provides a scalable, modular foundation for multi-location security and monitoring environments — easily adaptable for commercial, industrial, or enterprise use.

---
