# Chapter 2: The Operator Interface (Unary to n-ary)

## 2.1 The Principle of Functional Pluralism
UnReLa does not prescribe a fixed library of physical measurements or spatial relations. Instead, it defines a **Functional Interface** $\mathcal{F}(R_1, \dots, R_n)$ that allows one or more Regions to be queried for properties or interactions.

An implementation is UnReLa-compliant if it provides a mechanism to map a set of Regions to a result set, even if that set is empty. We distinguish between the **Mechanism** (the ability to query) and the **Library** (the specific functions available). This ensures the language remains ontology-agnostic, capable of supporting anything from molecular biology to galactic mapping.

---

## 2.2 Arity of Operators
The algebra supports operators of any degree. Arity is determined by the number of Region inputs required to produce a result:

1.  **Unary ($\mathcal{U}$):** Operates on a single Region $R$. These describe intrinsic properties (e.g., $Area(R)$).
2.  **Binary ($\mathcal{B}$):** Operates on a pair $(R_A, R_B)$. These describe relative properties (e.g., $Distance(R_A, R_B)$).
3.  **n-ary ($\mathcal{N}$):** Operates on an arbitrary set or sequence $\{R_1, \dots, R_n\}$. These describe collective properties (e.g., $MutualDisjointness(S)$).

---

## 2.3 The "Lift" Mechanism (Scaling Arity)
"Lifting" is the formal process of applying a lower-arity operator to a higher-arity set. This allows UnReLa to remain **count-invariant**, treating a scene with 2 objects or 2,000 objects using the same fundamental logic.

### I. The Pairwise Lift ($\mathcal{B} \to \mathcal{N}$)
A Binary operator is "lifted" to a set of regions $S$ by applying the operator to all unique pairs within that set.
$$\text{Lift}(\mathcal{B}, S) = \{ \mathcal{B}(R_i, R_j) : \forall R_i, R_j \in S, i \neq j \}$$
This creates a combinatorial map of interactions (e.g., all distances between all chairs in a room).

### II. The Functional Lift ($\mathcal{U} \to \mathcal{N}$)
A Unary operator is "lifted" to a set to determine the properties of every individual member.
$$\text{Lift}(\mathcal{U}, S) = \{ \mathcal{U}(R_1), \dots, \mathcal{U}(R_n) \}$$

### III. Aggregation ($\sigma$)
Once an operator is lifted, the resulting set of outputs is passed to an **Aggregator** to return a single result. Common aggregators include:
* **Statistical:** $\sigma_{mean}, \sigma_{min}, \sigma_{std\_dev}$ (e.g., "The average distance between objects").
* **Logical:** $\sigma_{and}, \sigma_{or}$ (e.g., "Are *all* objects in this set touching?").



---

## 2.4 Return Type Constraints
To ensure composability, every operator must return one of the following primitive types. This allows the output of one operator to serve as the input for another.

| Type | Symbol | Description | Example |
| :--- | :--- | :--- | :--- |
| **Scalar** | $S$ | A single numerical value. | $Volume$, $Temperature$, $Distance$. |
| **Vector** | $V$ | A magnitude + direction. | $Centroid$, $SurfaceNormal$. |
| **Tensor** | $T$ | Multi-dimensional arrays. | $InertiaTensor$, $StressTensor$. |
| **Field** | $\mathcal{F}$ | A function $p \to Value$. | $SDF$, $GradientField$. |
| **Region** | $R$ | A derived Region Tuple. | $Union$, $Intersection$, $MorphedRegion$. |
| **Predicate**| $P$ | A Boolean truth value. | $IsContained$, $IsTouching$. |

**Algebraic Closure:** Because an operator can return a **Region ($R$)**, UnReLa supports recursive nesting. $\mathcal{U}(\mathcal{B}_{union}(R_1, R_2))$ calculates the properties of a group as if it were a single object.

---

## 2.5 Exemplar Library (Non-Mandatory)
These are possibilities, not requirements. An implementation provides these only if the domain requires them.

### I. Unary Exemplars
* **Measure ($\mu$):** Physical extent (Area, Volume).
* **Topology:** Invariant signatures (Euler Characteristic, Betti Numbers).
* **Centroid ($C$):** The geometric anchor of the region.

### II. Binary Exemplars
* **Metric Distance ($d_{bb}$):** Boundary-to-boundary proximity.
* **Alignment ($\alpha$):** Relative orientation of $R_A$ axes to the $R_B$ gradient field.
* **Containment ($\subset$):** A predicate indicating if $R_A$ exists entirely within $R_B$.

### III. n-ary Exemplars
* **Convex Hull:** A derived Region representing the smallest convex envelope containing all regions in a set.
* **Centrality:** Identifying which Region in a set is closest to the collective centroid.

---

## 2.6 Availability and Null-Logic
UnReLa is designed for reasoning under uncertainty. **Any operator can return `NULL`.**

* **The Meaning of `NULL`:** A result is `NULL` if the property is non-existent (e.g., the "Principal Axis" of a sphere) or the data is insufficient to compute it.
* **Strict Propagation:** If any required input of an operator is `NULL`, the output is `NULL`. 
    * *Example:* If $Centroid(R_A)$ is `NULL`, then $Distance_{cc}(R_A, R_B)$ is automatically `NULL`.
* **Precision over Guessing:** It is better for a reasoning agent to receive `NULL` and handle the "unknown" than to receive an approximated or "guessed" value that violates the algebra.

---

## 2.7 The "Query-First" Execution Protocol
Reasoning agents (LLMs or Solvers) must follow this execution flow to ensure logical integrity:

1.  **Selection:** Identify the target Region(s).
2.  **Arity Match:** Select an operator of appropriate arity (or apply a **Lift**).
3.  **Invocation:** Execute $\mathcal{F}(R_1, \dots, R_n)$.
4.  **Null-Check:** If the result is `NULL`, trigger an "Undefined" reasoning branch.
5.  **Inference:** If typed, apply the result to the high-level ontology (e.g., "If $d_{bb} < 0.1$, then the objects are 'Touching'").