![](/launch/pic/demo.gif)

使用Velodyne激光雷达时，下载https://github.com/TixiaoShan/jackal_velodyne.git
并按如下执行。

# Simulate Jackal and Velodyne in Gazebo


## Dependency

```
sudo apt-get install ros-kinetic-jackal-*
sudo apt-get install ros-kinetic-velodyne-*
```

## Compile

You can use the following commands to download and compile the package.

```
cd ~/catkin_ws/src
git clone https://github.com/TixiaoShan/jackal_velodyne.git
cd ..
catkin_make
```

## 需要修改的地方
1. jackal_description包 urdf文件夹 jackal.gazebo 文件中
<gazebo>
    <plugin name="gazebo_ros_control" filename="libgazebo_ros_control.so">
      <robotNamespace>/</robotNamespace>
    </plugin>
 </gazebo>
改为
  <gazebo>
    <plugin name="gazebo_ros_control" filename="libgazebo_ros_control.so">
      <robotNamespace>/</robotNamespace>
      <legacyModeNS>true</legacyModeNS>
    </plugin>
  </gazebo>
即添加  <legacyModeNS>true</legacyModeNS>

2. Jackal_control 包 control.launch 中给 spawner 添加失败后重启，即 respawn="true"。

<node name="controller_spawner" pkg="controller_manager" type="spawner" respawn="true"
        args="jackal_joint_publisher jackal_velocity_controller" />

否则报一次  Controller Spawner couldn't find the expected controller_manager ROS interface. 之后便无法启动控制器，小车不能动。


## Run

```
roslaunch jackal_velodyne run.launch
```
rosrun joy joy_node

rosrun teleop_twist_joy teleop_node



## Sensor Configuration

Change sensor settings at the bottom of `jackal_velodyne.urdf.xacro`

```
parent="base_link"
name="velodyne"
topic="/velodyne_points"
hz="10"
samples="1800"
gpu="false"
```

## New Worlds

1. Put new world file to `worlds` folder
2. Change `run.launch` file to use the new world

## Upgrade Gazebo

It's recommended that upgrade your Gazebo to the latest version, which has great performance improvements.

```
sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
sudo apt update
sudo apt upgrade
```

## Teleop Jackal

1. Install teleop package: `sudo apt-get install ros-kinetic-teleop-twist-keyboard`
2. Run: `rosrun teleop_twist_keyboard teleop_twist_keyboard.py`

## Gmapping

1. Install: `sudo apt-get install ros-kinetic-slam-gmapping`
2. Run: `roslaunch jackal_velodyne gmapping_demo.launch`
3. Set the `2D Nav Goal` in Rviz
