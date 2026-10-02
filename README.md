# 3D Engine

A custom 3D graphics renderer built completely from scratch using vanilla JavaScript and the HTML5 Canvas API. Instead of traditional pixels, it draws solid-shaded polygons, wireframes, and vertex nodes entirely out of ASCII characters in real time.

---

## What It Does

- **Procedural Geometries**: Generate and switch between a Cube, Pyramid, Icosahedron, and Donut (Torus) on the fly using parametric math equations.
- **Solid Shading & Depth Sorting**: Combines triangle rasterization with a Z-buffer to handle hidden surfaces correctly, adding flat shading driven by a directional light source.
- **Wireframes & Nodes**: Toggleable overlays to view the underlying wireframe mesh and vertex points.
- **Mouse & Touch Controls**: Click and drag anywhere on the canvas to orbit and rotate the shapes smoothly.
- **Built-in Math Guide**: Includes an in-app modal explaining the underlying math—rotation matrices, perspective projection, and shading—alongside the code implementation.
- **Minimal Interface**: A clean, dark-themed UI built to stay out of the way.

---

## Under the Hood: The Mathematical Architecture

### 1. 3D Rotation Matrices
Before projection, local 3D vertex coordinates are rotated around the origin by applying separate rotation matrices for each axis ($\theta_x, \theta_y, \theta_z$):

- **X-Axis Rotation (`rotateX`)**:
  $$x' = x$$
  $$y' = y \cdot \cos(\theta_x) - z \cdot \sin(\theta_x)$$
  $$z' = y \cdot \sin(\theta_x) + z \cdot \cos(\theta_x)$$

- **Y-Axis Rotation (`rotateY`)**:
  $$x' = x \cdot \cos(\theta_y) + z \cdot \sin(\theta_y)$$
  $$y' = y$$
  $$z' = -x \cdot \sin(\theta_y) + z \cdot \cos(\theta_y)$$

- **Z-Axis Rotation (`rotateZ`)**:
  $$x' = x \cdot \cos(\theta_z) - y \cdot \sin(\theta_z)$$
  $$y' = x \cdot \sin(\theta_z) + y \cdot \cos(\theta_z)$$
  $$z' = z$$

### 2. Perspective Projection & Aspect Correction
To simulate a camera moving through 3D space (`distance = 4.2`), the engine translates points forward along the Z-axis, then projects them onto a 2D text grid using a field-of-view (`fov = 1.6`) multiplier:

$$\text{projX} = \left(\frac{x \cdot \text{fontAspectCorrection} \cdot \text{fov}}{z}\right) \cdot \left(\frac{\text{cols}}{2}\right) + \left(\frac{\text{cols}}{2}\right)$$

$$\text{projY} = \left(\frac{y \cdot \text{fov}}{z}\right) \cdot \left(\frac{\text{rows}}{2}\right) + \left(\frac{\text{rows}}{2}\right)$$

*Note on `fontAspectCorrection`: Because monospaced text characters are taller than they are wide ($\text{charWidth} / \text{charHeight}$), horizontal coordinates are scaled dynamically to prevent geometric stretching.*

### 3. Surface Normals & Flat Shading
For each triangle face defined by vertices $v_0, v_1, v_2$, edge vectors $\vec{A} = v_1 - v_0$ and $\vec{B} = v_2 - v_0$ are computed in world space. 
- A **surface normal vector $\vec{N}$** is derived using the cross product:
  $$\vec{N} = \vec{A} \times \vec{B}$$
- This normal vector is normalized to unit length, then evaluated against a static normalized directional light vector $\vec{L} = [0.577, 0.577, -0.577]$ using a **dot product**:
  $$\text{Intensity} = \max\left(0.15, \vec{N} \cdot \vec{L}\right)$$
- The resulting intensity scalar maps directly to an ASCII character index within the brightness ramp: `.,-~:;=!?*#$@`.

### 4. Barycentric Rasterization & Z-Buffering
Triangles are filled onto the grid using a bounding-box scanline approach with **Barycentric coordinates** ($\alpha, \beta, \gamma$):
- For every text cell inside a triangle's bounding box, barycentric weights are computed via edge functions using the denominator determinant:
  $$\text{denominator} = (y_1 - y_2)(x_0 - x_2) + (x_2 - x_1)(y_0 - y_2)$$
- If $\alpha \ge 0$, $\beta \ge 0$, and $\gamma \ge 0$, the text cell lies inside the triangle.
- The exact interpolated depth (`depth = alpha * z0 + beta * z1 + gamma * z2`) is checked against a 2D **Z-buffer** initialized to `Infinity`. If the new depth is closer than what is stored, the Z-buffer is updated and the corresponding ASCII character is drawn.
