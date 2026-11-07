# ROS 2 IIWA Visual Servoing

**Contributors:** Marco Bartone, Giacomo Caiazzo, Matteo De Simone and Nicola Monetti.

### Overview
A ROS 2 workspace for vision-based control of an iiwa manipulator using a camera mounted on its end-effector. It combines sphere and ArUco marker detection with velocity and inverse-dynamics torque control in Gazebo.

### Usage

Open the terminal, open the container and enter into the directory where you want to download the folder, then download it with:

      $ git clone https://github.com/giacomocaiazzo/Giacomo_Caiazzo_Homework_3
            
--------------------------------

It's possible to load two different worlds in Gazebo: new.world, which spawns the manipulator and a blue sphere and empty.world, which spawns the manipulator and an arucotag. To use one of them, open iiwa.launch.py (/ros2_iiwa/iiwa_bringup/launch folder) and change the last path string parameter ("new.world" for the sphere, "empty.world" for the arucotag) of the PathJoinSubstitution in line 328 as showed:

	iiwa_simulation_world = PathJoinSubstitution(
            [FindPackageShare(description_package),
                'gazebo/worlds', 'empty.world'] #empty.world contains arucotag, new.world contains sphere 
    	)


Furthermore, to detect the aruco marker or the sphere (because they are in different positions), open initial_position.yaml (/ros2_iiwa/iiwa_description/config folder) and comment/uncomment the joints initial positions of the aruco marker section or the sphere section.

--------------------------------

To build the packages, enter into the ROS2 workspace and build them with:

      $ colcon build

Atfer this, use the source command:

      $ source install/setup.bash
      

To load the world with the blue sphere and the manipulator with the camera for the detection, run the simulations with:

      $ ros2 launch iiwa_bringup iiwa.launch.py use_vision:=true

After this, open another terminal and use the following command to detect the sphere:
      
      $ ros2 run ros2_opencv ros2_opencv_node



In order to load the velocity controller, launch the simulation with the following command (this section only refers to the empty.world case):

      $ ros2 launch iiwa_bringup iiwa.launch.py command_interface:="velocity" robot_controller:="velocity_controller" use_vision:=true
      
To load the torque controller, use the previous commands on the terminal with command_interface:="effort" and robot_controller:="effort_controller".

After this, open another terminal and use the command:

      $ ros2 launch aruco_ros simple_single.launch.py
      
Open another terminal and use the following command specifying as node arguments task:= pos | lap | merge and cmd_interface:= velocity | effort (be sure not to launch velocity with the merge task):
      
      $ ros2 run ros2_kdl_package ros2_kdl_vision_control --ros-args -p cmd_interface:="<cmd_interface>" -p task:="<task>"
      
