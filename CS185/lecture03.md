- 1. - 1. # Supervised Learning of Behaviors: Models and Scaling

**Instructor:** Sergey Levine

**Course:** CS 185/285 (UC Berkeley)

------

## 1. Challenges in Fitting the Expert

Standard Behavioral Cloning (BC) often fails because the model cannot accurately represent the expert's policy. There are two primary reasons:

### A. Non-Markovian Behavior

Standard policies assume the **Markov Property**: $a_t$ depends only on the current observation $o_t$. However, human demonstrators are often non-Markovian (their actions depend on history).

- **The Problem:** If we see the same observation twice but performed different actions due to history, a Markovian model gets confused.
- **The Solution:** Use sequence models (e.g., **Transformers** or **RNNs**) to incorporate history: $\pi_\theta(a_t | o_{1:t})$.
- **The Risk - Causal Confusion:** Simply adding history can make the model over-rely on past actions (correlations) rather than the true causal cause of an action (e.g., the model thinks it’s moving because it moved in the last frame, not because of the goal).

### B. Multimodal Behavior

If an expert can solve a task in two different ways (e.g., going left or right around an obstacle), a simple Gaussian policy will **average** the actions, leading the robot to crash into the middle.

------

## 2. Theoretical Solutions for Multimodality

To represent multiple "modes" of action, we can use more expressive distributions:

### I. Autoregressive Discretization

Instead of predicting all dimensions of a high-dimensional action at once, we discretize them and predict one by one.

- **Derivation:** We factor the joint distribution of the action vector $a = [a^1, a^2, \dots, a^d]$:

$$P(a | s) = \prod_{i=1}^d P(a^i | a^1, \dots, a^{i-1}, s)$$

- **Implementation:** Use a Transformer to predict $a^1$, then feed $a^1$ back in to predict $a^2$, and so on.

### II. Diffusion & Flow Matching

These models treat action generation as an iterative refinement process.

- **Diffusion Policy:** Starts with Gaussian noise and "denoises" it into a specific action mode.
- **Action Chunking:** Instead of predicting $a_t$, the model predicts a sequence of future actions $[a_t, a_{t+1}, \dots, a_{t+k}]$. This ensures temporal consistency and smoother movements.

------

## 3. Narrow vs. Broad Data (The Pre-training Paradigm)

A major hurdle in robotics is the trade-off between **Quality** and **Diversity**.

| **Data Type**     | **Pros**                         | **Cons**                                              |
| ----------------- | -------------------------------- | ----------------------------------------------------- |
| **"Broad" Data**  | Large scale, diverse situations. | Often "bad" or suboptimal actions.                    |
| **"Narrow" Data** | High-quality expert actions.     | Doesn't see enough situations to recover from errors. |

**The Strategy: Pre-training + Post-training**

1. **Pre-training:** Train a base model on a massive dataset of "broad" (even suboptimal) data to learn world representations and general physics.
2. **Post-training (SFT):** Fine-tune on a small set (~20 hours) of high-quality "narrow" data to tell the robot exactly *what* the user wants it to do.

------

## 4. Multi-Task & Goal-Conditioned Learning

To make policies more robust, we can train them to reach specific goals rather than just mimicking a single trajectory.

### Goal-Conditioned BC (GCBC)

The policy is conditioned on a goal $g$: $\pi_\theta(a | s, g)$.

- **The Objective:** $\max_\theta \mathbb{E}_{(s, a, g) \sim \mathcal{D}} [\log \pi_\theta(a | s, g)]$
- **Self-Supervised Learning (Hindsight):** We can take *any* random interaction data where the robot reached state $s_T$ and treat that $s_T$ as the "goal" it was trying to reach. This allows us to learn from "failed" or random data.

### Learning from Play

Instead of structured demonstrations, we use "Play Data"—unstructured human interaction with the environment.

- **Method:** Sample two states from the play data (current $s$ and future $g$) and train the model to predict the action that connects them.

------

## Summary Recap

- **BC is not guaranteed to work** due to i.i.d. violations (Distributional Shift).
- **Fixing Shift:** Use **DAgger** (expert in the loop) or **Smart Data Augmentation** (teaching recovery).
- **Expressivity:** Use **Transformers** for history and **Diffusion/Autoregressive** models for multimodality.
- **Scaling:** Combine **Broad Pre-training** with **Narrow Fine-tuning** to achieve both robustness and precision.
