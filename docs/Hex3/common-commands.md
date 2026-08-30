# Common Commands

!!! note
    The following are useful commands to have handy when experimenting with or debugging your hexapod.

## Docker Containers
The following are commands used to setup or run a docker container for our environment. They would be run from inside the `non_real_time/container/` directory on your pi

* `./init_dev.sh`
  * initialize the docker container environment
* `./new_dev_shell.sh`
  *  opens a new shell in the container

## ROS2 Packages & Nodes
These commands are expected to be run inside of a development container (see above)

Hex3 drives its legs over CAN instead of a Teensy over serial. Joystick input flows through `control` into `tripod_gait`, which is turned into per-leg targets by `inverse_kinematics`, and finally dispatched to the motors by the `can` package.

* `ros2 launch hexapod_manager hexapod.launch.py`
  * launches the full walking stack in one shot: `control`, `joy`, `inverse_kinematics`, `can_interface`, and `tripod_gait`. This is the standard way to bring the hexapod up for xbox-controlled walking and requires the xbox controller to be on and connected to the pi via bluetooth before being run.
* `ros2 launch control xbox.yml`
  * launches just the joy and xbox controller config nodes used for communicating button/stick input as `/cmd_vel` and `/macros`. Useful when you only need controller input without the rest of the walking stack (e.g. when pairing with `led_controller` below). This launch also requires the xbox controller to be on and connected to the pi via bluetooth before being run.
* `ros2 run hexapod_manager led_controller`
  * launches a node responsible for controlling the led ring on the bottom of the hexapod. Can be used alongside `xbox.yml` or `hexapod.launch.py` to give color and pattern toggling capabilities. 
* `ros2 run hexapod_tester hexapod_tester`
  * launches a diagnostic node that sweeps the body pose and foot targets through a repeating pattern on `/body_pose` and `/foot_targets`, useful for exercising `inverse_kinematics` and the CAN link without a controller attached. Pair it with `ros2 launch hexapod_manager hexapod.launch.py` running in another shell.
* `ros2 topic echo <topic>` 
  * listens in to the specified topic. Useful options include `/joy`, `/cmd_vel`, `/macros`, `/body_pose`, `/foot_target`, and `/leg_commands`

## CAN

!!! note
    These commands should not be needed during normal operation of the Hexapod as there is a service to bring CAN up and down automatically, but they may be useful during debugging.

    These commands should be run in the Pi terminal, not in the docker container.

* Bring CAN down

```bash
sudo ip link set can0 down
```

* Set CAN bitrate

```bash
sudo ip link set can0 type can bitrate 500000
```

* Bring CAN up

```bash
sudo ip link set can0 up
```

* Monitor CAN

```bash
candump can0
```

* Send a dummy message

```bash
cansend can0 123#DEADBEEF
```
