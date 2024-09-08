# Homography-Decomposition

A homography is the most general formulation of linear transformations in 2D projective geometry, also known as a projective transformation. It can be decomposed into a sequence of transformations in a hierarchical manner:

### Homography Decomposition

The homography matrix \( H \) can be decomposed as:

```math
H = H_S H_A H_P = 
\left[ 
\begin{matrix} 
sR & t \\ 
0 & 1 
\end{matrix} 
\right] 
\left[ 
\begin{matrix} 
K & 0 \\ 
0^T & 1 
\end{matrix} 
\right] 
\left[ 
\begin{matrix} 
1 & v^T \\ 
0 & u 
\end{matrix} 
\right] 
= 
\left[ 
\begin{matrix} 
A & t \\ 
v^T & u 
\end{matrix} 
\right]
```


Where:
- \( H_S \): Similarity transformation (4 DoF)
- \( H_A \): Affine transformation (6 DoF)
- \( H_P \): Projective transformation (8 DoF)

### Transformations Overview

1. **Euclidean (Rigid) Transformation (3 DoF)**:
```math
   \left[ \begin{matrix} s r_{11} & s r_{12} & t_x \\ s r_{21} & s r_{22} & t_y \\ 0 & 0 & 1 \end{matrix} \right]
```

2. **Similarity Transformation (4 DoF)**:
```math
\begin{matrix}
s_x r_{11} & s_x r_{12} & t_x \\
s_y r_{21} & s_y r_{22} & t_y \\
0 & 0 & 1
\end{matrix}
```

3. **Affine Transformation (6 DoF)**:
```math
\begin{bmatrix}
s_x r_{11} & S_h x r_{12} & t_x \\
S_h y r_{21} & s_y r_{22} & t_y \\
0 & 0 & 1
\end{bmatrix}
```
s 
x
​
 , 
𝑠
𝑦
s 
y
​
 : Scaling factors along the x and y axes.
𝑟
11
,
𝑟
12
,
𝑟
21
,
𝑟
22
r 
11
​
 ,r 
12
​
 ,r 
21
​
 ,r 
22
​
 : Rotation matrix components.
𝑆
ℎ
𝑥
S 
h
​
 x, 
𝑆
ℎ
𝑦
S 
h
​
 y: Shearing factors along the x and y axes.
𝑡
𝑥
t 
x
​
 , 
𝑡
𝑦
t 
y
​
 : Translation distances along the x and y axes.

4. **Projective Transformation (8 DoF)**
```math
   H = \left[ \begin{matrix} h_{11} & h_{12} & h_{13} \\ h_{21} & h_{22} & h_{23} \\ h_{31} & h_{32} & h_{33} \end{matrix} \right]
```
### Homography and Image Transformation

The transformation of images is typically defined as the projection mapping between images of the same planar object taken from different positions by distortion-free cameras. The **Direct Linear Transformation (DLT)** is one of the simplest methods to estimate homography.

Given homogeneous coordinates \( (u, v, 1) \) in the first image and \( (u', v', 1) \) in the second image, we have:

\[
\left[ \begin{matrix} u' \\ v' \\ 1 \end{matrix} \right] = \left[ \begin{matrix} h_{11} & h_{12} & h_{13} \\ h_{21} & h_{22} & h_{23} \\ h_{31} & h_{32} & h_{33} \end{matrix} \right] \left[ \begin{matrix} u \\ v \\ 1 \end{matrix} \right]
\]

Expanding:

\[
u' = h_{11} u + h_{12} v + h_{13}, \quad v' = h_{21} u + h_{22} v + h_{23}
\]

### Homography Matrix Estimation

For each pair of corresponding points, we derive two linear equations. We need at least 4 pairs of points to compute the homography matrix between two images. In practice, more than 4 pairs are used to account for noise.

### Singular Value Decomposition (SVD)

To compute the homography matrix, we solve the equation \( Ah = 0 \) using SVD, where:

\[
A h = 0
\]

The matrix \( A \) has dimensions \( 2n \times 9 \) for \( n \geq 4 \) correspondences. The solution for the homography matrix is obtained as the right singular vector corresponding to the smallest singular value of \( A \).

### Decomposition of the Homography Matrix

Given a homography matrix \( H \) and intrinsic camera matrix \( K \), we can decompose \( H \) into rotation matrices \( R \), translation vectors \( t \), and plane normals \( n \) as:

\[
H = K(R + \frac{1}{d} t n^T) K^{-1}
\]

Where:
- \( d \) is the distance from the plane to the camera.

#### Faugeras SVD-Based Decomposition

The matrix \( H \) can be decomposed as:

\[
H = U \Lambda V^T
\]

Where \( U \) and \( V \) are orthogonal matrices, and \( \Lambda \) is a diagonal matrix containing the singular values of \( H \).

### References

- Multiple View Geometry in Computer Vision, 2nd Edition, Section 4.1.
- Zhang's Camera Calibration Algorithm: In-Depth Tutorial and Implementation, Section 3.2.1.
