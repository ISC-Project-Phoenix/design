# Hypervisor

## Summary

The hypervisor manages the simulation training loop automatically, such that training can be done overnight.

It is a ROS node, but will be launched by itself. When launched, it will spawn `Ros2 launch phoenix_training training.py.launch`
as a child process. This way, it can kill the ros nodes in between runs.

The hypervisor is also responsible for trimming data collected from all the runs according to score, then using that to actually train the 
NN using Keras. With this new weights file, it can feed that into the inference node via `Ros2 launch phoenix_training training.py.launch`,
and the loop continues.

See [here for more details](phoenix_training.md).

### Subscribes

- `/order_66` - Kills the child process `Ros2 launch phoenix_training training.py.launch` when received. This should be sent at the end
of each run.

### Genetic Algorithm

This node will need to cache run score tuples to speed up trimming.

TODO detail criteria for how we trim old runs based off score

### Keras Training

TODO detail the NN training itself. This will likely be lifted from jearocks work