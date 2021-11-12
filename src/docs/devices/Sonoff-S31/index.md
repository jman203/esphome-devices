---
title: Sonoff S31
date-published: 2019-10-11
type: plug
standard: us
---

## GPIO Pinout

| Pin    | Function                           |
| ------ | ---------------------------------- |
| GPIO0  | Push Button (HIGH = off, LOW = on) |
| GPIO12 | Relay and its status LED           |
| GPIO13 | Green LED (HIGH = off, LOW = on)   |
| GPIO1  | RX pin (for external sensors)      |
| GPIO3  | TX pin (for external sensors)      |

## Basic Configuration

```yaml
# Basic Config
esphome:
  name: sonoff_s31
  platform: ESP8266
  board: esp01_1m

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

logger:
  baud_rate: 0 # (UART logging interferes with cse7766)
api:
ota:

# Device Specific Config

uart:
  rx_pin: RX
  baud_rate: 4800

substitutions:
  #Change device name
  device_name: Sonoff S31

binary_sensor:
  - platform: gpio
    pin:
      number: GPIO0
      mode: INPUT_PULLUP
      inverted: True
    name: "${device_name} Button"
    on_press:
      - switch.toggle: relay
  - platform: status
    name: "${device_name} Status"

sensor:
  - platform: wifi_signal
    name: "${device_name} Signal"
    update_interval: 60s
  - platform: cse7766
    current:
      name: "${device_name} Current"
      accuracy_decimals: 1
    voltage:
      name: "${device_name} Voltage"
      accuracy_decimals: 1
    power:
      name: "${device_name} Power"
      accuracy_decimals: 1
  - platform: total_daily_energy
    name: "${device_name} Daily Energy"
    power_id: my_power

time:
  - platform: sntp
    id: my_time

switch:
  - platform: gpio
    name: "${device_name} Relay"
    pin: GPIO12
    id: relay

status_led:
  pin: GPIO13
```
