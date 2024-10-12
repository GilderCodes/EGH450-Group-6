# EGH450 Group 6 System Setup

This guide provides detailed instructions for setting up the full system for the EGH450 project. It covers the setup of the UAV, on-board computer, and ground control station (GCS).

**Note:** For the most up-to-date and detailed instructions specific to this project, please refer to the [QUTAS UAV Setup Guides (2024)](https://github.com/qutas/info/wiki/UAV-Setup-Guides-(2024)).

## Prerequisites

- Ubuntu Mate 20.04 (for Raspberry Pi 4)
- ROS Noetic
- PX4 Firmware (Version 1.13.2 recommended)
- QGroundControl (Version 4.1.3 recommended)

## Hardware Setup

- Airframe: S300 Frame with a Standard Quad autopilot-supported configuration
- Autopilot: Pix32 v6 Autopilot (running PX4 firmware FMU V6C, V 1.13.2)
- On-board Computer: Raspberry Pi 4 (running Ubuntu Mate Focal 20.04)
- Camera: OAK-D Lite Fixed Focus (USB-C connection)

## Installation Steps

### 1. Raspberry Pi 4 Setup

1. Download Ubuntu Mate 20.04 image for ARM64.
2. Install on Raspberry Pi 4 SD card.
3. Set up automatic login.
4. Update system software.

**Warning:** Be careful when handling the Raspberry Pi. Avoid placing it on metallic/conductive surfaces to prevent short circuits. Exercise caution with GPIO ports to avoid damage.

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

### 3. Additional Tools Installation

Install the following tools:
```
sudo apt-get install python3-wstool python3-rosdep
sudo rosdep init
rosdep update
```

### 4. Workspace Setup

1. Create and initialize a catkin workspace:
   ```
   mkdir -p ~/catkin_ws/src
   cd ~/catkin_ws
   catkin_make
   ```
2. Initialize wstool:
   ```
   cd ~/catkin_ws
   wstool init src
   ```

### 5. Install QUTAS Flight Stack (QFS)

1. Download QFS definitions:
   ```
   cd ~/catkin_ws/
   QFS_PACKAGE=qfs_noetic
   curl https://raw.githubusercontent.com/qutas/info/master/Stack/$QFS_PACKAGE.rosinstall > /tmp/$QFS_PACKAGE.rosinstall
   wstool merge -t src /tmp/$QFS_PACKAGE.rosinstall
   ```
2. Update and build the workspace:
   ```
   wstool update -t src
   rosdep install --from-paths src --ignore-src -r -y
   catkin_make
   source ~/catkin_ws/devel/setup.bash
   ```

### 6. Install Project Dependencies

Clone the following repositories:
```
cd ~/catkin_ws/src
git clone https://github.com/GilderCodes/qutas_lab_450.git
git clone https://github.com/GilderCodes/egh450_payload.git
git clone https://github.com/GilderCodes/operator_interfaces.git
git clone https://github.com/GilderCodes/breadcrumb.git
git clone https://github.com/GilderCodes/spar.git
git clone https://github.com/GilderCodes/depthai_publisher.git
```

### 7. MAVROS Setup

1. Install MAVROS:
   ```
   sudo apt install ros-noetic-mavros ros-noetic-mavros-extras
   ```
2. Install geographic datasets:
   ```
   roscat mavros install_geographiclib_datasets.sh | sudo bash
   ```

### 8. PX4 and QGroundControl Setup

1. Install QGroundControl on your local machine.
2. Connect the Pix32 v6 Autopilot to your computer via USB.
3. Use QGroundControl to flash PX4 firmware (Version 1.13.2) to the autopilot.
4. Calibrate vehicle sensors using QGroundControl.
5. Set the following PX4 parameters in QGroundControl:
   ```
   EKF2_EV_DELAY = 50
   EKF2_AID_MASK = 24
   EKF2_HGT_MODE = 3
   RTL_RETURN_ALT = 2
   RTL_DESCEND_ALT = 2
   MAV_1_CONFIG = TELEM 3
   MAV_1_FLOW_CTRL = Force off
   MAV_1_MODE = Onboard
   SER_TEL3_BAUD = 921600
   ```

### 9. Camera Setup

1. Connect the OAK-D Lite camera to the Raspberry Pi using USB-C.
2. Install DepthAI ROS package:
   ```
   sudo wget -qO- https://raw.githubusercontent.com/luxonis/depthai-ros/main/install_dependencies.sh | sudo bash
   sudo apt install libopencv-dev python-rosdep
   cd ~/catkin_ws/src
   git clone --branch noetic https://github.com/luxonis/depthai-ros.git
   cd ..
   rosdep install --from-paths src --ignore-src -r -y
   catkin_make -l1 -j1
   ```

### 10. QUTAS Lab Backend Setup

1. Set up the QUTAS Lab Backend using the `qutas_lab_450` package:
   ```
   roslaunch qutas_lab_450 environment.launch
   ```
2. Configure the `control.launch` file in your catkin workspace:
   ```
   roscp qutas_lab_450 px4_flight_control.launch ~/catkin_ws/launch/control.launch
   roscp mavros px4_pluginlists.yaml ./
   roscp mavros px4_config.yaml ./
   ```
3. Edit the `control.launch` file to specify your UAV and correct paths.

### 11. Distributed ROS Setup

1. Set up distributed ROS between onboard and GCS computers.
2. Configure the `/etc/hosts` file on both computers to include each other's IP addresses.
3. Set the `ROS_MASTER_URI` and `ROS_HOSTNAME` environment variables on both computers.

### 12. Testing

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
4. Test MAVROS connection:
   ```
   roslaunch ~/catkin_ws/launch/control.launch
   ```

### 13. Final Steps

1. Design preliminary GCS display using rviz and rqt.
2. Perform thrust testing and motor calibration.
3. Adjust PX4 parameters for optimal performance.
4. Test payload deployment system if applicable.
5. Implement path planning using the Breadcrumb package.
6. Set up image processing scripts for target detection.

## Additional Components

### Breadcrumb
Breadcrumb handles path-planning in cluttered environments. Refer to the [Breadcrumb GitHub repository](https://github.com/qutas/breadcrumb) for setup and usage instructions.

### Spar
Spar acts as a daemon to handle path-planning and navigation integration with the flight controller. Refer to the [Spar GitHub repository](https://github.com/qutas/spar) for setup and usage instructions.

### Image Processing
Implement image processing scripts using OpenCV and ROS for target detection and pose estimation. Consider using the [egh450_target_solvepnp](https://github.com/qutas/egh450_target_solvepnp) package for target pose estimation.

### Payload Actuation
If required, set up payload actuation using GPIO pins on the Raspberry Pi. Implement a ROS node to control the actuator based on mission parameters.

## Safety Considerations

- Always follow proper safety procedures when working with UAVs.
- Ensure all team members are familiar with emergency protocols.
- Conduct initial tests without propellers attached.
- Perform thorough checks before each flight.
- Use the MAVROS GUI in rqt for the arming procedure and monitor system status.

## Troubleshooting

- If experiencing lag with processed images, implement a timestamp check to process only the latest images.
- For issues with ROS packages, check the catkin workspace setup and ensure all dependencies are installed.
- If MAVROS fails to start, verify the installation of geographic datasets.

## Additional Resources

- [ROS Wiki](http://wiki.ros.org/)
- [PX4 Documentation](https://docs.px4.io/)
- [QGroundControl User Guide](https://docs.qgroundcontrol.com/master/en/)
- [DepthAI ROS GitHub](https://github.com/luxonis/depthai-ros)
- [MAVROS Documentation](http://wiki.ros.org/mavros)
- [Breadcrumb Documentation](https://github.com/qutas/breadcrumb)
- [Spar Documentation](https://github.com/qutas/spar)

Remember to check the [QUTAS UAV Setup Guides (2024)](https://github.com/qutas/info/wiki/UAV-Setup-Guides-(2024)) for the most up-to-date and detailed instructions specific to this project.
