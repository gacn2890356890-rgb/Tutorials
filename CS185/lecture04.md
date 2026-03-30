# Introduction to Reinforcement Learning (RL)

Reinforcement Learning is the study of **optimal decision-making** through interaction. Unlike supervised learning, where a model learns from a fixed dataset of "correct answers," an RL agent learns by trial and error, receiving feedback in the form of rewards.

------

## 1. Terminology & Notation

In the RL framework, we model the interaction between an **Agent** and an **Environment**.

- **State ($s_t \in \mathcal{S}$):** The configuration of the environment at time $t$. It is "Markovian" if it contains all necessary information to predict the future.
- **Observation ($o_t \in \mathcal{O}$):** A partial or noisy view of the state. In robotics, $s_t$ might be the exact position of every object, while $o_t$ is just the current camera frame.
- **Action ($a_t \in \mathcal{A}$):** The decision made by the agent (e.g., "apply 5N of force to the joint").
- **Policy ($\pi(a|s)$):** The agent's strategy—a mapping from states to a probability distribution over actions.
- **Reward Function ($r(s, a)$):** A scalar signal $R_t$. It defines the goal. Crucially, rewards can be **sparse** or **delayed**; a move that seems "bad" now (like sacrificing a piece in chess) might lead to a win later.
- **Transition Operator ($p(s'|s, a)$):** The dynamics of the world (physics). It defines the probability of landing in $s'$ given the current $s$ and action $a$.

------

## 2. Markov Models

The "Markov Property" states that the future is independent of the past given the present.

### 2.1 Markov Chain (MC)

A Markov Chain is a system that transitions between states according to specific probabilities.

- **Definition:** $M = \{\mathcal{S}, \mathcal{T}\}$

- **Transition Matrix:** For discrete states, $T$ is a matrix where $T_{ij} = P(s_{t+1}=j | s_t=i)$.

- **Stationary Distribution ($\mu$):** The probability distribution that remains unchanged after a transition. As $t \to \infty$:

  $$(T - I)\mu = 0 \implies T\mu = \mu$$

### 2.2 Markov Decision Process (MDP)

An MDP is a Markov Chain influenced by external decisions (actions) and guided by goals (rewards).

- **Definition:** $M = \{\mathcal{S}, \mathcal{A}, \mathcal{T}, r, \gamma\}$
- **Discount Factor ($\gamma \in [0, 1)$):** Determines the importance of future rewards. $\gamma=0$ makes the agent "myopic" (short-sighted), while $\gamma \to 1$ makes it "farsighted."

### 2.3 Partially Observable MDP (POMDP)

In the real world, we rarely see the full state.

- **Definition:** $M = \{\mathcal{S}, \mathcal{A}, \mathcal{O}, \mathcal{T}, \mathcal{E}, r\}$
- **Emission Probability ($\mathcal{E}$):** $p(o|s)$ describes the likelihood of seeing observation $o$ given the true state $s$.

------

## 3. The RL Objective

We define a **Trajectory** ($\tau$) as a sequence of states and actions:

$$\tau = (s_1, a_1, s_2, a_2, \dots, s_T, a_T)$$

The probability of a trajectory occurring under a policy $\pi_\theta$ is:

$$p_\theta(\tau) = p(s_1) \prod_{t=1}^T \pi_\theta(a_t|s_t) p(s_{t+1}|s_t, a_t)$$

**The Objective:** Find parameters $\theta$ that maximize the **Expected Return**:

$$J(\theta) = \mathbb{E}_{\tau \sim p_\theta(\tau)} \left[ \sum_{t=1}^T \gamma^{t-1} r(s_t, a_t) \right]$$

> [!TIP]
>
> **Why Expectations?**
>
> Consider a "dual-arm" robot assembly task. Because of sensor noise, motor friction, and varying object shapes, the exact same action might lead to different results. We optimize for the **average** outcome across thousands of possible "realities" to ensure robustness.

------

## 4. Value Functions

Value functions estimate "how good" it is to be in a certain state or to perform a certain action.

### 4.1 Q-Function (Action-Value)

The total expected return starting from $s_t$, **taking action $a_t$**, and following $\pi$ thereafter:

$$Q^\pi(s_t, a_t) = r(s_t, a_t) + \mathbb{E}_{s_{t+1}, a_{t+1}, \dots \sim \pi} \left[ \sum_{k=1}^{\infty} \gamma^k r(s_{t+k}, a_{t+k}) \right]$$

### 4.2 V-Function (State-Value)

The total expected return starting from $s_t$ and following $\pi$ immediately:

$$V^\pi(s_t) = \mathbb{E}_{a_t \sim \pi} [Q^\pi(s_t, a_t)]$$

------

## 5. Algorithm Taxonomy

| **Type**             | **Strategy**                                                 | **Key Example** |
| -------------------- | ------------------------------------------------------------ | --------------- |
| **Policy Gradients** | Directly optimize $\pi_\theta$ by calculating the gradient of $J(\theta)$. | REINFORCE, PPO  |
| **Value-Based**      | Learn the optimal $Q^*$ and derive a policy by picking $\arg\max_a Q^*(s, a)$. | DQN, Rainbow    |
| **Actor-Critic**     | The **Actor** ($\pi$) learns to act, and the **Critic** ($V$) learns to evaluate. | SAC, A3C        |
| **Model-Based**      | Learn the transition $p(s'\|s, a$ to simulate the future.    | Dreamer, MuZero |

------

## 6. Key Concepts & "Gotchas"

### On-Policy vs. Off-Policy

- **On-Policy:** Evaluates and improves the same policy used to collect data. If the policy changes, old data becomes useless (e.g., PPO).
- **Off-Policy:** Learns from data collected by *any* policy (stored in a **Replay Buffer**). This is significantly more sample-efficient but can be unstable (e.g., SAC).

### Bellman Error

We rarely know the true $Q^\pi$. Instead, we use the **Bellman Equation** to update our estimates:

$$Q(s, a) \approx r + \gamma \max_{a'} Q(s', a')$$

The **Bellman Error** is the residual of this equation:

$$\mathcal{E}_{Bellman} = \left\| Q(s, a) - (r + \gamma \max_{a'} Q(s', a')) \right\|^2$$

We minimize this error to "bootstrap" our way to the truth.

### Episodic Learning

Most tasks are divided into **Episodes** (one full run from start to "Game Over"). In contrast, **Infinite Horizon** tasks assume the agent lives forever, requiring a discount factor $\gamma < 1$ to prevent the total reward from becoming infinite.

> [!NOTICE]
>
> **Vibe Coding Pro-Tip:**
>
> In modern Embodied AI (like RT-2 or ACT), we often bypass the explicit $p(s'|s, a)$ model. Instead, we use the massive scale of data to let the Transformer "internalize" the physics. If your robot is shaking or failing, check if your $o_t$ has a **causal confusion** bug!
