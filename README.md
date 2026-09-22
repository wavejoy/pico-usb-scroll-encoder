# RP2040 USB Scroll Encoder

A simple USB scroll wheel built with an RP2040 / Raspberry Pi Pico and a rotary encoder.

Turn the encoder to scroll up and down. Press the encoder for a middle mouse click.

The device appears to your computer as a standard USB HID mouse, so **no drivers or host-side software are required**.

## Features

- USB HID mouse emulation
- Rotary encoder scrolling
- Encoder push button acts as middle click
- Adjustable scroll amount
- Reversible scroll direction
- Automatically starts when connected
- Works without any software running on the host computer
- Built with CircuitPython

## Hardware

You will need:

- Raspberry Pi Pico or compatible RP2040 board
- Rotary encoder / KY-040 style encoder module
- USB cable
- Jumper wires or suitable breakout board

## Wiring

| Rotary Encoder | Raspberry Pi Pico |
|----------------|-------------------|
| CLK | GP0 |
| DT | GP1 |
| SW | GP2 |
| + | 3V3 |
| GND | GND |

> **Important:** Power the encoder from **3.3V**, not 5V, when connecting its outputs directly to the RP2040 GPIO.

## Software Requirements

This project uses:

- CircuitPython
- Adafruit CircuitPython HID library

### 1. Install CircuitPython

Download the CircuitPython firmware for your Raspberry Pi Pico:

https://circuitpython.org/board/raspberry_pi_pico/

Hold the **BOOTSEL** button while connecting the Pico to your computer.

A drive named `RPI-RP2` should appear.

Copy the CircuitPython `.uf2` file onto the drive. The Pico will reboot and should then appear as a drive named:

```text
CIRCUITPY
```

### 2. Install Adafruit HID

Download the CircuitPython library bundle:

https://circuitpython.org/libraries

Make sure the bundle matches the major version of CircuitPython installed on your Pico.

From the downloaded bundle, copy:

```text
adafruit_hid
```

into:

```text
CIRCUITPY/lib/
```

Your Pico should look approximately like this:

```text
CIRCUITPY/
├── code.py
└── lib/
    └── adafruit_hid/
        ├── __init__.mpy
        ├── mouse.mpy
        └── ...
```

### 3. Install the Program

Copy the included `code.py` file to the root of the `CIRCUITPY` drive.

CircuitPython automatically runs `code.py` whenever the Pico boots.

Unplug the Pico and reconnect it normally. You do **not** need to hold BOOTSEL after CircuitPython has been installed.

## Configuration

The main settings are located at the top of `code.py`:

```python
SCROLL_AMOUNT = 1
INVERT = False
CLICK_ON_PRESS = True
```

### Scroll Amount

```python
SCROLL_AMOUNT = 1
```

Controls how many mouse-wheel increments are sent for each encoder movement.

Increase this value for faster scrolling.

### Reverse Scroll Direction

```python
INVERT = False
```

Change to:

```python
INVERT = True
```

if the encoder scrolls in the opposite direction from what you want.

### Disable Middle Click

```python
CLICK_ON_PRESS = True
```

Change to:

```python
CLICK_ON_PRESS = False
```

if you don't want pressing the encoder to send a middle mouse click.

## Pin Configuration

The GPIO assignments can also be changed in `code.py`:

```python
encoder = rotaryio.IncrementalEncoder(board.GP0, board.GP1)

button = digitalio.DigitalInOut(board.GP2)
```

This makes it easy to adapt the project to different RP2040 boards or custom PCBs.

## How It Works

CircuitPython's `rotaryio.IncrementalEncoder` monitors the two quadrature outputs of the rotary encoder.

When the encoder position changes, the program sends a USB HID mouse-wheel event:

```python
mouse.move(wheel=amount)
```

The encoder's push switch is connected to GP2 using the RP2040's internal pull-up resistor. When pressed, it sends:

```python
mouse.click(Mouse.MIDDLE_BUTTON)
```

Because everything is handled by the RP2040, the connected computer simply sees a standard USB mouse.

## Compatibility

The project should work with most computers that support standard USB HID devices, including:

- macOS
- Windows
- Linux

No drivers or background applications are required.

## Troubleshooting

### The Pico appears as `RPI-RP2`

The Pico is in BOOTSEL mode. Install the CircuitPython `.uf2` firmware.

### The Pico appears as `CIRCUITPY`

That's normal. CircuitPython exposes a USB storage drive while simultaneously operating as a USB HID device.

### `No module named 'adafruit_hid'`

Make sure the `adafruit_hid` folder from the CircuitPython library bundle is located at:

```text
CIRCUITPY/lib/adafruit_hid/
```

### The encoder does nothing

Check that:

- CLK is connected to GP0
- DT is connected to GP1
- SW is connected to GP2
- GND is connected to GND
- The encoder module is powered from 3.3V
- `code.py` is located in the root of `CIRCUITPY`

Also make sure your breakout board isn't upside down. Ask me how I know.

### Scrolling is backwards

Set:

```python
INVERT = True
```

Alternatively, swap the CLK and DT connections.

## License

This project is open source. See `LICENSE` for license information.
