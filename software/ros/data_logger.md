# data_logger

## Summary

This node collects training data for all modes of training (on the kart, in gazebo, or in a training loop). This data
consists of images from the camera, as well as a CSV labeling these images with the karts status at that time.

### Custom Messages

- `TrainingData` - Custom msg type matching the CSV columns except for image. Timestamped. (TODO define this off
  jearocks readme)

### Subscribes

- `/training_data` - TrainingData messages representing the current state of the kart
- `/camera/mid/rgb` - Camera data, to be synced with training data

Note that this node must sync the two topics

### Publishes

- `/run_folder` - Path to the folder we are currently writing images to

### Data format

TODO detail data format to match jearocks 