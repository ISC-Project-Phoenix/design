# run_mgr

## Summary

This node is used in phoenix_training to manage individual runs in sim.

This node will both manage the collection of the genetic algorithms score, and signal the end of a run to the
hypervisor.

### Subscribes

- `/run_folder` - sent from `data_logger` so this node knows where to write the score file to.
- `/camera/score/rgb` - Images from the camera pointed at the scoring strip under the ground.

### Publishes

- `/order_66` - Publishes a message when a run is done. Kills all nodes but hypervisor.

### Score file

The score file should just be a file score.txt that sits in the same directory the current runs image data.