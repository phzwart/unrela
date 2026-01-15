# Chapter 3: Semantic Mapping and Uncertainty Logic

## 3.1 The Augmented Region (Identity)
The **Reasoning Core** wraps the geometric Region ($R$) in a semantic envelope to provide identity and context. We define an **Augmented Region ($\mathcal{S}$)** as:

$$\mathcal{S} = \{ R, \text{Label}, \text{Attributes} \}$$

* **Label:** The categorical identity (e.g., "Wall", "Valve", "Femur").
* **Attributes:** Domain-specific non-spatial data (e.g., Material: Steel).

---

## 3.2 Flexible Value Representations (UQ)
UnReLa does not prescribe how a measurement is reported. Operators return a **Measured Quantity ($Q$)**, which can be represented in several formats depending on the implementation's sophistication.

### I. Deterministic Scalar ($S$)
The simplest form. A single real-valued number representing the "best guess" or absolute measurement.
* *Example:* $d_{bb} = 0.42m$

### II. Interval Representation ($I$)
Used when the system knows the bounds of error but not the shape of the noise.
* **Confidence/Credible Intervals:** $[v_{min}, v_{max}]$ (e.g., "The distance is between 0.40m and 0.44m").

### III. Probabilistic Distribution ($D$)
The most expressive form, used when a full PDF is available.
* **Distributional:** Represented via parameters (e.g., $\mu, \sigma$) or samples.

### IV. Handling Mixed Types
The UnReLa algebra provides "Collapse" and "Promotion" rules for mixed-type operations:
* **Promotion:** If a Scalar is compared to a Distribution, the Scalar is treated as a Dirac delta function ($\sigma=0$).
* **Collapse:** If a reasoning agent (like an LLM) requires a single decision point from a Distribution, it may "collapse" the value to its mean or median.

---

## 3.3 Semantic Relation Predicates
High-level relations are defined as logical expressions of UnReLa primitives. Because the primitives may include uncertainty, the resulting predicates are **Probabilistic Predicates**.

| Relation | Logic | Confidence Source |
| :--- | :--- | :--- |
| **Touching** | $d_{bb} < \epsilon$ | Percentage of $Q$ below threshold $\epsilon$. |
| **Aligned** | $\alpha > \theta$ | Probability that the axis alignment exceeds threshold $\theta$. |
| **Contained** | $(R_A \cap R_B) \approx R_A$ | Volume ratio of intersection vs. source. |

---

## 3.4 Constraint and Dissonance Logic
The reasoning core uses these values to check the world against **Ontological Constraints**.

* **Constraint:** "An 'Aisle' must have a $Clearance > 1.2m$."
* **The Dissonance Check:** If the measured $Clearance$ (whether scalar or distribution) falls significantly below the constraint, the system flags **Dissonance**.
* **Dissonance Resolution:** High dissonance suggests either:
    1. A physical violation of the rule.
    2. A misclassification (the 'Aisle' is actually a 'Shelf').
    3. High sensor noise (the $Interval$ is too wide to be certain).

---

## 3.5 The Probabilistic Scene Graph
The final output is a graph where:
* **Nodes:** Augmented Regions ($\mathcal{S}$).
* **Edges:** Named relations (e.g., "Supports", "ConnectsTo") with an associated **Truth Value** (a scalar 0-1 or a frequentist confidence).

This structure allows the system to say: *"I am 95% confident the Patient is 'LyingDown' on the Bed,"* while still allowing the underlying math to remain as simple as a scalar $d_{bb}$ calculation if that’s all the data provides.