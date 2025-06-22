Here is the English translation of the provided `README.md`:

---

# Autonomous Marine Weather Station Improvement

This project is an enhancement of the "Autonomous Marine Weather Station," which can be found at the following link.

The project has been simulated using **WOKWI**. Here's the link to the simulation.

## Additional Hardware Required

As we know, the buoy is controlled by an Arduino Uno, and the following components have been added:

* An infrared (IR) remote control operating at 38KHz with 20 keys.
* A 38KHz infrared receiver to receive signals from the remote.

## Updated Wiring Diagram

| Wire Color  | Arduino Pin | Connected To                                   | Function                       |
| ----------- | ----------- | ---------------------------------------------- | ------------------------------ |
| Red         | 5V          | DHT22, Pushbutton, ULN2003, MQ-2, I2C LCD, RTC | Power (VCC)                    |
| Black       | GND         | All modules (common)                           | Ground (GND)                   |
| White       | 12          | DHT22                                          | Temperature/humidity signal    |
| Yellow      | 13          | Pushbutton                                     | Button input                   |
| Orange      | A0          | LDR                                            | LDR sensor                     |
| Gray        | 4           | FIT0845                                        | Heater                         |
| Light Green | 8           | Motor A+                                       | Stepper motor                  |
| Pink        | 9           | Motor A-                                       | Stepper motor                  |
| Purple      | 10          | Motor B+                                       | Stepper motor                  |
| Brown       | 11          | Motor B-                                       | Stepper motor                  |
| Green       | 3           | Servo motor                                    | PWM control                    |
| Violet      | 7           | MQ-2                                           | Gas analog reading             |
| Green       | A4 (SDA)    | RTC DS1307, I2C LCD                            | I2C communication – data line  |
| Blue        | A5 (SCL)    | RTC DS1307, I2C LCD                            | I2C communication – clock line |
| Light Blue  | 2           | IR Receiver                                    | Receives IR pulses from remote |

## System Mode Management via IR Remote

The code manages different operating modes for the weather buoy using an IR remote to select operations. The possible states are grouped as follows:

---

### 1. System Display States (Test Mode)

These are handled in the `test_mode()` function. They refer to the operating states from the previous project, which were cycled using a button. Controlled via the `mode` variable, which can take five values (0 to 4), each mapped to a specific LCD display function:

| `mode` | State                        | Action                |
| ------ | ---------------------------- | --------------------- |
| 0      | Date and Time                | `display_date_hour()` |
| 1      | Light Intensity              | `display_lux()`       |
| 2      | Humidity and Temperature     | `display_dht22()`     |
| 3      | Air Quality (Clean/Polluted) | `display_MQ2()`       |
| 4      | Thermal System Status + Temp | `display_temp_ctl()`  |

---

### 2. One-Time IR Button States – `IR_mode_one_time()`

This mode runs only once per key press. Buttons change the current state (`estadoActual`) or modify parameters. Summary below:

#### Power ON/OFF

| IR Button (value) | State  | Action                                    |
| ----------------- | ------ | ----------------------------------------- |
| 162 - Power       | Toggle | `display_welcome()` / `display_goodbay()` |

#### Modify Thermal Parameters

| IR Button          | Action                     | Functions Involved                                |
| ------------------ | -------------------------- | ------------------------------------------------- |
| 144 - Next Arrow   | Increase temperature limit | `mod_temp_lim()` + `display_temp_lim_margin()`    |
| 224 - Prev Arrow   | Decrease temperature limit | Same                                              |
| 2 - Plus Symbol    | Increase margin            | `mod_temp_margin()` + `display_temp_lim_margin()` |
| 152 - Minus Symbol | Decrease margin            | Same                                              |

#### Mode/View Changes

(Changes both `estadoAnterior` and `estadoActual`)

| IR Button      | LCD Mode Displayed            |
| -------------- | ----------------------------- |
| 48 - Button 1  | Mode 1: Date and Time         |
| 24 - Button 2  | Mode 2: Light Intensity       |
| 122 - Button 3 | Mode 3: Humidity and Temp     |
| 16 - Button 4  | Mode 4: Air Quality           |
| 56 - Button 5  | Mode 5: Thermal System Status |
| 90 - Button 6  | Mode 6: Open Gate, Motor ON   |
| 66 - Button 7  | Mode 7: Close Gate, Motor OFF |
| 74 - Button 8  | Mode 8: Turn Heater ON        |
| 82 - Button 9  | Mode 9: Turn Heater OFF       |

---

### 3. Continuous IR Button States – `IR_mode_continuo()`

This mode executes continuously while the button is held. It uses the same IR buttons but directly calls the functions without changing `estadoActual`. Acts more like a direct control mode.

| IR Button      | State                 | Action                                      |
| -------------- | --------------------- | ------------------------------------------- |
| 34 - Test      | Test Mode             | `test_mode()`                               |
| 48 - Button 1  | Date and Time         | `display_date_hour()`                       |
| 24 - Button 2  | Light Intensity       | `display_lux()`                             |
| 122 - Button 3 | Humidity and Temp     | `display_dht22()`                           |
| 16 - Button 4  | Air Quality           | `display_MQ2()`                             |
| 56 - Button 5  | Thermal System Status | `display_temp_ctl()`                        |
| 90 - Button 6  | Open Gate, Motor ON   | `cold_pwr_on()` + `display_cold_ctl()`      |
| 66 - Button 7  | Close Gate, Motor OFF | `cold_pwr_off()` + `display_pwr_off_cold()` |
| 74 - Button 8  | Heater ON             | `warm_pwr_on()` + `display_warm_ctl()`      |
| 82 - Button 9  | Heater OFF            | `warm_pwr_off()` + `display_pwr_off_warm()` |

---

### 4. Internal System States

Variables that determine system behavior:

* `mode`: controls what is shown in `test_mode()`
* `button_value`: stores the last IR button value received
* `estadoActual`: current logical system state, used to maintain context between IR actions
* `estadoAnterior`: previous state, useful for rollback or recovery
* `pwr_on_flag`: indicates whether the system is powered on or off

---

## Conclusion

The improvements allow toggling via remote control between:

* A cyclic test mode
* A direct continuous control mode
* An IR event-driven mode where a button press triggers a one-time action

All of this provides a robust interface for remote control using an IR remote.

---

## How to Use

1. Connect all sensors and actuators to the microcontroller as indicated in the pin definitions (`#define ...`).
2. Upload the code to the microcontroller using the Arduino IDE.
3. Upon startup, the buoy will be in standby mode. Power it on using the remote and switch between modes with it.

---

## Demonstration Videos

You can find the demonstration video of the system in action in the main project repository under the name **"Video\_funcionamiento\_pr\_3"**.

---

## License

This project is open source and released under the **MIT License**.

---
