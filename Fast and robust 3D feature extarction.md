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

