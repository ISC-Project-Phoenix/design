# Phoenix_gazebo

This package contains the launch files for running phoenix in its gazebo simulation environment. Overall, this package
should attempt to be a virtual version of [phoenix_robot](phoenix_robot.md) as close as possible.

there are three main launch files:
- inference.py.launch: Runs the production version of phoenix, using the NN inference to drive the kart
- data_collect.py.launch: Runs phoenix in data collection mode, labeling images for offline training
- common.py.launch: Launch file that launches nodes common between the above two files

## Ros Config

Red = common.py.launch

Black = inference.py.launch

Blue = common.py.launch

![](images/phnx_gazebo.svg)

Currently, github isn't on mermaid 9.3.0, so we can't embed this mermaid directly.
Here's the [source](https://mermaid.live/edit#pako:eNqVU8tqIzEQ_BUhyM3GkKMOe1lD9pJcAjkNiB6pZ6yNpDYtjU0S8u-RLK_NjuOEzEWvqpKqevpNGrIolUwZMq4djAxhubvtoiif8ZDSGgdhKASKZfDEar9xGReD814x2hnSQoZLXO8nnAG3TPYzIJjnLjbszY34A2z3wChczMgDGEzt7A5esSexXP4S46vu2dkRlVLtoQ1yuX-AN-aXkGpCexpH5HJQV0qsDARkWAVnVzz2X_LLtiPNlM5sshQa5-StEiDWEGJm8h756M2y26EOpTA67V02mwv12aVFn6mnvDLB6h36JvOXXnQmndEjbXXeu5RnNq_eU-3-kzq9twbi4nhy_p_F67llBhcLT9f10eAFrtFdelbi9-NTEjUXF2Cs5T494OGhTV0ckDGaGsDhN_rWTITdOZcrRZur_qzkn7BryeVCFokAzpYee6sKncwbDNhJVaYRpxKP72QX3wsUpkyPL9FIlXnChZy29tyVUg3gU9lF6zLxfevbQ_u-fwCc0kec)