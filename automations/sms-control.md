# SMS Alarm Control (Twilio)

This automation lets authorized managers **arm/disarm sites** and **toggle sirens** by sending SMS commands to your Twilio number.  
It’s designed for cross-site control with fine-grained authorization — granting access per phone number and per site.

> **Dependency:** This relies on the **Twilio integration** in Home Assistant, which handles both inbound SMS events and outbound replies.  
> Details for configuring the integration are covered in [`/automations/twilio-integration.md`](./twilio-integration.md).

---
## Purpose

- Allow trusted users to control site alarms and sirens **without VPN or app access**.  
- Use a central **allowlist of phone numbers** to authorize commands.  
- Provide **immediate SMS feedback** confirming actions.

When an **“ARM”** command is received, the site’s Node-RED flow will automatically arm the alarm **after hours** (per your Node-RED schedule).  
When **“DISARM”** is used, the site’s alarm automation is **completely disabled**, regardless of time.

---

## 📱 Command Reference

The following commands are examples of how SMS-based site control works.  
You can update these to match your own **locations**, **site codes**, or **custom automation names**.

| Command | Action |
|:--|:--|
| `ARM SV` | Arm **SV location** (enable after-hours logic) |
| `DISARM SV` | Disarm **SV location** (disable all alarm logic) |
| `ARM SF715` | Arm **SF715 location** |
| `DISARM SF715` | Disarm **SF715 location** |
| `ARM JF` | Arm **JF location** |
| `DISARM JF` | Disarm **JF location** |
| `SIRENS SV ON` | Turn **ON** all sirens at the **SV location** (interior + exterior) |
| `SIRENS SV OFF` | Turn **OFF** all sirens at the **SV location** |
| `SIRENS SF715 ON` | Turn **ON** all sirens at the **SF715 location** |
| `SIRENS SF715 OFF` | Turn **OFF** all sirens at the **SF715 location** |
| `SIRENS JF ON` | Turn **ON** all sirens at the **JF location** |
| `SIRENS JF OFF` | Turn **OFF** all sirens at the **JF location** |
| `COMMANDS` | Returns a list of available commands by SMS |

> 🏷️ **Note:**  
> The examples above use `SV`, `SF715`, and `JF` as site identifiers.  
> These can be replaced or expanded for as many sites as your organization manages.
> You can easily **add or remove** site commands, or restrict them by phone number using the **allowlist** (explained below).

---

## How Authorization Works

- Each block begins by checking whether the incoming **`From`** number is in the **allowlist**.  
- If **authorized**, the message body is **trimmed** and converted to uppercase for matching.  
- If **unauthorized**, the sender receives an automatic **“Access denied.”**

The example code uses `+15051234567` as placeholder numbers — replace them with your real phone numbers.  
You can also use **different allowlists per command**, giving you granular control (e.g., only SF managers can arm/disarm **SF715**).

---

## Entities These Commands Control

These SMS commands manipulate the same **input_booleans** used by your dashboard buttons:

| Entity | Function |
|:--|:--|
| `input_boolean.<site>_alarm_boolean` | **Arming/Disarming** logic |
|  | `on` = armed → Node-RED will arm after hours and trigger on AI/motion |
|  | `off` = disarmed → fully disables alarms at all times |
| `input_boolean.<site>_sirens_boolean` | **All Sirens On/Off** |
|  | `on` = activates all configured sirens/LEDs |
|  | `off` = shuts them all down |

This ensures that **SMS control**, **dashboard control**, and **Node-RED automation** remain perfectly in sync.

---

## Home Assistant Automation (YAML)

> Paste this into **Settings → Automations → YAML mode**, or directly into your `automations.yaml`.  
> Only edit **phone numbers** and **entity IDs** — the logic below should remain unchanged.

```yaml
alias: HQ Twilio SMS → Alarm Control (allowlist + replies)
triggers:
  - event_type: twilio_data_received
    trigger: event
actions:
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'ARM SV' }}"
    then:
      - target:
          entity_id: input_boolean.sv_alarm_boolean
        action: input_boolean.turn_on
      - data:
          message: The South Valley Alarm has been armed!
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'DISARM SV' }}"
    then:
      - target:
          entity_id: input_boolean.sv_alarm_boolean
        action: input_boolean.turn_off
      - data:
          message: The South Valley Alarm has been disarmed!
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'ARM SF715' }}"
    then:
      - target:
          entity_id: input_boolean.sf715_alarm_boolean
        action: input_boolean.turn_on
      - data:
          message: The 715 Santa Fe Alarm has been armed!
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'DISARM SF715' }}"
    then:
      - target:
          entity_id: input_boolean.sf715_alarm_boolean
        action: input_boolean.turn_off
      - data:
          message: The 715 Santa Fe Alarm has been disarmed!
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'ARM JF' }}"
    then:
      - target:
          entity_id: input_boolean.jf_alarm_boolean
        action: input_boolean.turn_on
      - data:
          message: The Jefferson Alarm has been armed!
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'DISARM JF' }}"
    then:
      - target:
          entity_id: input_boolean.jf_alarm_boolean
        action: input_boolean.turn_off
      - data:
          message: The Jefferson Alarm has been disarmed!
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'SIRENS SV ON' }}"
    then:
      - target:
          entity_id: input_boolean.sv_sirens_boolean
        action: input_boolean.turn_on
      - data:
          message: South Valley sirens have been turned ON.
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'SIRENS SV OFF' }}"
    then:
      - target:
          entity_id: input_boolean.sv_sirens_boolean
        action: input_boolean.turn_off
      - data:
          message: South Valley sirens have been turned OFF.
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'SIRENS SF715 ON' }}"
    then:
      - target:
          entity_id: input_boolean.sf715_sirens_boolean
        action: input_boolean.turn_on
      - data:
          message: 715 Santa Fe sirens have been turned ON.
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'SIRENS SF715 OFF' }}"
    then:
      - target:
          entity_id: input_boolean.sf715_sirens_boolean
        action: input_boolean.turn_off
      - data:
          message: 715 Santa Fe sirens have been turned OFF.
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'SIRENS JF ON' }}"
    then:
      - target:
          entity_id: input_boolean.jf_sirens_boolean
        action: input_boolean.turn_on
      - data:
          message: Jefferson sirens have been turned ON.
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'SIRENS JF OFF' }}"
    then:
      - target:
          entity_id: input_boolean.jf_sirens_boolean
        action: input_boolean.turn_off
      - data:
          message: Jefferson sirens have been turned OFF.
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
      - condition: template
        value_template: "{{ trigger.event.data.Body | trim | upper == 'COMMANDS' }}"
    then:
      - data:
          message: >-
            Available commands: ARM SV, DISARM SV, ARM SF715, DISARM SF715, ARM
            JF, DISARM JF, SIRENS SV ON, SIRENS SV OFF, SIRENS SF715 ON, SIRENS
            SF715 OFF, SIRENS JF ON, SIRENS JF OFF
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
  - if:
      - condition: template
        value_template: >-
          {{ trigger.event.data.From not in ['+15051234567', '+15051234567',
          '+15051234567', '+15051234567', '+15051234567'] }}
    then:
      - data:
          message: Access denied.
          target:
            - "{{ trigger.event.data.From }}"
        action: notify.twilio_sms
mode: single
```
## Flow Explanation

### 🧩 Trigger
Listens for the `twilio_data_received` event, which carries:
- `From` → the sender’s phone number  
- `Body` → the SMS message content  

### 🔐 Authorization
Each `if:` block starts by checking `trigger.event.data.From` against an **allowlist**.  
If the number is **not present** in the allowlist, the final block sends an **“Access denied.”** message.

### 🧠 Command Matching
The message body is normalized using:
```jinja
{{ trigger.event.data.Body | trim | upper }}
```
### ⚙️ Actions
**ARM/DISARM** → flips the site’s `input_boolean.<site>_alarm_boolean`.  
- `on` = **armed** (Node-RED will auto-arm after hours & react to AI/motion)  
- `off` = **fully disarmed** (no alarm triggers at any time)

**SIRENS ON/OFF** → flips `input_boolean.<site>_sirens_boolean`,  
which your Node-RED flows translate into `switch.turn_on` / `switch.turn_off`  
calls for all sirens and LED alarms.


### 💬 Feedback
Every successful action replies via `notify.twilio_sms`  
to the sender, confirming exactly what happened.

---

## 🛡️ Security & Operations Tips

- **Per-site access:** Use different allowlists per section to restrict managers to their own locations.  
- **Secrets:** Move phone numbers to `!secret` variables for version-control hygiene.  
- **Audit:** In Home Assistant, events and automation runs are logged — use them as an audit trail.  
- **Rate limiting:** If needed, add a short delay or cooldown to avoid rapid toggling.  
- **Consistency:** Ensure the booleans referenced here match the entities used in your dashboard and Node-RED flows.  

---

## 🔗 Related Docs

**Dashboard buttons these map to:**  
- [/dashboard/alarm-arming.md](../dashboard/alarm-arming.md)  
- [/dashboard/sirens-control.md](../dashboard/sirens-control.md)  

**Node-RED logic overview:**  
- [/dashboard/alarm-arming.md](../dashboard/alarm-arming.md)  
- [/dashboard/sirens-control.md](../dashboard/sirens-control.md) *(flow screenshots)*  

**Twilio setup:**  
- [/automations/twilio-integration.md](./twilio-integration.md) — credentials, `twilio:` config, and enabling the `event → notify` bridge.  

---

## 💡 Why It Matters

- 🔐 **Least-privilege control** from known phones only  
- ⚡ **Instant response** to arm/disarm or trigger/silence sirens  
- 🧠 **Aligned** with your existing dashboard + Node-RED design  
- 🌐 **Multi-site ready** with clear separation of permissions by command  

