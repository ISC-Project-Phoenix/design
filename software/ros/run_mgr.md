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

## Scoring Algorithm

Scoring is archived by pointing a camera in gazebo towards a colored strip under the ground. This colored strip
represents the desirable area for the kart to be in. The run should lose points if the camera stops seeing this color.
This camera is also used for finding the finish line to end runs.

In detail:
- Start run with score of 1000
- For each second we don't see road, decrease score by 100
- For each second, decrease score by 1 (thus serving as a timeout)
- If either score == 0 or the finish line was seen 
  - Write score to score.txt in `/run_folder`. 
  - Fire `/order_66`

### Score file

The score file should just be a file score.txt that sits in the same directory the current runs image data, and
contains the score as an int.