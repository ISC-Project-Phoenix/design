# Throttle By Wire ECU

## Overview

The throttle ECU has a simple job - translate throttle commands to ESC
commands. This can be received via CAN or the pedal. The idea is that the pedal will act as an auton cancel switch (much like cruse control in a car)
that can be used to cancel auton and take control if someone is actually sitting in the kart. Because this auton state is stored in the interface board,
this board will always write a `Set Speed` message to the ESC.

## Inputs
- **Priority CAN** - For `Set Speed` messages.
- **Physical 0-5k Pedal** - Physical pedal input.

## Outputs
- **ESC Connection** - A physical connection to the ESC
- **Priority CAN** - `Auton Disable` messages

## Functional Requirements
- REQ1: On `Set Speed` message received, set the DAC to the appropriate proportion.
- REQ2: If a signal from the pedal is detected, then set the DAC proportional to that input, and send a `Auton Disable`
message. Until another CAN message is received, do not send more `Auton Disable` messages. 
- REQ3: If `Training Mode` is received, then from now on send `Set Speed` messages proportional to the pedal input when depressed.