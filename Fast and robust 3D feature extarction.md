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

* **Properties** of **Good** features

    1. Sensitivity: easy to detect features
    
    2. Repeatability: the same feature is detected
    
    3. Robustness: robust to external noise
    
    4. Distance measure: possible to define a distance between features.

<br/>

### 2. Flat Region Removal

* **Common characteristic of lines and planes**: dominant vertical component

<br/>

* **Method**: Prune all the points that do not expand along the gravity vector direction

    1. Representation of each point: **the polar coordinate**(Spherical depth image) 
    
        = **azimuth**(*theta*), **elevation**(*phi*), and **the range**(*d*)

    2. *Index image*: to avoid moving large amount of data
    
    3. When a new point cloud is generated, **compute** its Cartesian representation *P*, the associated spherical depth image *D_p*
    
        -> using the **projection function** & **index image**
    
    4. **Vertical feature** criteria: the number of points of projections(*p_vertical*) within a range > (threshold) 
    
        (Iterate through **all its rows**)
    
    5. This method allows to detect structures not perfectly vertical.
    
>> ![dsf](https://user-images.githubusercontent.com/42059549/61766531-18ce4780-ae1c-11e9-86c1-6775df5bb95c.JPG)
    
<br/>    

### 3. Surface Normal Computation

* **Method**: locally characterizing the surrounding surface with **its normal *n***

    -> ***n***: computed by evaluating the **covariance matrix of Gaussian distribution** of all the points that lie in a certain  neighborhood of the query point *P*
    
    -> For each point **p_i**: compute the **mean(*mu*)** and the **covariance(*sigma*)** of the Gaussian distribution
    
    ![dsf](https://user-images.githubusercontent.com/42059549/61766717-d35e4a00-ae1c-11e9-8d02-d7949e31b444.JPG)  

    (*V_i*: the set of points composing the neighborhood of **p_i**, and *mu* is the centroid of *V_i*)
    
    -> normal vector: the **eigenvector** associated with the **smallest eigenvalue**.

* **key issue**: determining the **size of the region** over which a normal is computed.

    Within a sphere or radius *r* centered on the query point **p_i**
    
![dsf](https://user-images.githubusercontent.com/42059549/61769142-45865d00-ae24-11e9-939f-e7f154780e11.JPG)
    
![dsf](https://user-images.githubusercontent.com/42059549/61775407-dadd1d80-ae33-11e9-9edf-cbb6c8ac8c4a.JPG)    
    
<br/>    

### 4. Segmentation and 3D feature extraction

* **General idea**

    1. **Compute clusters, or segments**, belonging to well defined objects in the scene.
    
    > (**CAUTION**: the order of cluster processing: fit **a line** -> (if fitting **fails**) -> try **a plane**)
    
    2. For each cluster, **perform 3D line/plane data fitting**.
    
    3. **Determine** if a cluster is well approximated with a line or a plane feature by computing a measure of **the quality of the fitting**.
    
    - **Criteria** to discriminate neighboring points to the current cluster: **point distance** and **the angle difference of the normals.**
    
    ![dsf](https://user-images.githubusercontent.com/42059549/61776303-b7b36d80-ae35-11e9-835a-1f98e20d4c03.JPG)
    
    - **Remove** the clusters containing **smaller number** of points than **threshold**.
    
* **After segmentation**

    1. **Check** which of them can be approximated by a line or a plane.
    
    - **Fit** the segment with a 3D line or a 3D plane
    
    - Given a cluster, **compute** the **covariance matrix of the Gaussian distribution** of all the points in cluster.
    
        1. line: two eigenvalues < third eigenvalue
        
        2. plane: one eigenvalue < other eigenvalues
        
    2. **The residual error *e*** must be **small** if the feature model approximates well.
    
    ![dsf](https://user-images.githubusercontent.com/42059549/61777998-ea129a00-ae38-11e9-9f5d-03da4eeacfca.JPG)
    
    3. **Paper's method**: checks **both** at **the shape of the eigenvectors** and **the value of the residual error**.
    
    4. More formally, **one more constraints** for valid features(lines, planes)
    
    ![dsf](https://user-images.githubusercontent.com/42059549/61778671-22ff3e80-ae3a-11e9-8fd6-f674db2a0cd9.JPG) ![dsf](https://user-images.githubusercontent.com/42059549/61778704-33171e00-ae3a-11e9-8ee9-9ed28de428e8.JPG)
