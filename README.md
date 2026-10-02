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

## Under the Hood: The Math

### 1. 3D Rotation Matrices
To spin points around the origin, the engine applies standard rotation formulas across the X, Y, and Z axes. For instance, rotating around the X-axis looks like this:

$$x' = x$$
$$y' = y \cdot \cos(\theta) - z \cdot \sin(\theta)$$
$$z' = y \cdot \sin(\theta) + z \cdot \cos(\theta)$$

### 2. Perspective Projection
To flatten 3D coordinates onto a 2D text grid, we use a basic perspective division. Pushing the Z-coordinate forward and scaling X and Y inversely by depth creates the illusion of distance:

$$\text{projX} = \left(\frac{x \cdot \text{aspect}}{\text{z}}\right) \cdot \text{fov} \cdot \left(\frac{\text{cols}}{2}\right) + \left(\frac{\text{cols}}{2}\right)$$

### 3. Flat Shading & Z-Buffering
Every polygon is split into triangles for rendering:
- A **Z-buffer** tracks depth so closer triangles correctly block background ones.
- A surface normal vector $\vec{N}$ is calculated using the cross product of the triangle's edges.
- **Lighting** is figured out using the dot product between that surface normal and a directional light vector:
  $$\text{Intensity} = \vec{N} \cdot \text{LightDir}$$
- That final number picks out an ASCII character from a brightness ramp like `.,-~:;=!?*#$@` to shade the surface.
