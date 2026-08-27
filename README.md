# Smart Distance Measurer (ATmega32)

An ultrasonic distance measurement system built on the ATmega32 microcontroller, using an HC-SR04 sensor for distance sensing and a 16x2 LCD for real-time display output.

## Overview

The system triggers the HC-SR04 ultrasonic sensor, measures the echo pulse width using a hardware timer and external interrupt, converts the pulse duration into a distance in centimeters, and displays the result live on a 16x2 character LCD.

## How It Works

1. The microcontroller sends a 15 µs trigger pulse to the HC-SR04's TRIGGER pin.
2. The sensor emits an ultrasonic burst and the ECHO pin goes high for a duration proportional to the distance to the nearest object.
3. An external interrupt (INT0) captures the rising and falling edges of the echo signal, starting and stopping Timer1 to measure the pulse width.
4. The captured timer value is converted to distance (`pulse / 58` for cm) and displayed on the LCD.

## Hardware

- **Microcontroller:** ATmega32 (1 MHz internal clock)
- **Sensor:** HC-SR04 Ultrasonic Distance Sensor
- **Display:** 16x2 character LCD (4-bit/8-bit parallel interface)
- **Passive components:** 10kΩ pull-up/pull-down resistors on programmer/interrupt lines

### Pin Connections

| Signal            | ATmega32 Pin |
|-------------------|--------------|
| HC-SR04 TRIGGER   | PD0          |
| HC-SR04 ECHO      | INT0 (PD2)   |
| LCD Data (D0–D7)  | PORTB        |
| LCD RS             | PD6          |
| LCD Enable         | PD5          |
| HC-SR04 VCC        | +5V (Pin 1)  |
| HC-SR04 GND        | GND (Pin 4)  |

![Circuit Schematic](Images/schematic.png)

## Firmware

Written in embedded C (AVR-GCC), the firmware:

- Configures Timer1 and INT0 for pulse-width capture
- Sends LCD initialization commands (function set, display on/cursor off, clear display)
- Continuously triggers the sensor, computes distance, and refreshes the LCD display in a polling loop
- Uses `itoa()` to convert the computed distance into a displayable string

## Files

- `distance_measurer.c` — Main firmware source
- `Images/schematic.png` — Circuit schematic (ATmega32, HC-SR04, LCD wiring)

## Build & Flash

Compile with AVR-GCC and flash using your programmer of choice (e.g. USBasp, AVRISP):

```bash
avr-gcc -mmcu=atmega32 -DF_CPU=1000000UL -Os -o distance_measurer.elf distance_measurer.c
avr-objcopy -O ihex distance_measurer.elf distance_measurer.hex
avrdude -c usbasp -p atmega32 -U flash:w:distance_measurer.hex
```

## Future Enhancements

- Add a 7-segment display output alongside/instead of the LCD for a compact readout
- Add distance-threshold alerts (e.g. buzzer or LED when object is too close)
- Replace polling-based trigger loop with a timer-interrupt-driven periodic measurement
- Add UART logging for distance data over time

## Author

Shreeraj Patil — B.Tech Electronics & Communication Engineering, CHARUSAT
