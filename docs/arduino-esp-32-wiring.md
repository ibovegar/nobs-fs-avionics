# Wiring Map: Which Wire Goes Where

This table tells you which pin on the board each wire connects to, for the **Arduino Nano ESP32**.
For a drawing of the same wiring, see [wiring-diagram.svg](wiring-diagram.svg) (or open
[wiring-diagram.html](wiring-diagram.html) in a browser).

This box has **4 push buttons** (PB1–PB4), **2 rotary encoders** (ENC1–ENC2, no push switch) and
**6 momentary toggle switches** (T1–T6, (ON)-OFF-(ON): spring-return to the center). There's also a
**status LED**, which is part of the build, not optional.

The panel has two rows, with the Nano in the middle behind it:

| | Left of the Nano | | | Right of the Nano | | |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Row 1** | PB1 | PB2 | PB3 | T1 | T2 | T3 |
| **Row 2** | ENC1 | ENC2 | PB4 | T4 | T5 | T6 |

Buttons and encoders go to the analog header on the left, toggles and the LED to the digital
header on the right, in an order that keeps every wire short and stops any two from crossing.

**How to read a row:** find the control (e.g. "T1"), look at which terminal it is ("UP throw"),
then run a wire from that terminal to the pin listed in the board's column. Pin names like `A3` or
`D12` are printed on the board itself. Every control's common terminal connects to **GND**
(ground), see the last rows.

The "Virtual HID Button" column is just for reference: it's the button number the sim sees when you
use that control. You don't wire anything for it.

> 💡 Each terminal has one wire to its signal pin (from this table) and the common terminal wires
> to **GND**. No resistors or extra parts needed apart from the LED's; the firmware handles that.

| Component Group | Component Label | Hardware Connection | Arduino Nano ESP32 Pin | Virtual HID Button | Action Type |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **ENC1** | Encoder 1 | Phase A Terminal | **A3** | Button 1 | CW Pulse |
| | Encoder 1 | Phase B Terminal | **A4** | Button 2 | CCW Pulse |
| **ENC2** | Encoder 2 | Phase A Terminal | **A5** | Button 3 | CW Pulse |
| | Encoder 2 | Phase B Terminal | **A6** | Button 4 | CCW Pulse |
| **Push buttons** | PB1 | Signal Terminal | **A2** | Button 5 | Push |
| | PB2 | Signal Terminal | **A1** | Button 6 | Push |
| | PB3 | Signal Terminal | **A0** | Button 7 | Push |
| | PB4 | Signal Terminal | **A7** | Button 8 | Push |
| **T1** ((ON)-OFF-(ON)) | Toggle 1 | UP Terminal | **D12** | Button 9 | Held up |
| | Toggle 1 | DOWN Terminal | **D11** | Button 10 | Held down |
| **T2** ((ON)-OFF-(ON)) | Toggle 2 | UP Terminal | **D10** | Button 11 | Held up |
| | Toggle 2 | DOWN Terminal | **D9** | Button 12 | Held down |
| **T3** ((ON)-OFF-(ON)) | Toggle 3 | UP Terminal | **D8** | Button 13 | Held up |
| | Toggle 3 | DOWN Terminal | **D7** | Button 14 | Held down |
| **T4** ((ON)-OFF-(ON)) | Toggle 4 | UP Terminal | **D0** | Button 15 | Held up |
| | Toggle 4 | DOWN Terminal | **D2** | Button 16 | Held down |
| **T5** ((ON)-OFF-(ON)) | Toggle 5 | UP Terminal | **D3** | Button 17 | Held up |
| | Toggle 5 | DOWN Terminal | **D4** | Button 18 | Held down |
| **T6** ((ON)-OFF-(ON)) | Toggle 6 | UP Terminal | **D5** | Button 19 | Held up |
| | Toggle 6 | DOWN Terminal | **D6** | Button 20 | Held down |
| **Ground Loop** | All Components | Encoder C, push-button second terminals, toggle center terminals | **GND** | *None* | System Ground |
| **Status LED** | Status LED | Anode, via 120 Ω resistor | **D1** | *None* | Boot/Connect Status |
| | Status LED | Cathode | **GND** | *None* | Boot/Connect Status |

> 💡 **Status LED:** wired with a 120 Ω current-limiting resistor from **D1** to **GND**. It blinks
> while the board is booting/waiting for USB enumeration and lights steady once the host PC has
> enumerated it. If no host shows up within **30 seconds** it goes dark. Unlike the other Nobs
> modules it isn't on D4 (T5 uses D4). D1 is also the chip's serial TX pin, so the LED flickers
> for a moment at power-up while the bootloader prints.

> 💡 **Momentary toggles:** the center terminal goes to **GND**. Holding the lever one way connects
> the center to one outer terminal, the other way to the other, and letting go returns it to the
> center (both buttons released). A bat-handle toggle closes the terminal *opposite* the lever, so
> if UP and DOWN come out reversed, swap that toggle's two outer wires (or its two pins in the
> firmware).

> 💡 **D0/D1 as plain GPIO:** these pins double as the hardware UART (Serial) on most Arduino
> boards, but this firmware disables "USB CDC On Boot" (see
> [`firmware/arduino_eps32_nano/build_opt.h`](../firmware/arduino_eps32_nano/build_opt.h)) and never
> calls `Serial.begin()`, so they're free for a toggle input (D0) and the LED (D1).

> 💡 **Spare pin:** **D13** is unused (it's also the board's built-in amber LED). Leave **B0** and
> **B1** unconnected: B1 selects the chip's boot mode, and pulling it to ground stops the board
> from starting.
