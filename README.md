# Autonomous Navigation: Real-Time Perception System using ROS

## Overview
This repository contains `autonomous_navigation`, a real-time, event-driven ROS (Robot Operating System) package designed for autonomous vehicles[cite: 1]. It bridges the gap between offline perception algorithms and real-time deployment by processing continuous sensor streams frame-by-frame and publishing the results immediately for downstream path planning and control modules[cite: 1].

## System Architecture
The perception pipeline is managed by a central processing unit called `/vision_bev_node`[cite: 1]. It separates perception algorithms from ROS networking logic to ensure each component is independently testable and reusable[cite: 1].

### ROS Topics
**Subscribed (Inputs):**
* `/velodyne_points`: Raw 3D LiDAR point cloud (`sensor_msgs/PointCloud2`)[cite: 1].
* `/camera/front/image_raw`: RGB image from the front-facing camera (`sensor_msgs/Image`)[cite: 1].

**Published (Outputs):**
* `/autonomous_nav/bev_image`: 608x608 Bird's-Eye View map representing intensity, height, and density (`sensor_msgs/Image`)[cite: 1].
* `/autonomous_nav/detections`: 2D bounding boxes with object class IDs and YOLOv8 confidence scores (`vision_msgs/Detection2DArray`)[cite: 1].

## Modules
The package follows a modular design divided into three core Python scripts[cite: 1]:
* **`bev_processor.py`**: Contains the core BEV algorithm, converting raw point cloud arrays into a 3-channel tensor[cite: 1].
* **`detection_utils.py`**: Wraps the YOLOv8 object detection model, processes camera frames, computes IoU, and filters vehicle-relevant COCO classes[cite: 1].
* **`vision_bev_node.py`**: The main ROS node that connects inputs, executes processing modules, and publishes outputs[cite: 1].

## Runtime Configuration
Critical parameters are exposed via ROS parameters (`rosparam`) to allow system tuning at launch time without recompiling source code[cite: 1]. 

Key parameters include:
* `~bev/x_min`, `~bev/x_max`, `~bev/y_min`, `~bev/y_max`: Forward and lateral ROI bounds[cite: 1].
* `~bev/z_min`, `~bev/z_max`: Height thresholds[cite: 1].
* `~bev/grid_size`: BEV output resolution (default: 608)[cite: 1].
* `~yolo/conf_thresh`: Minimum detection confidence (default: 0.15)[cite: 1].
* `~enable_camera`: Toggle camera processing[cite: 1].

## Validation
The pipeline was compiled using `catkin_make` on ROS Noetic[cite: 1]. The BEV algorithm and YOLOv8 detection were successfully validated using both live ROS node testing and an independent test bench script (`generate_report_visuals.py`) that generated a synthetic 3D driving scene with 22,276 points[cite: 1].
