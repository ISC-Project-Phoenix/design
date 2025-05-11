# Roboteq

The Roboteq HDC2450 is a motor controller than can be used to power phnx. This document will go over
some notes about how to interact with it.

## Connection
TODO: explain how we do it

NEW:
- **USB (CDC Serial)**: Appears as a virtual COM port for interactive command and configuration.  
- **CAN Bus**: Use CAN_P/CAN_N pins; typical bus speed is 1 Mb/s (or project-specific rate).  
- **Ethernet (TCP/IP)**: Connect via RJ45 for remote command, configuration, and telemetry.  

OLD:
The roboteq can be connected by either RS-232 or USB. In either case, UART is the underlying protocol.
Once this is done, an interactive terminal can be used to send commands, queries, or change settings.

USB is as simple as you would image, mounting as a serial device.

For RS232, connect to pins 2 and 3 on the big connector as RX and TX out respectively (we should
have a harness for this). Ground is pin 5. This is true RS232, so +-10V. This means you will need
to use an RS232 to TTL or USB adapter. ISC has a few, and they are cheap. Once connected, the TTL will
work the same as the USB protocol.

These run @ 115,200 1N1

## Commands

Commands are simple ASCII strings starting with ! for commands, ? for queries, ^ for updating settings,
and ~ for getting settings. \r OR _ can be used to end commands. The Roboteq responses will always be \r terminated.

Note that all commands and settings are valid for this session only.

Notably, the serial will echo all of your inputs back by defualt. This allows for an 'interactive' session.
When connecting programmatically however, this is really annoying, and can be disabled with:

`^ECHOF 1 _`

Note that this command will still echo AND give a +, but all after will not.

An easy way to read the responses of the Roboteq is to just read until a \r is found. On commands,
the controller will send a + on success and a - on failure. For queries, a large text response will come back.
The manual has great examples for this and USB allows for easy experimentation.

Some commands differ between the modes the Roboteq is in, like torque control. These are cool, but not yet
used on phnx. By default, it defaults to open loop control.

### Notable commands

- `^ECHOF`: Turns off echo
- `!G {channel} {0-1000}`: Powers a motor channel to some power. In open loop mode, this is just PWM duty cycle.
- `?V {num}`: Gets the voltage of 1 the controller or 2 the battery.
