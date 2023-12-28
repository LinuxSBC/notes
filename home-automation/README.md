# Home Assistant and Tasmota

## Device linking

I have two devices in a room, a smart switch (connected to a light fixture) and a smart plug (connected to a lamp). I wanted the smart switch to turn on both itself and the lamp, but turning either of them on from Home Assistant would only turn on that one. Vitally, I want the switch to turn on the light fixture regardless of server connection, which was the most difficult part.

### 1. Tasmota method

This is the best way I found to do it, but it requires that the switch be running Tasmota. 

Tasmota commands:
```
ButtonTopic 0
Rule1 on Button1#state do backlog Publish stat/%topic%/TOGGLE %power1%; Power 2 endon
Rule1 1```

Home Assistant automation:
```yaml
alias: Lights Sync
description: ""
trigger:
  - platform: mqtt
    topic: stat/[[ YOUR TOPIC GOES HERE ]]/TOGGLE
    id: turn_off
    payload: "1"
  - platform: mqtt
    topic: stat/[[ YOUR TOPIC GOES HERE ]]/TOGGLE
    id: turn_on
    payload: "0"
condition: []
action:
  - choose:
      - conditions:
          - condition: trigger
            id:
              - turn_on
        sequence:
          - service: switch.turn_on
            target:
              entity_id: [[ YOUR ENTITY GOES HERE ]]
            data: {}
      - conditions:
          - condition: trigger
            id:
              - turn_off
        sequence:
          - service: switch.turn_off
            target:
              entity_id: [[ YOUR ENTITY GOES HERE ]]
            data: {}
mode: single
```

You can find a blueprint of it at https://gist.github.com/LinuxSBC/8cb8a601be78818ce19f5c2328374071. 

## Devices

Power monitoring: https://www.amazon.com/Emporia-Monitor-Circuit-Electricity-Metering/dp/B08CJGPHL9?th=1 is high-quality, works with HA, can be easily flashed with ESPHome, and is cheaper than more open alternatives.

Smart plugs: Several options, but the [Kauf PLF12](https://www.amazon.com/dp/B0BKR2CMJN)is cheap and pre-flashed. It also has power monitoring.

Companies to look at:
- Martin Jerry (cheap, consistent, pre-flashed switches with a range of options like dimmer and 3-way)
- ATHOM (lots of pre-flashed options)
- Cloudfree (a variety)

Virage Labs was great, but they're now dead.
