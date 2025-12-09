# ESPHome Irrigation System with ES32A08 Expansion Board

This repository provides a comprehensive example of an irrigation controller utilizing the ESP32, ESPHome, and the ES32A08 expansion board.

This project is currently a work in progress, and more detailed documentation and explanations will be added progressively.

![Home Assistant Irrigation Dashboard](images/irrigation-dashboard.jpg)

## Key Features

- Supports up to 8 zones, or 7 zones plus a pump control; expandable with RS485 relay boards.
- Automated irrigation scheduling.
- Irrigation timing adjusted according to actual rainfall data and weather forecasts.
- Operational independence from Home Assistant and network connectivity.
- Separate "controller" logic for drip irrigation, e.g. garden, thujas, plantations.
- UI allows easy toggling of automation logic without modifying the code.
- Automatic water pressure control with configurable minimum and maximum thresholds per zone.

---

## Table of contents

- [Hardware](#hardware)
  - [Required](#required)
  - [Pump Control Hardware](#pump-control-hardware)
  - [Optional](#optional)
- [Disclaimer](#disclaimer)
- [Connection Diagram](#connection-diagram)
  - [With Pump And up to 7 Zones](#with-pump-and-up-to-7-zones)
- [How It Works](#how-it-works)
  - [Automatic water pressure control](#automatic-water-pressure-control)
- [Simple Setup](#simple-setup)
- [Winterization](#winterization)
- [Contributing](#contributing)


## Hardware

### Required

- [**ESP32-WROOM-32E or 32UE**](https://s.click.aliexpress.com/e/_DBjuSlX) – The 38-pin version is **mandatory**; the ESP32-WROOM-32UE variant with an external antenna is recommended for better signal quality away from high-voltage cables.  
![ESP32-WROOM-32UE](images/esp32-wroom-32ue.jpg)

- [**ES32A08** Expansion Board](https://s.click.aliexpress.com/e/_DE1CWk5) by _eletechsup_ – An affordable and well-suited board featuring ample I/O and 8 relay outputs. [Learn more about its integration with ESPHome in my dedicated repository](https://github.com/makstech/esphome-es32a08-expansion-board-example).  
![ES32A08](images/es32a08.jpg)

- [**12V Power Supply**](https://s.click.aliexpress.com/e/_DmWa6VJ) – Powers the ES32A08 board. 1A (12W) PSU is sufficient. The _Mean Well HDR-15-12_ offers a compact, DIN rail-mountable solution.  

- **24V AC Transformer** – My choice was an encapsulated 230VAC to 24VAC 30VA (1.25A) transformer. A bit overkill but it handles opening up all 7 valves if I need to dump all the water from the system quickly.

- ... and everything releated to the irrigation itself, like 24VAC valves, rotors, drippers, etc.

### Pump Control Hardware

- [**AC Contactor**](https://s.click.aliexpress.com/e/_DmPWcfj) – To reliably manage a high-power pump. Make sure that the contactor can handle the power of your pump + some overhead. It operates NO (Normally Open) circuits with either 12V DC or regular AC input. I've got the `2P 16A 2NO 230VAC`, which means that it switches 2 Normally Open circuits (Live and Neutral) using 230VAC.

- [**Pressure sensor**](PRESSURE-SENSORS.md) – Monitors water pressure. Choosing a sensor that is **correct for your case** is important, and there are many options. Don't be like me and buy three different sensors—try to choose the right one once. See more details in [Pressure sensors](PRESSURE-SENSORS.md).

- [**Step Down Voltage Module from 12V to 5V**](https://s.click.aliexpress.com/e/_DDEQ7e5) – Converts the 12V output from the PSU to 5V for the pressure sensor.  

### Optional

- [**External Antenna with U.FL/IPEX Pigtail**](https://s.click.aliexpress.com/e/_DEGqnTP) – For **32UE** ESP32 variant.

## Disclaimer

I am not responsible for any damage, injury, or consequences that may result from attempting to replicate or modify this project. Working with electricity can be dangerous, and it is important to take proper precautions. If you are not familiar with electrical wiring or unsure about any part of this project, consult a qualified electrician before proceeding. Always follow local electrical codes and safety guidelines.

## Connection Diagram

Here’s a really basic explanation and ASCII-style diagram of how things are hooked up.

### With Pump And up to 7 Zones

I use a 1KW pump, and I woudn't trust the onboard relays, so I've hooked it up to a contactor.  

The contactor is wired to `NO1` output, which means in the YAML config, the `relay_1` switch controls the pump.  
Neutral line from the transformer goes to its own bus bar, and the Live output is connected from `COM2` through `COM8`. I've got them daisy-chained.
Valves for the zones are connected to `NO2` through `NO8` along with Neutral. This setup allows individual control of each zone from `relay_2` to `relay_8`.

<!-- https://asciiflow.com/#/share/eJytVbtu2zAU%2FRWCU4rGBuzm4WaL3SAREMtG3XrSIgRsYkCWAkrKA0GAgsiYQQOhevDo0VPRyejX6EtC6mFZEslIaAkJEqXLe8659%2FLyCdrmHMETeDYZXzhzBDSMZ9emN3NscD%2FzbsDYn9%2BCva%2BTg97hB7gPLfMRYWb%2BZMA7hF1mZsCT7r4BH9jz8xF%2Fe%2BRfesfszUMPHpsYEBTG6QBcatMzoOkgoq8R%2FSm%2FgmRFxSwAgmEYtuhzRMnWQ7gZOLZnXnkOjuhSgBhuuOB4SVMAwm4qlRL%2Bjugisy%2BZLZqDLUVBEpKuOn0v6KI07GhMAvlXZPdHnpwiiYzCSrNvfQ8MB%2F1Uw6%2BXLCIJGBmMhh32Qx91xPpqiAliQALEGVddqyyunAgrHOxYFsIyImSMkev6GMWTGHSZ11QuskpkoSwdRZUwnAmyXQenk39TqoqmJKmFWJRU5xGLtcs2XKc7BePJdwnndRMVa%2B6uxR3%2Bt3jI0rIQ6M9WhJtu61z%2FUiBBE7HfsGm7Pxw8T4JSIRYoUlBKhwD6lSF8asXNunWaQqtEKEtNxHnbBkrAS2Z2kAL3t8CSwX7ryRs%2FBmQuSZ0toYahyvJZZ1J32po65CWWUvCCs0KnDFJt2aPSV%2BpDEB35HjatHW9g21rCzUe%2BCzQ9x6nrtu%2B7oG%2Fistu0FrKybuqWVgJfqDSS%2B%2BQjLqSds0AEUfcUS70I9q4AuDopg5Kpad0hF3Ta7faxcPdXKbBWOBp22YLeO87r7VOBoqbXSh%2BlfFSEaozm55YBn%2BHzG23RlSc%3D) -->
```
              AC LIVE IN ┌─────────┐      ┌────┐                    
                    │  ┌─►Contactor├──────►Pump│                    
                    │  │ └────────▲┘      └────┘                    
                    │  ├───┐      │                                 
  ┌───────────────┐ │  │ ┌─▼──────┴─┐                               
┌─┤Input MCB      ◄─┘  │ │COM1   NO1│                   ┌──────────┐
│ ├───────────────┤    │ │Controller│                   │Pressure  │
├─►Pump MCB       ├────┘ └──────────┘                   │Sensor    │
│ ├───────────────┤      ┌───────┐                      │          │
├─►Controller MCB ├──────►12V PSU├──────┬──────────────┬►1-12V     │
│ ├───────────────┤      └───────┘      │              └►2-GND     │
└─►Transformer MCB├────┐ ┌───────────┐  │              ┌►3-RS485-A │
  └───────────────┘    └─►Transformer│  │              ├►4-RS485-B │
                         │N      LIVE│  │              │└──────────┘
                         └┬────────┬─┘  │ ┌──────────┐ │            
                      ┌───▼───┐    │    │ │Controller│ │            
                      │Neutral│    │    ├─►+12V IN   │ │            
                      │Bus Bar│    │    └─►GND       │ │            
                      └───┬───┘    │      │     RS485◄─┘            
       ┌────────────◄─────┘        │      │          │              
       │Valves 1...7│              └──────►COM2...8  │              
       └────────────◄─────────────────────┤NO2...8   │              
                                          └──────────┘              
```

## How It Works

You can see a detailed example of the setup in [`irrigator-with-pump-2-controllers.yaml`](irrigator-with-pump-2-controllers.yaml), which includes:
- Two controllers: one for sprinklers and another for drip irrigation.
- Pump control.
- Automatic water pressure control.

### Automatic water pressure control

> Make sure to read the [Pressure sensors](PRESSURE-SENSORS.md) page about choosing the correct pressure sensor.

The YAML configuration implements automatic water pressure control around user-defined targets. Each zone sets a minimum and maximum pressure (`pressure_target_min` / `pressure_target_max` in bar) using template number entities. The core logic lives in the `script.keep_pressure`, which drives the pump relay based on real-time pressure readings.

Conceptually, the script does the following:

- Uses the `pressure` sensor as the current system pressure.
- Enforces a hard safety limit: if the pressure goes above 5 bar, the script stops the pump and exits the loop.
- While running, repeatedly checks:
  - If the pump is on and the pressure has reached the configured **maximum**, it turns the pump **off**.
  - If the pressure drops below the configured **minimum** and the pump is off, it turns the pump **on**.
- Waits a short delay between iterations.

This creates a simple hysteresis around the target range (for example, 0.6–2.2 bar for drippers). The current configuration in [`irrigator-with-pump-2-controllers.yaml`](irrigator-with-pump-2-controllers.yaml) uses an RS485 (Modbus) sensor for accurate and noise-resistant readings; examples for 5V and 4–20 mA variants can be found in [Pressure sensors](PRESSURE-SENSORS.md) page.

## Simple Setup

If you don’t need pump control or multiple controllers, this simpler configuration might be a better fit. It uses all 8 relays for zone valves and a single sprinkler controller.

You can find the full example YAML in [`irrigator-simple.yaml`](irrigator-simple.yaml). This setup includes:

- Control for 8 irrigation zones using the 8 onboard relays.
- One standard sprinkler controller.
- No pump controls.

This is a great starting point if you want a straightforward irrigation controller without extra complexity.

## Home Assistant Automations

> [!NOTE]
> In future, I plan on moving the automation part to ESP32, but for now, it works on the Home Assistant side


## Winterization

Everyone’s setup is different: local climate, hardware layout, and even what “winter” means can vary a lot. Where I am, winter can go down to around -25°C, so I have to be quite careful. You will need to decide what makes sense for your system, what you must do, and what you can safely skip.

My winterization routine currently looks roughly like this:

- Depressurize the system and clean the pump’s inlet filter.
- Remove the suction pipe from the river and flush it with clean city water by using the pump to draw from a container.
- Depressurize the system again. Disconnect the vacuum/suction pipe and thoroughly clean all parts that were in the water, with soap, brush, etc.. 
- Clean the pump filter again.
- Connect city water directly to the pump and start pumping. Manually open each valve in turn and flush until clean water comes out. Depressurize the system again and disconnect the pump.
- Connect an air compressor via a [1/4" to air-compressor connector](https://s.click.aliexpress.com/e/_c3ELFpl3) and repeat the cycle of manually opening each valve, blowing out water until no more water is coming out.
  - **Important: do not let rotor sprinklers “run” dry.** As soon as they only spray a mist instead of a normal water arc, stop immediately or you may damage the rotor.
- Any removable and sensitive parts (like pressure sensor) are taken off.

And that’s “basically” it for my case. After that, the system is effectively dry and depressurized.

## TODOs

- [ ] Rain sensor
- [ ] The math behind irrigation duration
- [ ] Home Assistant automation
- [ ] Home Assistant dashboard
- [ ] References
- [ ] The installation process
- [ ] References for pipe sizing, cable sizing
- [ ] Challanges
- [ ] Move the automation to ESP?
- [ ] Integrate flow meter

## Contributing
Feel free to fork this repository and contribute by submitting pull requests.
