# Roboteq

The Roboteq HDC2450 is a motor controller than can be used to power phnx. This document will go over
some notes about how to interact with it.

## Hardware Specifications

- **Continuous current**: 100 A  
- **Peak current**: 300 A (60 s)  
- **Voltage range**: up to 48 V  
- **Inputs**: enable pin
- **Communication**: USB (CDC serial), CAN bus, Ethernet (TCP/IP)  
- **Safety**: STO PLe, Cat 3, SIL 3  

## Connection

The roboteq is connected by USB.
Once this is done, an interactive terminal can be used to send commands, queries, or change settings.

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

Refer to the official RoboG4 manual for the full command set and programming examples.
