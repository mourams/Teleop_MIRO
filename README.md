# Teleop_MIRO
Software Architecture: Teleoperation with MIRO using gesture and speech
Using Motion Capture for localization and gesture recognition
* Video demo and Report: https://drive.google.com/drive/folders/1Rzf7u2zSbs5_qsjepidc3gciCaW--DGE

**Objective:**
ROS-based software architecture that drives the MIRO robot to successfully perform a given task, specifically to go to a certain position with respect to a object in the workspace, using pointing gestures and issuing commands via speech which could be interpreted by the system. We use as feedback the information provided by a visual tracking system, in this case the Motion Capture by OptiTrack.

**Usage Instructions:**
- Clone the master branch of Teleop_MIRO repository inside the `src` folder of your `catkin_ws`
- The following 2 OpenCV steps are **optional**. You can either follow them, or comment out relevant code in `miro_teleop/src/command_logic.cpp`
    - (optional) Install OpenCV following these instructions: https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html
    - (optional) Set OpenCV path corresponding to your system and add dependency in `miro_teleop/CMakeLists.txt`
- Run `catkin_make` from inside `catkin_ws`
- Download MIRO MDK and MIRO app from http://labs.consequentialrobotics.com/miro/mdk/
- Turn on MIRO. Run the app and connect with MIRO, to find MIRO's IP. Ensure your machine is on the same network as MIRO.
- Edit the `~/.profile` file in root@<MIRO's IP>, set ROS_MASTER_IP with machine IP running the roscore. Source it.
- Run/Enable bridge on the app. You might need to do this at frequent intervals if MIRO stops responding.
- In the Motive software for Mocap Optitrack, enable Data Streaming and set broadcast IP to the machine running the roscore.
- Setup up markers in the Motion Capture area. Create Rigid Bodies in Motive (Robot, Obstacle, Gesture in order) after aligning required local axes with global axes.
- Launch the application using `roslaunch miro_teleop miro_teleop.launch`
- `Spatial Reasoner` will run immediately displaying the first direction of mapping. Press any key to continue after each such image is displayed. `Spatial Reasoner` will display 4 images, whereas `Pertinence Mapping` will generate an image plot each time look command is entered in the interpreter.
- User points towards required target. Then, enter look command in `Interpreter`.
- Wait till path is generated by RRT* (see display on command logic terminal). Then issue go command on `Interpreter`.
- You can also issue stop command while MIRO is moving to pause. Entering go will make it resume its originak trajectory.
- MIRO halts once goal is reached.
- Pointing to a different direction and issuing look command can be used asynchronously in between actions to reset MIRO's trajectory with a new path.
- Keep an eye on the Motive screen. If rigid bodies are not being tracked properly, you might need to create rigid bodies again with proper alignment.


**Accomplishments:**

MiRo and obstacle in the motion capture area with markers on them.

- Pointing gesture on the ground is used to calculate a goal-point where MiRo has to reach.
- Voice command - look (for now typed in the workstation) is used to orient MiRo towards the goal.
- Montecarlo simulation is used to generate an approximate point near the goal-point.
- RRT* planner is used to generate a path from MiRo to the montecarlo given goal-point.
- Voice command - go (for now typed in the workstation) is used to move MiRo towards the goal, as it avoids the obstacle.
- MiRo reaches the goal position.
- MiRo can also be stopped by a voice command - stop (for now typed in the workstation).

 
**Modules (files) in the system:**

    File 'miro_teleop/src/command_logic.cpp' - Main Command Logic node.
    File 'miro_teleop/src/interpreter.cpp' - Interpreter node.
    File 'miro_teleop/src/gesture_processing.cpp' - Gesture Processing server.
    File 'miro_teleop/src/monte_carlo.cpp' - Monte Carlo Simulation server.
    File 'miro_teleop/src/pertinence_mapping.cpp' - Pertinence Mapping server.
    File 'miro_teleop/src/spatial_reasoner.cpp' - Spatial Reasoner server.
    File 'miro_teleop/src/robot_controller.cpp' - Robot Controller node.
    File 'miro_teleop/srv/GestureProcessing.srv' - Gestutre Processing service
    File 'miro_teleop/srv/MonteCarlo.srv' - Monte Carlo Simulation service
    File 'miro_teleop/srv/PertinenceMapping.srv' - Pertinence Mapping service
    File 'miro_teleop/srv/SpatialReasoner.srv' - Spatial Reasoner service
    File 'miro_teleop/srv/rrtStarSRV.srv' - RRT* service
    File 'miro_teleop/msg/Path.msg' - msg used to transfer trajectory from Command Logic to Robot Controller
    File 'rrtStar/src/rrts_main.cpp' - RRT* server
    File 'mocap_optitrack-master/launch/mocap.launch' - Launches mocap node
    File 'miro_teleop/launch/miro_teleop.launch' - Launches the whole application including mocap nodes
    File 'run_miro_teleop' - Shell script to run all nodes in separate terminals
    File 'doc/html/index.html' - Doxygen generated documentation

 
**Limitations and proposed extensions of the system:**
 - Dependence on Motion Capture data: Code in robot_controller.cpp to be modified for implementing other sensors like odometry.
 - Workspace is 4mx4m square with origin at center: HSIZE and VSIZE in all cpp files to be modified for changing dimensions. Modify RES in all files for required discretization resolution.
 - One static obstacle in workspace: Modify spatial_reasoner.cpp to include more.
 - Basic string matching to interpret commands: Call grammar parsing ROSJAVA service from interpreter.cpp.
