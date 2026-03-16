# Supervised Learning of Behaviors: Models and Scaling

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

### 1. Core Intuition: What is Flow Matching?

While **Diffusion** generates actions by "slowly denoising" Gaussian noise, **Flow Matching** offers a more direct, geometric perspective:

- **Vector Field:** Imagine space filled with "arrows." These arrows indicate the direction and velocity required to transform noise (disorder) into expert actions (order).
- **Probability Path:** It defines a straight-line path from Gaussian noise $x_0 \sim \mathcal{N}(0,I)$ to an expert action $x_1$.
- **The Goal:** Instead of learning to denoise, the neural network learns this **Vector Field** (Velocity Field). At any time $t$, the model predicts the "velocity" needed to push the current state along that straight line toward the expert action.

------

### 2. Mathematical Derivation

The core of Flow Matching is finding a vector field $v_t(x)$ that generates a flow matching the target distribution.

#### A. Defining the Probability Path

We want to transform noise $x_0$ into expert action $x_1$. The simplest path is **Conditional Linear Interpolation**:

$$x_t = \psi_t(x_1) = (1-t)x_0 + tx_1$$

where $t \in [0, 1]$.

- At $t=0$, $x_t = x_0$ (pure noise).
- At $t=1$, $x_t = x_1$ (perfect expert action).

#### B. Finding Target Velocity

Velocity is the derivative of displacement with respect to time:

$$v_t(x_1) = \frac{dx_t}{dt} = \frac{d}{dt} [(1-t)x_0 + tx_1] = x_1 - x_0$$

This $x_1 - x_0$ is our **Target Velocity**. It tells us that moving in this constant direction will successfully turn noise into the desired action.

#### C. Conditional Flow Matching Objective

In practice, we don't know $x_0$ at inference time; we only know the current noisy state $x_t$. We train a network $v_\theta(x_t, t)$ to predict this target velocity:

$$\mathcal{L}_{CFM} = \mathbb{E}_{t, x_0, x_1} [\| v_\theta(x_t, t) - (x_1 - x_0) \|^2]$$

where $x_t = (1-t)x_0 + tx_1$.

> **Key Insight:** This is essentially **Supervised Learning**.
>
> - **Input:** Current noisy action $x_t$ and time $t$.
> - **Label:** The ideal velocity vector $(x_1 - x_0)$.

------

### 3. Training & Implementation (Lecture Analysis)

The [PowerPoint Presentation](https://rail.eecs.berkeley.edu/deeprlcourse/static/slides/lec-3.pdf) illustrates how to convert this math into a robotic policy:

#### Training Process

1. **Sampling:** Draw an expert action $x_1$ from the dataset and a noise sample $x_0$ from a Gaussian distribution.
2. **Interpolation:** Randomly pick a time $t \in [0, 1]$ and calculate $x_t = (1-t)x_0 + tx_1$.
3. **Prediction:** Input $x_t$, time $t$, and the current observation $O$ (e.g., image features) into the network $v_\theta$.
4. **Optimization:** Minimize the **MSE Loss** between predicted velocity and the true direction $(x_1 - x_0)$.

#### Inference (Sampling) Process

When the robot performs the task:

1. Start with pure noise $x_0$.

2. **ODE Integration:** Use a simple **Euler Method** to iteratively update the action:

   $$x_{t+\Delta t} = x_t + v_\theta(x_t, t, O) \cdot \Delta t$$

3. After a few iterations, the noise transforms into a precise, continuous action sequence.

------

### 4. Comparison: Flow Matching vs. Diffusion

| **Feature**             | **Diffusion (DDPM)**                        | **Flow Matching**                                      |
| ----------------------- | ------------------------------------------- | ------------------------------------------------------ |
| **Path Shape**          | Curved, stochastic diffusion paths          | **Straight paths** (Optimal Transport)                 |
| **Training Complexity** | Complex variational bounds/noise prediction | **Simple Least Squares regression**                    |
| **Sampling Speed**      | Requires many steps to be precise           | **High efficiency**; straight paths allow larger steps |
| **Physical Intuition**  | "Erasing" noise                             | **"Moving" particles** (Kinematic)                     |

------

### 5. Summary

Flow Matching redefines action generation as finding the **Optimal Path**. By utilizing **Conditional Flow Matching**, it avoids the complex probabilistic derivations required by Diffusion.

In robotic control, its "straight-path" characteristic makes action generation extremely stable and fast. When combined with **Action Chunking**, the model predicts the evolution of an entire sequence $x$ within the vector field. This handles **multimodality** (the field can pull noise toward different peaks) and produces exceptionally smooth motions.

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
