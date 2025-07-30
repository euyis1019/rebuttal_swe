## Response to Reviewer DjBE

Thank you for your detailed feedback. We appreciate your recognition of our interesting idea of leveraging and improving upon previous trajectories, as well as the performance improvements demonstrated through our experiments and ablation studies. We are also grateful for your positive assessment of the advantages our approach offers in **cross-trajectory information utilization** compared to existing MCTS methods. Below, we address each of your main concerns in detail:

### Method Implementation Details (For W1)
##### Case Study: Fixing UnrecognizedUnit.__eq__ in Astropy
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

### Ground Truth Leakage Concern (For W2)

We acknowledge that our use of the term **"test pass rate"** may have led to confusion, as it could be misinterpreted as indicating the use of ground-truth tests. In fact, the "pass rate" refers only to the internal validation of structural constraints necessary for a trajectory to be considered valid and potentially effective—**not whether it actually solves the task**. We will revise this in the final version, modifying it to **"validation rate"** and describing our evaluation function in more detail.

Our **evaluation function** consists of two components: 

1. **Rule-based reward**, denoted as **AutoEval()**
2. **Model-based reward**, denoted as **ExpertEval()**

**Important**: Neither component relies on ground-truth labels or executes any actual tests. 

To elaborate, **AutoEval()** is a rule-based mechanism designed to assess whether a generated trajectory meets minimal submission criteria. These criteria include, but are not limited to: 
- Ensuring the final patch file is non-empty
- Verifying that the trajectory contains a sufficient proportion of code-editing steps
- Checking whether the trajectory length falls within a reasonable range

This procedure **does not execute any code, run any tests, or access ground-truth labels**. It only performs static analysis of the trajectory structure.

### Concrete Examples (For W3)

As shown in the complete example provided in our response to W1, we have illustrated in detail **how each operation specifically modifies and improves the trajectories**. This example comprehensively demonstrates the entire process—from initial trajectory generation, quality evaluation, and cross-trajectory fusion to the selection of the final solution—including **concrete changes in trajectory content** and the resulting improvements.

### Additional Clarifications

We will add a dedicated limitation section in the final version to discuss computational overhead and the applicable scope of our method. Our approach has indeed achieved **significant performance gains** (up to 55% relative improvement) and **SOTA results**, which validate the effectiveness of the trajectory-level self-evolution paradigm.


## Response to Reviewer c1xU

Thank you for your detailed feedback. We appreciate your recognition of the strengths of our work: the paper thoroughly articulates the need for more diverse and optimized multi-step reasoning in LLM agents, and the three-step mechanism (**Revision**, **Recombination**, and **Refinement**) represents a substantial advancement over existing trajectory sampling and MCTS methods. We are also grateful for your positive evaluation of the performance improvements demonstrated on the SWE-bench benchmark. Below, we address each of your main concerns in detail:

### Clarification on the Term "Self-Evolution" (For W1)

We greatly appreciate this important question regarding terminology. We understand the reviewer's concerns about our use of the term **"self-evolution,"** and would like to clarify why we believe this term accurately captures the essence of our approach. SE-Agent embodies a **genuine evolutionary principle** that goes beyond individual trajectory sampling:

**1) Addressing the Homogenization Problem:**

Traditional multi-trajectory sampling often suffers from severe homogenization—multiple trajectories tend to converge to structurally similar solutions, especially when using smaller models. Although multiple samples are generated, the effective search space remains limited. We observed similar findings in our experiments with DeepSeek-R1, where smaller models trained with GRPO actually performed worse than those using distillation.

**2) A True Evolutionary Mechanism:**

SE-Agent establishes **systematic information exchange** among trajectories through three core operations:
- **Revision**: Targeted improvement of individual trajectories through self-reflection
- **Recombination**: Transfer of successful reasoning patterns across trajectories, creating novel hybrid solutions
- **Refinement**: Iterative optimization of the trajectory pool based on collective performance

**3) Emergent Collective Intelligence:**

Unlike simple sampling, trajectories in SE-Agent **actively influence and evolve with each other**. Superior partial reasoning strategies propagate within the population, while inferior approaches are eliminated, resulting in **emergent collective intelligence** at the population level.

**Concrete Case Demonstration:**

##### Case Study: Fixing UnrecognizedUnit.__eq__ in Astropy
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

### Experimental Fairness (For W2)

Thank you for raising this important concern regarding the fairness of our experimental comparisons. We **fully acknowledge** that using different model versions in Figure 2 might affect the fairness of our comparisons.

To ensure a more rigorous and fair evaluation, we conducted **additional experiments** after the paper submission, using the same model across all methods:

**Updated Experimental Results:**

We standardized all experimental conditions by using **Claude-4-Sonnet** as the base model for every method. The results are shown below:

| Method                          | Performance on SWE-bench Verified |
|---------------------------------|-----------------------------------|
| SWE-agent + Claude 4 Sonnet     | 66.6%                            |
| Augment Agent v1                | 70.4%                            |
| OpenHands + Claude 4 Sonnet     | 70.4%                            |
| Moatless Tools + Claude 4 Sonnet| 70.8%                            |
| TRAE                            | 75.2%                            |
| **SE-Agent + Claude 4 Sonnet**  | **80.0%**                        |

*All experiments use Claude-4-Sonnet as the backbone model for fair comparison.*

These results further validate the **generality and effectiveness** of our self-evolution framework, achieving **SOTA performance** on SWE-bench Verified. We are currently submitting these performance results to the **SWE-bench leaderboard**.

### More Ablation Study of Refinement (For W3)

Thank you for your insightful comments. We initially did not include an ablation study for the Refinement operation because it plays a **central role** within the SE-Agent framework. However, to ensure the completeness of our ablation analysis, we have now conducted additional experiments by removing the Refinement component:

| Model            | SE-Agent | w/o Revision | w/o Recombination | w/o Refinement | w/o All |
|------------------|----------|--------------|-------------------|---------------|---------|
| DeepSeek-V3      | **54.8%**    | 36.6%        | 44.6%             | 34.0%         | 31.6%   |
| Qwen-2.5-72b     | **38.8%**    | 23.8%        | 28.8%             | 22.4%         | 18.8%   |
| Llama-3.1-70b    | **32.6%**    | 20.4%        | 27.4%             | 19.0%         | 15.4%   |
| GPT-4o           | **40.4%**    | 27.4%        | 33.4%             | 25.2%         | 22.4%   |
| Claude-3.7       | **61.2%**    | 45.6%        | 50.6%             | 47.8%         | 40.6%   |

The results demonstrate that **all three components make significant contributions** to the overall performance of SE-Agent. We will include a detailed discussion of this ablation study in the revised version of the paper.

### Clarification on "Pilot Trajectories" (For W4)

"Pilot trajectories" refer simply to the trajectories generated in the initial stage using different planning strategies. Specifically:

**Multi-strategy trajectory generation:**
- **P-greedy**: Rapid localization and fixing
- **P-systematic**: Systematic problem analysis
- **P-defensive**: Defensive programming approach
- **P-minimal**: Minimal modification
- **P-comprehensive**: Comprehensive testing and validation

These diverse initial trajectories provide the **foundation** for subsequent evolutionary operations, ensuring **broad coverage of the solution space** from the very beginning. Baseline methods can also utilize these diverse initial trajectories; however, they **lack our mechanism** for cross-trajectory interaction and evolution.

### Additional Clarifications

- **Spelling Correction**: Thank you for pointing out the typo in line 120; we will correct it in the revised version.
- **Limitation Discussion**: We will add a dedicated **"Limitations"** section in the revised version to clearly discuss the computational overhead, applicable scope, and potential constraints of our approach.

We believe these clarifications and additional experiments **fully address** the reviewer's concerns and demonstrate the **robustness and fairness** of our evaluation. SE-Agent achieves significant performance improvements through genuine trajectory-level evolution, which **cannot be attained** by traditional sampling methods.


## Response to Reviewer JbMn

Thank you for reviewing our paper and providing such insightful feedback. We greatly appreciate your recognition of the clarity of our writing, the well-structured mathematical formulations, and the **strong empirical performance** of our proposed framework on the SWE-bench Verified. Furthermore, we have thoroughly addressed your concern as follows:

### Inference Time Comparison (For W1)

As **SE-Agent** is designed with efficiency in mind, particularly when compared to frameworks like SWE-Search (based on MCTS), we conduct additional experiments to quantify the average inference time required to solve a single instance successfully. This evaluation focuses on the average wall-clock time per solved case, which we believe is a practical metric for real-world deployment and large-scale adoption. The results are as follows:

| Method               | Inference Time (min) | Resolved Rate (%) |
|----------------------|---------------------|-------------------|
| SWE-Agent            | 15.61               | 40.6              |
| SWE-Search (MCTS)    | 33.42               | 47.4              |
| **SE-Agent (Ours)**  | **31.06**           | **61.2**          |

*All results are based on Claude-3.7-Sonnet model*

**Our findings show that SE-Agent consistently achieves lower inference time compared to SWE-Search(MCTS)**. This confirms the advantage of our method in **avoiding the heavy computational cost** associated with test-time scaling techniques like MCTS. The proposed SE-Agent not only **improves performance** but also enables **efficient exploration** without incurring significant inference overhead. We will include this comparison and discussion in the revised version of the paper to better support our claims regarding SE-Agent's practical efficiency.

Due to time constraints, we are currently only able to provide inference time comparison results based on Claude-3.7-Sonnet. However, we fully recognize the importance of this evaluation and commit to including a comprehensive inference time analysis—covering all relevant baselines—in the revised version of the paper.


## Response to Reviewer aGqC

Thank you for reviewing our paper and providing such insightful feedback. We greatly appreciate your positive comments highlighting the **strong empirical results** and the substantial relative improvements our method achieves over established baselines on the challenging SWE-bench benchmark. We are also pleased that you recognized the generalizability of our approach across both open-source and closed-source LLMs. We have thoroughly addressed your concerns as follows:

### The contributions of SE-Agent (For W1)

We agree that leveraging historical experience for improvement is indeed an integral part of our approach, and ablation studies have demonstrated its importance. However, it is important to clarify that this component is actually built upon the **interaction and recombination of multiple trajectories**, followed by further self-evolution. This process corresponds to the crossover and mutation operations in genetic evolutionary theory. The core contribution of SE-Agent fundamentally lies in **expanding the search space through multi-trajectory interaction**, thus overcoming the cognitive limitations of single-trajectory reasoning. It is not merely about refining or improving a single trajectory based on past experience.

ExpeL and AvaTar are both representative studies of experience-based improvement and iterative optimization. However, they share a common limitation: the lack of a mechanism for **cross-trajectory interaction**. Even if self-reflection is performed, the isolated nature of individual trajectories makes them prone to cognitive limitations and local optima.

Similar insights are observed in our experiments with DeepSeek-R1: smaller models trained with GRPO performed worse than those using distillation. This is primarily due to the severe homogenization of independently sampled trajectories, which fails to expand the search space effectively. Classic techniques such as MCTS can enlarge the search space by generating multiple candidate paths, but due to the absence of timely information exchange among these paths, the search efficiency remains low, and the search space is still limited. In contrast, while classic MCTS generates multiple candidate paths from independent sampling of the same model, these paths lack real-time information flow and complementary collaboration, resulting in limited search efficiency and a narrower solution space compared to what our trajectory interaction mechanism can achieve in terms of breadth and diversity.

**Compared to MCTS**: Our experiments show that SE-Agent achieves **significant improvements** over MCTS-based methods in both search efficiency and effectiveness. SE-Agent introduces a **trajectory-level evolutionary mechanism**, enabling information exchange across different reasoning paths. This cross-trajectory interaction provides two key benefits: 

a. **Breaking cognitive boundaries**: Through competition and collaboration among trajectories, we overcome the limitations of individual reasoning paths.

b. **Knowledge transfer**: Our recombination process facilitates the transfer of successful reasoning patterns across trajectories, while the revision step uses these recombined trajectories as targeted references for further improvement.

This mechanism fosters **emergent intelligence at the population level**. In our experiments, different LLMs consistently exhibited evolutionary improvements (as shown in Table 1), with relative gains of up to **112% (Llama-3.1-70B)** and **51% (Claude-3.7-Sonnet)**, demonstrating **true capability evolution** that surpasses what can be achieved by sampling individual trajectories alone.

### The Choice of Benchmark (For W2)

We focus on **SWE-bench Verified** in this work because it presents significant challenges, requiring cross-file bug localization, patch generation, and test validation over real-world repositories. Even top-performing models currently achieve only 50%–60% success on this benchmark. Additionally, its tasks naturally involve rich interaction trajectories, making it a fitting testbed for SE-Agent. Moreover, we note that leading related works, such as **SWE-agent and SWE-search**, also report their main results on **SWE-bench Verified**. Strong performance in this setting thus provides a meaningful indicator of effectiveness on similarly complex tasks.

To our knowledge, LiveCodeBench is a dynamically updated competitive programming benchmark that emphasizes single-turn generation and algorithmic reasoning, rather than multi-step interaction or environment feedback. In contrast, Terminal-Bench is specifically designed to evaluate agents in interactive terminal-based tasks, such as compilation, installation, script execution, and service setup. This benchmark aligns well with the capabilities of SE-Agent, and we are excited about its potential in that domain. However, adapting our method to Terminal-Bench involves additional engineering, so we leave this as a promising direction for future work.

### A description of the Operators (For W3&W4)

In order to better demonstrate our specific operations (**Revision**, **Recombination**, and **Refinement**), we show a specific case below.

##### Case Study: Fixing UnrecognizedUnit.__eq__ in Astropy
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

### Discussion of Resource Overhead (For W5)

Overall, while our method introduces **additional cost** due to its use of multiple trajectories, we believe this overhead is **justified by the significant performance gains** it enables. The design is intended to overcome the limitations of single-trajectory reasoning, which often leads to suboptimal solutions.

In Figure 5, the unit of "Maximum Cost" is **USD** (LLM backbone is Claude-3.7 Sonnet API). For example, if the maximum cost is limited to **`$2`**, execution will be terminated when the cost of an agent exceeds `$2` for each task.

We have conducted additional experiments to better analyze the trade-off between cost and performance. When the maximum cost is limited to `$1`, **SE-Agent** achieves 41.6%, slightly below SWE-Search (43.8%) but still above SWE-Agent (38.4%). When the cap is raised to `$2`, all methods approach their best performance, but **SE-Agent** significantly outperforms the baselines (**61.2%** vs. SWE-Search's 47.4% and SWE-Agent's 40.6%). 

Moreover, we report Cost per Resolved Instance without any budget cap: 
- **SE-Agent: `$3.54`**
- SWE-Search: `$4.08`
- SWE-Agent: `$4.66`

This indicates that, despite a higher initial cost per run, SE-Agent is **more cost-efficient** in terms of successfully solving tasks.