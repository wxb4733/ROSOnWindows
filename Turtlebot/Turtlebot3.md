# Turtlebot 3
Getting Started with the Turtlebot 3 running Windows. The ROS for Ubuntu documentation is located at the [Robotis website](http://emanual.robotis.com/docs/en/platform/turtlebot3/overview/). 
The documentation on this page will describe the differences between Ubuntu and Windows.

# Windows Requirements
## Windows Software
The Turtlebot 3 uses a Lidar which requires the following driver.
+ [CP2102 Driver](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)

# Guide
## General notes
The turtlebot documentation uses the unix command 'export' to set environment variables, instead use the following:
```
set TURTLEBOT3_MODEL=Waffle
```

Please use turtlebot3_bringup-win.launch which has Windows device bindings.

## 6. Setup
### 6.1 PC Setup
Please follow the instructions for setting up your computer with [ROS on Windows](README.md).

### 6.2 SBC Setup
You can bypass this section

### 6.3 OpenCR Setup
Please follow the Windows instructions for the [Robotis OpenCR board in the Robotis Manual](http://emanual.robotis.com/docs/en/parts/controller/opencr10/).

Before proceeding, make sure the motors turn by pressing the motor test buttons near the USB connector.

> BUG: We're working to identify a sync error coming from rosserial, which ultimately leads to a board reset.

### 6.4 Compatible devices
ROS on Windows requires a x64 bit Windows 10 Desktop or Windows 10 IoT Enterprise, and compatible hardware. 

> ROS on Windows was brought up using [Up2](http://www.up-board.org/upsquared/) and an Intel Nuc.

## 11 Simulation
As of the September release, Gazebo has not been ported to Windows yet. 


## Create a new workspace
In a Command Window set up with the ROS environment, create a directory for your robot workspaces and a workspace for turtlebot.

```
mkdir c:\ws\turtlebot3\src
cd c:\ws\turtlebot3\src
catkin_init_workspace
git clone -b melodic-devel https://github.com/ROBOTIS-GIT/turtlebot3_msgs
git clone -b melodic-devel https://github.com/ms-iot/turtlebot3_simulations
git clone -b melodic-devel https://github.com/ms-iot/turtlebot3.git 
git clone -b melodic-devel https://github.com/ms-iot/hls_lfcd_lds_driver
cd c:\ws\turtlebot3
rosdep install --from-paths src --ignore-src -r -y
```

As of the September release of ros-melodic for Windows, Gazebo has not been ported. Please disable this portion of the build. 
```
echo > turtlebot3_simulations\turtlebot3_gazebo\CATKIN_IGNORE
```


## Customize Turtlebot Launch Files
Modify the ROS Launch files to map the devices to the correct COM port. To determine which COM ports you require, right click on the Windows Start Menu, Select Device Manager.

Under the `Ports (COM & LPT)` node:
 * USB Serial Debice (COMx) is the OpenCR board. 
 * Silicon Labs CP210x USB to UART Bridge (COMy) is the Lidar

Enter the COM port in the correct fields in the launch files below:

*turtlebot3_bringup/launch/turtlebot3_core-win.launch*

```
<node pkg="rosserial_python" type="serial_node.py" name="turtlebot3_core" output="screen">
    <param name="port" value="COMx"/>
```

*turtlebot3_bringup/launch/turtlebot3_lidar-win.launch*

```
  <node pkg="hls_lfcd_lds_driver" type="hlds_laser_publisher" name="turtlebot3_lds" output="screen">
    <param name="port" value="COMy"/>
```


## Build Nodes
To build the turtlebot packages, enter the turtlebot3 workspace and build using the catkin build system. 
```
cd c:\ws\turtlebot3
catkin_make install -DCMAKE_BUILD_TYPE=RelWithDebInfo
```

Now inform ROS where to find your turtlebot code by merging the turtlebot install environment with the ROS environment. Please ensure you do this every time you open a command window. 

```
c:\ws\turtlebot3\install\setup.bat
```

> If you forget to merge the turtlebot environment by calling the setup batch file, you'll get an error such as this: 
> `RLException: [turtlebot3_robot.launch] is neither a launch file in package [turtlebot3_bringup] nor is [turtlebot3_bringup] a launch file name`

# Running Turtlebot

## No Robot - No Problem!
rViz is tool which allows you to visualize a representation of a robot, and project fake data in order to exerise or develop logic. The turtlebot simulation is
in the turtlebot3_simulations package. 

In one command window, start `roscore`.

In another command window, start the simulation environment.

```
roslaunch turtlebot3_simulations turtlebot3_fake.launch
```

You can create your own logic which reads `/odom` or publish `/cmd_vel` to move the virtual robot.

## Run Turtlebot3 with Sensors connected to your devlopment machine.
If you have Turtlebot3 hardware, you can plug the sensors directly into your development machine to iterate on fuctionality with 
your development machine. Perform the steps to set up the launch file for your development system.

In one command window, start `roscore`.

In another command window, launch the turtlebot robot code.

```
roslaunch turtlebot3_bringup turtlebot3_robot.launch
```

