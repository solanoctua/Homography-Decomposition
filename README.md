# Homography-Decomposition

A homography is the most general formulation of linear transformations in 2D projective geometry, also known as a projective transformation. It can be decomposed into a sequence of transformations in a hierarchical manner:

### Homography Decomposition

The homography matrix $H$ can be decomposed as:

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
- \( $H_S$ \): Similarity transformation (4 DoF)
- \( $H_A$ \): Affine transformation (6 DoF)
- \( $H_P$ \): Projective transformation (8 DoF)

### Transformations Overview

1. **Euclidean (Rigid) Transformation (3 DoF)**:
```math
H_E =
\left[
\begin{matrix}
s r_{11} & s r_{12} & t_x \\
s r_{21} & s r_{22} & t_y \\
0 & 0 & 1
\end{matrix}
\right]
```

2. **Similarity Transformation (4 DoF)**:
```math
H_S =
\left[
\begin{matrix}
s_x r_{11} & s_x r_{12} & t_x \\
s_y r_{21} & s_y r_{22} & t_y \\
0 & 0 & 1
\end{matrix}
\right]
```

3. **Affine Transformation (6 DoF)**:
```math
H_A =
\begin{bmatrix}
s_x r_{11} & S_h x r_{12} & t_x \\
S_h y r_{21} & s_y r_{22} & t_y \\
0 & 0 & 1
\end{bmatrix}
```

4. **Projective Transformation (8 DoF)**
```math
H_P =
\left[
\begin{matrix}
h_{11} & h_{12} & h_{13} \\
h_{21} & h_{22} & h_{23} \\
h_{31} & h_{32} & h_{33}
\end{matrix}
\right]
```
### Homography and Image Transformation

The transformation of images is typically defined as the projection mapping between images of the same planar object taken from different positions by distortion-free cameras. The **Direct Linear Transformation (DLT)** is one of the simplest methods to estimate homography.

Given homogeneous coordinates $(u, v, 1)$ in the first image and $(u', v', 1)$ in the second image, we have:

```math
\left[
\begin{matrix}
u' \\
v' \\
1
\end{matrix}
\right]
=
\left[
\begin{matrix}
h_{11} & h_{12} & h_{13} \\
h_{21} & h_{22} & h_{23} \\
h_{31} & h_{32} & h_{33}
\end{matrix}
\right]

\left[
\begin{matrix}
 u \\
 v \\
 1
\end{matrix}
\right]
```

Expanding:

The equations for the transformed coordinates $(u', v')$ in homogeneous coordinates are given by:

```math
u' = h_{11} u + h_{12} v + h_{13}
```
```math
v' = h_{21} u + h_{22} v + h_{23}
```
```math
1 = h_{31} u + h_{32} v + h_{33}
```

Further, by multiplying the denominator to the left, we can transform the equations into:

```math
(h_{31} u + h_{32} v + h_{33}) u' = h_{11} u + h_{12} v + h_{13} \\
```
```math
(h_{31} u + h_{32} v + h_{33}) v' = h_{21} u + h_{22} v + h_{23} \\
```
```math
\Rightarrow 0 = h_{11} u + h_{12} v + h_{13} - h_{31} u' u - h_{32} u' v - h_{33} u' \\
```
```math
\Rightarrow 0 = h_{21} u + h_{22} v + h_{23} - h_{31} v' u - h_{32} v' v - h_{33} v'
```
This is a pair of homogeneous equations (since the right hand side is zero).

We rewrite these equations in matrix notation as $Ah = 0$: 

```math
\begin{bmatrix}
u & v & 1 & 0 & 0 & 0 & -u'u & -u'v & -u' \\
0 & 0 & 0 & u & v & 1 & -v'u & -v'v & -v' \\
\end{bmatrix}
\begin{bmatrix}
h_{11} \\ h_{12} \\ h_{13} \\
h_{21} \\ h_{22} \\ h_{23} \\
h_{31} \\ h_{32} \\ h_{33} \\
\end{bmatrix}
=
\begin{bmatrix}
0 \\
0
\end{bmatrix}
```
The homography matrix $H$ is a $3 \times 3$ homogeneous matrix. Its final element, $h_{33}$, is normalized to $1$ so that $H$ has only 8 degrees of freedom.

To solve for the homography matrix, we need corresponding coordinate points. Each pair of matched coordinate points yields two linear equations. Thus, at least four pairs of corresponding points are required to compute the homography matrix between two images.

In practice, more than four pairs of corresponding points are typically used to account for coordinate errors caused by noise. With $n$ correspondences $n \geq 4$, a matrix $A$ with dimensions $2n \times 9$ is generated:
```math
A = \begin{bmatrix}
u_1 & v_1 & 1 & 0 & 0 & 0 & -u_1 u'_1 & -v_1 u'_1 & -u'_1 \\
0 & 0 & 0 & u_1 & v_1 & 1 & -u_1 v'_1 & -v_1 v'_1 & -v'_1 \\
\vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots \\
\vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots \\
\vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots \\
\vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots \\
 u_n & v_n & 1 & 0 & 0 & 0 & -u_n u'_n & -v_n u'_n & -u'_n \\
 0 & 0 & 0 & u_n & v_n & 1 & -u_n v'_n & -v_n v'_n & -v'_n
\end{bmatrix}
\begin{bmatrix}
h_{11} \\ h_{12} \\ h_{13} \\ h_{21} \\ h_{22} \\ h_{23} \\ h_{31} \\ h_{32} \\ h_{33} 
\end{bmatrix}
=
\begin{bmatrix}
0 \\ \vdots \\ \vdots \\ \vdots \\ \vdots \\ \vdots \\\vdots \\ \vdots \\ 0
\end{bmatrix}
```

Finally, Singular Value Decomposition (SVD) is used to obtain the homography matrix $H$. The matrix $H$ is extracted as the solution to the equation $A \mathbf{h} = 0$, where $\mathbf{h}$ is the vector of the elements of the homography matrix.
```math
A = U S V^T
```

- $U$ is a $2n \times 2n$ unitary matrix.
- $S$ is a $2n \times 9$ diagonal matrix with non-negative real values arranged in descending order down the diagonal.
- $V$ is a $9 \times 9$ unitary matrix.

```math
A =
\begin{bmatrix}
U_{1,1} & \cdots & U_{1,2n} \\
⋮ & ⋱ & ⋮ \\
U_{2n,1} & \cdots & U_{2n,2n}
\end{bmatrix}
\begin{bmatrix}
𝑠_1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 𝑠_2 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 𝑠_3 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 𝑠_4 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 𝑠_5 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 𝑠_6 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 𝑠_7 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 𝑠_8 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 𝑠_9 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
\vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 
\end{bmatrix}
\begin{bmatrix}
V_{1,1} &  \cdots &  V_{1,9} \\
⋮ & ⋱ & ⋮ \\
V_{9,1} &  \cdots &  V_{9,9}
\end{bmatrix}
```


The diagonal elements of $S$, $s_1, s_2, s_3, s_4, s_5, s_6, s_7, s_8, s_9$, are called the singular values of the decomposed matrix $A$. Each singular value $s_i$ has an associated column vector $u_i$ in $U$ called a left singular vector of $A$ and a dedicated row vector $v_i^T$ in $V^T$, called a right singular vector.

The unit singular vector corresponding to the smallest singular value is the solution $h$. The solution is the eigenvector of $A^T A$ associated with the smallest eigenvalue, which is the right singular vector $v_k$ associated with the smallest singular value $s_k = \text{min}(s_1, \ldots, s_9)$.

If the resulting homography transformation for the corresponding point sets is exact, the value of $s_k$ is zero. This is generally the case when the homography is calculated from 4 corresponding point pairs, which is the required minimum number to solve for the eight degrees of freedom. If more than 4 point pairs are involved, the system is overdetermined (which is the usual case). Here, the value of $s_k$ indicates the residual or “goodness of fit” of the resulting homography.

In the case of an # overdetermined system #, the obtained solution minimizes the equation $A h = 0$ in the least-squares sense. Here, instead of demanding an exact solution, one attempts to find an approximate solution, namely a vector \$h$ that minimizes a suitable cost function. The question that naturally arises then is: what should be minimized? Clearly, to avoid the solution $h = 0$ an additional constraint is required. Generally, a condition on the norm is used, such as $ \|h\| = 1$. The value of the norm is unimportant since \( h \) is only defined up to scale.

```math
\text{minimize} \, \| A h \| \, \text{such that} \, \| h \| = 1
```
Which is equivalent to
```math
\text{minimize} \, h^T A^T A h \, \text{such that} \, \| h \| = 1
```

The solution is the (unit) eigenvector of $A^T A$ with the least eigenvalue. Equivalently, the solution is the unit singular vector corresponding to the smallest singular value of $A$.

For further details, one may refer to *Multiple View Geometry in Computer Vision*, Second Edition, Section 4.1 Estimation – 2D Projective Transformations, page 88. Or *Zhang's Camera Calibration Algorithm: In-Depth Tutorial and Implementation* Section 3.2.1.


### Homography Matrix Estimation

For each pair of corresponding points, we derive two linear equations. We need at least 4 pairs of points to compute the homography matrix between two images. In practice, more than 4 pairs are used to account for noise.

### Singular Value Decomposition (SVD)

To compute the homography matrix, we solve the equation $Ah = 0$ using SVD, where:

```math
A h = 0
```

The matrix $A$ has dimensions $2n \times 9$ for $n \geq 4$ correspondences. The solution for the homography matrix is obtained as the right singular vector corresponding to the smallest singular value of $A$.

### Decomposition of the Homography Matrix

Given a homography matrix $H$ and intrinsic camera matrix $K$ , we can decompose $H$ into rotation matrices $R$, translation vectors $t$, and plane normals $n$ as:

```math
H = K(R + \frac{1}{d} t n^T) K^{-1}
```

,where $d$ is the distance from the plane to the camera.

#### Faugeras SVD-Based Decomposition

The matrix $H$ can be decomposed as:

```math
H = U \Lambda V^T
```

Where $U$ and $V$ are orthogonal matrices, and $\Lambda$ is a diagonal matrix containing the singular values of $H$.

### References

- Multiple View Geometry in Computer Vision, 2nd Edition, Section 4.1.
- Zhang's Camera Calibration Algorithm: In-Depth Tutorial and Implementation, Section 3.2.1.
