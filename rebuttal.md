# Response to Reviewer DjBE

Thank you for your detailed feedback. We appreciate your recognition of our interesting idea of leveraging and improving upon previous trajectories, as well as the performance improvements demonstrated through our experiments and ablation studies. We are also grateful for your positive assessment of the advantages our approach offers in **cross-trajectory information utilization** compared to existing MCTS methods. Below, we address each of your main concerns in detail:


## **Method Implementation Details (For W1)**

### **Case Study: Fixing UnrecognizedUnit.__eq__ in Astropy**

Problem Description: Astropy's UnrecognizedUnit.__eq__ method throws exceptions instead of returning False when compared with None, violating Python's safety conventions for comparison operations.

**Step 1: Revision Operation - Multi-Strategy Trajectory Generation**

First, generate 5 initial trajectories using 5 different planning strategies:

| Prompt Variant | Trajectory Summary | Main Issues |
|----------------|-------------------|-------------|
| P-greedy | `grep→view→quick-edit` | Only handles None, ignores other types |
| P-tests-first | `pytest→search→edit` | Too many test failures |
| P-linter-aware | `flake8→edit→pytest` | Indentation issues, logic still wrong |
| P-defensive | `try-catch→comprehensive` | Overly broad exception handling |
| P-minimal | `single-line→submit` | Incomplete functionality |

Then perform reflection and revision operations on each trajectory to generate corresponding improved versions:

**Example - Evolution of P-greedy trajectory:**
- **Original trajectory issue**: Overly narrow type checking
- **Revised trajectory**: `grep → view → add comprehensive tests → edit(improve type annotations, add exception handling) → pytest`

Output: 10 revised trajectories (original 5 + revised 5) proceed to the next stage.

**Step 2: Refinement.Selection - Quality-Based Trajectory Filtering**

Use evaluation function for quality filtering:
$$\text{Reward}(t, T) = \alpha \cdot \text{TaskCompletion}(t,T) + \beta \cdot \text{ReasoningQuality}(t) + \gamma \cdot \text{Efficiency}(t)$$

Scoring includes two components:
- **LLM scoring**: Reasoning quality and solution reasonableness evaluation
- **Rule-based scoring**: Code completeness, syntax error checking, structural validation

Result: Remove low-quality trajectories, retain 5 for the next step.

**Step 3: Recombination Operation - Cross-Trajectory Knowledge Fusion**

Based on task complexity, orchestrate operator combinations to generate 5 new trajectories:
- **2× Crossover**: Fuse complementary segments from different trajectories  
- **2× Transfer Learning**: Transfer successful patterns to target trajectories
- **1× Restructure**: Global restructuring optimization

**Specific trajectory fusion examples:**

**Crossover #1 - Precise Localization × Comprehensive Testing:**
- Trajectory A: `grep "UnrecognizedUnit" → edit eq → submit` (fast but shallow)
- Trajectory B: `pytest → analyze failures → view test context → comprehensive edit` (slow but thorough)
- Crossover Result: `grep "UnrecognizedUnit" → view test_units.py → analyze eq usage pattern → targeted comprehensive fix`

**Transfer #1 - Exception Handling Pattern Migration:**
- Source: try-except (TypeError, ValueError) pattern from defensive programming trajectory
- Target: Simple None check from greedy trajectory  
- Transfer Result: `None check + multi-type exception catching + graceful degradation`

**Restructure - Global Optimization:**
- Global pattern recognition: All trajectories revolve around the core issue of "type-safe comparison operations"
- Redundancy elimination: Merge duplicate search and localization steps
- Structural reorganization: `Problem understanding → Precise localization → Context analysis → Type-safe design → Comprehensive validation`

Output: Extended pool contains 10 trajectories (original 5 + newly generated 5)

**Iterative Self-Evolution Support**

SE-Agent supports multi-round iterative optimization, continuing the evolution process based on the current trajectory pool:

**Round 2 Evolution (simplified representation):**
- **Step2'**: Reflection ⟨5 trajectories⟩ → Revision ⟨...⟩ → `T2'` (10 trajectories)
- **Step3'**: Quality-Filtering ⟨T2'⟩ → Select ⟨...⟩ → `T3` (5 trajectories)
- **Step4'**: Recombination ⟨T3⟩ → [Crossover×2, Transfer×2, Restructure×1] → `T4` (10 trajectories)

Convergence condition: Stop when consecutive K rounds of highest reward improvement < ε, or reach preset maximum rounds. This iterative mechanism ensures **SE-Agent** can continuously optimize until finding high-quality solutions or meeting stop conditions.

**Step 4: Final Solution Selection**

Select the highest-scoring trajectory from candidates as the final solution:

**Core Improvement**: SE-Agent evolved from single-point failure repair to a **type-safe universal solution**, generating entirely new solution methods through **explicit inter-trajectory interactions** (Crossover, Transfer, Restructure), covering all invalid input scenarios.

**Key Innovation**: This trajectory-level collaboration is **unachievable by traditional methods** like MCTS that rely solely on model capabilities for diversified sampling—they can only generate mutually independent trajectories, **unable to achieve collaborative evolution and knowledge transfer** between trajectories.

## **Ground Truth Leakage Concern (For W2)**

Our evaluation function consists of two components: 

1. **Rule-based reward**, denoted as AutoEval()
2. **Model-based reward**, denoted as ExpertEval()

**Important**: Neither component relies on ground-truth labels. 

To elaborate, **AutoEval()** is a rule-based mechanism designed to assess whether a generated trajectory meets minimal submission criteria. These criteria include, but are not limited to: 
- Ensuring the final patch file is non-empty
- Verifying that the trajectory contains a sufficient proportion of code-editing steps
- Checking whether the trajectory length falls within a reasonable range

This procedure **does not require access to ground-truth labels** or test oracles.

We acknowledge that our use of the term **"test pass rate"** may have led to confusion, as it could be misinterpreted as indicating the use of ground-truth tests. In fact, the "pass rate" refers only to the internal validation of structural constraints necessary for a trajectory to be considered valid and potentially effective—**not whether it actually solves the task**. We will revise this in the final version, modifying it to **"validation rate"** and describing our evaluation function in more detail.

## **Concrete Examples (For W3)**

As shown in the complete example provided in our response to W1, we have illustrated in detail **how each operation specifically modifies and improves the trajectories**. This example comprehensively demonstrates the entire process—from initial trajectory generation, quality evaluation, and cross-trajectory fusion to the selection of the final solution—including **concrete changes in trajectory content** and the resulting improvements.

### **Additional Clarifications**

We will add a dedicated limitation section in the final version to discuss computational overhead and the applicable scope of our method. Our approach has indeed achieved **significant performance gains** (up to 55% relative improvement) and **SOTA results**, which validate the effectiveness of the trajectory-level self-evolution paradigm.


# Response to Reviewer c1xU

Thank you for your detailed feedback. We appreciate your recognition that our three-step mechanism represents a substantial advancement over existing trajectory sampling and MCTS methods. Below, we address each of your main concerns:


## **Clarification on the Term "Self-Evolution" (For W1)**

We understand the reviewer's concerns about our use of the term **"self-evolution."** SE-Agent embodies a **genuine evolutionary principle** that goes beyond individual trajectory sampling:

**1) Addressing the Homogenization Problem:**

Traditional multi-trajectory sampling suffers from severe homogenization—trajectories converge to similar solutions, especially with smaller models. The effective search space remains limited despite multiple samples.

**2) A True Evolutionary Mechanism:**

SE-Agent establishes **systematic information exchange** among trajectories through three core operations:
- **Revision**: Targeted improvement of individual trajectories through self-reflection
- **Recombination**: Transfer of successful reasoning patterns across trajectories, creating novel hybrid solutions
- **Refinement**: Iterative optimization of the trajectory pool based on collective performance

**3) Emergent Collective Intelligence:**

Unlike simple sampling, trajectories in SE-Agent **actively influence and evolve with each other**. Superior partial reasoning strategies propagate within the population, while inferior approaches are eliminated, resulting in **emergent collective intelligence** at the population level.

**Concrete Case Demonstration:**

### **Case Study: Fixing UnrecognizedUnit.__eq__ in Astropy**

**Problem Description**: Astropy's UnrecognizedUnit.__eq__ method throws exceptions instead of returning False when compared with None, violating Python's safety conventions for comparison operations.

**Step 1: Revision Operation - Multi-Strategy Trajectory Generation**

First, generate 5 initial trajectories using 5 different planning strategies:

| Prompt Variant | Trajectory Summary | Main Issues |
|----------------|-------------------|-------------|
| P-greedy | `grep→view→quick-edit` | Only handles None, ignores other types |
| P-tests-first | `pytest→search→edit` | Too many test failures |
| P-linter-aware | `flake8→edit→pytest` | Indentation issues, logic still wrong |
| P-defensive | `try-catch→comprehensive` | Overly broad exception handling |
| P-minimal | `single-line→submit` | Incomplete functionality |

Then perform reflection and revision operations on each trajectory to generate corresponding improved versions:

**Example - Evolution of P-greedy trajectory:**
- **Original trajectory issue**: Overly narrow type checking
- **Revised trajectory**: `grep → view → add comprehensive tests → edit(improve type annotations, add exception handling) → pytest`

**Output**: 10 revised trajectories (original 5 + revised 5) proceed to the next stage.

**Step 2: Refinement.Selection - Quality-Based Trajectory Filtering**

Use evaluation function for quality filtering:
$$\text{Reward}(t, T) = \alpha \cdot \text{TaskCompletion}(t,T) + \beta \cdot \text{ReasoningQuality}(t) + \gamma \cdot \text{Efficiency}(t)$$

Scoring includes two components:
- **LLM scoring**: Reasoning quality and solution reasonableness evaluation
- **Rule-based scoring**: Code completeness, syntax error checking, structural validation

**Result**: Remove low-quality trajectories, retain 5 for the next step.

**Step 3: Recombination Operation - Cross-Trajectory Knowledge Fusion**

Based on task complexity, orchestrate operator combinations to generate 5 new trajectories:
- **2× Crossover**: Fuse complementary segments from different trajectories  
- **2× Transfer Learning**: Transfer successful patterns to target trajectories
- **1× Restructure**: Global restructuring optimization

**Specific trajectory fusion examples:**

**Crossover #1 - Precise Localization × Comprehensive Testing:**
- **Trajectory A**: `grep "UnrecognizedUnit" → edit eq → submit` (fast but shallow)
- **Trajectory B**: `pytest → analyze failures → view test context → comprehensive edit` (slow but thorough)
- **Crossover Result**: `grep "UnrecognizedUnit" → view test_units.py → analyze eq usage pattern → targeted comprehensive fix`

**Transfer #1 - Exception Handling Pattern Migration:**
- **Source**: try-except (TypeError, ValueError) pattern from defensive programming trajectory
- **Target**: Simple None check from greedy trajectory  
- **Transfer Result**: `None check + multi-type exception catching + graceful degradation`

**Restructure - Global Optimization:**
- **Global pattern recognition**: All trajectories revolve around the core issue of "type-safe comparison operations"
- **Redundancy elimination**: Merge duplicate search and localization steps
- **Structural reorganization**: `Problem understanding → Precise localization → Context analysis → Type-safe design → Comprehensive validation`

**Output**: Extended pool contains 10 trajectories (original 5 + newly generated 5)

**Iterative Self-Evolution Support**

SE-Agent supports multi-round iterative optimization, continuing the evolution process based on the current trajectory pool:

**Round 2 Evolution (simplified representation):**
- **Step2'**: Reflection ⟨5 trajectories⟩ → Revision ⟨...⟩ → `T2'` (10 trajectories)
- **Step3'**: Quality-Filtering ⟨T2'⟩ → Select ⟨...⟩ → `T3` (5 trajectories)
- **Step4'**: Recombination ⟨T3⟩ → [Crossover×2, Transfer×2, Restructure×1] → `T4` (10 trajectories)

**Convergence condition**: Stop when consecutive K rounds of highest reward improvement < ε, or reach preset maximum rounds. This iterative mechanism ensures SE-Agent can continuously optimize until finding high-quality solutions or meeting stop conditions.

**Step 4: Final Solution Selection**

Select the highest-scoring trajectory from candidates as the final solution:

**Core Improvement**: SE-Agent evolved from single-point failure repair to a **type-safe universal solution**, generating entirely new solution methods through **explicit inter-trajectory interactions** (Crossover, Transfer, Restructure), covering all invalid input scenarios.

**Key Innovation**: This trajectory-level collaboration is **unachievable by traditional methods** like MCTS that rely solely on model capabilities for diversified sampling—they can only generate mutually independent trajectories, **unable to achieve collaborative evolution and knowledge transfer** between trajectories.

## **Experimental Fairness (For W2)**

We acknowledge using different model versions might affect comparison fairness. We conducted **additional experiments** using the same model across all methods:

**Updated Results with Claude-4-Sonnet:**

| Method                          | Performance on SWE-bench Verified |
|---------------------------------|-----------------------------------|
| SWE-agent + Claude 4 Sonnet     | 66.6%                            |
| Augment Agent v1                | 70.4%                            |
| OpenHands + Claude 4 Sonnet     | 70.4%                            |
| Moatless Tools + Claude 4 Sonnet| 70.8%                            |
| TRAE                            | 75.2%                            |
| **SE-Agent + Claude 4 Sonnet**  | **80.0%**                        |

These results validate our framework's effectiveness, achieving **SOTA performance** on SWE-bench Verified.

## **More Ablation Study of Refinement (For W3)**

To ensure completeness of our ablation analysis, we conducted additional experiments removing the Refinement component:

| Model            | SE-Agent | w/o Revision | w/o Recombination | w/o Refinement | w/o All |
|------------------|----------|--------------|-------------------|---------------|---------|
| DeepSeek-V3      | **54.8%**    | 36.6%        | 44.6%             | 34.0%         | 31.6%   |
| Qwen-2.5-72b     | **38.8%**    | 23.8%        | 28.8%             | 22.4%         | 18.8%   |
| Llama-3.1-70b    | **32.6%**    | 20.4%        | 27.4%             | 19.0%         | 15.4%   |
| GPT-4o           | **40.4%**    | 27.4%        | 33.4%             | 25.2%         | 22.4%   |
| Claude-3.7       | **61.2%**    | 45.6%        | 50.6%             | 47.8%         | 40.6%   |

The results demonstrate that **all three components make significant contributions** to the overall performance of SE-Agent. We will include a detailed discussion of this ablation study in the revised version of the paper.

## **Clarification on "Pilot Trajectories" (For W4)**

"Pilot trajectories" are initial trajectories using different planning strategies:
- **P-greedy**: Rapid localization and fixing
- **P-systematic**: Systematic problem analysis
- **P-defensive**: Defensive programming approach
- **P-minimal**: Minimal modification
- **P-comprehensive**: Comprehensive testing

These provide the **foundation** for evolutionary operations, ensuring **broad solution space coverage**. Baselines lack our cross-trajectory interaction mechanism.

### **Additional Clarifications**

We will correct the typo in line 120 and add a **"Limitations"** section discussing computational overhead and applicable scope. These clarifications demonstrate SE-Agent's **robustness** through genuine trajectory-level evolution **unattainable by traditional sampling methods**.


# Response to Reviewer JbMn

Thank you for reviewing our paper and providing such insightful feedback. We greatly appreciate your recognition of the clarity of our writing, the well-structured mathematical formulations, and the **strong empirical performance** of our proposed framework on the SWE-bench Verified. Furthermore, we have thoroughly addressed your concern as follows:


## **Inference Time Comparison (For W1)**

As **SE-Agent** is designed with efficiency in mind, particularly when compared to frameworks like SWE-Search (based on MCTS), we conduct additional experiments to quantify the average inference time required to solve a single instance successfully. This evaluation focuses on the average wall-clock time per solved case, which we believe is a practical metric for real-world deployment and large-scale adoption. The results are as follows:

| Method               | Time Cost (min) | Resolved Rate (%) |
|----------------------|-----------------|-------------------|
| SWE-Agent            | 15.61           | 40.6              |
| SWE-Search (MCTS)    | 33.42           | 47.4              |
| **SE-Agent (Ours)**  | **31.06**       | **61.2**          |

*All results are based on Claude-3.7-Sonnet model*

**Our findings show that SE-Agent consistently achieves lower inference time compared to SWE-Search(MCTS)**. This confirms the advantage of our method in **avoiding the heavy computational cost** associated with test-time scaling techniques like MCTS. The proposed SE-Agent not only **improves performance** but also enables **efficient exploration** without incurring significant inference overhead. We will include this comparison and discussion in the revised version of the paper to better support our claims regarding SE-Agent's practical efficiency.

Due to time constraints, we are currently only able to provide inference time comparison results based on Claude-3.7-Sonnet. However, we fully recognize the importance of this evaluation and commit to including a comprehensive inference time analysis—covering all relevant baselines—in the revised version of the paper.


# Response to Reviewer aGqC

Thank you for your insightful feedback and recognition of our **strong empirical results** and substantial improvements over baselines on SWE-bench. We address your concerns as follows:

## **The contributions of SE-Agent (For W1)**

While leveraging historical experience is part of our approach, SE-Agent's core contribution lies in **expanding the search space through multi-trajectory interaction**, overcoming single-trajectory reasoning limitations. This process corresponds to crossover and mutation operations in genetic evolutionary theory, not merely refining individual trajectories.

ExpeL and AvaTar lack **cross-trajectory interaction** mechanisms. Despite self-reflection, isolated trajectories remain prone to cognitive limitations and local optima.

Our DeepSeek-R1 experiments show smaller models with GRPO performed worse than distillation due to trajectory homogenization. Classic MCTS generates multiple paths but lacks real-time information exchange, resulting in limited search efficiency compared to our trajectory interaction mechanism.

**Compared to MCTS**: SE-Agent introduces **trajectory-level evolutionary mechanisms** enabling information exchange across reasoning paths, providing:

a. **Breaking cognitive boundaries**: Competition and collaboration overcome individual path limitations
b. **Knowledge transfer**: Successful patterns transfer across trajectories through recombination

This fosters **emergent population-level intelligence**. Our experiments show relative gains up to **112% (Llama-3.1-70B)** and **51% (Claude-3.7-Sonnet)**, demonstrating **true capability evolution** beyond individual trajectory sampling.

## **The Choice of Benchmark (For W2)**

We focus on **SWE-bench Verified** because it presents significant challenges requiring cross-file bug localization, patch generation, and test validation over real-world repositories. Even top models achieve only 50-60% success. Leading works like **SWE-agent and SWE-search** report main results on this benchmark.

LiveCodeBench emphasizes single-turn generation rather than multi-step interaction. Terminal-Bench aligns well with SE-Agent's capabilities but requires additional engineering work—a promising future direction.

## **A description of the Operators (For W3&W4)**

We demonstrate our specific operations through the following case:

### **Case Study: Fixing UnrecognizedUnit.__eq__ in Astropy**

**Problem Description**: Astropy's UnrecognizedUnit.__eq__ method throws exceptions instead of returning False when compared with None, violating Python's safety conventions for comparison operations.

**Step 1: Revision Operation - Multi-Strategy Trajectory Generation**

First, generate 5 initial trajectories using 5 different planning strategies:

| Prompt Variant | Trajectory Summary | Main Issues |
|----------------|-------------------|-------------|
| P-greedy | `grep→view→quick-edit` | Only handles None, ignores other types |
| P-tests-first | `pytest→search→edit` | Too many test failures |
| P-linter-aware | `flake8→edit→pytest` | Indentation issues, logic still wrong |
| P-defensive | `try-catch→comprehensive` | Overly broad exception handling |
| P-minimal | `single-line→submit` | Incomplete functionality |

Then perform reflection and revision operations on each trajectory to generate corresponding improved versions:

**Example - Evolution of P-greedy trajectory:**
- **Original trajectory issue**: Overly narrow type checking
- **Revised trajectory**: `grep → view → add comprehensive tests → edit(improve type annotations, add exception handling) → pytest`

**Output**: 10 revised trajectories (original 5 + revised 5) proceed to the next stage.

**Step 2: Refinement.Selection - Quality-Based Trajectory Filtering**

Use evaluation function for quality filtering:
$$\text{Reward}(t, T) = \alpha \cdot \text{TaskCompletion}(t,T) + \beta \cdot \text{ReasoningQuality}(t) + \gamma \cdot \text{Efficiency}(t)$$

Scoring includes two components:
- **LLM scoring**: Reasoning quality and solution reasonableness evaluation
- **Rule-based scoring**: Code completeness, syntax error checking, structural validation

**Result**: Remove low-quality trajectories, retain 5 for the next step.

**Step 3: Recombination Operation - Cross-Trajectory Knowledge Fusion**

Based on task complexity, orchestrate operator combinations to generate 5 new trajectories:
- **2× Crossover**: Fuse complementary segments from different trajectories  
- **2× Transfer Learning**: Transfer successful patterns to target trajectories
- **1× Restructure**: Global restructuring optimization

**Specific trajectory fusion examples:**

**Crossover #1 - Precise Localization × Comprehensive Testing:**
- **Trajectory A**: `grep "UnrecognizedUnit" → edit eq → submit` (fast but shallow)
- **Trajectory B**: `pytest → analyze failures → view test context → comprehensive edit` (slow but thorough)
- **Crossover Result**: `grep "UnrecognizedUnit" → view test_units.py → analyze eq usage pattern → targeted comprehensive fix`

**Transfer #1 - Exception Handling Pattern Migration:**
- **Source**: try-except (TypeError, ValueError) pattern from defensive programming trajectory
- **Target**: Simple None check from greedy trajectory  
- **Transfer Result**: `None check + multi-type exception catching + graceful degradation`

**Restructure - Global Optimization:**
- **Global pattern recognition**: All trajectories revolve around the core issue of "type-safe comparison operations"
- **Redundancy elimination**: Merge duplicate search and localization steps
- **Structural reorganization**: `Problem understanding → Precise localization → Context analysis → Type-safe design → Comprehensive validation`

**Output**: Extended pool contains 10 trajectories (original 5 + newly generated 5)

**Iterative Self-Evolution Support**

SE-Agent supports multi-round iterative optimization, continuing the evolution process based on the current trajectory pool:

**Round 2 Evolution (simplified representation):**
- **Step2'**: Reflection ⟨5 trajectories⟩ → Revision ⟨...⟩ → `T2'` (10 trajectories)
- **Step3'**: Quality-Filtering ⟨T2'⟩ → Select ⟨...⟩ → `T3` (5 trajectories)
- **Step4'**: Recombination ⟨T3⟩ → [Crossover×2, Transfer×2, Restructure×1] → `T4` (10 trajectories)

**Convergence condition**: Stop when consecutive K rounds of highest reward improvement < ε, or reach preset maximum rounds. This iterative mechanism ensures SE-Agent can continuously optimize until finding high-quality solutions or meeting stop conditions.

**Step 4: Final Solution Selection**

Select the highest-scoring trajectory from candidates as the final solution:

**Core Improvement**: SE-Agent evolved from single-point failure repair to a **type-safe universal solution**, generating entirely new solution methods through **explicit inter-trajectory interactions** (Crossover, Transfer, Restructure), covering all invalid input scenarios.

**Key Innovation**: This trajectory-level collaboration is **unachievable by traditional methods** like MCTS that rely solely on model capabilities for diversified sampling—they can only generate mutually independent trajectories, **unable to achieve collaborative evolution and knowledge transfer** between trajectories.

## **Discussion of Resource Overhead (For W5)**

While our method introduces **additional cost** due to multiple trajectories, this overhead is **justified by significant performance gains**.

In Figure 5, "Maximum Cost" is in **USD** (Claude-3.7 Sonnet API). At `$2` limit, execution terminates when cost exceeds `$2` per task.

Cost-performance analysis shows:
- At `$1` limit: SE-Agent (41.6%) vs. SWE-Search (43.8%) vs. SWE-Agent (38.4%)
- At `$2` limit: **SE-Agent (61.2%)** vs. SWE-Search (47.4%) vs. SWE-Agent (40.6%) 

Moreover, we report Cost per Resolved Instance without any budget cap: 
- **SE-Agent: `$3.54`**
- SWE-Search: `$4.08`
- SWE-Agent: `$4.66`

Despite higher initial cost per run, SE-Agent is **more cost-efficient** for successfully solving tasks.