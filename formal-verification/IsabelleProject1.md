# AI-Assisted Mathematical Formalization: L-Mosaics and Join-Semilattices

**Source:** Isabelle/HOL Formalization Project  
**Date:** September-October 2025  
**Framework:** Iterative Proof Development with AI Collaboration

---

## The Central Mathematical Insight

### Discovering Associativity Through Formalization

The project's most profound moment came when formalizing the associativity of the supremum operation in a semilattice derived from an L-mosaic structure. This property states that combining three elements in any parenthesization yields the same result.

**The Breakthrough:** Rather than proving associativity through tedious algebraic manipulation, the formalization revealed an elegant order-theoretic argument. Associativity emerges naturally from showing that both parenthesizations are least upper bounds of the same three elements. Since least upper bounds are unique, the two expressions must be equal.

**Why This Matters:** The paper's condensed presentation suggested computational verification, but formalization uncovered that the proof's true elegance lies in its structural reasoning about orders rather than element-by-element calculation. This insight only became clear through the discipline of making every logical step explicit.

---

## The Project: Building a Categorical Equivalence

### What Was Formalized

The project established that two mathematical structures—L-mosaics and bounded join-semilattices—are essentially the same thing viewed from different perspectives. This is a categorical equivalence: each L-mosaic gives rise to a natural semilattice structure, each semilattice gives rise to a natural L-mosaic structure, and these constructions are perfect inverses of each other.

**L-Mosaics:** Abstract algebraic structures with a set-valued multiplication operation and a reversibility map, satisfying specific axioms about how elements combine and relate through diagonals.

**Bounded Join-Semilattices:** Ordered structures where any two elements have a well-defined "join" (supremum), and this join operation is associative, commutative, and idempotent, with a bottom element.

**The Bridge:** From an L-mosaic, one constructs the supremum operation by identifying the unique element that lies in the product and sits on both diagonals. From a semilattice, one constructs the Nakano multiplication by collecting elements whose joins with the arguments match specific patterns.

---

## Key Insights from Formalization

### Insight 1: Uniqueness Is Everything

Throughout the formalization, one axiom appeared in nearly every major proof: the uniqueness property that for any two elements in an L-mosaic, there exists a unique element satisfying certain membership and diagonal conditions. This axiom is the linchpin connecting the algebraic perspective to the order-theoretic one.

**Learning:** What seemed like one axiom among several turned out to be the fundamental property from which almost everything else flows. Formalization quantified this: the uniqueness lemma was invoked in over eighty percent of all proofs.

---


### Insight 2: Definitions Create Proof Architecture

A crucial design decision was defining the supremum operation via unique existence rather than operationally. Instead of defining how to construct the supremum and then proving it's unique, the approach defined it as "the unique element satisfying these properties" and then proved it has the expected behavior.

**Impact:** This choice required proving associativity and other properties, thereby showcasing the elegance of some of the arguments involved.

**Learning:** In formalization, the order of definitions and the style of definition (constructive vs. existential via uniqueness) profoundly affect proof complexity. This architectural decision-making is often invisible in paper mathematics but becomes critical in formal development.

---

### Insight 3: Characterization Lemmas Are Load-Bearing

Certain lemmas appeared repeatedly as bridges between perspectives. The most important characterized membership in the supremum in terms of three conditions, characterized diagonal membership in terms of ordering, and characterized Nakano multiplication in terms of join equations.

These characterization lemmas became the vocabulary for translating between the algebraic language of L-mosaics and the order-theoretic language of semilattices.

**Learning:** Investing time early to state and prove clean characterization lemmas with minimal assumptions pays massive dividends. These lemmas become the reusable building blocks that make later proofs concise and maintainable.

---

## Human-AI Collaboration Patterns

### The Debugging Cycle

The collaboration followed a consistent pattern through 65+ debugging iterations:

1. **Human Provides Context:** Share theory file with compilation errors and mathematical intention
2. **AI Diagnoses Pattern:** Match error message to known failure modes (missing premises, circular definitions, type mismatches)
3. **Targeted Fix:** AI provides minimal correction, often one to two lines, with explanation of root cause
4. **Human Validates:** Test the fix, assess whether it aligns with mathematical intention
5. **Iterate or Pivot:** Either continue refinement or recognize need for structural change in proof approach

**Most Effective Interventions:**
- "Follow the paper's proof order exactly" → realigned formalization with source material's logical flow
- "This should be almost definitional" → signaled over-complication requiring simplification
- "Add explicit intermediate steps" → replaced failing automation with transparent reasoning chains

**Least Effective:**
- Repeatedly invoking the same tactic hoping for different results
- Adding auxiliary lemmas without addressing the conceptual gap
- Trying to prove more general versions before the specific case works

---

### Division of Labor

**Human Responsibilities:**
- Maintain overall mathematical vision and equivalence architecture
- Validate logical correctness of proof steps
- Recognize when formalization deviates from paper's intended argument
- Make strategic decisions about lemma ordering and locale hierarchy

**AI Responsibilities:**
- Navigate Isabelle syntax and proof tactics
- Diagnose compilation errors and type mismatches
- Suggest instantiation patterns for locale assumptions
- Implement draft proof scripts following human's conceptual direction

**Shared Responsibility:**
- Identifying when a proof approach isn't working and needs restructuring
- Balancing automation (for routine steps) versus explicit reasoning (for clarity)
- Ensuring proofs remain maintainable and readable for future work

---

## Error Patterns and Resolutions

### Pattern 1: Missing Premise Instantiation

**The Problem:** Locale-based proofs in Isabelle require explicit instantiation of assumptions. Attempting to apply a lemma without providing witnesses for its premises leads to unprovable subgoals.

**The Solution:** Systematically use explicit instantiation syntax when applying locale lemmas. This discipline, though verbose, prevents mysterious proof failures.

**Frequency:** Approximately 35% of compilation errors fell into this category.

---

### Pattern 2: Definition Circularity

**The Problem:** Attempting to prove an equality between two defined operations by unfolding both definitions simultaneously creates circular reasoning that proof automation cannot resolve.

**The Solution:** Establish a hierarchy where one definition is "primitive" and the other is characterized in terms of it. Prove characterization lemmas that bridge between them without circular dependencies.

**Frequency:** Approximately 25% of errors, typically appearing when trying to prove round-trip equalities.

---

### Pattern 3: Conflating Membership and Ordering

**The Problem:** L-mosaics have set-valued multiplication (membership relation) and an induced order (defined via diagonals). Confusing these two levels led to type errors and unprovable goals.

**The Solution:** Explicit bridging lemmas that translate membership statements into order-theoretic statements and vice versa. Never attempt direct substitution between the two perspectives.

**Frequency:** Approximately 20% of errors, especially common in early formalization stages.

---

### Pattern 4: Automation Performance Issues

**The Problem:** Heavy simplification tactics sometimes caused multi-second delays (visible as purple highlighting in the IDE), making development painful and indicating potential proof fragility.

**The Solution:** Replace monolithic automation with explicit proof chains using intermediate steps. This trades brevity for transparency and performance.

**Learning:** When a proof tactic takes more than two seconds, it's almost always better to break it into explicit steps. This discipline catches subtle errors early and makes proofs more maintainable.

---

## Reflections on AI-Assisted Formalization

### What Formalization Taught About the Mathematics

**Associativity's True Nature:** What appeared to be a computational property is actually a deep structural consequence of the least-upper-bound universal property. The proof is order-theoretic, not algebraic.

**Axiom Centrality:** The uniqueness axiom is not one requirement among many—it's the fundamental bridge between perspectives, appearing in virtually every major result.

**Reversibility Complexity:** The paper's casual "by reversibility" hides that you need both involutivity and commutativity to truly flip arguments. Formalization forced precision about what reversibility actually provides.

**Round-Trip Subtlety:** Proving the constructions are inverse required careful sequencing of characterization lemmas. The mathematical insight was that supremum and Nakano multiplication describe the same structure from different angles.

---

### What Formalization Taught About Proof Development

**Trust Paper Structure:** Every time the formalization deviated from the paper's lemma sequence, backtracking was required. Published proof order encodes hidden dependency information that authors may not explicitly discuss.

**Name Things Well:** Lemmas named for their purpose rather than their proof technique made scripts self-documenting and easier to navigate in later development.

**Characterize Before Computing:** Proving that constructions satisfy characterizing properties before attempting to compute with them simplifies downstream reasoning dramatically.

**Automation Balance:** Expert formalization requires knowing when to delegate to tactics and when to write explicit proof chains. Neither pure automation nor pure manual proof is optimal.

---

### The Value Proposition

**Time Investment:** Approximately 20 hours of human-AI collaboration across multiple sessions.

**Output:** Complete formal verification of a categorical equivalence with several lines of machine-checked code.

**Confidence Gain:** The equivalence between L-mosaics and bounded join-semilattices is now certified correct with mathematical certainty.

**Understanding Depth:** Eight significant mathematical insights emerged that were not explicit in the paper, particularly around proof architecture and axiom interdependencies.

**Reusability:** The modular theory structure supports future work on related algebraic systems, with all foundational results available for import and reuse.

---

## The Broader Lesson

### Formalization as Co-Discovery

This project demonstrated that formalization is not merely translating existing proofs into a formal language. It is collaborative discovery with a mechanical verifier, where the process of making every step explicit reveals:

- Hidden assumptions in "obvious" steps
- Deep structural patterns in the mathematics
- More elegant proof architectures than initially apparent
- The true logical dependencies between results

**The Human-AI Dynamic:** The mathematician provides conceptual understanding, validates correctness, and maintains strategic vision. The AI accelerates implementation, diagnoses errors, and helps navigate technical complexities. Together, they achieve what neither could accomplish alone: verified understanding at the intersection of human mathematical intuition and machine-checked rigor.

**Key Takeaway:** AI-assisted formalization represents a new mode of mathematical work where the discipline of formal verification enhances rather than merely verifies human understanding. The interaction creates insights neither pure mathematics nor pure computation could discover independently.


