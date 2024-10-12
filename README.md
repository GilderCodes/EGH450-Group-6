# EGH450 Group 6 System Setup

This guide provides instructions for setting up the full system for the EGH450 project.

**Note:** For more detailed information, please refer to the [QUTAS UAV Setup Guides (2024)](https://github.com/qutas/info/wiki/UAV-Setup-Guides-(2024)).

## Prerequisites

- Ubuntu Mate 20.04 (for Raspberry Pi 4)
- ROS Noetic

## Installation Steps

### 1. Raspberry Pi 4 Setup

1. Download Ubuntu Mate 20.04 image for ARM64.
2. Install on Raspberry Pi 4 SD card.
3. Set up automatic login.
4. Update system software.

For detailed Raspberry Pi setup, visit: [Ubuntu Mate for Raspberry Pi](https://ubuntu-mate.org/raspberry-pi/)

### 2. ROS Noetic Installation

1. Update Ubuntu repositories:
   ```
   sudo apt-get update
   ```
2. Install ROS Noetic (Desktop-Full):
   ```
   sudo apt install ros-noetic-desktop-full
   ```
3. Set up bash environment and install dependencies.

For complete ROS installation instructions, visit: [ROS Noetic Installation Guide](http://wiki.ros.org/noetic/Installation/Ubuntu)

### 3. Workspace Setup

1. Create and initialize a catkin workspace:
   ```
   mkdir -p ~/catkin_ws/src
   cd ~/catkin_ws
   catkin_make
   ```

For more information on catkin workspaces, see: [Creating a Workspace for catkin](http://wiki.ros.org/catkin/Tutorials/create_a_workspace)

### 4. Install Project Dependencies

1. Clone required repositories:
   ```
   cd ~/catkin_ws/src
   git clone https://github.com/GilderCodes/qutas_lab_450.git
   git clone https://github.com/GilderCodes/egh450_payload.git
   git clone https://github.com/GilderCodes/operator_interfaces.git
   git clone https://github.com/GilderCodes/breadcrumb.git
   git clone https://github.com/GilderCodes/spar.git
   git clone https://github.com/GilderCodes/depthai_publisher.git
   ```

2. Install DepthAI ROS package:
   ```
   sudo wget -qO- https://raw.githubusercontent.com/luxonis/depthai-ros/main/install_dependencies.sh | sudo bash
   sudo apt install libopencv-dev python-rosdep
   sudo rosdep init
   rosdep update
   cd ~/catkin_ws/src
   git clone --branch noetic https://github.com/luxonis/depthai-ros.git
   cd ..
   rosdep install --from-paths src --ignore-src -r -y
   ```

3. Build the workspace:
   ```
   catkin_make -l1 -j1
   ```

For more on DepthAI ROS, visit: [DepthAI ROS GitHub](https://github.com/luxonis/depthai-ros)

### 5. Configuration

1. Set up distributed ROS between onboard and GCS computers.
2. Configure PX4 firmware on the flight controller.
3. Calibrate sensors, radio, and ESCs using QGroundControl.

For PX4 configuration, see: [PX4 User Guide](https://docs.px4.io/master/en/)

### 6. Testing

1. Test camera functionality:
   ```
   roslaunch depthai_examples rgb_publisher.launch
   rosrun rqt_image_view rqt_image_view
   ```

2. Test distributed ROS setup.

3. Run the uavasr_emulator for GCS testing:
   ```
   roslaunch uavasr_emulator emulator.launch
   ```

For more on uavasr_emulator, visit: [UAVASR Emulator GitHub](https://github.com/qutas/uavasr_emulator)

### 7. Final Steps

1. Design preliminary GCS display using rviz and rqt.
2. Perform thrust testing and motor calibration.
3. Adjust PX4 parameters for optimal performance.

For detailed instructions on specific components, refer to the respective repository documentation.

## Additional Resources

- [ROS Wiki](http://wiki.ros.org/)
- [PX4 Documentation](https://docs.px4.io/)
- [QGroundControl User Guide](https://docs.qgroundcontrol.com/master/en/)

Remember to check the [QUTAS UAV Setup Guides (2024)](https://github.com/qutas/info/wiki/UAV-Setup-Guides-(2024)) for the most up-to-date and detailed instructions specific to this project.
