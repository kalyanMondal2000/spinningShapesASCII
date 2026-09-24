# Solid ASCII 3D Engine

A lightweight, high-performance 3D graphics rendering engine built from scratch in vanilla JavaScript and HTML5 Canvas. It renders solid-shaded polygons, wireframes, and vertex nodes entirely using ASCII characters in real-time.

---

## Features

- **Custom 3D Geometries**: Switch between a Cube, Square Pyramid, Icosahedron, and Donut (Torus) generated procedurally via mathematical parametric equations.
- **Solid Shading & Depth Testing**: Features triangle rasterization combined with a Z-buffer to correctly handle hidden surface removal and flat shading based on a directional light source.
- **Wireframe & Node Overlays**: Toggleable wireframe outlines and vertex markers.
- **Interactive Controls**: Click and drag (or touch and drag) anywhere on the canvas to rotate the 3D shapes freely in real-time.
- **Math & Code Documentation**: Built-in modal window breaking down the core mathematics (rotation matrices, perspective projection, and rasterization shading) and how they are integrated into the source code.
- **Clean Minimalist UI**: Sleek monochromatic dark interface designed for readability and focus.

---

## Mathematical Architecture

### 1. 3D Rotation Matrices
Points in 3D space are rotated around the origin using standard rotation matrices across the X, Y, and Z axes. For example, rotation around the X-axis:
$$x' = x$$
$$y' = y \cdot \cos(\theta) - z \cdot \sin(\theta)$$
$$z' = y \cdot \sin(\theta) + z \cdot \cos(\theta)$$

### 2. Perspective Projection
To project 3D coordinates onto a 2D text grid, the engine applies a perspective division formula. Z-coordinates are shifted forward by a camera distance factor, and X/Y coordinates are scaled inversely relative to depth ($z$):
$$\text{projX} = \left(\frac{x \cdot \text{aspect}}{\text{z}}\right) \cdot \text{fov} \cdot \left(\frac{\text{cols}}{2}\right) + \left(\frac{\text{cols}}{2}\right)$$

### 3. Flat Shading & Z-Buffering
Polygon faces are broken down into triangles. 
- A **Z-buffer** ensures pixels closer to the virtual camera overwrite pixels further away.
- A surface normal vector $\vec{N}$ is computed using the cross product of the triangle's edges.
- **Lighting intensity** is calculated via the dot product of the surface normal and a directional light vector:
$$\text{Intensity} = \vec{N} \cdot \text{LightDir}$$
- The resulting scalar value selects an ASCII density character from the ramp: `.,-~:;=!?*#$@`.

---