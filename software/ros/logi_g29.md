# Logitech g29 wheel node

## Summary

This node is an interface to the logitech g29 driving wheel, pedals, and shifter. It doesn't output joy values, but
rather
outputs ackermannDrive and Twist messages directly, bypassing the need for something like joy_telop_twist.

### Subscribes

- `/joy` - Raw joy output from the normal joy node.

### Publishes

- `/ack_vel` - AckermannDrive outputs from the wheel

- `/cmd_vel` - Twist outputs from the wheel

### Params

- `max_throttle_speed` - Velocity at which we consider the throttle fully pressed. This should match the config set in
  phnx_io_ros, or gz_io_ros.

- `max_braking_speed` - Negative velocity at which we consider the brake fully pressed. This should match the field set
  in
  phnx_io_ros, or gz_io_ros.

- `max_steering_rad` - Max steering wheel angle, in radians. Assumed to be symmetrical.

## Notes

The raw joy interface gives wheels and pedals in percents, so it's best to first create an ackermann message using the
max throttle brake and wheel params. During this creation, we need to convert the wheel angle to virtual ackermann wheel
angle by using phoenix's steering ratio, and convert pedal percents by just multiplying percent pressed by max values.
We may as well pub this, and then also ackermann->twist it to output our actual
final twist.

If the wheel angle is greater than the max angle, we should pin it to max. Could use force feedback to help with this if
we get it figured out.

Have brake take priority over throttle if both are pressed, since only one can be encoded in the message.