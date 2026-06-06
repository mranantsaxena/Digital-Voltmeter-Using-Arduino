# Digital-Voltmeter-Using-Arduino

Simulation Link : https://www.tinkercad.com/things/l50JpfrU136-digital-voltmeter?sharecode=puwdnh3JuR-mHb93zRWVin6ge4_uOz81dAxvkDi2tg0

A digital voltmeter simulation built with an Arduino Uno (ATmega328P). It safely measures external DC voltages using a hardware voltage divider (90.9kΩ/10kΩ) to step down the input. The firmware leverages the internal 10-bit ADC to calculate the original source voltage, outputting real-time data to an I2C 16x2 LCD. 

## 🛠️ Hardware Architecture
* **Microcontroller:** Arduino Uno (ATmega328P)
* **Display:** 16x2 Character LCD (I2C Interface)
* **Sensing Circuit:** Voltage Divider Network
    * $R_1$ (Top Resistor) = 90.9 kΩ
    * $R_2$ (Bottom Resistor) = 10 kΩ
* **Input Simulation:** 10kΩ Potentiometer (acting as a variable DC power supply for testing)

## 📐 Mathematical Model & Signal Conditioning
Microcontroller GPIO pins operate at a 5V logic level. Direct measurement of higher source voltages will destroy the ADC pins. To safely measure external voltages, the hardware utilizes a voltage divider to step down the signal.

The analog signal at pin `A0` is processed by the ATmega328P's 10-bit ADC, which maps the 0–5V hardware input into 1024 discrete digital steps. 

The firmware calculates the stepped-down voltage ($V_{out}$) reading at the pin:

$$V_{out} = \frac{\text{ADC Value} \times 5.0}{1024.0}$$

The original source voltage ($V_{in}$) is then dynamically reconstructed by reversing the standard voltage divider formula:

$$V_{in} = V_{out} \times \left( \frac{R_1 + R_2}{R_2} \right)$$

## 🔌 Circuit Wiring Guide

### I2C LCD Connection (Optimized Footprint)
By utilizing the I2C protocol, the display interface reduces the required microcontroller footprint from twelve standard parallel data lines down to just four:
* **GND** $\rightarrow$ Arduino GND
* **VCC** $\rightarrow$ Arduino 5V
* **SDA** $\rightarrow$ Arduino A4
* **SCL** $\rightarrow$ Arduino A5

### Voltage Sensing Circuit
* Connect $R_1$ (90.9kΩ) and $R_2$ (10kΩ) in series.
* Connect the free end of $R_2$ to **Arduino GND**.
* Connect the free end of $R_1$ to the **Positive terminal** of the voltage source being measured.
* Connect the junction between $R_1$ and $R_2$ directly to **Arduino Analog Pin A0**.

## 💻 Firmware Overview
The code is written in C++ using the standard Arduino framework. It utilizes `Wire.h` and `LiquidCrystal_I2C.h` for efficient display communication. The main loop features continuous polling of the `A0` pin with a 300ms delay to ensure display stability and prevent screen flickering, alongside serial output at 9600 baud for headless data logging and debugging.

### Dependencies
* [LiquidCrystal_I2C Library](https://github.com/johnrickman/LiquidCrystal_I2C)
