# Dashed Line Detection and PID Control for Duckiebot

This project implements a system for detecting dashed lines in a Duckiebot's camera feed and controlling its movement using a PID controller. The system involves detecting the dashed line using computer vision techniques, calculating the tracking error, and using a PID controller to correct the robot's trajectory.

## Overview

The system is composed of two main components:

1. **Dashed Line Detection**: This component processes images from the Duckiebot's camera, detects contours corresponding to dashed lines, computes the centers of mass of these contours, and fits a spline to these points. The spline is used to determine the deviation of the Duckiebot from the desired path.

2. **PID Control**: The PID controller takes the error between the reference heading (`theta_ref`) and the current heading (`theta_hat`) of the robot to compute the control command. The controller adjusts the angular velocity (`omega`) to bring the robot back onto the correct path.

## Components

### Dashed Line Detection

The dashed line detection algorithm works as follows:

1. **Image Preprocessing**:
   - The input image is converted to HSV color space to highlight yellow (commonly used for dashed lines).
   - A Gaussian blur is applied to the image to reduce noise and improve contour detection.

2. **Contour Detection**:
   - The processed image is thresholded to create a mask for yellow regions.
   - Contours are detected in the mask, and the centers of mass of these contours are calculated.

3. **Spline Fitting**:
   - If enough points are detected, a spline is fitted to the points using the `scipy` library.
   - This spline represents the detected dashed line and is used for further calculations of the tracking error.

4. **Error Calculation**:
   - The tracking error is calculated by finding the closest point on the spline to the desired reference point (the "zero point").
   - The error is normalized based on its maximum and minimum values, which helps in controlling the robot's motion smoothly.

5. **Visualization**:
   - The detected points and the fitted spline are drawn on the image for debugging and visualization purposes.
   - The error is displayed on the image and published for further processing by the PID controller.

### PID Controller

The PID controller adjusts the Duckiebot's angular velocity to correct its path. It works by calculating the tracking error based on the robot's current and desired heading, and uses proportional, integral, and derivative terms to compute the control signal.

1. **Proportional (P)**: The error between the reference and the current heading.
2. **Integral (I)**: The accumulated error over time to address any steady-state error.
3. **Derivative (D)**: The rate of change of the error to predict future error and reduce overshoot.

### Workflow

1. **Image Subscription**: The dashed line detection node subscribes to the camera feed, processes the image to detect dashed lines, and calculates the error.
2. **Error Publication**: The error, both raw and normalized, is published to a topic where the PID controller subscribes.
3. **Control Command**: The PID controller calculates the required angular velocity (`omega`) and linear velocity (`v`) to correct the robot's trajectory and publishes this command.

### Dependencies
The following dependencies are required to run this project:

cv2 (OpenCV): For image processing and contour detection.
numpy: For numerical operations and array handling.
scipy: For spline fitting.
duckietown: For integration with Duckiebot's software environment.
rospy: For ROS (Robot Operating System) communication and node management.
