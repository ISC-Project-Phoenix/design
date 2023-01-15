# data_logger

## Summary

This node collects training data for all modes of training (on the kart, in gazebo, or in a training loop). This data
consists of images from the camera, as well as a CSV labeling these images with the karts status at that time.

### Custom Messages

- `TrainingData` - Custom msg type matching the CSV columns except for image. Timestamped. The following is its fields.
    - `image_file_name`: Path to image these labels apply to. Should be in current directory.
    - `steering_angle `: Steering *wheel* angle in a range of -1 to 1
    - `throttle       `: Throttle pedal input, in range 0-1.0
    - `brake          `: Brake pedal input, in range 0-1.0
    - `linux_time     `: Unix timestamp of the record, including ns.
    - `velocity       `: Speed of the vehicle in m/s.
    - `velocity_x     `: Velocity of the vehicle in the x-axis.
    - `velocity_y     `: Velocity of the vehicle in the y-axis.
    - `velocity_z     `: Velocity of the vehicle in the z-axis.
    - `position_x     `: TODO this was used in OSCAR, but we can't get this from IRL data, so just don't use for now
    - `position_y     `: TODO this was used in OSCAR, but we can't get this from IRL data, so just don't use for now
    - `position_z     `: TODO this was used in OSCAR, but we can't get this from IRL data, so just don't use for now

### Config

- `data_path` - Directory to write data into. Note that log data will be in `data_path`/"%Y-%m-%d-%H-%M-%S-%f"/ not in
  this folder directly.

### Subscribes

- `/training_data` - TrainingData messages representing the current state of the kart
- `/camera/mid/rgb` - Camera data, to be synced with training data

Note that this node must sync the two topics.

### Publishes

- `/run_folder` - Path to the folder we are currently writing images to. That is `data_path`/something

### Data format

Each runs data is stored in the same directory (specified by `data_path`). When data_logger starts, it will make a new
folder named the current system time formatted to "%Y-%m-%d-%H-%M-%S". Inside this folder, data_logger will create
a single csv file containing the labels, and many images whose filename are referenced in the csv. Images will be named
as the current system time formatted as "%Y-%m-%d-%H-%M-%S-%f".jpg.

The CSV headers are

```image_file_name / steering_angle / throttle / brake / linux_time / velocity / velocity_x / velocity_y / velocity_z / position_x / position_y / position_z```

matching the format documented for `TrainingData`.