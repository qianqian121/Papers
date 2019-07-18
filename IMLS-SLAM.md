# IMLS-SLAM: Scan-to-Model Matching Based on 3D Data (ICRA2018)
<br/>
Author: Jean-Emmanuel Deschaud

MINES ParisTech, PSL Research University, Centre for Robotics, 60 Bd St Michel 75006 Paris, France
<br/>
<br/>

**keywords**: localization, SLAM
<br/>
<br/>

## Motivation

In the last 10 years, 3D depth sensors such as LiDAR have proved to be very useful to perceive the environment in autonomous driving.

However, **few methods** exist that directly use these **3D data for odometry**.


<br/>

## Contribution

This paper presents **a new low-drift SLAM algorithm** based **only** on **3D LiDAR data**. (no data from IMU, GPS, or cameras)

<br/>

The method relies on **a scan-to-model matching framework**. 

1. Use **a specific sampling strategy** based on the LiDAR scans.

2. Define our model as **the previous localized LiDAR sweeps**.

3. Use **the Implicit Moving Least Squares (IMLS) surface representation**.

<br/>

## Challenges

* The amount of data to process

* The sparse density of collected range images


<br/>

## Related work

* 6-DOF SLAM LiDAR with mapping

1. ICP(Interative Closest Point): 

    a well-known scan-to-scan registration or point-to-plane matching method
    
2. Stop-scan-go strategy

3. Build a vexel grid using a continuous spinning 2D laser

4. De-skew the range image along the trajectory considering spinning effect of 3D LiDAR

5. 3D LiDAR: sparse voxelized representation & generalization of ICP

6. LOAM(LiDAR Odometry And Mapping):

    focus on edges and planar features in the 2D LiDAR sweep
    
    match in a map for edge-line and planar-planar surfcae matching

***

<br/>

## Method

### 1. Scan Egomotion and Dynamic Object Removal

<br/>

* **Input**

    - **Scan *S***: the data coming from one rotation of the LiDAR sensor
    
    - **Displacement**: the movement of the vehicle during the acquisition time of a scan
    
        -> assumption: the egomotion is relatively similar between two consecutive scans 

<br/>

* **Output**

    - **Actual egomotion**: computed by the previous relative displacement
    
    - **Discrete position (*τ(t)*)**: The position solutions for the vehicle positions
    
        -> any vehicle pose: computed as a _linear interpolation_ between previous and current scan *τ(t)*
    
    - **Transformation of the vehicle pose**: *τ(t_k)* relative to its first position (at time *t_k* for scan *k*)

<br/>

* **Process**

1. Estimate the discrete position *˜τ(t_k)*

    -> Use **only** previous odometry: *˜τ(t_k) = τ(t_k−1) ∗ τ(t_k−2)^(-1) ∗ τ(t_k−1)*

<br/>

2. Build the point cloud scan *S_k*

    -> Use a linear interpolation of positions between *τ(t_k−1)* and *˜τ(t_k)* 
    
    ***Good** egomotion approximation*: when the angular and linear velocities of the LiDAR are *smooth and continuous*.

<br/>

3. Remove all dynamic objects from the scan.

    -> Requires a high level of semantic information
        
    1. *Detect* *the ground points* in the scan using a voxel growing.
    
    2. *Remove* *these ground points*.
    
    3. *Cluster* *the remaining points* with a distance to the nearest point less than 0.5 m.
    
    4. *Discard* from *the scan small group of points* instead of dynamic objects. (*small objects*: represent *pedestrians, cars, buses, or trucks*)
    
        -> *discard all of small objects* from the scene assuming that they could be dynamic objects.
        
    5. *Remove* *groups of points* whose bounding box is less than 14 m from a vehicle frame.

    6. *Add back* the ground points to the scan point cloud.

<br/>

4. Do a rigid registration of that poind cloud scan to our map: to find *τ(t_k)*

(*NOTE*: 

*Registration*: the alignment of two measurement through some transformation -> *Not* necessary *relative sensor pose* in all applications

*SLAM*: find the sensors motion through a scene, and map the scene at the same time)

<br/>
    
### 2. Scan Sampling Strategy

Keep *the axes of the vehicle frame* instead of using principal axes of the point cloud from the covariance matrix

Define the LiDAR scan point cloud in the vehicle frame with axes(*(X_v, Y_v, Z_v)*)

    -> Most of the planar areas are aligned to *(X_v, Y_v, Z_v)*
    
* **Process**

1. Compute *the normal* at every point

    -> For every point, keep the planar scalar(*a_2D*) of its neighborhood
    
2. Compute the *nine values* for every point *x_i* in the scan cloud *S_k*

(*Not* mandatory in our method to have planar zones, but it *improves* the quality of matching)





