# Nobs Avionics: Arduino Nano ESP32 Firmware

This is the program (the "firmware") that runs on an **Arduino Nano ESP32** board and turns it
into a USB game controller for Microsoft Flight Simulator. Once it's loaded, the board shows up
to your PC and to the Nobs app as **"Nobs Avionics"** with **20 buttons**:

| Buttons | What they are |
| :------ | :------------ |
| 1–4 | 2 rotary knobs (each gives: turn right, turn left) |
| 5–8 | 4 push buttons (PB1–PB4) |
| 9–20 | 6 momentary toggles (each gives: held up, held down) |

**Files you'll use** (they're all in this folder):
- The firmware: [`arduino_eps32_nano.ino`](./arduino_eps32_nano.ino)
- A small settings file next to it: [`build_opt.h`](./build_opt.h) *(leave it where it is, it's important)*
- Which wire goes where: [`docs/arduino-esp-32-wiring.md`](../../docs/arduino-esp-32-wiring.md)
  and the drawing [`docs/wiring-diagram.svg`](../../docs/wiring-diagram.svg)

> 👍 Everything needed is already in this project folder. You do **not** have to edit any Arduino
> program files. Built with the Arduino ESP32 board package version `2.0.18-arduino.5`.

## Loading the firmware

Setup and flashing are exactly the same as for the other Nobs modules. Follow
[the Nobs Autopilot firmware guide](https://github.com/ibovegar/nobs-fs-autopilot/blob/main/firmware/arduino_eps32_nano/README.md)
from **Step 1**, opening this folder's `arduino_eps32_nano.ino` instead. In short:

1. Install **Arduino ESP32 Boards** (by Arduino) in the Boards Manager and pick
   **Arduino Nano ESP32**.
2. Set **Tools → USB Mode** to **Normal mode (TinyUSB)** and **Pin Numbering** to
   **By Arduino pin (default)**.
3. Open `arduino_eps32_nano.ino` (keep `build_opt.h` beside it) and click **Upload**.

The **first** upload just works. After that the board reports its own Nobs identity, so later
uploads need update mode first: **double-tap RESET** (the yellow light goes off and a green light
pulses), click **Upload**, then press **RESET** once.

Or from the command line, in the project folder:

```powershell
& "<path to your Arduino IDE>\resources\app\lib\backend\resources\arduino-cli.exe" `
  compile -b arduino:esp32:nano_nora --output-dir .\out firmware\arduino_eps32_nano

# Double-tap RESET so the green light is pulsing, then:
& "$env:LOCALAPPDATA\Arduino15\packages\arduino\tools\dfu-util\0.11.0-arduino5\dfu-util.exe" `
  --device 0x2341:0x0070 -D .\out\arduino_eps32_nano.ino.bin -Q
```

## Status LED

| What the LED does | What it means |
| :---------------- | :------------ |
| Blinks fast | Booting / waiting for the PC to recognise the board |
| Steady on | The PC has recognised the board |
| Off | No PC showed up within **30 seconds** (e.g. a charge-only cable). It starts blinking again if the board is disconnected and reconnected. |

A quick flicker right at power-up is normal: the LED is on D1, which the chip's bootloader uses
for a moment before the firmware starts.

## Checking that it works

Press `Win`, type **Set up USB game controllers**, open it, select **Nobs Avionics**, click
**Properties**, and use the controls; you should see the matching buttons light up:

- Turning a knob blinks its turn-right or turn-left button once per click (more when you spin fast).
- Push buttons and toggles light their button for as long as you hold them.

If a knob counts the wrong way, swap its A and B pins in `encA`/`encB`. If a toggle's UP and DOWN
are reversed, swap its pins in `togUp`/`togDn` (or swap the two outer wires).

## Board identity

The board uses vendor ID `303A` and defaults to PID `8104` / **"Nobs Avionics"**. Avionics owns the
PID block `8104`–`8107` (the block after Nobs Yoke's `8100`–`8103`), so a second unit would be:

```
SET_ID:8105:Nobs Avionics 2
```

`GET_ID` reads back what's stored. This works the same way as on the other modules; see
[board identity](https://github.com/ibovegar/nobs-fs-autopilot/blob/main/docs/board-identity.md).

## Knob acceleration

Each knob speeds up when you spin it fast. The strength is set per knob over the board's serial
port (`A0<n>` / `A1<n>` with `n` from 0 = off to 255 = full; `A0?` reads it back), and is saved on
the board. The default is full acceleration.
