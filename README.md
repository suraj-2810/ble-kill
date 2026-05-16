# nrf-bluetooth-nullifier

A Bluetooth disruption device built with an ESP32 WROOM-32 and nRF24L01 PA+LNA module. Demonstrates how low-cost hardware can interfere with Bluetooth Classic and BLE devices operating in the 2.4GHz band using a continuous carrier wave and frequency hopping.

---

## ⚠️ Legal Disclaimer

This project is for **educational and research purposes only**.

- Only use on devices you own or have **explicit written consent** to test
- RF jamming may be **illegal in your country** (in India, unauthorized jamming violates the Indian Wireless Telegraphy Act)
- The author takes no responsibility for misuse of this project
- Always operate in a controlled, isolated environment

---

## How It Works

Bluetooth operates on the 2.4GHz ISM band (2.402–2.480 GHz), using frequency hopping spread spectrum (FHSS) to avoid interference. This device exploits that band using two mechanisms:

**Constant Carrier Wave**
The nRF24L01's `startConstCarrier()` function transmits a continuous unmodulated RF signal at maximum power. This saturates the 2.4GHz band making it difficult for Bluetooth devices to maintain their frequency hopping pattern.

**Channel Sweeping**
The firmware sweeps across all 79 Bluetooth channels and known Classic Bluetooth channels in rapid succession, ensuring no channel is left unaffected.

The result is audio dropout, connection instability, and failed pairing attempts on nearby Bluetooth devices.

---

## Hardware Required

| Component | Qty | Notes |
|---|---|---|
| ESP32 WROOM-32 | 1 | Any 30-pin DevKit with BT — confirmed with OceanLabz CH340 variant |
| nRF24L01 PA+LNA with SMA Antenna | 1 | PA+LNA variant recommended for maximum power |
| nRF24L01 Adapter Board | 1 | HW-200 or equivalent — provides stable 3.3V regulation |
| Mini Breadboard | 1 | For wiring junction |
| Male-to-Female Jumper Wires | 7 | 20cm recommended |
| USB-A to USB-C Cable | 1 | For flashing and power |

**Estimated cost: ~₹900–1000**

---

## Wiring

Connect the nRF24L01 adapter board to the ESP32 using the following pin mapping:

| nRF24L01 Adapter Pin | ESP32 GPIO | Wire |
|---|---|---|
| VCC | 3V3 | Power |
| GND | GND | Ground |
| CE | GPIO 15 | Chip Enable |
| CSN | GPIO 5 | Chip Select |
| SCK | GPIO 18 | SPI Clock |
| MOSI (MO) | GPIO 23 | SPI Data Out |
| MISO (MI) | GPIO 19 | SPI Data In |
| IRQ | — | Not connected |

> ⚠️ Always power nRF24L01 from 3V3, never 5V or VIN. Use the adapter board for stable voltage regulation.

---

## Software Requirements

- [Arduino IDE 2.x](https://www.arduino.cc/en/software)
- ESP32 board package by Espressif Systems
- [RF24 library by TMRh20](https://github.com/nRF24/RF24)

### Install ESP32 Board Package

In Arduino IDE go to **File → Preferences** and add this URL to Additional Boards Manager URLs:

```
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
```

Then go to **Tools → Board → Boards Manager**, search `esp32` and install **esp32 by Espressif Systems**.

### Install RF24 Library

Go to **Tools → Manage Libraries**, search `RF24` and install **RF24 by TMRh20**.

---

## Flashing

1. Clone this repo
2. Open `firmware/nrf_bluetooth_nullifier.ino` in Arduino IDE
3. Select board: **Tools → Board → ESP32 Dev Module**
4. Select port: **Tools → Port → /dev/ttyUSB0** (Linux) or **COMx** (Windows)
5. Click Upload

The device starts jamming automatically on every boot. No interaction required after flashing.

---

## Diagnostic Test

Before flashing the jammer firmware, verify your hardware is wired correctly by uploading `firmware/diagnostic.ino`. Open Serial Monitor at 115200 baud and press the EN button.

Expected output if wiring is correct:
```
=== Hardware Diagnostic ===
nRF24L01... OK
Model = nRF24L01+
PA Power = PA_MAX
```

If you see `FAILED — check wiring`, recheck your SPI connections (CE, CSN, SCK, MOSI, MISO).

---

## Demo Results

Tested on:
- Bluetooth speaker (Classic BT) — complete audio dropout at close range
- Wireless earphones (BLE) — audio breaking and lag at close range

Effect range with single PA+LNA module: complete disruption within ~20cm, heavy disruption up to ~50cm.

---

## File Structure

```
nrf-bluetooth-nullifier/
├── firmware/
│   ├── nrf_bluetooth_nullifier.ino   # Main jammer firmware
│   └── diagnostic.ino                # Hardware diagnostic sketch
└── README.md
```

---

## Built By

**Suraj Sinha**

---

## References

- [nrfBlueNullifier by WireBits](https://github.com/WireBits/nrfBlueNullifier) — original single-module firmware this build is based on
- [RF24 Library Documentation](https://nrf24.github.io/RF24/)
- [ESP32 Technical Reference](https://www.espressif.com/sites/default/files/documentation/esp32_technical_reference_manual_en.pdf)
