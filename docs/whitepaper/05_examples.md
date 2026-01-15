# Chapter 5: Reasoning Use Cases & Inference Patterns

## 5.1 Introduction
This chapter illustrates the application of the UnReLa Core and Semantic Layer through three fundamental reasoning patterns: **Validation**, **Inference**, and **Qualitative Assessment**. In these examples, the reasoning agent (LLM) interacts with a **Probabilistic Scene Graph (PSG)**.

---

## 5.2 Case 1: PSG Validation (The Spatial Auditor)
**Objective:** Verify if a fully labeled and measured scene is physically and ontologically consistent.

**Scenario:** A warehouse monitoring system reports a "Heavy Crate" ($R_1$) resting on a "Cardboard Box" ($R_2$).

**Execution Flow:**
1.  **Metric Query:** The agent queries $d_{bb}(R_1, R_2)$ and $Alignment(A_1, A_2)$.
2.  **Observation:** $d_{bb} \approx 0$ (Contact) and $Alignment \approx 1$ (Vertical stacking).
3.  **Constraint Lookup:** Ontology Rule: `Support(A, B) -> Size(A) >= Size(B)` (Mass/Inertia proxy).
4.  **Algebraic Result:** Query returns $Size(R_1) = 5.0m^3$, $Size(R_2) = 0.5m^3$.

**Outcome:**
> **Status:** **CRITICAL DISSONANCE**.
> **Logic:** The supported object ($R_1$) exceeds the supporting object ($R_2$) by an order of magnitude.
> **Inference:** Flagged as "Structural Failure" or "Misclassification."



---

## 5.3 Case 2: Data In-filling (The Diagnostic Inference)
**Objective:** Identify a missing label or attribute based on surrounding spatial context and topological constraints.

**Scenario:** In a surgical PSG, `Region_X` is detected with high geometric precision but no label. It is held by a `Forceps` near the `Liver`.

**Execution Flow:**
1.  **Topology Query:** Agent queries $b_n(Region_X)$. Result: $b_1=1$ (The object is a loop).
2.  **Metric Query:** Agent queries $d_{bb}(Region_X, Liver)$. Result: $0.005m$ (Adjacency).
3.  **Ontology Filter:** * `Gallbladder`: Violates $b_1$ (is a cavity $b_2$, not a loop $b_1$).
    * `Suture`: Matches $b_1$ and proximity context.
    * `Vessel_Clip`: Matches $b_1$ but violates expected $Size$ (too large).

**Outcome:**
> **Status:** **LABEL INFERRED**.
> **Result:** `Region_X` is assigned `Label: Suture` with `Confidence: 0.88`.

---

## 5.4 Case 3: Qualitative Assessment (The Expectation Generator)
**Objective:** Predict the expected distribution of measurements in a standard, correctly labeled scene.

**Scenario:** A "Dining Room" ontology is loaded. Labels are confirmed: `Table`, `Chair`, `Floor`. No metrics have been measured yet.

**Execution Flow:**
1.  **Inverse Reasoning:** The agent projects the "Functional Intent" of the labels into the UnReLa algebra.
2.  **Constraint Generation:**
    * **Table Height:** $Clearance(R_{top}, R_{floor})$ should return an $Interval[0.7m, 0.9m]$.
    * **Stability:** $Alignment(A_{table}, \vec{v}_{gravity})$ should return $Dirac(1.0)$.
    * **Proximity:** $Lift(d_{bb}, \{Chairs, Table\})$ should return a $Gaussian(\mu=0.2m, \sigma=0.5m)$.

**Outcome:**
> **Status:** **EXPECTATION TEMPLATE GENERATED**.
> **Utility:** This template is used to detect anomalies. If the real-time $d_{bb}$ query returns $2.5m$, the agent flags "Disorganized Scene" despite labels being correct.



---

## 5.5 Summary of Reasoning Modes

| Mode | Input | Process | Output |
| :--- | :--- | :--- | :--- |
| **Validation** | PSG (Full) | Constraint Check | Dissonance Score |
| **Inference** | PSG (Partial) | Filter by Topology/Adjacency | Label/Attribute Candidates |
| **Assessment** | Ontology (Labels) | Algebra Projection | Expected Value Distributions |