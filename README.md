Genvex Optima 270 for ESPHome
=============================

This repository contains an [ESPHome](https://esphome.io/) package for
controlling a **Genvex central ventilation unit** in [Home
Assistant](https://www.home-assistant.io). It works for my ECO 400 XL unit, and
I speculate that it works for all units based on Optima 270, although I'll be
curious whether temperature sensor values will be calculated correctly. Open an
issue so we can figure it out together.

Hardware
--------

Use a board that's compatible with ESPHome and has an RS-485 driver with
connections for A, B and GND. I think the proper cabling is to connect A and B
via a twisted pair and to connect GND via either another pair and/or or the
cable shielding.

Connect to the CTS pins on the Genvex unit according to the circuit diagrams in
its installation manual. The pins may be hidden under a sticker that you can
peel off. Terminate the RS-485 bus with a 120 ohm resistor across the A and B
terminals at both ends.

I found a solder-free solution with the [LILYGO
T-CAN485](https://www.lilygo.cc/products/t-can485) board plus two 120 ohm
resistors and some twisted pair cable (Ethernet will do) bought at my local
electronics store. The T-CAN485 board comes with an extra screw terminal adapter
that's attached to the CAN bus, which you can pull out and put into your Genvex
unit instead (at least it fits in my ECO 400 XL). The board has Wi-Fi and works
with ESPHome, requiring just a USB-C power supply (or 5-12V DC if you prefer).

Software
--------

This repository is intended to be used via [the Remote Packages mechanism in
ESPHome](https://next.esphome.io/guides/configuration-types.html#remote-git-packages)
like so:

```yaml
packages:
  optima270:
    url: https://github.com/jbj/genvex-optima-270
    file: genvex-optima-270.yaml
    refresh: 0d # Always use the latest version

substitutions:
  genvex_uart_id: my_uart_id
  # override other variables if needed:
  # genvex_baud_rate, genvex_address, genvex_parity

uart:
  id: my_uart_id
  # configure pins and other UART settings for your board
```

If you use the LILYGO T-CAN485 as described above, you can use [the ESPHome
package I wrote for it](https://github.com/jbj/t-can485-esphome), linking the
two packages together by choosing the same UART id:

```yaml
packages:
  lilygo:
    url: https://github.com/jbj/t-can485-esphome
    file: rs485.yaml
    refresh: 0d
  optima270:
    url: https://github.com/jbj/genvex-optima-270
    file: genvex-optima-270.yaml
    refresh: 0d # Always use the latest version

substitutions:
  # For the `optima270` package
  genvex_uart_id: modbus_uart
  # For the `lilygo` package
  rs485_uart_id: modbus_uart
```

That should be all the configuration you need, plus the parts that Home
Assistant generates for you when you create a new device.
