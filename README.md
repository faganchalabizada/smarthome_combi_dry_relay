# Combi dry relay — circuit interlock

Home Assistant blueprint: turn the **combi dry relay ON only when ≥1 heating circuit is effectively open**.

## Install in Home Assistant

**Settings → Automations & Scenes → Blueprints → Import blueprint**

```text
https://github.com/faganchalabizada/smarthome_combi_dry_relay/blob/main/combi_dry_relay.yaml
```

Then **Create automation** from this blueprint.

## What you select

1. **Сухое реле комби** — dry-contact switch on the boiler  
2. **Контуры без задержки** — radiator TRV position sensors / climates (open immediately)  
3. **Контуры с задержкой** — underfloor servo switches  
4. **Задержка открытия** — seconds after servo `on` before the circuit counts as open (default **120**). The automation **counts this time itself** via `last_changed`.

## Logic

| Circuit type | Treated as open when |
| --- | --- |
| `sensor` | numeric state **> 0** |
| `climate` | `hvac_action == heating` |
| `switch` (immediate list) | `on` |
| `switch` (delayed list) | `on` **and** age ≥ delay |

- Relay **ON** if any circuit is effectively open  
- Relay **OFF** if all are closed / still within servo delay  
- Triggers on open/close, HA start, and every 30s (so delays expire)  
- Does **not** drive servos/TRVs (keep `termostat_relay` for that)

## Example

| Role | Entities | Delay |
| --- | --- | --- |
| Immediate | `sensor.radiator_gostinoi_position`, `sensor.termostat_radiator_spalni_position`, `sensor.termostat_radiatora_ofis_position` | — |
| Delayed | `switch.servoprivod`, `switch.servoprivody_l1`, `switch.servoprivody_l2` | 120 s |
| Dry relay | `switch.0xa4c13821f907c705` | — |
