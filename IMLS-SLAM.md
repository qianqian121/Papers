## IMLS-SLAM: Scan-to-Model Matching Based on 3D Data (ICRA2018)
<br/>
Author: Jean-Emmanuel Deschaud

MINES ParisTech, PSL Research University, Centre for Robotics, 60 Bd St Michel 75006 Paris, France
<br/>
<br/>

**keywords**: localization, SLAM
<br/>
<br/>

### Motivation

In the last 10 years, 3D depth sensors such as LiDAR have proved to be very useful to perceive the environment in autonomous driving.

However, **few methods** exist that directly use these **3D data for odometry**.


<br/>

### Contribution

This paper presents **a new low-drift SLAM algorithm** based **only** on **3D LiDAR data**.

<br/>

The method relies on **a scan-to-model matching framework**. 

1. Use **a specific sampling strategy** based on the LiDAR scans.

2. Define our model as **the previous localized LiDAR sweeps**.

3. Use **the Implicit Moving Least Squares (IMLS) surface representation**.

<br/>

### Challenges

* The amount of data to process

* The sparse density of collected range images


<br/>

### Related work

* 6-DOF SLAM LiDAR with mapping

1. ICP(Interative Closest Point): a well-known scan-to-scan registration method





