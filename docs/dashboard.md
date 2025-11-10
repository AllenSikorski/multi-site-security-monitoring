## 1) AI 24-Hour Status (Per-Camera Indicator)

This section shows whether a given camera has positively detected a **person** within the last 24 hours. Tiles reset automatically so you can quickly confirm recent activity.

---

### Dashboard Card (Lovelace)

```yaml
type: custom:button-card
entity: input_boolean.sv_interior_person_activity
name: SV Interior AI
icon: mdi:cctv
size: 50%
show_state: false
state:
  - value: "on"
    color: green
    icon: mdi:cctv
  - value: "off"
    color: red
    icon: mdi:cctv-off
