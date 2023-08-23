# data_logger

## Summary

This node collects a subset of data from the kart in a tabular format (as opposed to a bag). This data
consists of images from the camera, as well as a CSV labeling these images with the karts status at that time.

### Config

- `data_path` - Directory to write data into. Note that log data will be in `data_path`/"%Y-%m-%d-%H-%M-%S"/ not in
  this folder directly.

The following config values refer to fields of /odom_ack messages:
- `max_throttle_speed` - Velocity at which we consider the throttle fully pressed. This should match the config set in
  phnx_io_ros, or gz_io_ros. (TODO remove this and instead just get directly from the output of the control loop in PIR)

- `max_braking_speed` - Negative velocity at which we consider the brake fully pressed. This should match the field set
  in
  phnx_io_ros, or gz_io_ros.(TODO remove this and instead just get directly from the output of the control loop in PIR)

- `max_steering_rad` - Max steering wheel angle, in radians. Assumed to be symmetrical.

### Subscribes

- `/odom_ack` - AckermannDrive messages representing the current state of the kart. See module readme for more info.
- `/camera/mid/rgb` - Camera data, to be synced with training data

Note that this node must sync the two topics.

### Publishes

- `/run_folder` - Path to the folder we are currently writing images to. That is `data_path`/something
    - Qos: This uses reliable transport and transient local durability

### Data format

Each runs data is stored in the same directory (specified by `data_path`). When data_logger starts, it will make a new
folder named the current system time formatted to "%Y-%m-%d-%H-%M-%S". Inside this folder, data_logger will create
a single csv file containing the labels, and many images whose filename are referenced in the csv. Images will be named
as the current system time formatted as "%Y-%m-%d-%H-%M-%S-%f".jpg.

The CSV headers are

```image_file_name / steering_angle / throttle / brake / linux_time / velocity / velocity_x / velocity_y / velocity_z```

- `image_file_name`: Path to image these labels apply to. Should be in current directory.
- `steering_angle `: Steering *wheel* angle in a range of -1 to 1 (TODO change to exact angle)
- `throttle       `: Throttle pedal input from control loop, in range 0-1.0
- `brake          `: Brake pedal input from control loop, in range 0-1.0
- `linux_time     `: Unix timestamp of the record, including ns.
- `velocity       `: Speed of the vehicle in m/s.
- `velocity_x     `: Velocity of the vehicle in the x-axis.
- `velocity_y     `: Velocity of the vehicle in the y-axis.
- `velocity_z     `: Velocity of the vehicle in the z-axis.