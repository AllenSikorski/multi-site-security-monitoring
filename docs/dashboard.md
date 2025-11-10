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
``` 
### Entity Definition (configuration.yaml)
The boolean initializes as false so the tile remains red until there’s a positive AI person ID from the camera.

```yaml
input_boolean:
  sv_interior_person_activity:
    name: SV Interior Person Activity
    initial: false

### 24-Hour Auto-Reset (HA Automations)
This automation turns the corresponding boolean on when a person is detected, waits 24 hours, then turns it off.
(Adjust the delay for different reset windows—minutes, hourly, etc.)

```yaml
alias: SV Exterior Back Yard Person Detected
description: Turns on a boolean for 24 hours when motion is detected in the back yard
triggers:
  - entity_id: binary_sensor.sv_back_yard_person_detected
    to: "on"
    trigger: state
conditions: []
actions:
  - target:
      entity_id: input_boolean.sv_exterior_back_yard_person_activity
    action: input_boolean.turn_on
  - delay: "24:00:00"
  - target:
      entity_id: input_boolean.sv_exterior_back_yard_person_activity
    action: input_boolean.turn_off
mode: restart

This pattern makes it easy to glance at the dashboard and confirm whether anyone has been detected in the last 24 hours for each area.
