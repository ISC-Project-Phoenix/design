# Automatic Emergency Braking ECU

## Overview
The Automatic Emergency Braking (AEB) system is designed to be a supplemental safety system
for the automatic driving. The goal of AEB is to lower the speed of the kart as much as possible
before a potential collision, to mitigate the damage to both objects. This is necessary as the E2E inference
is non-deterministic, and thus requires additional deterministic safety systems to ensure functional safety.    

## Inputs
- **Perception CAN**
  - **Range sensors** - any sensor that outputs `Range Data` frames.
  - **Encoder data** - any sensor that outputs `Encoder Count` frames.

## Outputs 
- **EStop** - direct connection to Estop circuit to allow for emergency braking.

## Algorithm:

```mermaid
graph TD
    A[init] --parallel--> wait[Wait for CAN range message]
    wait --> B{TTC under n seconds?}
    A --parallel--> vel[Wait for CAN velocity message]
        vel --> velrec[Update velocity variable]
        velrec --> vel

    B -- yes --> E[Trigger estop]
    B -- no --> wait
```