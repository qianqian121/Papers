# IMLS-SLAM: Scan-to-Model Matching Based on 3D Data (ICRA2018)
<br/>
Author: Jean-Emmanuel Deschaud

MINES ParisTech, PSL Research University, Centre for Robotics, 60 Bd St Michel 75006 Paris, France
<br/>
<br/>

**keywords**: localization, mapping, SLAM
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

> -> Use **only** previous odometry: *˜τ(t_k) = τ(t_k−1) ∗ τ(t_k−2)^(-1) ∗ τ(t_k−1)*

<br/>

2. Build the point cloud scan *S_k*

> -> Use a linear interpolation of positions between *τ(t_k−1)* and *˜τ(t_k)* 
    
>> ***Good** egomotion approximation*: when the angular and linear velocities of the LiDAR are *smooth and continuous*.

<br/>

3. Remove all dynamic objects from the scan.

> -> Requires a high level of semantic information
        
>> 1. *Detect* *the ground points* in the scan using a voxel growing.
    
>> 2. *Remove* *these ground points*.
    
>> 3. *Cluster* *the remaining points* with a distance to the nearest point less than 0.5 m.
    
>> 4. *Discard* from *the scan small group of points* instead of dynamic objects. (*small objects*: represent *pedestrians, cars, buses, or trucks*)
    
>>> -> *discard all of small objects* from the scene assuming that they could be dynamic objects.
        
>> 5. *Remove* *groups of points* whose bounding box is less than 14 m from a vehicle frame.

>> 6. *Add back* the ground points to the scan point cloud.

<br/>

4. Do a rigid registration of that poind cloud scan to our map: to find *τ(t_k)*

(*NOTE*: 

*Registration*: the alignment of two measurement through some transformation -> *Not* necessary *relative sensor pose* in all applications

*SLAM*: find the sensors motion through a scene, and map the scene at the same time)

<br/>
    
### 2. Scan Sampling Strategy

Keep *the axes of the vehicle frame* instead of using principal axes of the point cloud from the covariance matrix

Define the LiDAR scan point cloud in the vehicle frame with axes(*(X_v, Y_v, Z_v)*)

> -> Most of the planar areas are aligned to *(X_v, Y_v, Z_v)*

<br/>

* **Process**

1. Compute *the normal* at every point

> -> For every point, keep the planar scalar(*a_2D*) of its neighborhood

<br/>

2. Compute the *nine values* for every point *x_i* in the scan cloud *S_k*

    six values: the observability of the different unknown angles(*roll, pitch, yaw*) of the vehicle 

    last three values: observability of the unknown translations

(*Not* mandatory in our method to have planar zones, but it *improves* the quality of matching)

<br/>

![nine_values](https://user-images.githubusercontent.com/42059549/61431454-808e1980-a968-11e9-8670-9ee8288edde5.JPG)

<br/>

3. Sort the nine lists in descending order: first points of every list = more observability related to the unknown parameters.

<br/>

4. Select from each list starting from the beginning of the list.

<br/>

5. Find *the closest point* *p_c* of *x* in the model cloud until we find *s* samples from each list.

<br/>

![fig2](https://user-images.githubusercontent.com/42059549/61431494-a0254200-a968-11e9-9085-78950431212b.JPG)

<br/>
    
### 3. Scan-To-Model Matching with Implcit Moving Least Squares (IMLS) Surface representation

Implicit surface: defined by a Truncated Signed Distance Function (TSDF) 

> -> problem: the surface is defined by a voxel grid(empty, SDF, unknown), usable only in a small volume space

Performance of results: "scan-to-model matching" > "classical scan-to-scan matching"

<br/>

* **Process**

1. *MLS(Moving Least Square) surface*: generates as smooth surface from a raw noisy point cloud.

<br/>

2. Define *IMLS surface* *I(x)*: *behaves as a distance function* close to the surface.

> -> Weights *W_i(x)*: *decreases* quickly when points are far from *x*.

<br/>

![equation1](https://user-images.githubusercontent.com/42059549/61431552-cb0f9600-a968-11e9-94d5-30f88465824a.JPG)

<br/>

3. Define our point cloud map *P_k* as the accumulation of *n* previous localized scans.

<br/>

4. Localize the current scan *S_k* in point cloud *P_k*.

> -> *Find the transformation* *R* and *t* that *minimizes* the sum of squared IMLS distances

> -> Due to *exponential weights*, we can**not** approximate that nonlinear least-square optimization problem by *linear least-square*
    
<br/>

5. Have a point cloud *Y_k*, the set of projected points *y_j*.

<br/>

6. Look for *the transformation* *R* and *t* that minimizes the sum. (*assumption*: small angle like ICP)
 
<br/>

7. *Iterate* until a maximum number of iterations has been made.

<br/>

8. Get *final transformation*: composed of the transformation *between the first and alst iteration of the scan*.

<br/>

9. Compute *he new point cloud* from raw data of the currenet scan

<br/>

10. Compute *linear interpolation* of vehicle position between *τ(t_k-1)* and *τ(t_k)*.

<br/>

![fig3](https://user-images.githubusercontent.com/42059549/61431602-f72b1700-a968-11e9-8255-840df6128886.JPG)

***

<br/>

## Experiments and Results

<br/>

### Comparison of classical ICP scan matching and IMLS scan-to-model framework

* Advantage: *move* the scan converge towards the implicit surface, *improve* the quality of matching

<br/>

### Environment

* Implemented in **C++** using only the **FLANN** library(for nearest neighbor research with k-d tree) and **Eigen**

* **Velodyne HDL32, Velodyne HDL64** are used to test on a real outdoor dataset with **10 Hz**

* **one CPU core** at **4 GHz**, uses **less than 1 Go of RAM**

* **Sampling points** in each list: *s* = 100

* *h* = 0.06 m for the IMLS surface definition

* *r* = 0.20 m : maximum distance for neighbors search

* **The number of matching iterataions**: 20 (keep a constant timing process)

* **The number of scans we have in the model point cloud**: *n* = 100

<br/>

### Results

The paper measures *the distance* between the first and last localized scan as an error metric.

<br/>

* **Comparison with IMLS-SLAM and ICP**

<br/>

![fig1](https://user-images.githubusercontent.com/42059549/61431317-08275880-a968-11e9-8ddb-37e1b2761167.JPG)

Fig1: The good superposition of the two loops with IMLS-SLAM

<br/>

* **Small portion of the point cloud generated from the SLAM**

<br/>

![fig4](https://user-images.githubusercontent.com/42059549/61432122-635a4a80-a96a-11e9-8798-c0bbaff9aa4d.JPG)

Fig4: Good egomotion of the vehicle during each scan

The distance error between the first and last scan with IMLS SLAM: 16 m (a drift of only 0.40%)

<br/>

* **Comparision with IMLS-SLAM and LOAM in the KITTI dataset**

<br/>

![table1](https://user-images.githubusercontent.com/42059549/61432227-ae745d80-a96a-11e9-860a-484f73b65026.JPG)

Table1: IMLS-SLAM has better performance than LOAM 
