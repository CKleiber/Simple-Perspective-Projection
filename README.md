This is a simple implementation of **perspective projection**. 
I'm using a **left-handed** coordinate system, defined by the right, up and forward axis, with the camera pointing towards the positive forward direction, as well as **homogeneous coordinates** for the calculations. Therefore the matrices are $4\times4$.

## Brief explanation of the individual steps:

### 1. From Model to World Space

Any kind of object has its intrinsic coordinate system (model space). This object can be placed relative to the world origin in the world space by using 3D transformations, such as translation, scaling or rotations. 

Applying such transformations on the vertices (edges, faces) in the model space transforms the object into the world space. 

### 2. From World to View Space

In this step, a camera, defined also by its transformation matrix relative to the world space, should observe the object by modelling perspective projection. 

However, to make thing as simple as possible in the next step, everything in the world space should be transformed, such that the camera is located at the origin, with its viewing direction aligning with the positive forward axis. Therefore, every object needs to be transformed, such that it keeps its relative position to the camera.

This is done by applying the **view matrix** on the vertices in the world space:

$$
\mathbf{V} = 
\begin{pmatrix}
r_1 & r_2 & r_3 & -p_1 \\
u_1 & u_2 & u_3 & -p_2 \\
f_1 & f_2 & f_3 & -p_3 \\
0 & 0 & 0 & 1 \\
\end{pmatrix}
$$

where $\mathbf{r}, \mathbf{u}, \mathbf{f}$ describe the camera axes and $\mathbf{p}$ the camera position in the world space.

### 3. From View to Projection Space

Lastly, it is key to understand that the cone containing all objects visible to the camera, given the field of view parameters, is a **square base frustum**. This shape should then be transformed into **normalized device coordinates** (NDC) $(x, y, z) \in [-1, 1] \times [-1, 1] \times [0, 1]$, i.e. a cuboid, allowing easy projection.

To do this, the projection matrix is defined as

$$
\mathbf{P} = 
\begin{pmatrix}
\tan{(\text{FOV}_h/2)}^{-1} & 0 & 0 & 0 \\
0 & \tan{(\text{FOV}_v/2)}^{-1} & 0 & 0 \\
0 & 0 & \frac{f}{f-n} & \frac{-fn}{f-n} \\
0 & 0 & 1 & 0 \\
\end{pmatrix}
$$

with $\text{FOV}_{h/v}$ describing the horizontal and vertical field of view angles and $f$ and $n$ denoting the near and far clipping planes, dictating what distance range to camera will be rendered. 

This matrix essentially scales the objects relative to their distances to the camera. 

After this transformation, the vectors should be divided by the last component in the homogeneous coordinates. Then, the first two dimensiones denote the 2D projection, while the third dimension contains depth information, relevant for rendering order.
