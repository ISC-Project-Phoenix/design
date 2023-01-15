# Inference

## Summary

The inference node actually runs the trained NN, sending it's output as twist commands.

### Subscribes

- `/camera/mid/rgb` - Camera input the inference will run on. Should be the same camera that was used in the training
  data.
- `/odom` - Odom source. This node will only use the velocity component

### Publishes

- `/nav_vel` - Twist message corresponding to NN output

### Configs

- `model_path` - path to the directory of the model to load. This should have a json and h5 keras file. 

## Notes

It would be nice if this node could be made flexible across CUDA and CPU, to allow for more people to run it. 