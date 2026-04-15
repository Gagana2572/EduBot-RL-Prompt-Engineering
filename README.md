# EduBot RL — Adaptive AI Tutoring Agent

Hey! This is my take-home final project for the Reinforcement Learning for Agentic AI Systems course. I built an adaptive tutoring agent that uses RL to figure out which questions to ask students based on how they're actually doing — instead of just throwing random questions at them.

The idea is pretty simple: if you're a beginner, you shouldn't be getting hit with hard questions. The agent learns this on its own through trial and error. That was honestly the coolest part of the whole project.

---

## What it does

EduBot RL is a tutoring agent that covers 5 math topics (algebra, geometry, fractions, decimals, statistics) and learns to personalize question difficulty and topic selection for each student. It uses two RL methods working together:

- **Q-Learning** — decides how hard each question should be based on the student's skill level
- **UCB Contextual Bandit** — decides which topic to focus on based on which ones produce the most learning

After 2,000 training episodes it went from 33% accuracy to 67% — and the coolest thing is it learned on its own to only give easy questions to beginners. I never programmed that in. It just figured it out from the reward function.

---

## Results at a glance

| Metric | Random (untrained) | EduBot RL (trained) |
|---|---|---|
| Student accuracy | 33.3% | 67.3% |
| Skill gain per session | 0.020 | 0.040 |
| Easy question % (beginners) | 30% | 100% |

The combined Q-Learning + Bandit system also reached 50% accuracy in just 2 episodes vs 150 episodes for Q-Learning alone. That 75x speedup was a big surprise honestly.

---

## Project structure

```
EduBot_RL.ipynb        # main notebook — run this in Google Colab
EduBot_RL_Report.pdf   # full technical report
README.md              # you're reading it
```

---

## How to run it

I built this entirely in Google Colab so there's no setup needed on your machine.

**Step 1** — Open the notebook in Colab

Go to [colab.research.google.com](https://colab.research.google.com), click File → Open notebook → GitHub, and paste this repo URL. Or just download `EduBot_RL.ipynb` and upload it directly.

**Step 2** — Run all cells in order

Click Runtime → Run all. The whole thing takes about 2-3 minutes to train.

**Step 3** — Check the outputs

Each cell prints its results and the graphs save automatically. The before/after comparison at the end is the most interesting part to look at.

That's it honestly. No installations, no terminal, just run it.

---

## What each cell does

| Cell | What it does |
|---|---|
| Cell 1 | Checks Python version |
| Cell 2 | Installs numpy, matplotlib, pandas |
| Cell 3 | Imports everything, sets seed to 42 |
| Cell 4 | StudentSimulator class — models a learner |
| Cell 5 | QLearningAgent class — the Q-Learning RL agent |
| Cell 6 | Training loop — 2,000 episodes, tracks everything |
| Cell 7 | Learning curve graphs — 6 panels |
| Cell 8 | ContextualBandit + combined system |
| Cell 9 | Comparison graphs — Q-Learning alone vs combined |
| Cell 10 | Before/after demo across beginner/intermediate/advanced |
| Cell 11 | Saves everything to JSON |

---

## The RL methods I used

### Q-Learning (Value-Based)

This is the main learning algorithm. It builds a table of Q-values that maps each student state and action to an expected reward. After each question it updates using the Bellman equation:

```
Q(s, a) = Q(s, a) + alpha * [reward + gamma * max(Q(next_state)) - Q(s, a)]
```

- Learning rate (alpha): 0.1
- Discount factor (gamma): 0.9
- Epsilon started at 1.0 and decayed to 0.05 over training
- Q-table ended up with 15 unique states (out of a possible 243)

### Contextual Bandit with UCB

The bandit handles topic selection. It uses the Upper Confidence Bound formula to balance trying new topics vs sticking with ones that work:

```
UCB(topic) = avg_reward + sqrt(2 * ln(total_pulls) / attempts)
```

Topics that haven't been tried much get a big exploration bonus. After 10,000 interactions it naturally concentrated on fractions (57.9%) and decimals (22.5%) because those produced the highest learning rewards.

### Reward Function

This was the trickiest part to get right. If I just rewarded correct answers, the agent learned to ask only easy questions. So I added a 10x skill improvement bonus:

```
+1.0  correct answer
+1.5  correct on medium (if skill > 0.35)
+2.0  correct on hard (if skill > 0.60)
-0.5  wrong answer
-1.5  wrong on hard question (if skill < 0.40)
+10 x skill_gain  improvement bonus
```

The 10x multiplier was the key. Once I added that the agent stopped gaming the system and started actually teaching.

---

## Things I'm proud of

**The emergent behavior** — The agent learned that beginners need easy questions without me ever telling it that. It just came out of the reward function. That felt like actual machine learning doing its thing.

**Error handling** — Every step of training is wrapped in try/except so if anything breaks it logs the error and keeps going. Zero errors across 20,000 training steps.

**Reproducibility** — Everything uses seed 42 so you'll get the exact same results every time you run it.

**Student types** — The agent works differently for beginner, intermediate, and advanced students. Beginners get easy questions, advanced students get hard ones. It figured out the right strategy for each type.

---

## What I'd add with more time

- **QuestionGeneratorTool** — right now the agent picks difficulty levels but doesn't generate real questions. A proper question bank with actual math problems would make this deployable as a real tutoring app
- **Statistical validation** — running 5 seeds and reporting mean ± std would strengthen the results
- **DQN** — swap the Q-table for a neural network to handle more topics and continuous skill values
- **Web app** — the Q-table saves to JSON so deploying this as a web interface would actually be pretty straightforward

---

## Framework

This project is built on the Humanitarians.AI **Dewey framework** for adaptive tutorial agents. The bandit acts as the curriculum agent (decides what to teach) and the Q-agent acts as the scaffolding agent (decides how hard to teach it). Both are loosely coupled and communicate through a shared reward signal, which matches the Dewey framework's agent composition model.

---

## Dependencies

```
Python 3.12
numpy 2.0.2
matplotlib 3.10.0
pandas 2.2.2
```

All pre-installed in Google Colab. No extra setup needed.

---

## References

- Sutton & Barto (2018). Reinforcement Learning: An Introduction. MIT Press.
- Auer et al. (2002). Finite-time analysis of the multiarmed bandit problem. Machine Learning.
- Watkins & Dayan (1992). Q-learning. Machine Learning.
- VanLehn (2011). The relative effectiveness of human tutoring and ITS. Educational Psychologist.
- Lattimore & Szepesvari (2020). Bandit Algorithms. Cambridge University Press.
- Humanitarians.AI (2025). Dewey Framework Documentation.

---

## Author

Gagana M — 002310881
Reinforcement Learning for Agentic AI Systems
April 2026
