# Chapter 4: The Formal Spatial Ontology

## 4.1 Definition
An **UnReLa Spatial Ontology ($\mathcal{O}$)** is the formal specification that constrains the infinite possibility space of the algebra into a domain-specific reality. It acts as the "Source of Truth" that a reasoning engine (LLM) uses to validate, infer, or predict the state of a Scene Graph.

A complete Ontology is defined as a triplet:
$$\mathcal{O} = \{ \mathcal{C}, \mathcal{P}, \mathcal{K} \}$$

* **$\mathcal{C}$ (Class Hierarchy):** The set of allowed semantic labels and their inheritance relationships.
* **$\mathcal{P}$ (Predicate Library):** The mapping of Algebraic Primitives to symbolic, human-readable relations.
* **$\mathcal{K}$ (Knowledge Constraints):** The strict ruleset governing "valid" or "expected" states.

---

## 4.2 The Predicate Library ($\mathcal{P}$)
The Predicate Library defines how numerical results (Scalars, Vectors, or Fields) from the **Operator Interface** are interpreted as symbolic relations. Each predicate is defined by a primitive and a threshold ($\epsilon$).

### I. Proximity Predicates
* **Touching($R_A, R_B$):** True if $d_{bb}(R_A, R_B) < \epsilon_{touch}$
* **Proximal($R_A, R_B$):** True if $d_{bb}(R_A, R_B) \in [\epsilon_{touch}, \epsilon_{far}]$

### II. Directional Predicates
* **Above($R_A, R_B$):** $Alignment(A_{up}, \nabla\Phi_B) \approx 1$ AND $Clearance(R_A, R_B, \vec{v}_{down}) > 0$.
* **Parallel($R_A, R_B$):** The dot product of primary axes $A_{i} \cdot A_{j} \approx 1$.



---

## 4.3 Knowledge Constraints ($\mathcal{K}$)
Constraints are the "Logic Core" of the ontology. They are formulated as **Integrity Axioms** that the geometric data must satisfy to be considered semantically valid.

### I. Existence Axioms (Unary Constraints)
"If Region $R$ is labeled `Class_X`, it MUST possess specific geometric or topological signatures."
* *Example:* If $R$ is a `Vessel`, it must satisfy $b_1 \geq 1$ (it must have a lumen/hole).

### II. Relational Axioms (Binary Constraints)
"If $R_A$ is `Class_X` and $R_B$ is `Class_Y`, their interaction must satisfy specific primitives."
* *Example:* If $R_A$ is a `Suture` and $R_B$ is `SoftTissue`, the ontology may require $\text{Intersection}(R_A, R_B) \neq \emptyset$.

### III. Stability Axioms (n-ary Constraints)
"Rules governing the equilibrium of a set $S$."
* *Example:* In a gravity-bound ontology, an object is `Stable` only if $d_{bb} \approx 0$ relative to a `Support` object located along its downward normal vector.

---

## 4.4 Calibration and Scale ($\epsilon$)
Ontologies are scale-dependent. The same Algebraic Primitive ($d_{bb}$) requires different thresholds based on the domain of application. The Ontology must provide a **Calibration Vector** to map these symbols to the physical coordinate system.

* **Micro-Scale (Pathology):** $\epsilon_{touch} = 10^{-7}m$.
* **Macro-Scale (Urban Planning):** $\epsilon_{touch} = 10^{-1}m$.

---

## 4.5 The Dissonance Function
The ultimate utility of the Formal Ontology is to calculate **Dissonance ($D$)**. When a Scene Graph ($\mathcal{G}$) is compared against an Ontology ($\mathcal{O}$), $D$ represents the sum of all violated constraints, weighted by the uncertainty/confidence ($UQ$) of the measurements.

$$D(\mathcal{G}, \mathcal{O}) = \sum \text{Violations}(\mathcal{K}) \times \text{Confidence}(Q)$$

* **Low Dissonance:** The scene is "Normal" and "Understood."
* **High Dissonance:** Indicates a physical fault, a misclassification, or a sensor error requiring intervention.