# Artpark Robotics Challenge

ARTPARK’s Robotics Challenge 2021-22, required a robot to demonstrate janitorial tasks that would be typically
performed in a washroom. The tasks were limited to clearing any rubbish that may be on the floor followed by cleaning
the washbasin and the washbasin counter using a sanitizing liquid finally, the robot would also mop the floor of the
restroom. The robot was given a brief opportunity to scout out the restroom area so that it can assess the environment
and create a representation of it for planning and navigation purposes

- Worked as a part of team Giga Robotics under the guidance of Juan Miguel Jimeno (Creator of LinoRobot and
Champ), Prateek Nagras (CEO, Acceleration Robotics), Dr.K V Gangadharan (Professor, NIT Karnataka). A
budget of 5000$ was provided to realise the robot from simulation to hardware
- Designed and built a janitorial robot with a mobile base and a robotic arm with Autonomous Navigation
Capabilities using ROS1. The robot used a Lidar and a depth camera for obstacle avoidance and navigation. An
object detection pipeline using Yolov4 was implemented in order to identify trash on the floor
- Prior to the hardware round a dockerized submission of the gazebo simulation demonstrating the solution was
implemented

## Related Links
- Link to competion Video : [click here](https://www.youtube.com/watch?v=HfuiV0cgzNs)
- Link to robot's ros packages : [click here](https://github.com/NickNair/GigaRoboticsArtpark) 


## 1. The Robot
The robot has a footprint of 45x45 cm. The robot has 4 mecanum wheels, which enable holonomic motion. The robot has an RPLidar A1 sensor laser sensor which is present in the base of the robot. The lidar is mainly used for autonomous navigation. On the base, the robot has a 6 DoF robotic arm which is used to pick and place trash, spray and wipe. The gripper of the arm has an Intel D435 camera mounted on it. This is used for pose estimation of various items in the washroom and for 3D obstacle avoidance.

![Alt Text](https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/apbot.png)

## 2. Trash picking
The camera on the arm is used to detect the trash as soon as it enters the washroom. Then, the robot moves to the detected trash items. Once it gets to a trash item, it detects it again to increase precision and accuracy. It then gets posoition of the trash item (the camera is a depth camera).

![Alt Text](https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/trash_picking.gif)


## 3. Cleaning markings
The robot is equipped with a mop below its base, which can be lifted and lowered, and is used to clean the markings on the floor.

![Alt Text](https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/marking_cleaning.gif)


## 4. Counter Spray
The gripper of the robot is equipped with a nozzle, which is used to spray sanitization liquid on the counter.

![Alt Text](https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/spray_sim.gif)


## 5. Counter Wipe
The arm grips the sponge cleaner and wipes the sanitization liquid on the counter.

![Alt Text](https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/wipe.gif)


## 6. Object detection
<p align="center">
 <img src="https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/yolo.png" width="125"/>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
 <img src="https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/roboflow.png" width="125"/>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
 <img src="https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/opencv.png" width="125"/> 
</p>

For the perception capabilities required for some of the above tasks, custom datasets trained on the [YOLOv4](https://arxiv.org/abs/2004.10934) Object Detection architecture, which is a state of the art object detection model, was used along with an infernence pipeline designed using OpenCV. The datasets used for training were created from images taken by us and a few images from the simulation environment. The images were annotated and converted to the training format (which was YOLO Darknet in this case) using an online tool called [Roboflow](https://docs.roboflow.com/). They provide an interface to upload images and create datasets suited to a particular training format. The two datasets used in a our projects are the trash-marking dataset and the dustbin dataset.

<p align="center">
 <img src="https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/trash.png" width="500"/>
 <img src="https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/detection.jpeg" width="500"/>  
</p>


## 7. Docker and World Bringup (The following will build docker and launch the world in gazebo)

```
git clone https://github.com/jaimandal10/artpark_robotics_challenge.git
cd artpark_robotics_challenge
. run.sh
```

## 8. Load trash, dustbins & markings (move them around)

* Open a new terminal and run
```
docker exec -it  artpark_workspace_sim_container bash
```
* Source the workspace
```
source artpark_workspace/devel/setup.bash
```
* Make scripts executable
```
chmod +x artpark_workspace/src/GigaRoboticsArtpark/apbot_nav/scripts/*
chmod +x artpark_workspace/src/GigaRoboticsArtpark/apbot_description/scripts/*
```
* Spawn trash
```
rosrun apbot_description trash_spawner.py
```

* Spawn dustbins
```
rosrun apbot_description dustbin_spwaner.py
```

* Spawn markings
```
rosrun apbot_description marking_spawner.py
```

## 9. Launch the robot
* Open a new terminal and run
```
docker exec -it  artpark_workspace_sim_container bash
```
* Source the workspace
```
source artpark_workspace/devel/setup.bash
```
* Spawn robot
### Please do not move the robot after launching(This affects the map building as it starts as soon as the robot is launched). Please specify the desired launch coordinates in the terminal while launching. You can use the image as reference to estimate the coordinates of your desired location.

```
roslaunch apbot_description robot.launch x:="2.5" y:="-0.8" yaw:="-3.14"
```

<img src="https://github.com/jaimandal10/artpark_robotics_challenge/blob/main/media/robot_poses.png" width="500"/>


## 10. Start the run
* Open a new terminal and run
```
docker exec -it  artpark_workspace_sim_container bash
```
* Source the workspace
```
source artpark_workspace/devel/setup.bash
```
* Start run (all tasks)
```
rosrun apbot_nav main_sequence.py
```

### Performing individual tasks (These runs are to be run separately from the above main sequence)

* To perform only markings cleaning and trash pick and place
```
rosrun apbot_nav trash_markings_sequence.py
```
* To perform only spraying and wiping sequence
```
rosrun apbot_nav spray_sequence.py
```
