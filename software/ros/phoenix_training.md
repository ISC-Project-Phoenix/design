# Phoenix_training

This is the package used for training the NN automatically. It assumes you have a prior trained weights file from IRL or
gazebo collected data,
and will then begin the gazebo data collection loop automatically.

The entry point to this package is the [hypervisor node](hypervisor.md).

## ROS structure

This is the logical node structure of the training loop.

```mermaid
stateDiagram-v2

    run_mgr --> hypervisor: /order_66
    hypervisor --> runtime: spawns

    state runtime {
        gz_bridge --> inference: /odom
        gz_bridge --> gz_io_ros: /odom
        gz_bridge --> run_mgr: /camera/mid/rgb
        gz_bridge --> inference: /camera/mid/rgb
        gz_bridge --> data_logger: /camera/mid/rgb

        gz_io_ros --> data_logger: /training_data
        data_logger --> disk: CSVs and images
        data_logger --> run_mgr: /run_folder

        inference --> gz_bridge: /robot/cmd_vel

        run_mgr --> disk: Score file
    }
```

## Scoring Algorithm

Scoring is archived by pointing a camera in gazebo towards a colored strip under the ground. This colored strip
represents the desirable area for the kart to be in. The run should lose points if the camera stops seeing this color.
This camera is also used for finding the finish line to end runs.

## Algorithm (implemented by hypervisor)

The overall training algorithm:

- Load prior weights
    - Until model is deemed fit:
        - for 1 to n:
            - have hypervisor spawn `Ros2 launch phoenix_training training.py.launch` process, loading a random gazebo map
            - gazebo will collect data and write score to a file
            - kill `Ros2 launch phoenix_training training.py.launch` process when run is done (`/order_66`)
        - hypervisor trims bad runs based on score
        - hypervisor uses Keras to train NN using trimmed data
        - load new weights

Visualised:

```mermaid
graph BT
    init((Input path to pre trained model)) --> loop{Score converged at maxima?}
    loop -- yes --> Exit(((exit)))

    loop -- no --> load[Load current model from path]
    load --> runloop

    subgraph runs
    runloop{Have we run n times?} -- no --> loadmap[Spawn training enviornment with random gazebo map]
    loadmap --> collect[Training enviornment collects data and score]
    collect -- run finishes --> killn[Kill training enviornment]
    killn --> runloop 
    end

    runloop -- yes --> trim[Trim/delete lower scored runs]
    trim --> train[Train new model with keras using collected data]
    train --> env[Overwrite path to model with the path to this new model]
    env --> loop
```

## Training data format

TODO detail data format
