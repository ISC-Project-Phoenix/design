# Inference

## Summary

The inference node actually runs the trained NN, sending it's output as twist commands.

### Subscribes

- `/camera/mid/rgb` - Camera input the inference will run on. Should be the same camera that was used in the training
  data.
- `/odom_ack` - AckermannDrive messages to feed encoder values to inference. See module readme for more info.

### Publishes

- `/nav_vel` - Twist message corresponding to NN output
- `/ack_vel` - AckermannDrive message corresponding to NN output

### Configs

- `model_path` - path to the directory of the model to load. This should have a json and h5 keras file.

- `max_throttle_speed` - Velocity at which we consider the throttle fully pressed. This should match the config set in
  phnx_io_ros, or gz_io_ros.

- `max_braking_speed` - Negative velocity at which we consider the brake fully pressed. This should match the field set
  in
  phnx_io_ros, or gz_io_ros.

- `max_steering_rad` - Max steering wheel angle, in radians. Assumed to be symmetrical.

## Notes

It would be nice if this node could be made flexible across CUDA and CPU, to allow for more people to run it.

The model currently outputs things as percents, so we will need to multiply them by our max values to get values for
a twist output. It's best to do this by first creating an ackermann message by multiplying the max pedal speeds by their
percent,
(giving brake priority over throttle), getting the wheel angle by multiplying its percent by max angle, then applying
the
steering ratio to the wheel angle to get the final virtual ackermann angle. This message can then be ackermann ->
Twisted.