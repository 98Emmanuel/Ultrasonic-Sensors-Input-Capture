# STM32 Ultrasonic Distance Measurement (HC-SR04) Using Timer Input Capture

## Author

Emmanuel Odongo

## Overview

This project demonstrates how to interface the **HC-SR04 Ultrasonic Distance Sensor** with an **STM32 microcontroller** using **Timer Input Capture**. The system measures the **pulse width of the echo signal** using hardware timers and converts it into distance.

Instead of polling the echo pin, the project uses **hardware input capture interrupts**, allowing precise timing and efficient CPU usage.

Measured distances are transmitted to a PC via **UART serial communication**.

---

## Hardware Used

* STM32 Microcontroller (TIM2 used for Input Capture)
* HC-SR04 Ultrasonic Sensor
* USB-to-UART connection (USART2)
* Optional voltage divider for Echo pin protection

---

## Sensor Working Principle

1. The STM32 sends a **10 µs trigger pulse** to the HC-SR04.
2. The sensor emits an ultrasonic burst.
3. When the echo returns, the **ECHO pin goes HIGH**.
4. The ECHO pin stays HIGH until the sound returns.
5. The **width of the HIGH pulse represents the travel time of the sound wave**.

Distance is calculated using:

Distance (cm) = Time (µs) / 58

---

## System Architecture

Trigger Signal → Ultrasonic Sensor → Echo Pulse
Echo Pulse → Timer Input Capture → Interrupt Callback
Callback → Pulse Width Calculation → Main Loop
Main Loop → Distance Calculation → UART Output

---

## Timer Configuration

### TIM2 (Input Capture)

* Channel: TIM2_CH1
* Mode: Input Capture
* Initial Edge: Rising
* Prescaler: 79
* Timer Clock: 80 MHz
* Tick Resolution: 1 µs

### TIM6 (Microsecond Delay Timer)

Used to generate precise microsecond delays for the trigger signal.

---

## GPIO Configuration

| Pin      | Function               |
| -------- | ---------------------- |
| PB3      | Trigger Output         |
| Echo Pin | TIM2_CH1 Input Capture |
| PB6      | Debug LED Toggle       |

Trigger pin configuration:

```
GPIO_MODE_OUTPUT_PP
GPIO_NOPULL
```

Echo pin configuration:

```
GPIO_MODE_AF
GPIO_NOPULL
Alternate Function: TIM2_CH1
```

---

## Software Workflow

1. Send **10 µs trigger pulse** to ultrasonic sensor.
2. Timer waits for **rising edge of Echo signal**.
3. Input capture interrupt stores first timestamp.
4. Capture polarity switches to **falling edge detection**.
5. Falling edge occurs when echo returns.
6. Timer stores second timestamp.
7. Pulse width is calculated.
8. Distance is computed and printed over UART.

---

## Input Capture Callback Logic

The interrupt callback measures the pulse width of the echo signal.

```
Rising edge → store start time
Switch to falling edge detection
Falling edge → store end time
Pulse width = end - start
Set measurement flag
```

This ensures precise measurement using hardware timers.

---

## Example Output

Serial console output:

```
This is the distance: 23.45
This is the distance: 23.38
This is the distance: 23.41
```

---

## Key Embedded Concepts Demonstrated

* STM32 Timer Input Capture
* Interrupt-driven sensor measurement
* Hardware pulse width measurement
* Edge polarity switching
* Microsecond delay using timer
* UART debugging output
* Sensor interfacing

---

## Project Structure

```
Core/
 ├── Inc/
 ├── Src/
main.c
README.md
```

---

## Future Improvements

* Moving average filter for stable readings
* Echo timeout protection
* Multiple ultrasonic sensors
* DMA-based capture
* LCD/OLED distance display

---

## License

This project is provided for learning and educational purposes.
