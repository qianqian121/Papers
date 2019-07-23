# Fast and Robust 3D Feature Extraction from Sparse Point Clouds (IROS2016)
<br/>
Author: Jacopo Serafin_1, Edwin Olson_2 and Giorgio Grisetti_1

<br/>
<br/>

1_Department of Computer, Control, and Management Engineering “Antonio Ruberti” of Sapienza University of Rome. Via Ariosto 25, I00185 Rome, Italy
2_Computer Science and Engineering Department, University of Michigan. 2260 Hayward St. Ann Arbor, MI 48109, USA

<br/>
<br/>

**keywords**: localization, mapping, SLAM
<br/>
<br/>


## Motivation

Difficult to **match 3D point clouds** due to the **sparse and non-uniform** point clouds

<br/>
<br/>

## Contribution

1. This paper propose **feature-based approach** using **Principal Components Analysis(PCA)** of neighborhoods of points **from a single sparse point cloud**, which results in mathematically **principled line and plane features**.

2. This paper show how this type of **feature extraction** can be done **efficiently and robustly** even on **non-uniformly sampled pointclouds**.

<br/>

## Robust 3D Feature Extraction

### 1. Problem Formulation

* Properties of **Good** features

    1. Sensitivity: easy to detect features
    
    2. Repeatability: the same feature is detected
    
    3. Robustness: robust to external noise
    
    4. Distance measure: possible to define a distance between features.
    
* **Output**: 6DOF estimated pose

<br/>
<br/>

1. **Point cloud segmentation**: single scan’s point cloud -> project it onto a range image

2. **Feature extraction**: obtain distinctive **planar** and **edge features**

3. **LiDAR odometry**: use feature extracted from 2nd module -> Find **transformation** between two consecutive scans

4. **LiDAR mapping**: features are processed, **register** to global point cloud map

5. **Transform integration**: fuse **lidar odometry** and **lidar mapping** -> Output: final estimated pose

<br/>

>>> ![fig2](https://user-images.githubusercontent.com/42059549/61606726-ad9f3c80-ac86-11e9-9f9a-a1ac208affec.png)

<br/>

## vs. LOAM

* **Similar or better** accuracy

* **Reduced** computational expense

* **Lightweight** LiDAR odometry and mapping


***

<br/>

## Lightweight LiDAR odometry and mapping

### 1. Segmentation

<br/>

**Improve processing efficiency** and **feature extraction accuracy**

Perform **fast** and **reliable** feature extraction

**Omit the clusters** that have fewer than 30 points

<br/>
<br/>


* **Obtained Three properties for each point**

1. Its label as a **ground point** or **segmented point**

2. its **column and row index** in the range image

3. Its **range value**

<br/>
<br/>

### 2. Feature extraction

**Extract** features from **ground points** and **segmented points**.

* *S* : the set of continuous points of *p_i* from the same row of the range image

(Half of the points in *S* : on either side of *p_i* )

* **Evaluate** the **roughness** of point *p_i* in *S*

**Roughness** : Calculation of **curvature** from *S*(the set of continuous points) 

![eq1](https://user-images.githubusercontent.com/42059549/61608278-0e317800-ac8d-11e9-9bd4-be95c16ceeaa.JPG)

***c* > *c_th*** : **edge** feature -> **F_e**: the set of all edge features from all sub-images

***c* < *c_th*** : **planar** feature -> **F_p**: the set of all planar features from all sub-images

(*c_th* can be changed to reduce the number of features and filter out unstable features)

<br/>
<br/>

### 3. LiDAR odometry

Estimate the seonsor motion between two consecutive scans.

* Transformation between two scans: performing **point-to-edge**, **point-to-plane** scan-matching

= Find the corresponding features in ***F_e(t)*** and ***F_p(t)*** from feature sets **F_e(t-1)** and **F_p(t-1)** of **the previous scan**


* **Improvement** in feature matching accuracy and efficiency

1. **Label Matching**

    Since each feature in *F_e(t)* and *F_p(t)* is encoded with its label after segmentation, we **only find correspondences** that have the same label

    -> It helps **improve the matching accuracy**
    
<br/>    

2. **Two-step L-M optimization**

    (**The Levenberg-Marquardt(L-M) method**: applied to find the **minimum-distance transformation** between the two consecutive scans)

    A series of nonlinear expressions for the distance -> **single** comprehensive distance vector

    1. Estimate [*t_z, theta_roll, theta_pitch*] : match the planar features

    2. Estimate remaining [*t_x, t_y, theta_yaw*] : match the edge features
    
    ∴ **6D transformation** between two consecutive scans : by fusing [*t_z, theta_roll, theta_pitch*] and [*t_x, t_y, theta_yaw*].

<br/>

3. **LiDAR Mapping**

**Match features** in {F_e(t), F_p(t)} to a surrounding point cloud map Q(t-1) to further refine the pose transformation

**The L-M method** is used here again to obtain the final transformation

* **Main difference** in LeGO-LOAM: how the final point cloud map is stored -> save each individual feature set {F_e(t), F_p(t)}

    1. Choose the feature sets in the field of view of the sensor
    
    2. Integrate pose-graph SLAM into LeGO-LOAM

<br/>

***

<br/>
<br/>

### 4. Experiments & results

1. **Feature extraction**

The number of **unstable features** from LeGO-LOAM is **reduced** greatly after point cloud segmentation.

![fig4](https://user-images.githubusercontent.com/42059549/61676369-9750b980-ad36-11e9-9b0f-972546cc1397.JPG)

<br/>

2. **LiDAR mapping**

Due to **erroneous feature associations** caused by unstable features, the map from LOAM **diverges** twice during operation.

![fig5](https://user-images.githubusercontent.com/42059549/61676421-cff09300-ad36-11e9-99d5-d6c8e880d50a.JPG)

<br/>

3. **Pose estimation accuracy**

Compare **the translational and rotational difference** between the final and initial pose

* **Similar** low-drift pose estimation: LOAM, LeGO-LOAM

* **LOAM**'s pose estimation diverges : 

    (1) no edge features are extracted from ground covered by grass

    (2) noisy sensor readings from grass and trees -> unreliable feature extraction

![fig7](https://user-images.githubusercontent.com/42059549/61676852-5f4a7600-ad38-11e9-898c-787ef809cd69.JPG)



