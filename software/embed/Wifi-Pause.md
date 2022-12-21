# WIFI/Pause ECU

## Overview

This ECU serves as an interface to the Pause circuitry. This works in two
directions. If the WIFI receives a red flag, Pause should be set. If electronics
or WIFI sets the Pause, this ECU will send a slowly increasing series of brake messages
in order to slow the kart. Once Pause is unset, the breaks should be released. It should be
noted that the pause circuitry will cut power to the motor itself. 

In order to prevent a race on slowing the breaks, this ECU will first send a
`Lock Brake` message on the Priority Can to prevent the Interface ECU from sending
brake messages that would otherwise override the slowly increasing brake messages.
When priority is unset, `Unlock Brake` will be sent to allow for free control again.

## Inputs

- **WIFI Antenna** - Connects to EvGrandPrix's flag network. TODO details
- **Pause Circuitry** - A physical connection to the Pause circuit. TODO details

## Outputs

- **Priority CAN**
  - **Brake Messages**
  - **Brake Lock/Unlock Messages**
- **Pause Circuitry**