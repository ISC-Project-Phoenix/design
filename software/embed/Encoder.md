# Encoder ECU

## Overview

The Encoder ECU reads quadrature encoder signals from a Taiss incremental rotary encoder and publishes wheel velocity data over the CAN bus. This data is consumed by the `phnx_io_ros` node and republished on the `/odom_can` topic for the speed-control loop citeturn2file0.

## Hardware Specifications

* **Encoder**: Taiss incremental optical AB quadrature encoder, 600 pulses per revolution (P/R), 5–24 V supply, 6 mm shaft.
* **Wiring**:

  * Channel A → PA0 (EXTI0) configured for rising-edge interrupt.
  * Channel B → PA1 (digital input) to determine rotation direction.

## Configuration Parameters

* `ENCODER_TEETH = 600` P/R
* `WHEEL_CIRC_METER` = wheel circumference in meters (project-specific)
* `ENCODER_SAMPLE_PERIOD_US = 10 * 5000 = 50000` µs (50 ms between samples)

## Software Architecture

### Quadrature Capture (ISR)

1. **Interrupt**: EXTI0 ISR (`encoderISR()`) triggers on each rising edge of Channel A (PA0).
2. **Direction**: Read Channel B (`HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_1)`).

   * If **High**, increment global `ticks` counter.
   * If **Low**, decrement `ticks` counter.

### Periodic Sampling (Timer)

A hardware timer fires every `ENCODER_SAMPLE_PERIOD_US` µs, invoking `timer_irq()`:

1. Compute `delta_ticks = ticks - last_ticks` (signed 32‑bit).
2. Store `mag_delta = abs(delta_ticks)` in a `uint16_t`.
3. Calculate linear speed (m/s):

   ```c
   float speed_m_s = (delta_ticks / (float)ENCODER_TEETH)
                     * WHEEL_CIRC_METER
                     / (ENCODER_SAMPLE_PERIOD_US / 1e6f);
   ```
4. Construct CAN frame:

   * **ID**: `CanID::Encoder` (numeric ID defined in `candefs.hpp`).
   * **DLC**: 6 bytes.
   * **Data\[0-1]**: `mag_delta` (high byte, low byte).
   * **Data\[2-5]**: raw IEEE‑754 `speed_m_s` (4 bytes, little-endian).
5. Store frame in `pending_can` for transmission.

## CAN Message Specification

| Byte | Field            | Type          | Description                                         |
| ---- | ---------------- | ------------- | --------------------------------------------------- |
| 0-1  | Δticks magnitude | `uint16_t` BE | Absolute change in encoder ticks since last sample. |
| 2-5  | Speed (m/s)      | `float32` LE  | Computed wheel linear speed (raw IEEE‑754 float).   |

> **Note**: The arbitration ID `CanID::Encoder` is defined in `candefs.hpp` and should be matched on the CAN subscriber side. citeturn6file1

## Integration with phnx\_io\_ros

The `phnx_io_ros` node listens for the Encoder CAN frame and republishes the data on the `/odom_can` ROS topic, closing the feedback loop for wheel-speed control citeturn2file0.

## Usage Notes

* Verify external pull-ups or filtering on PA0/PA1 if required by the encoder hardware.
* Confirm timer prescaler and base clock settings yield the desired 50 ms sample period.
* Monitor the CAN bus for periodic Encoder frames to validate operation.
