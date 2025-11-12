# 🛰️ Multi-Site Security Monitoring Platform

A distributed automation and security monitoring system designed, installed, and configured entirely from scratch.  
This platform integrates AI-based person detection, Zigbee automation, and secure SMS-based control to protect multiple commercial sites.

---

## 🧠 Overview
This project demonstrates a fully functional, production-style multi-site security platform that automates after-hours surveillance and alarm control.  
The system intelligently triggers alerts only on **AI-confirmed human detection**, avoiding false positives from animals or movement.

It provides centralized visibility, remote management, and a secure SMS interface for authorized users to arm, disarm, or control sirens.

---

## 🏗️ Architecture

<img width="906" height="885" alt="image" src="https://github.com/user-attachments/assets/47012712-7c33-4847-b550-35c5a7624edd" />

- **AI Detection Layer** – Network cameras with onboard AI detect confirmed human presence before sending event triggers.  
- **Automation & Control Layer** – Zigbee smart plugs (Third Reality & SONOFF) activate LED and siren alarms through local automations.  
- **Core Logic Layer** – Node-RED automations coordinate all sensors, detection events, and siren triggers.  
- **SMS Control Interface** – Twilio integration allows authorized users to arm/disarm or toggle sirens via text message.  
- **Hypervisor Hosting** – Can run on VMware ESXi, Proxmox, or any preferred virtualization environment for portability and easy scaling.  
- **Optional Cloud Hosting** – Can be deployed to EC2 or other cloud VMs for remote access and centralized management.  
- **Networking** – VLAN-segmented network ensures isolation of camera, automation, and control traffic.

---

## ⚙️ Features
- AI-verified person detection reduces false alarms  
- Automated after-hours arming/disarming using Node-RED logic  
- Secure SMS commands for manual override (ARM, DISARM, SIRENS ON/OFF)  
- Multi-site dashboard with arm/disarm indicators  
- Fault-tolerant design with local automation fallback (no cloud dependency)  
- Modular — easily adapted for additional locations or IoT devices  

---

## 🧰 Tech Stack
| Category | Technologies |
|-----------|--------------|
| Automation & Control | Node-RED, Zigbee (Third Reality, SONOFF) |
| AI Detection | Smart IP Cameras with onboard AI analytics |
| Communication | MQTT, Twilio API (SMS gateway) |
| Virtualization | VMware ESXi, Proxmox VE |
| Monitoring & Visualization | Custom dashboard with site status and logs |

---

## 🔒 Security
- SMS commands limited to a verified allowlist of users  
- VLAN separation between camera, automation, and management layers  
- Node-RED input validation and secure credential storage  
- Local failover logic ensures siren control even if external connectivity drops  

---

## 🧩 Author
**Allen Sikorski**  
Systems Administrator / Automation Engineer  
[LinkedIn](https://linkedin.com/in/AllenSikorski) | [GitHub](https://github.com/AllenSikorski)
