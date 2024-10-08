
# Breezy Box: Wall-Mounted Air Purifier for A1 Mini+ 

Breathe easy with Breezy Box, a compact and efficient DIY air purifier designed for wall-mounted convenience. This project is perfect for anyone looking to improve their indoor air quality, and it's optimized for 3D printing on A1 Mini.

**Features:**

* **Wall-Mounted Design:** Frees up valuable floor space and blends seamlessly into your home environment.
* **A1 Mini Compatible:** Optimized for printing on the Bambu Lab A1 Mini (128 x 128 x 128 mm build volume), but printable on larger printers too.
* **Efficient Filtration:** Accommodates two 120mm PC fans for powerful air circulation and filtration (fans not included).
* **Easy Assembly:** Simple construction with clear instructions and minimal hardware.

**Materials:**

* 1.5 kg PLA filament
* 16 x M5 16mm screws
* 12 x M3 8mm screws
* 2 x 120mm PC fans (not included)
* 12V 3A power supply
* 12V to 5V USB step-down converter
* ESP32-WROOM-32 board
* 1x Ikea FÖRNUFTIG air filter
* Optional: Addressable LED strip (WS2812B or similar)
* Optional: 1 to 5 PWM fan hub

**Print Settings:**

* 8% adaptive cubic infill
* 5 bottom and top layers
* 2 walls

**Electronics:**

* The ESP32 controls the fan speed via PWM and can optionally control an addressable LED strip.
* A 12V to 3.3V buck converter powers the ESP32 from the 12V power supply.
* A 12V to 5V USB step-down converter powers the optional LED strip.

**ESP Home Code:**

```
# Taco sensor (fan speed)
sensor:
  - platform: pulse_counter
    pin: 
      number: GPIO25   # Connect to any input PIN on the ESP
      mode:
        input: true
        pullup: true
    unit_of_measurement: 'RPM'
    id: fan_hub_speed
    name: pwm_fan_hub_speed
    accuracy_decimals: 0
    filters:
      - multiply: 0.5  # De
    update_interval: 3s

  - platform: pulse_counter
    pin: 
      number: GPIO26   # Connect to any input PIN on the ESP
      mode:
        input: true
        pullup: true
    unit_of_measurement: 'RPM'
    id: fan_single_speed_1
    name: pwm_fan_single_speed_1
    accuracy_decimals: 0
    filters:
      - multiply: 0.5  # De
    update_interval: 3s

  - platform: pulse_counter
    pin: 
      number: GPIO27   # Connect to any input PIN on the ESP
      mode:
        input: true
        pullup: true
    unit_of_measurement: 'RPM'
    id: fan_single_speed_2
    name: pwm_fan_single_speed_2
    accuracy_decimals: 0
    filters:
      - multiply: 0.5  # De
    update_interval: 3s

# PWM Signal
output:
  - platform: ledc
    id: pwm_fan_speed_control
    pin: GPIO13
    # 25KHz is standard PC fan frequency, minimises buzzing
    frequency: "25000 Hz" 
    # my fans stop working below 13% powerful.
    # also they're  powerful and loud, cap their max speed to 80%
    min_power: 8%
    max_power: 80%
    # At 0, actually turn it off, otherwise the power keeps going.
    zero_means_zero: true

fan:
  - platform: speed  # Changed to automatic for dynamic control
    output: pwm_fan_speed_control
    name: "PWM Fan"

light:
  - platform: fastled_clockless
    id: led_strip
    name: "Air Purifier LEDs"
    chipset: WS2812B  # Or the appropriate chipset for your strip
    pin: GPIO12  # Choose a suitable GPIO pin for SPI MOSI
    num_leds: 80
    rgb_order: GRB
```
