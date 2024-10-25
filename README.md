# ESPHome Irrigation System based on ES32A08 Expansion Board

// TODO: intro

## Key Features

- Up to 8 zones or 7 zones and a pump.
- Automatic irrigation scheduling based on sun elevation.
- Irrigation duration based on actual past precipitation data + forecast. 
- Can also function completely without Home Assistant or even without network.
- Separate "controller" logic for drip irrigation, e.g. garden, thujas, plantations.
- UI switchable logic for everything-automation, without the need to change any code.
- All mounted in a nice consumer unit on DIN rails.

## Hardware

### Required

- [**ESP32-WROOM-32E or 32UE**](https://s.click.aliexpress.com/e/_DBjuSlX). It **must** be 38-pin version, and ESP32-WROOM-32UE version is recommended to have an external antenna further away from the high(er) voltage cables.  
![ESP32-WROOM-32UE](images/esp32-wroom-32ue.jpg)

- [**ES32A08** Expansion Board](https://s.click.aliexpress.com/e/_DE1CWk5) by _eletechsup_. It is a pretty affordable but very fitting board that has plenty of I/O and most importantly, 8 relays. [More about it in my repo]((https://github.com/makstech/esphome-es32a08-expansion-board-example)) specifically about the use of this board with ESPHome.  
![ES32A08](images/es32a08.jpg)

- [**12V Power Supply**](https://s.click.aliexpress.com/e/_DmWa6VJ). This will supply the ES32A08 board. Anything above 1A (10W) is more than sufficient. _Mean Well HDR-15-12_ is a compact option. At this point, I don't remember why, but I bought 60W version.

- **24V AC Transformer**. I've bought an encapsulated 230VAC to 24VAC 30VA (1.25A) transformer. Again, a bit overkill but it handles opening up all 7 valves if I need to dump all the water from the system quickly.

- ... and everything releated to the irrigation itself, like 24VAC valves, rotors, drippers, and so on.

### Required for Pump Control

- [**AC Contactor**](https://s.click.aliexpress.com/e/_DmPWcfj). I don't exactly trust the relays on the board to handle a powerful pump. Make sure that the contactor can handle the power of your pump + some overhead and it's NO (Normally Open). It can be toggled by either 12V PSU, or regular AC. I've got the `2P 16A 2NO 230VAC`, which means that it switches 2 Normally Open circuits (Live and Neutral) using 230VAC.

- [**5V Pressure Sensor**](https://s.click.aliexpress.com/e/_DlGBTMD). This will be used to measure the water pressure in the system to then control the pump. It outputs the voltage based on the pressure and with a simple math formula we can get the pressure.  
![5V Pressure Sensor](images/5v-pressure-sensor.jpg)

- [**Step Down Voltage Module from 12V to 5V**](https://s.click.aliexpress.com/e/_DDEQ7e5). Converting 12V from PSU, to 5V that the pressure sensor accepts.

### Optional

- [**100 nF Ceramic Capacitor**](https://s.click.aliexpress.com/e/_DDLb1XJ). [**Recommended by Espressif**](https://docs.espressif.com/projects/esp-idf/en/v4.4/esp32/api-reference/peripherals/adc.html#minimizing-noise) to minimize the noise in ADC readings.

- [**External Antenna with U.FL/IPEX Pigtail**](https://s.click.aliexpress.com/e/_DEGqnTP). For **32UE** ESP32 variant.

## Disclaimer

I am not responsible for any damage, injury, or consequences that may result from attempting to replicate or modify this project. Working with electricity can be dangerous, and it is important to take proper precautions. If you are not familiar with electrical wiring or unsure about any part of this project, consult a qualified electrician before proceeding. Always follow local electrical codes and safety guidelines.

## TODOs

- [ ] Yaml example
- [ ] Home Assistant automation
- [ ] The math behind irrigation duration
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
