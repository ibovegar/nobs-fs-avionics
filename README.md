# Nobs Avionics

A build-it-yourself control panel for flight simulators: **2 rotary encoders**, **4 push buttons**
and **6 momentary toggle switches**, wired to an **Arduino Nano ESP32**. It plugs in over USB and
shows up as a standard game controller (HID gamepad) named **"Nobs Avionics"** (`303A` / `8104`),
recognised directly by MSFS and the Nobs app with no drivers to install.

The front panel has two rows of six, with the Nano in the middle behind it:

| | Left | | | Right | | |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Row 1** | PB1 | PB2 | PB3 | T1 | T2 | T3 |
| **Row 2** | ENC1 | ENC2 | PB4 | T4 | T5 | T6 |

That makes **20 buttons** in total:

| Input | Buttons |
| :--- | :--- |
| ENC1, ENC2: rotary encoders (no push), turn right and turn left as one button each | 1–4 |
| PB1–PB4: push buttons | 5–8 |
| T1–T6: momentary (ON)-OFF-(ON) toggles, held up and held down as one button each | 9–20 |

A status LED blinks while the board waits for the PC, stays on once it's recognised, and goes dark
if no PC shows up within 30 seconds.

## Design Philosophy

**Every control springs back or clicks, so nothing on the panel holds a state the sim can
disagree with.** The toggles are momentary: flick one up or down and it returns to the centre,
so it acts as a command ("next", "previous", "on", "off") rather than a position the sim has to
match. The encoders have no end stops. All of it can be found and operated by feel, which keeps
the panel VR-friendly:

* **Momentary toggles, not latching ones:** a (ON)-OFF-(ON) toggle can never be out of sync with
  the sim after a reload or an autopilot change; every flick is a fresh command.
* **Encoders for dialing:** turning a knob to set a value is more natural, and with acceleration
  for fast spins more precise, than pressing a button repeatedly.
* **Driver-Free by Design:** the board presents itself as a standard HID gamepad, so MSFS and the
  Nobs app recognise it the moment it's plugged in.

> 📸 This module is a fresh design: the front panel and mounting plate are modeled
> ([models/](models/)), but it hasn't been physically built yet, so there are no product photos
> here.

## Docs

- **[Which wire goes where](docs/arduino-esp-32-wiring.md)**: the button + pin map. For a drawing
  of the same wiring, see the [wiring diagram](docs/wiring-diagram.svg) (or open
  [wiring-diagram.html](docs/wiring-diagram.html) in a browser).
- **[Loading the firmware](firmware/arduino_eps32_nano/README.md)**: first-time flashing,
  re-flashing, the status LED, and the encoder acceleration commands.

## Nobs FS Companion App

The [**Nobs FS app**](https://github.com/ibovegar/nobs-fs-app) is the companion application for
communicating with and configuring the Nobs family of panels, including this one. It automatically
detects Nobs devices by their USB identity (VID `303A`, per-product PID block), so the right
device is selected even when other game controllers are connected.

Use it to:
* **Verify wiring & test inputs:** watch every encoder, button and toggle register live as you
  turn, press or flick it, handy for confirming the build before binding anything in the sim.
* **Track multiple panels:** the Devices page lets you add extra instances of the panel, each with
  its own ID and name (see [Setting the device ID & name](#setting-the-device-id--name-in-brief)
  below), so the app and the sim can tell them apart.

See the app repository for installation and usage details: <https://github.com/ibovegar/nobs-fs-app>

## Setting the device ID & name (in brief)

The board's name and USB product ID aren't compiled in; they're stored on the board, so the same
firmware can be set up as any Nobs profile. Out of the box this is **"Nobs Avionics"** (`303A` /
`8104`). To change it, send a single line over the board's serial port:

```
SET_ID:8104:Nobs Avionics
```

The board saves the new name + ID, replies `OK:8104:Nobs Avionics`, and reboots so it takes effect
(`GET_ID` reads back the current values). For multiple panels, give each one the next ID in the
block (`8104`–`8107`), e.g. `SET_ID:8105:Nobs Avionics 2`. Full details, including the Windows
name-cache refresh, are in the Nobs Autopilot's
**[board identity guide](https://github.com/ibovegar/nobs-fs-autopilot/blob/main/docs/board-identity.md)**;
it works the same way here.

Firmware is derived from the [Nobs Autopilot](https://github.com/ibovegar/nobs-fs-autopilot)
firmware.
