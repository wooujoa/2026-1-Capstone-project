# Vision-Based Robotic Grasping Pipeline

This repository index groups the main modules used for a vision-based robotic grasping pipeline.  
Each module is maintained as an independent GitHub repository and can be accessed through the links below.

## System Overview

```text
RGB-D Camera
    │
    ├── Camera / Robot Calibration
    │       └── calibration
    │
    ├── Reference / Marker Localization
    │       └── aruco_3d_locator
    │
    └── Target Object Segmentation
            └── sam3_one_shot_seg
                    │
                    └── Object Point Cloud
                            │
                            └── anygrasp_sam3_ros2
                                    │
                                    └── Grasp Candidates / Best Grasp
                                            │
                                            └── master_capstone
                                                    └── Integrated Robot Task
```

## Repositories

### 1. [master_capstone](https://github.com/wooujoa/master_capstone)

Main repository for integrating the individual perception and manipulation modules into the capstone robot system.

**Role**
- Integrates the overall vision-to-manipulation pipeline
- Connects perception results with robot task execution
- Serves as the top-level repository for running the complete capstone scenario

---

### 2. [sam3_one_shot_seg](https://github.com/wooujoa/sam3_one_shot_seg)

One-shot object segmentation module based on SAM 3.

**Role**
- Segments a target object from RGB images using a reference object/example
- Uses the segmentation result to isolate the target object from the scene
- Generates object-related point-cloud outputs that can be consumed by downstream grasp-planning modules
- Provides target/object point-cloud topics for the grasp pipeline

---

### 3. [anygrasp_sam3_ros2](https://github.com/wooujoa/anygrasp_sam3_ros2)

ROS 2 bridge between the SAM 3 segmentation output and AnyGrasp.

**Role**
- Subscribes to SAM 3 point-cloud topics such as `/yolo/target_pc` or `/yolo/object_pc`
- Runs AnyGrasp inference on the segmented object point cloud
- Publishes multiple grasp candidates and the best grasp pose
- Publishes grasp width and RViz2 visualization markers

**Main Outputs**
- `/anygrasp/grasps` — `geometry_msgs/PoseArray`
- `/anygrasp/best_grasp` — `geometry_msgs/PoseStamped`
- `/anygrasp/best_width` — `std_msgs/Float32`
- `/anygrasp/grasp_markers` — `visualization_msgs/MarkerArray`

---

### 4. [calibration](https://github.com/wooujoa/calibration)

Calibration utilities for aligning camera measurements with the robot coordinate system.

**Role**
- Handles geometric calibration required to convert perception results into robot-usable coordinates
- Establishes the transformation relationship between camera and robot frames
- Provides the coordinate-transform basis used when applying detected object or grasp poses to robot motion

---

### 5. [aruco_3d_locator](https://github.com/wooujoa/aruco_3d_locator)

ArUco-marker-based 3D localization utility.

**Role**
- Detects ArUco markers in camera images
- Estimates marker position/orientation in 3D
- Provides a known visual reference for checking or establishing spatial relationships between camera, workspace, and robot frames
- Can be used as a debugging/reference tool for pose and coordinate-transform verification

---

## Pipeline Summary

| Repository | Main Function | Input | Output |
|---|---|---|---|
| `master_capstone` | System integration and robot task execution | Perception / grasp results | Integrated robot behavior |
| `sam3_one_shot_seg` | One-shot target-object segmentation | RGB / RGB-D image + reference target | Object mask / point cloud |
| `anygrasp_sam3_ros2` | 6-DoF grasp-pose inference | Segmented object point cloud | Grasp candidates / best grasp |
| `calibration` | Camera–robot coordinate calibration | Calibration observations | Frame transformation |
| `aruco_3d_locator` | ArUco-based 3D localization | Camera image / camera parameters | Marker 3D pose |

## Overall Flow

1. Calibrate the camera and robot coordinate relationship.
2. Detect or segment the target object using `sam3_one_shot_seg`.
3. Convert the segmented target region into an object point cloud.
4. Pass the point cloud to `anygrasp_sam3_ros2`.
5. Infer grasp candidates and select the best grasp pose.
6. Transform the grasp pose into the robot coordinate frame.
7. Execute the manipulation task through the integrated `master_capstone` system.

---

## Repository Links

- [master_capstone](https://github.com/wooujoa/master_capstone)
- [sam3_one_shot_seg](https://github.com/wooujoa/sam3_one_shot_seg)
- [anygrasp_sam3_ros2](https://github.com/wooujoa/anygrasp_sam3_ros2)
- [calibration](https://github.com/wooujoa/calibration)
- [aruco_3d_locator](https://github.com/wooujoa/aruco_3d_locator)
