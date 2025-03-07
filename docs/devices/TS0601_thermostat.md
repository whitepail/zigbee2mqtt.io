---
title: "TuYa TS0601_thermostat control via MQTT"
description: "Integrate your TuYa TS0601_thermostat via Zigbee2MQTT with whatever smart home
 infrastructure you are using without the vendors bridge or gateway."
---

*To contribute to this page, edit the following
[file](https://github.com/Koenkk/zigbee2mqtt.io/blob/master/docs/devices/TS0601_thermostat.md)*

# TuYa TS0601_thermostat

| Model | TS0601_thermostat  |
| Vendor  | TuYa  |
| Description | Radiator valve with thermostat |
| Exposes | lock (state), switch (state), battery_low, position, climate (current_heating_setpoint, local_temperature, system_mode, running_state, local_temperature_calibration, away_mode, preset), away_preset_days, boost_time, comfort_temperature, eco_temperature, force, max_temperature, min_temperature, week, away_preset_temperature, linkquality |
| Picture | ![TuYa TS0601_thermostat](../images/devices/TS0601_thermostat.jpg) |
| White-label | Moes HY369RT, SHOJZJ 378RT |

## Notes


### Pairing

While pairing, keep the valve close to the coordinator.

1. *Turn the display on*: Short press home (:house:).
2. *Enter settings*: Long press home (:house:) for 3sec.
3. *Select WiFi settings*: Press the plus button (:heavy_plus_sign:) button 4 times to see the digital **`5`** on the right hand side and the blinking WiFi logo.
4. *Enter WiFi settings*: Press home (:house:) once again. Now only WiFi logo is showing without blinking.
5. *Enable pairing mode*: Long press home (:house:). WiFi logo is now blinking.
6. *Keep display on*: Touch home (:house:) every few seconds.

### Local temperature
If you'd like to force device to send local_temperature you can use this mqtt command:
* `topic`: zigbee2mqtt/FRIENDLY_NAME/set/local_temperature_calibration
* `payload`: YOUR_CURRENT_CALIBRATION_VALUE

YOUR_CURRENT_CALIBRATION_VALUE can be 0, but if you calibrated temperature for this device send current value.
After this command thermostat responds with two messages. One for calibration change confirmation, and other with current local_temperature.

### Controlling device specific features

- If all you need is to control on and off, you can set "force" with topic `zigbee2mqtt/FRIENDLY_NAME/set`. The payload values are:
  open -> fully opens valve and stays there
  close -> fully closes valve and stays there
  normal -> normal valve operation

    ```json
    {
        "force": "open"
    }
    ```

- Use topic `zigbee2mqtt/FRIENDLY_NAME/set/schedule` to set the schedule of the device with 6 timeslots on workdays or holidays (e.g. weekend). Example payload values are:

    ```json
    {
        "workdays":[
            {"hour":6,"minute":0,"temperature":19},
            {"hour":8,"minute":0,"temperature":20},
            {"hour":18,"minute":0,"temperature":21},
            {"hour":20,"minute":30,"temperature":20},
            {"hour":22,"minute":0,"temperature":19},
            {"hour":23,"minute":30,"temperature":15}
        ]
    }
    ```

    ```json
    {
        "holidays":[
            {"hour":6,"minute":0,"temperature":19},
            {"hour":8,"minute":0,"temperature":20},
            {"hour":18,"minute":0,"temperature":21},
            {"hour":20,"minute":30,"temperature":20},
            {"hour":22,"minute":0,"temperature":19},
            {"hour":23,"minute":30,"temperature":15}
        ]
    }
    ```
- You can set "week" schedule pattern with topic `zigbee2mqtt/FRIENDLY_NAME/set`. The payload values are:
  5+2 -> to be used when workdays for example are monday-friday and saturday & sunday are holidays
  6+1 -> to be used when workdays for example are monday-saturday and sunday is a holiday
  7 -> to be used when workdays schedule will be used for the whole week

    ```json
    {
        "week": "5+2"
    }
    ```

- You can set "boost time" with topic `zigbee2mqtt/FRIENDLY_NAME/set`. But be aware that it rounds the values down to multiple of 100.

    ```json
    {
        "boost_time": 200
    }
    ```
- You can set "comfort temperature" level on the device with topic `zigbee2mqtt/FRIENDLY_NAME/set`.

    ```json
    {
        "comfort_temperature": 21
    }
    ```
- You can set "eco temperature" level on the device with topic `zigbee2mqtt/FRIENDLY_NAME/set`.

    ```json
    {
        "eco_temperature": 17
    }
    ```


## OTA updates
This device supports OTA updates, for more information see [OTA updates](../information/ota_updates.md).


## Exposes

### Lock 
The current state of this lock is in the published state under the `child_lock` property (value is `LOCK` or `UNLOCK`).
To control this lock publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"child_lock": "LOCK"}` or `{"child_lock": "UNLOCK"}`.
To read the current state of this lock publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"child_lock": ""}`.

### Switch 
The current state of this switch is in the published state under the `window_detection` property (value is `ON` or `OFF`).
To control this switch publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"window_detection": "ON"}`, `{"window_detection": "OFF"}` or `{"window_detection": "TOGGLE"}`.
To read the current state of this switch publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"window_detection": ""}`.

### Battery_low (binary)
Indicates if the battery of this device is almost empty.
Value can be found in the published state on the `battery_low` property.
It's not possible to read (`/get`) or write (`/set`) this value.
If value equals `true` battery_low is ON, if `false` OFF.

### Switch 
The current state of this switch is in the published state under the `valve_detection` property (value is `ON` or `OFF`).
To control this switch publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"valve_detection": "ON"}`, `{"valve_detection": "OFF"}` or `{"valve_detection": "TOGGLE"}`.
To read the current state of this switch publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"valve_detection": ""}`.

### Position (numeric)
Position.
Value can be found in the published state on the `position` property.
It's not possible to read (`/get`) or write (`/set`) this value.
The unit of this value is `%`.

### Climate 
This climate device supports the following features: `current_heating_setpoint`, `local_temperature`, `system_mode`, `running_state`, `local_temperature_calibration`, `away_mode`, `preset`.
- `current_heating_setpoint`: Temperature setpoint. To control publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"current_heating_setpoint": VALUE}` where `VALUE` is the °C between `5` and `35`. To read send a message to `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"current_heating_setpoint": ""}`.
- `local_temperature`: Current temperature measured on the device (in °C). To read send a message to `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"local_temperature": ""}`.
- `system_mode`: Mode of this device. To control publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"system_mode": VALUE}` where `VALUE` is one of: `heat`, `auto`, `off`. To read send a message to `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"system_mode": ""}`.
- `preset`: Mode of this device (similar to system_mode). To control publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"preset": VALUE}` where `VALUE` is one of: `schedule`, `manual`, `boost`, `complex`, `comfort`, `eco`. To read send a message to `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"preset": ""}`.
- `running_state`: The current running state. Possible values are: `idle`, `heat`. To read send a message to `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"running_state": ""}`.
- `away_mode`: Away mode. To control publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"away_mode": "ON"}` or `{"away_mode": "OFF"}`. To read send a message to `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"away_mode": ""}`.

### Switch 
The current state of this switch is in the published state under the `auto_lock` property (value is `AUTO` or `MANUAL`).
To control this switch publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"auto_lock": "AUTO"}`, `{"auto_lock": "MANUAL"}` or `{"auto_lock": "undefined"}`.
To read the current state of this switch publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"auto_lock": ""}`.

### Switch 
The current state of this switch is in the published state under the `away_mode` property (value is `ON` or `OFF`).
To control this switch publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"away_mode": "ON"}`, `{"away_mode": "OFF"}` or `{"away_mode": "undefined"}`.
To read the current state of this switch publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/get` with payload `{"away_mode": ""}`.

### Away_preset_days (numeric)
Away preset days.
Value can be found in the published state on the `away_preset_days` property.
It's not possible to read (`/get`) this value.
To write (`/set`) a value publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"away_preset_days": NEW_VALUE}`.

### Boost_time (numeric)
Boost time.
Value can be found in the published state on the `boost_time` property.
It's not possible to read (`/get`) this value.
To write (`/set`) a value publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"boost_time": NEW_VALUE}`.
The unit of this value is `s`.

### Comfort_temperature (numeric)
Comfort temperature.
Value can be found in the published state on the `comfort_temperature` property.
It's not possible to read (`/get`) this value.
To write (`/set`) a value publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"comfort_temperature": NEW_VALUE}`.
The unit of this value is `°C`.

### Eco_temperature (numeric)
Eco temperature.
Value can be found in the published state on the `eco_temperature` property.
It's not possible to read (`/get`) this value.
To write (`/set`) a value publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"eco_temperature": NEW_VALUE}`.
The unit of this value is `°C`.

### Force (enum)
Force the valve position.
Value can be found in the published state on the `force` property.
It's not possible to read (`/get`) this value.
To write (`/set`) a value publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"force": NEW_VALUE}`.
The possible values are: `normal`, `open`, `close`.

### Max_temperature (numeric)
Maximum temperature.
Value can be found in the published state on the `max_temperature` property.
It's not possible to read (`/get`) this value.
To write (`/set`) a value publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"max_temperature": NEW_VALUE}`.
The unit of this value is `°C`.

### Min_temperature (numeric)
Minimum temperature.
Value can be found in the published state on the `min_temperature` property.
It's not possible to read (`/get`) this value.
To write (`/set`) a value publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"min_temperature": NEW_VALUE}`.
The unit of this value is `°C`.

### Week (enum)
Week format user for schedule.
Value can be found in the published state on the `week` property.
It's not possible to read (`/get`) this value.
To write (`/set`) a value publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"week": NEW_VALUE}`.
The possible values are: `5+2`, `6+1`, `7`.

### Away_preset_temperature (numeric)
Away preset temperature.
Value can be found in the published state on the `away_preset_temperature` property.
It's not possible to read (`/get`) this value.
To write (`/set`) a value publish a message to topic `zigbee2mqtt/FRIENDLY_NAME/set` with payload `{"away_preset_temperature": NEW_VALUE}`.
The unit of this value is `°C`.

### Linkquality (numeric)
Link quality (signal strength).
Value can be found in the published state on the `linkquality` property.
It's not possible to read (`/get`) or write (`/set`) this value.
The minimal value is `0` and the maximum value is `255`.
The unit of this value is `lqi`.

## Manual Home Assistant configuration
Although Home Assistant integration through [MQTT discovery](../integration/home_assistant) is preferred,
manual integration is possible with the following configuration:


{% raw %}
```yaml
lock:
  - platform: "mqtt"
    state_topic: true
    availability_topic: "zigbee2mqtt/bridge/state"
    command_topic: "zigbee2mqtt/<FRIENDLY_NAME>/set"
    value_template: "{{ value_json.child_lock }}"
    payload_lock: "LOCK"
    payload_unlock: "UNLOCK"
    state_locked: "LOCKED"
    state_unlocked: "UNLOCKED"
    command_topic_postfix: "child_lock"

switch:
  - platform: "mqtt"
    state_topic: true
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.window_detection }}"
    command_topic: "zigbee2mqtt/<FRIENDLY_NAME>/set"
    command_topic_postfix: "window_detection"
    state_off: "OFF"
    state_on: "ON"
    icon: "mdi:window-open-variant"

binary_sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    value_template: "{{ value_json.battery_low }}"
    payload_on: true
    payload_off: false
    device_class: "battery"

switch:
  - platform: "mqtt"
    state_topic: true
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.valve_detection }}"
    command_topic: "zigbee2mqtt/<FRIENDLY_NAME>/set"
    command_topic_postfix: "valve_detection"
    state_off: "OFF"
    state_on: "ON"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "%"
    value_template: "{{ value_json.position }}"
    icon: "mdi:valve"

climate:
  - platform: "mqtt"
    availability_topic: "zigbee2mqtt/bridge/state"
    temperature_unit: "C"
    temp_step: 0.5
    min_temp: "5"
    max_temp: "35"
    current_temperature_topic: true
    current_temperature_template: "{{ value_json.local_temperature }}"
    mode_state_topic: true
    mode_state_template: "{{ value_json.system_mode }}"
    modes: 
      - "heat"
      - "auto"
      - "off"
    mode_command_topic: true
    action_topic: true
    action_template: "{% set values = {'idle':'off','heat':'heating','cool':'cooling','fan only':'fan'} %}{{ values[value_json.running_state] }}"
    temperature_command_topic: "current_heating_setpoint"
    temperature_state_template: "{{ value_json.current_heating_setpoint }}"
    temperature_state_topic: true
    hold_modes: 
      - "schedule"
      - "manual"
      - "boost"
      - "complex"
      - "comfort"
      - "eco"
    hold_command_topic: true
    hold_state_template: "{{ value_json.preset }}"
    hold_state_topic: true
    away_mode_command_topic: true
    away_mode_state_topic: true
    away_mode_state_template: "{{ value_json.away_mode }}"

switch:
  - platform: "mqtt"
    state_topic: true
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "MANUAL"
    payload_on: "AUTO"
    value_template: "{{ value_json.auto_lock }}"
    command_topic: "zigbee2mqtt/<FRIENDLY_NAME>/set"
    command_topic_postfix: "auto_lock"
    state_off: "MANUAL"
    state_on: "AUTO"

switch:
  - platform: "mqtt"
    state_topic: true
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.away_mode }}"
    command_topic: "zigbee2mqtt/<FRIENDLY_NAME>/set"
    command_topic_postfix: "away_mode"
    state_off: "OFF"
    state_on: "ON"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "-"
    value_template: "{{ value_json.away_preset_days }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "s"
    value_template: "{{ value_json.boost_time }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "°C"
    value_template: "{{ value_json.comfort_temperature }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "°C"
    value_template: "{{ value_json.eco_temperature }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    value_template: "{{ value_json.force }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "°C"
    value_template: "{{ value_json.max_temperature }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "°C"
    value_template: "{{ value_json.min_temperature }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    value_template: "{{ value_json.week }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "°C"
    value_template: "{{ value_json.away_preset_temperature }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "lqi"
    value_template: "{{ value_json.linkquality }}"
    icon: "mdi:signal"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    icon: "mdi:update"
    value_template: "{{ value_json['update']['state'] }}"

binary_sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_on: true
    payload_off: false
    value_template: "{{ value_json.update_available}}"
```
{% endraw %}


