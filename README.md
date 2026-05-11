# Z2M PTM 215Z Smart Dimmer — Home Assistant Blueprint

A [Home Assistant](https://www.home-assistant.io/) blueprint for smooth, hardware-level dimming using the **EnOcean PTM 215Z** (Friends of Hue) and **Light Solutions Lightbee ZigBee** switch — via [Zigbee2MQTT](https://www.zigbee2mqtt.io/).

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2FPlanckus%2Fha-blueprint-ptm215z-smart-dimmer%2Fmain%2Fblueprints%2Fptm215z_smart_dimmer.yaml)

---

## What it does

This blueprint turns your 4-button switch into a full lighting controller with one automation per switch — no scripts, no helpers, no extra automations needed.

| Button | Action | Result |
|--------|--------|--------|
| **1** | Short press | 💡 Turn on — restores last known brightness |
| **2** | Hold | ☀️ Dim up smoothly — release to stop anywhere |
| **3** | Short press | 🌑 Turn off |
| **4** | Hold | 🌙 Dim down smoothly — release to stop anywhere |

### How dimming works

Unlike transition-based approaches that ramp to a fixed target, this blueprint uses the native Zigbee **`brightness_move`** command. This means:

- The **bulb's own firmware** handles the dimming — it's perfectly smooth
- **Release the button** at any brightness level to stop exactly there
- Works simultaneously across all bulbs in a light group
- No HA polling, no scripts, no helpers required

---

## Supported switches

| Switch | Zigbee2MQTT support |
|--------|-------------------|
| **EnOcean PTM 215Z** (Friends of Hue) | ✅ Full support |
| **Light Solutions Lightbee ZigBee switch** | ✅ Full support |
| Any other Friends of Hue compatible switch using PTM 215Z | ✅ Should work |

---

## Requirements

- [Home Assistant](https://www.home-assistant.io/) with the MQTT integration configured
- [Zigbee2MQTT](https://www.zigbee2mqtt.io/) add-on (or standalone)
- A supported switch paired to Zigbee2MQTT
- A light or light group where each bulb's **friendly name in HA matches its Zigbee2MQTT device name**

> **Note on light groups:** The blueprint sends `brightness_move` individually to each bulb in the group. It reads member entities from the group and uses their HA friendly names as Z2M device names. This works automatically if you have not renamed bulbs differently in HA vs Z2M.

---

## Installation

### Option 1 — One-click import (recommended)

Click the button below to import the blueprint directly into your Home Assistant:

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2FPlanckus%2Fha-blueprint-ptm215z-smart-dimmer%2Fmain%2Fblueprints%2Fptm215z_smart_dimmer.yaml)

### Option 2 — Manual import via URL

1. In Home Assistant, go to **Settings → Automations & Scenes → Blueprints**
2. Click **Import Blueprint** (bottom right)
3. Paste this URL:
   ```
   https://raw.githubusercontent.com/Planckus/ha-blueprint-ptm215z-smart-dimmer/main/blueprints/ptm215z_smart_dimmer.yaml
   ```
4. Click **Preview** → **Import Blueprint**

### Option 3 — Manual file install

1. Download [`ptm215z_smart_dimmer.yaml`](blueprints/ptm215z_smart_dimmer.yaml)
2. Place it in your HA config folder:
   ```
   config/blueprints/automation/custom/ptm215z_smart_dimmer.yaml
   ```
3. Restart Home Assistant or reload blueprints

---

## Configuration

Once imported, create a new automation from the blueprint:

1. Go to **Settings → Automations & Scenes → Blueprints**
2. Find **"Z2M PTM 215Z - Smart Dimmer"** and click **Create Automation**
3. Fill in the fields:

| Field | Example | Description |
|-------|---------|-------------|
| **Switch MQTT topic** | `zigbee2mqtt/switch_living_room` | Full MQTT topic of your switch in Z2M |
| **Light / Light group** | `light.living_room` | The HA light entity or group to control |
| **Zigbee2MQTT base topic** | `zigbee2mqtt` | Only change if you customized your Z2M base topic |
| **Dimming speed** | `85` | Speed of brightness change (85 = full range in ~3 sec) |

4. Give the automation a name and click **Save**

### Finding your switch MQTT topic

In Zigbee2MQTT, go to your switch device and look at its **friendly name**. The MQTT topic is your base topic + the friendly name:

```
zigbee2mqtt/<friendly_name>
```

For example, if your switch is named `switch_living_room` in Z2M, the topic is `zigbee2mqtt/switch_living_room`.

---

## Dimming speed reference

| Speed value | Time to go full range (0–100%) |
|-------------|-------------------------------|
| `40` | ~6 seconds |
| `85` | ~3 seconds ⭐ recommended |
| `128` | ~2 seconds |
| `255` | ~1 second |

---

## Troubleshooting

**Buttons 1 and 3 work but dimming doesn't**
→ Check your switch MQTT topic is correct. Open Z2M → your device → check the friendly name matches exactly (including underscores vs hyphens).

**Dimming works but all bulbs don't follow**
→ Make sure each bulb's HA friendly name matches its Z2M device name exactly. Check in Z2M under Devices.

**Nothing works at all**
→ Go to **Developer Tools → MQTT** in HA and listen on `zigbee2mqtt/#`. Press a button and check what topic and payload arrives.

---

## License

MIT — free to use, share, and modify.
