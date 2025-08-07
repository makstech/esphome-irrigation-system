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

[Hardware](#hardware) | [Required Hardware](#required) | [Required Hardware for Pump Control](#pump-control-hardware) | [Optional Hardware](#optional)  
[Connection Diagram](#connection-diagram)  
[How It Works](#how-it-works) | [How Pressure Sensor Works](#pressure-sensor)  
[Simple Setup](#simple-setup)


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

- [**5V Pressure Sensor**](https://s.click.aliexpress.com/e/_DlGBTMD) – Monitors water pressure, providing voltage output that translates into pressure readings through a simple calculation.  
![5V Pressure Sensor](images/5v-pressure-sensor.jpg)

- [**Step Down Voltage Module from 12V to 5V**](https://s.click.aliexpress.com/e/_DDEQ7e5) – Converts the 12V output from the PSU to 5V for the pressure sensor.  

### Optional

- [**100 nF Ceramic Capacitor**](https://s.click.aliexpress.com/e/_DDLb1XJ) – [**Recommended by Espressif**](https://docs.espressif.com/projects/esp-idf/en/v4.4/esp32/api-reference/peripherals/adc.html#minimizing-noise) to minimize the noise in ADC readings.

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

<!-- https://asciiflow.com/#/share/eJylVUFvmzAU%2FiuWr6siEWlr11tCqg1pIZHSceLCOm%2BLRqAy0DaqKkVoxx04WCmHHHPsadop2q%2Fhl8yEODbBBpJaRLHx8%2Fu%2B973H8yP0nBmCl%2FBqMv7ozxAwMJ5%2Bd8Kp74H7afgDjKPZLTyDrjNHmJo92vAO4YBu2%2FCye2bDB%2Fr%2F%2Fl0%2Bm%2BdvLs7pLEQPIV3YEJRGTwefDOsKGCbIyO%2BMLNRPUpyomCVAPmzbk73OSLx3stzovhc6N6GPM7KSgC43eazbIydgxPRHlAEt%2F2QkZfYHZulJeCuZWirqVb9NCZClRIi0UPSfzO5vbaLKPBiLteHdRiEY6v1dJM%2B%2FmDQFXqyPhhrdMEdaqyi3vmMgT3Tds2ZC5pi0XrDvugirMNupmGzZrHiN8UCrDNPaOqotGcpyjFEQRBgVq9epUBdewjGlJsJ%2BPEFe4GO2ErXgAm8VUX2WWtcC48lnRRgvhdHbnU0sIJe1eb0aqrykHLFAtXS9Dma5wehrbiQyI8XONXa84JuPZ4UoFTdJ26IT%2BXwwBzI%2BL3s%2BX1zn5mduxvjUhVtblbJAmlpkyuTb94M5cl3%2FHgx61z3GRzHotlnM8rtlj3Ro09SgBTL1aITLpipG3rYaE5UwbqWhZFDyWGq%2FiVDy0vZ1JE5soijEjiu4BEJy3%2BTfpGFysKN896MA9B1c9i3mLa9XdgUc45lUUlGqzDKOpYHeQGc4i0pX5ThtL0vualHxKYnycCHBjS3HvUMB0DqdzrmkrmUFTfvqaNilBy6a%2Fbf7xCVxHfuszdGOUgOnFuOkC9KGT%2FDpPwop1eY%3D) -->
```
              AC LIVE IN ┌─────────┐      ┌────┐                       
                    │  ┌─►Contactor├──────►Pump│                       
                    │  │ └────────▲┘      └────┘                       
                    │  ├───┐      │                                    
  ┌───────────────┐ │  │ ┌─▼──────┴─┐                                  
┌─┤Input MCB      ◄─┘  │ │COM1   NO1│                                  
│ ├───────────────┤    │ │Controller│                     ┌───────────┐
├─►Pump MCB       ├────┘ └──────────┘                     │Pressure   │
│ ├───────────────┤      ┌───────┐        ┌──────┐        │Sensor     │
├─►Controller MCB ├──────►12V PSU├──────┬─►5V PSU│        │           │
│ ├───────────────┤      └───────┘      │ │   VCC├────────►red VCC    │
└─►Transformer MCB├────┐ ┌───────────┐  │ │   GND├─────┬──►black GND  │
  └───────────────┘    └─►Transformer│  │ └──────┘     │┌─┤yellow DATA│
                         │N      LIVE│  │              ││ └───────────┘
                         └┬────────┬─┘  │ ┌──────────┐ ││              
                      ┌───▼───┐    │    │ │Controller│ ││              
                      │Neutral│    │    └─►+12V IN   │ ││              
                      │Bus Bar│    │      │       GND◄─┘│              
                      └───┬───┘    │      │    V1 ADC◄──┘              
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

### Pressure sensor

In the YAML configuration, I manage the water pressure using three sensors:
1. The first sensor measures the voltage output from the pressure sensor.
2. The second calculates the actual pressure in bars.
3. The third uses a moving average to stabilize the readings for consistent pressure control.

We're using `0dB` attenuation, suitable for `100 mV ~ 950 mV` as per the [ESP32 ADC guidelines](https://docs.espressif.com/projects/esp-idf/en/v4.4/esp32/api-reference/peripherals/adc.html#adc-attenuation). Considering that we have to divide the input voltage by `0.1887`, at 950mV (ESP32 side) that is the equivalent of ~5V (board side). Our pressure sensor ouputs only up to 4.5V, and that's at 1.2MPa (12 bar), which, we hopefully will never reach. Realistically, most sprinklers are rated up to 5 bar, and drippers up to 3 bar. So the typical working range is about 450mV (~5 bar).

The documentation of the pressure sensor states: `Vout = VCC * (0.75 * Pressure + 0.1)`, from this, we can switch around the numbers and get the pressure formula, which is `Pressure = (Vout - 0.1 * VCC) / (0.75 * VCC)`, this would return the pressure in MPa, so we multiply by `10` to get the bar value.  
At 0 bar, the pressure sensor floats between 0.4 and 0.54V.

_Check out the [full yaml file](irrigator-with-pump-2-controllers.yaml) for complete details, including all units, device classes, etc._
```yaml
  # Pressure sensor voltage
  - platform: adc
    id: pressure_v_int
    name: "Pressure Voltage"
    accuracy_decimals: 2
    update_interval: 500ms
    # 0db is rated up to 950mV (ESP32 side), with the ES32A08 board it means up to 5.0V (0.95 * 0.1887)
    attenuation: 0db
    samples: 64
    pin:
      number: GPIO32 # V1 input on the board
      mode:
        input: true
    filters:
      # 0.1887 is the ratio of voltage divider with 43kΩ and 10kΩ resistors in series
      # This is same as `x / 10.0 * (10.0 + 43.0)`
      - lambda: return x / 0.1887;
      # Our update_interval is 0.5s, and we send every 2 updates, i.e. every second
      - sliding_window_moving_average:
          window_size: 3
          send_every: 2
      - round: 3
      # Don't need any higher resolution
      - delta: 0.02

  # Pressure
  - platform: template
    id: pressure_int
    name: "Pressure"
    accuracy_decimals: 2
    lambda: !lambda |-
      float pressure_v = id(pressure_v_int).state;
      float psu_v = 5.0;
      if (pressure_v > 0.54) {
        return (pressure_v - 0.1 * psu_v) / (0.75 * psu_v) * 10.0;
      } else {
        return 0.0;
      }
    update_interval: 1s
    filters:
      - round: 2
      - delta: 0.03
```

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



## TODOs

- [ ] Intro
- [ ] Rain sensor
- [x] Yaml example
- [ ] Home Assistant automation
- [ ] The math behind irrigation duration
- [x] The math behind pressure sensor
- [ ] Home Assistant dashboard
- [ ] Connection diagrams
- [ ] References
- [ ] The installation process
- [ ] References for pipe sizing, cable sizing
- [ ] Challanges
- [ ] Winterization
- [ ] Move the automation to ESP?
- [ ] Integrate flow meter

## Contributing
Feel free to fork this repository and contribute by submitting pull requests.
