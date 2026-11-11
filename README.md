# ROS 2 IIWA Visual Servoing

**Contributors:** Marco Bartone, Giacomo Caiazzo, Matteo De Simone and Nicola Monetti.

### Overview
A ROS 2 workspace for vision-based control of an iiwa manipulator using a camera mounted on its end-effector. It combines sphere and ArUco marker detection with velocity and inverse-dynamics torque control in Gazebo.

### Usage

Open a terminal in the container and clone the repository from the directory where you want to keep the workspace.

```bash
git clone https://github.com/giacomocaiazzo/ros2-iiwa-visual-servoing.git
cd ros2-iiwa-visual-servoing
```

#### Select the Gazebo world and initial joint positions

In [iiwa.launch.py](ros2_iiwa/iiwa_bringup/launch/iiwa.launch.py), set the last element of the path in `iiwa_simulation_world` to the desired world.

| World | Target |
| --- | --- |
| `new.world` | Blue sphere |
| `empty.world` (default) | ArUco marker |

In [initial_positions.yaml](ros2_iiwa/iiwa_description/config/initial_positions.yaml), uncomment the entire `initial_positions` block for the selected target and comment out the other block.

#### Build and source the workspace

Run these commands from the repository root after selecting the world and initial positions. Rebuild after changing either file.

```bash
colcon build
source install/setup.bash
```

For each additional terminal, enter the same container, go to the repository root, and run `source install/setup.bash` before running a ROS 2 command.

#### Blue sphere detection

With `new.world` selected, launch the simulation with the end-effector camera enabled.

```bash
ros2 launch iiwa_bringup iiwa.launch.py use_vision:=true
```

In a second terminal, start sphere detection.

```bash
ros2 run ros2_opencv ros2_opencv_node
```

#### ArUco marker detection and visual servoing

With `empty.world` selected, launch the simulation using one of the following commands.

**Velocity control**

```bash
ros2 launch iiwa_bringup iiwa.launch.py command_interface:=velocity robot_controller:=velocity_controller use_vision:=true
```

**Torque control**

```bash
ros2 launch iiwa_bringup iiwa.launch.py command_interface:=effort robot_controller:=effort_controller use_vision:=true
```

In a second terminal, start ArUco marker detection.

```bash
ros2 launch aruco_ros simple_single.launch.py
```

In a third terminal, start the visual servoing node. Replace `<cmd_interface>` with `velocity` or `effort` to match the simulation, and replace `<task>` with `pos`, `lap`, or `merge`. Use `effort` for `merge`.

```bash
ros2 run ros2_kdl_package ros2_kdl_vision_control --ros-args -p cmd_interface:="<cmd_interface>" -p task:="<task>"
```
