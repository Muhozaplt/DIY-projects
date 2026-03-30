# 📡 Building My Own Meshtastic Node — A Complete Guide

> A hands-on walkthrough of building a LoRa mesh networking device using WisBlock hardware and Meshtastic firmware.

---

## Table of Contents

- [Overview](#overview)
- [What is Meshtastic?](#what-is-meshtastic)
- [Components Used](#components-used)
- [Technical Specifications](#technical-specifications)
- [Hardware Assembly](#hardware-assembly)
- [Firmware & Configuration](#firmware--configuration)
- [Mesh Role Setup](#mesh-role-setup)
- [Battery & Enclosure](#battery--enclosure)
- [Testing the Node](#testing-the-node)
- [Results & Range](#results--range)
- [Resources](#resources)

---

## Overview

This guide documents how I built my own [Meshtastic](https://meshtastic.org/) nodes using RAKwireless WisBlock hardware. Meshtastic is an open-source, off-grid mesh networking project that lets devices communicate over LoRa radio without any internet or cellular connection — perfect for hiking, disaster comms, or just geeking out.

---

## What is Meshtastic?

[Meshtastic](https://meshtastic.org/) is a free, open-source project that turns cheap LoRa radio boards into a long-range, off-grid mesh communication network. Devices relay messages across the mesh, extending range well beyond what a single radio can achieve.

Key features:
- **No internet required** — works completely off-grid
- **Long range** — LoRa radios can reach several kilometres line-of-sight
- **Encrypted** — AES-256 encrypted by default
- **GPS support** — share and track positions on a map
- **Phone integration** — connect via Bluetooth to the Meshtastic app (iOS/Android)

---

## Components Used

| Component | Model | Purpose |
|---|---|---|
| 🧠 Core Module | RAK4631 | Nordic nRF52840 SoC + SX1262 LoRa transceiver |
| 📋 Base Board | RAK19007 | WisBlock Base Board (30 × 60 mm) |
| 📺 Display | RAK1921 | 0.96" OLED display module |
| 📡 Antenna | 2 dBi rubber duck antenna | External LoRa antenna |
| 🔋 Battery | 3.7V 1100mAh LiPo | Polymer Lithium Ion |

---

## Technical Specifications

### Core Module — RAK4631

- **SoC:** Nordic nRF52840 (ARM Cortex-M4F @ 64 MHz)
- **LoRa Chip:** Semtech SX1262
- **Firmware:** cane be flashed from the web [here](https://flasher.meshtastic.org/) (use chrome or edge browser for this) 
- **Bluetooth:** BLE 5.0 (for phone app connectivity)
- **Interface:** WisBlock IO connector (plugs directly into base board)

### Base Board — RAK19007

- **Dimensions:** 30 × 60 mm
- **Sensor Slots:** 4 × WisBlock sensor slots
- **IO Slot:** 1 × WisBlock IO slot
- **Power:** USB-C charging + LiPo battery management (onboard TP4054 charger)
- **Connectors:** nano-SIM slot, I²C, SPI, UART exposed via headers

### Antenna

- **Type:** 2 dBi rubber duck (omnidirectional)
- **Connector:** IPEX/U.FL → SMA adapter
- **Gain:** 2 dBi (improved range vs PCB trace antenna)

### Battery

- **Chemistry:** Lithium Polymer (LiPo)
- **Voltage:** 3.7V nominal
- **Capacity:** 1100 mAh
- **Connector:** JST 1.25mm 2-pin (matches RAK19007 battery port)

---

## Hardware Assembly

### Step 1 — Prepare the RAK4631 Core Module

The RAK4631 can be flashed on the board from: https://flasher.meshtastic.org/. And verify the module is seated correctly before mounting.

> ⚠️ **Handle the RAK4631 with care.** Avoid touching the antenna pads or U.FL connector pins.

### Step 2 — Mount RAK4631 onto RAK19007 Base Board

1. Align the RAK4631 with the **WisBlock Core slot** (the large central slot on the RAK19007).
2. Press firmly and evenly until the connector clicks into place.
3. Secure with the two small M1.2 screws included with the base board.

```
RAK19007 Base Board
┌─────────────────────────────┐
│  [ WisBlock Core Slot ]     │  ← RAK4631 goes here
│                             │
│  [Sensor] [Sensor]          │
│  [Sensor] [Sensor]          │
│                    [IO]     │
│  [USB-C]  [Battery JST]     │
└─────────────────────────────┘
```

### Step 3 — Attach the RAK1921 OLED Display

1. Locate the **IO slot** on the RAK19007.
2. Align the RAK1921 OLED module pins with the slot.
3. Press firmly until seated. The OLED faces upward.

> 💡 The RAK1921 uses I²C and is automatically detected by Meshtastic firmware.

### Step 4 — Connect the Antenna

1. Locate the **U.FL connector** on the RAK4631.
2. Attach the U.FL end of your antenna pigtail cable.
3. Press straight down firmly — you'll feel/hear a faint click.
4. Screw the SMA end of the pigtail to your **2 dBi rubber antenna**.

> ⚠️ **Never power on the LoRa radio without an antenna connected.** This can damage the SX1262 transceiver.

### Step 5 — Connect the Battery

1. Plug the **LiPo JST connector** into the battery port on the RAK19007.
2. Ensure polarity is correct — the RAKwireless JST standard is **positive on the left**.
3. The onboard charger will handle charging when USB-C is connected.

---

## Firmware & Configuration

### Connecting to the Meshtastic App

1. Download the **Meshtastic app** on your phone ([iOS](https://apps.apple.com/app/meshtastic/id1586432531) | [Android](https://play.google.com/store/apps/details?id=com.geeksville.mesh)).
2. Power on your node — the OLED should display the Meshtastic boot screen.
3. On your phone, open the app → tap **+** → select **Bluetooth**.
4. Select your device from the list (e.g. `Meshtastic_XXXX`).
5. Pair using the PIN shown on the OLED display.

### Basic Configuration

Once connected via the app, configure these settings:

| Setting | Recommended Value |
|---|---|
| **Region** | Set to your country/region (e.g. `AU_915`) |
| **Node Name** | Give your node a unique name |
| **Role** | `CLIENT` (see below) |
| **LoRa Preset** | `LONG_FAST` or `LONG_SLOW` for range |
| **Bluetooth PIN** | Set a custom PIN |

> 🇦🇺 **Australian users:** Set region to `AU_915` to comply with local frequency regulations (915–928 MHz).

---

## Mesh Role Setup

### Client Role (with OLED Display)

This node uses the `CLIENT` role — the standard role for a handheld or portable device that sends/receives messages and displays them on screen.

**Why CLIENT?**
- Participates in mesh routing (relays messages)
- Connects to phone app via Bluetooth
- Displays messages and node info on the RAK1921 OLED
- Low power mode available to extend battery life

**Role configuration in the app:**

```
Device Config → Role → CLIENT
```

The OLED will show:
- Node name & short address
- Battery percentage
- Number of nodes in mesh
- Incoming messages

---

## Battery & Enclosure

### Battery Life Estimates

| LoRa Preset | BLE On | Estimated Runtime |
|---|---|---|
| LONG_FAST | Yes | ~12–16 hours |
| LONG_SLOW | Yes | ~18–24 hours |
| LONG_SLOW | No (BT off) | ~30–40 hours |

> 💡 Disable Bluetooth when not syncing with your phone to significantly extend battery life.

### Charging

The RAK19007 includes onboard USB-C charging. Simply connect a standard USB-C cable to charge the LiPo. A full charge from empty takes approximately **1.5–2 hours**.

### Enclosure Ideas

- **3D printed case** — RAKwireless publishes STL files for WisBlock enclosures on their GitHub
- **Waterproof project box** — Drill holes for antenna SMA passthrough and USB-C
- **Clip-on carabiner case** — Ideal for hiking/outdoor use

---

## Testing the Node

### Self-Test Checklist

- [ ] OLED displays boot screen on power-on
- [ ] Node appears in Meshtastic app via Bluetooth
- [ ] Region is correctly configured
- [ ] Antenna is firmly connected
- [ ] Battery charging works via USB-C
- [ ] Node appears on [Meshtastic map](https://meshtastic.org/map/) (if GPS module added)

### Sending a Test Message

1. Open the Meshtastic app.
2. Tap the **Messages** tab.
3. Send a message to `#Primary` channel.
4. Confirm it appears on the OLED display.
5. If other nodes are in range, they should receive and relay the message.

---

## Results & Range

> Results vary significantly based on terrain, antenna height, and LoRa settings.

In my testing:

- **Urban environment:** 1–2 km node-to-node
- **Suburban with elevation:** 3–5 km
- **Line-of-sight (hilltop):** 8+ km

The 2 dBi external antenna provided a noticeable improvement over relying on a PCB trace antenna alone.

---

## Resources

| Resource | Link |
|---|---|
| Meshtastic Official Site | https://meshtastic.org |
| Meshtastic Docs | https://meshtastic.org/docs |
| RAK4631 Datasheet | https://docs.rakwireless.com/Product-Categories/WisBlock/RAK4631 |
| RAK19007 Datasheet | https://docs.rakwireless.com/Product-Categories/WisBlock/RAK19007 |
| RAK1921 OLED Docs | https://docs.rakwireless.com/Product-Categories/WisBlock/RAK1921 |
| WisBlock GitHub | https://github.com/RAKWireless/WisBlock |
| Meshtastic Firmware GitHub | https://github.com/meshtastic/firmware |
| Meshtastic App (Android) | https://play.google.com/store/apps/details?id=com.geeksville.mesh |
| Meshtastic App (iOS) | https://apps.apple.com/app/meshtastic/id1586432531 |

---

## Contributing

Found an issue or want to add to this guide? Feel free to open a PR or raise an issue.

---

## License

This project documentation is released under the [MIT License](LICENSE).

---

*Built with ❤️ and LoRa radio waves.*
