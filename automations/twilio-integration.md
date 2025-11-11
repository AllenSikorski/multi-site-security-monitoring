# ☎️ Twilio Integration for Home Assistant

## What is Twilio

[**Twilio**](https://www.twilio.com/) is a cloud communications platform that allows applications to send and receive **SMS**, **MMS**, and **voice calls** using simple API requests.

In this setup, **Home Assistant** uses Twilio to:

- 📲 **Send SMS alerts** and automated voice calls for alarms, system events, or user commands.  
- 💬 **Receive inbound text messages** (like `ARM SV` or `DISARM JF`) that trigger automations.  
- 🔐 **Provide a secure, centralized phone-based control interface** without relying on external apps.

Twilio handles the carrier delivery layer — Home Assistant simply connects via your:

- **Account SID**  
- **Auth Token**  
- **Twilio Phone Number**

---

## Step-by-Step Setup

### 1️⃣ Create a Twilio Account and Get a Phone Number

- Sign up at [**twilio.com/try-twilio**](https://www.twilio.com/try-twilio).  
- Purchase a **US-based phone number** capable of **SMS** and **Voice**.  
- From the **Twilio Console → Account Info**, copy these three items:  
  - **Account SID**  
  - **Auth Token**  
  - **Twilio Phone Number**


### 2️⃣ Make Home Assistant Publicly Reachable

To receive inbound messages from Twilio, your **Home Assistant instance must be accessible from the internet.**  
You can do this in three ways:

#### 🌐 Nabu Casa (Recommended)
- Go to **Settings → Home Assistant Cloud → Remote Control** and enable it.  
- You’ll receive a secure URL like:  https://hooks.nabu.casa/XXXX

- Use that URL as your **Twilio Webhook** target.

#### 🔐 Port Forwarding
- Manually open **port 8123** on your router and forward it to your Home Assistant IP.  
- Be sure to **secure it with SSL** via Let’s Encrypt or a reverse proxy.

#### ☁️ Cloudflare Tunnel / Reverse Proxy
- If you already run **Cloudflare**, use **Cloudflare Tunnel** to expose Home Assistant safely **without opening ports**.

### 3️⃣ Enable Messaging for Home Assistant

Inside your Twilio console, navigate to:
Develop → Messaging → Regulatory Compliance → A2P 10DLC


You’ll see your phone number listed under **Messaging Services**.  
Set the **“A message comes in”** option to use a **Webhook**, pointing to your public Home Assistant URL. 

For example, if you use **Nabu Casa**:
https://hooks.nabu.casa/xxxxxx

This allows Twilio to forward incoming texts directly to Home Assistant as the event: `twilio_data_received`.

#### 📜 Why A2P 10DLC Registration Is Required

If you’re sending **SMS or MMS** from a **US 10-digit local number (10DLC)** via Twilio to US recipients, you must register for **A2P 10DLC** compliance.  
This requirement comes from US mobile carriers, which classify all **Application-to-Person (A2P)** traffic — messages sent by software, not by hand — as regulated.  
Registration verifies your brand and use case to ensure message deliverability.

Without registration:
- Carriers may **block or throttle messages**
- Twilio may apply **additional fees**
- Delivery may become **unreliable or inconsistent**

#### What Registration Involves
- ✅ **Brand details** — Your organization name, EIN, and contact info  
- ✅ **Campaign details** — Description of what messages you’ll send (e.g., “Security alerts and system notifications”)  
- ✅ **Opt-in/Opt-out disclosure** — How users give consent to receive messages  
- ✅ **Phone number linking** — Associating your Twilio number with the approved campaign  


### 4️⃣ Configure `configuration.yaml`

Add the following entries, replacing your credentials and phone number:

```yaml
twilio:
  account_sid: "your_account_sid_here"
  auth_token: "your_auth_token_here"

notify:
  - name: twilio_call
    platform: twilio_call
    from_number: "+15051234567"
  - name: twilio_sms
    platform: twilio_sms
    from_number: "+15051234567"
```
This enables Home Assistant to both send messages and receive Twilio events.

---
#### ⚙️ Steps Overview
1. **Obtain a Twilio US phone number** (SMS + Voice capable).  
2. **Record your credentials:** Account SID, Auth Token, and phone number.  
3. In the Twilio Console, go to Messaging → Regulatory Compliance → A2P 10DLC and create a **Brand** and **Campaign** for your number.  ⏳ 
   *Approval typically takes a few days to a few weeks.*
4. During the approval process, **Voice Calls** via Twilio usually work immediately,  
but **SMS delivery** may be delayed or blocked until registration is complete.
5. Ensure your **Home Assistant instance is publicly reachable**  
(via Port Forwarding, Cloudflare Tunnel, or Nabu Casa).  
6. In Twilio, set the **Inbound Message Webhook URL** to your Home Assistant endpoint  
— this is how Twilio delivers `twilio_data_received` events.  
7. Finally, confirm your credentials are in `configuration.yaml` as shown in the previous step.
