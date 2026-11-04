# Combi dry relay — circuit interlock

Home Assistant blueprint: include the combi boiler dry relay only when **at least one heating circuit is effectively open**.

## Install

1. Home Assistant → **Settings → Automations & Scenes → Blueprints → Import blueprint**
2. Paste:

```text
https://github.com/faganchalabizada/smarthome_combi_dry_relay/blob/main/combi_dry_relay.yaml
```

3. Create an automation from the blueprint.

## Inputs

| Input | Meaning |
| --- | --- |
| **Сухое реле комби** | Dry-contact / winter-mode relay (`switch`) |
| **Контур N — сущность** | What means “opening”: servo `switch`, TRV `sensor` position, or `climate` (`hvac_action=heating`) |
| **Контур N — задержка** | Seconds to wait after open command before counting the circuit (0 = radiator, ~120 = underfloor servo) |

Up to 6 circuits. Empty slots are ignored.

## Behaviour

- Runs on circuit open/close, dry-relay changes, HA start, and every ~30s (so delays can expire).
- Tracks time with `last_changed` of each circuit entity.
- Turns relay **on** if any circuit is commanded open **and** its delay has elapsed.
- Turns relay **off** if no circuit is effectively open.
- Does **not** control floor servos or TRVs (use your existing thermostat→servo automations).

## Example (this house)

| Circuit | Entity | Delay |
| --- | --- | --- |
| Гостиная TRV | `sensor.radiator_gostinoi_position` | 0 |
| Спальня TRV | `sensor.termostat_radiator_spalni_position` | 0 |
| Офис TRV | `sensor.termostat_radiatora_ofis_position` | 0 |
| Коридор ТП | `switch.servoprivod` | 120 |
| Кухня ТП | `switch.servoprivody_l2` | 120 |
| Ванная ТП | `switch.servoprivody_l1` | 120 |
| Dry relay | `switch.0xa4c13821f907c705` | — |
