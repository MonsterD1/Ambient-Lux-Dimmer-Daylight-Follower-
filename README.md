# 🌤️ Ambient Lux Dimmer – Daylight Follower

A Home Assistant Blueprint that dims your lights **proportionally to the ambient light level** measured by a light sensor.

Unlike the classic "lux dimmer" approach (lights on when it gets dark), this blueprint is designed for **daylight-following** use cases: the brighter it is outside, the brighter your light – perfect for ambient lighting, bias lighting, or garden lights that should mirror natural daylight.

---

## ✨ Features

- 📈 **Linear interpolation** between a minimum and maximum lux value
- 🔒 **Clamped output** – brightness never spikes or jumps outside your defined range
- 🎨 Optional **fixed color or color temperature** setting
- ⏱️ Optional **transition time** for smooth dimming
- ✅ Fully configurable **run conditions** (e.g. time-of-day, presence)
- 🧹 No over/under-range fallback values – no edge case bugs

---

## 🆚 Difference to Smart Lux Dimmer

This blueprint is based on the math of [Smart Lux Dimmer by AntonH](https://gist.github.com/Twanne/02dfb154084a8b9bf927c29160df4af3), but is rebuilt for the **inverted / daylight-follow use case**:

| | Smart Lux Dimmer | Ambient Lux Dimmer |
|---|---|---|
| Typical use case | Turn lights on when it gets dark | Mirror natural daylight |
| Brightness at high lux | Low (lights off in daylight) | High (follows daylight) |
| Outside-range handling | Separate fallback values | Clamped to defined range |
| Risk of brightness spikes | Yes (with over/under enabled) | ❌ None |

---

## ⚙️ Configuration

| Parameter | Description |
|---|---|
| **Light Sensor** | Any HA sensor with `device_class: illuminance` |
| **Target Lights** | One or more lights to control |
| **Max Lux** | Lux value at which brightness is at its maximum |
| **Min Lux** | Lux value at which brightness is at its minimum |
| **Brightness at Max Lux** | Light brightness (%) when lux ≥ Max Lux |
| **Brightness at Min Lux** | Light brightness (%) when lux ≤ Min Lux |
| **Transition Time** | Seconds for the light to fade to the new value |
| **Color / Temperature** | Optional fixed color or color temperature |
| **Run Conditions** | Optional conditions (e.g. only between 07:00–22:00) |

---

## 📐 How it works

The brightness is calculated using **linear interpolation** between two anchor points:

```
brightness = slope × lux + constant
```

Where:
- `slope = (brightness_at_max_lux - brightness_at_min_lux) / (max_lux - min_lux)`
- `constant = brightness_at_max_lux - (slope × max_lux)`

The result is **clamped** to `[brightness_at_min_lux … brightness_at_max_lux]`, so values outside the lux range never cause unexpected brightness jumps.

---

## 💡 Example

> **Bias light behind a TV / monitor**
>
> - Max Lux: `3500` → Brightness: `100%`
> - Min Lux: `100` → Brightness: `10%`
> - Transition time: `30s`
>
> The bias light will smoothly follow the natural daylight throughout the day.
> At night (< 100 lux) it stays at 10%. On a bright sunny day (> 3500 lux) it stays at 100%.

---

## 📥 Installation

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/MonsterD1/Ambient-Lux-Dimmer-Daylight-Follower-/main/ambient_lux_dimmer_daylight_follower.yaml)


Or manually: copy the YAML file into your `config/blueprints/automation/` directory.

---

## 📋 Requirements

- Home Assistant `2024.10.0` or newer
- A light sensor with `device_class: illuminance`
- A dimmable light entity

---

## 🙏 Credits

- Based on the math and structure of [Smart Lux Dimmer](https://gist.github.com/Twanne/02dfb154084a8b9bf927c29160df4af3) by **AntonH**
- Original lux math concept by **@nickrout** on the Home Assistant Community Forum
