# Pose Graph Optimization for UAVs

This code provides a robust Pose Graph Optimization (PGO) backend designed specifically for UAVs. It integrates LiDAR odometry from FAST-LIO with global positioning data from a Pixhawk flight controller via MAVROS.

## Important Note

This project is built upon and modified from [FAST_LIO_GPS](https://github.com/yxw027/FAST_LIO_GPS). Major modification is only made in ~/src/laserPosegraphOptimization_gps_pcd.cpp

## Overview

This repository performs backend optimization by fusing three main data sources:

1. **LiDAR Odometry**: Optimized scans and odometry provided by FAST-LIO.
2. **GPS (Global Constraints)**: UTM-converted coordinates from Pixhawk (`NavSatFix`) to provide absolute global positioning.
3. **Scan Context (Loop Closure)**: Spatial descriptor-based loop detection to recognize previously visited locations and eliminate accumulated drift.
