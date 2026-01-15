# Chapter 1: The Region Tuple ($R$)

## 1.1 Philosophy of Representation
In traditional computer vision and spatial modeling, objects are often represented as discretized approximations: pixel masks, point clouds, or meshes. While useful for rendering, these representations are **implementation-dependent**. 

**UnReLa** (Universal Relation Language) treats an object not as a collection of data points, but as a formal **Region ($R$)**. A Region is a continuous mathematical manifold defined by its internal properties and its boundary. This allows the algebra to remain valid whether the underlying data is a high-resolution MRI, a LiDAR scan, or a symbolic CAD model.

---

## 1.2 The Formal Definition
A Region $R$ is defined by the 4-tuple:

$$R = (O, g, \Phi, A)$$

### I. Origin ($O$)
The **Origin** is a distinguished point $O \in \mathbb{R}^n$ that serves as the local $(0,0,0)$ reference for the region. 
* It is usually, but not necessarily, the geometric **Centroid**. 
* It provides the "anchor" for translating intrinsic coordinates to a global scene frame.

### II. Metric Tensor ($g$)
The **Metric Tensor** $g$ defines the geometry of the space within and immediately surrounding the region. 
* **Euclidean Default:** In most cases, $g$ is the identity matrix $\delta_{ij}$, representing flat space where the shortest distance between two points is a straight line.
* **Non-Euclidean Extension:** By defining $g$, UnReLa can operate on curved manifolds (e.g., analyzing the relationship between cells on the surface of a spherical organ).

### III. Boundary Rule ($\Phi$)
The **Boundary Rule** is a membership function that determines the spatial extent of the region. It is typically expressed as an implicit function:
$$\Phi(p) : \mathbb{R}^n \to \mathbb{R}$$
* **Inside:** $\Phi(p) < 0$
* **Boundary:** $\Phi(p) = 0$
* **Outside:** $\Phi(p) > 0$

This formulation is equivalent to a **Signed Distance Function (SDF)**. It is the most critical component of the tuple, as all distance-based relations ($d_{bb}$) are derived from $\Phi$.

### IV. Principal Axes ($A$)
**Principal Axes** $A = \{ v_1, v_2, \dots, v_n \}$ are a set of orthogonal vectors derived deterministically from the region's geometry.
* These are typically the **Eigenvectors** of the Inertia Tensor.
* They define the "Orientation" of the object without requiring an arbitrary external "Up" or "Forward" vector.

---

## 1.3 The Complement and the World
Every Region exists within a **World ($W$)**. 
* The **Interior** is the set of points where $\Phi(p) < 0$.
* The **Exterior** or **Void** is the set of points where $\Phi(p) > 0$.

In UnReLa, the "Void" is not merely empty space; it is a derived region that can itself be subject to unary and binary operators (e.g., calculating the volume of the space *between* two objects).

---

## 1.4 Scale and Dimensionality
UnReLa is **Dimension-Agnostic**. The tuple $R$ functions identically in:
* **2D:** Areas, perimeters, and planar alignments.
* **3D:** Volumes, surface areas, and spatial orientations.
* **$N$-Dimensional:** Abstract feature spaces or temporal-spatial manifolds.

---

## 1.5 Closure Property
The output of a Set Operator (Union $\cup$, Intersection $\cap$, Difference $\setminus$) on two or more Regions MUST result in a valid Region Tuple or a **Null Region**. This ensures that complex compound objects (e.g., a "table" made of five "cylinders") can be treated as a single $R$ for higher-order reasoning.