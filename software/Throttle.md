# Throttle By Wire ECU

## Overview

The throttle ECU has a simple job - translate throttle commands to ESC
commands.

## Inputs
- **Priority CAN** - For `Set Speed` messages.


## Outputs
- **ESC Connection** - A physical connection to the ESC

## Algorithm

The ESC takes 0-5V input. Because of this we can either use a controller with a 
DAC, or hack it together with PWM. Either way, because we have a linear throttle curve,
we can simply output a voltage that is (5 * (perc/100))V, where perc is the percent integer 
contained in the CAN message.