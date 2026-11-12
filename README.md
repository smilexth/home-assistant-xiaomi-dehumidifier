# Xiaomi Smart Humidifier/Dehumidifier Integration for Home Assistant

![GitHub stars](https://img.shields.io/github/stars/tsunglung/XiaomiHumidifier)
![GitHub forks](https://img.shields.io/github/forks/tsunglung/XiaomiHumidifier)
![GitHub watchers](https://img.shields.io/github/watchers/tsunglung/XiaomiHumidifier)

This is a custom component for Home Assistant to integrate the Xiaomi Smart Humidifier/Dehumidifier.

> **Fork notice** — this is a fork of [tsunglung/XiaomiHumidifier](https://github.com/tsunglung/XiaomiHumidifier)
> carrying one fix (see below) while the change is submitted upstream
> ([PR #14](https://github.com/tsunglung/XiaomiHumidifier/pull/14)). All credit for the integration
> itself goes to the original author.

## What this fork fixes — multiple identical units

**Symptom**: with two units of the same model (e.g. 2× `dmaker.derh.22ht`), the second unit
only creates its `humidifier` entity; all `sensor` / `switch` / `button` entities are silently
dropped with *"Platform does not generate unique IDs … already exists - ignoring"*.

**Root cause**: those platforms built `unique_id` from the model string, so every same-model
unit generated identical unique_ids.

**Fix**: one line per platform (`sensor.py`, `switch.py`, `button.py`) — use the per-device
entry MAC that was already passed into every constructor:

```diff
-return "{}_{}".format(self._name, self.entity_description.key)
+return "{}_{}".format(self._unique_id, self.entity_description.key)
```

**Migration**: after installing this fix, entities are recreated under MAC-based unique_ids;
entities previously created under model-based ids become orphans — remove them from the
entity registry once the new set is verified. Single-unit installs are unaffected apart from
the unique_id format.

## Installation

Until the fix is merged into this fork's `master`, install from the
`fix/per-device-unique-ids` branch (or wait for the upstream merge):

- **HACS**: add this repository as a custom repository, then copy the contents of
  `custom_components/xiaomi_miio_humidifier/` from the `fix/per-device-unique-ids` branch
  into your `config/custom_components/xiaomi_miio_humidifier/` and restart Home Assistant.
- Setup afterwards is unchanged — see *Setup* below.

Credits: Thanks to [Rytilahti](https://github.com/rytilahti/python-miio) for all the work.

## Supported devices

| Name                        | Model                  | Model no. |
| --------------------------- | ---------------------- | --------- |
| Xiaomi Smart Humidifier/Dehumidifier    | dmaker.derh.22ht       | |
| Xiaomi Smart Humidifier/Dehumidifier    | dmaker.derh.22l      | |
| Xiaomi Smart Humidifier/Dehumidifier    | dmaker.derh.50l      | |

## Features

### Xiaomi Smart Dehumidifier

Supported models: `dmaker.derh.22ht`, `dmaker.derh.22l`, `dmaker.derh.50l`

* Power (on, off)
* Mode (Smart, Sleep, Clothes Drying)
* Wifi LED (on, off)
* Physical Controls Lock (on, off)
* Buzzer (on, off)
* Dry After off switch (on, off)

## Install

You can install component with [HACS](https://hacs.xyz/) custom repo: HACS > Integrations > 3 dots (upper top corner) > Custom repositories > URL: `tsunglung/XiaomiHumidifier` > Category: Integration

Or manually copy `xiaomi_miio_humidifier` folder to `custom_components` folder in your config folder.

Then restart HA.

## Setup


1. With GUI. Configuration > Integration > Add Integration > Xiaomi Smart Humidifier/Dehumidifier
   1. If the integration didn't show up in the list please REFRESH the page
   2. If the integration is still not in the list, you need to clear the browser cache.
2. Enter your Xiaomi Account and Password
3. Select the Humidifier/Dehumidifier device that you want to integrate.

Or you also can manually input Humidifier/Dehumidifier IP address and token
