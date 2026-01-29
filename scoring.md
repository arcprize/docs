# ARC-AGI-3 Scoring

ARC-AGI-3 is an Interactive Reasoning Benchmark that measures the learning efficiency of frontier AI systems. The benchmark includes 175 game environments that require the test-taker to adapt to novel mechanics in previously unseen environments. It will launch in Q1 ‘26.

For more information on ARC-AGI-3, see the [ARC-AGI-3 preview launch](https://www.youtube.com/watch?v=3T4OwBp6d90&t=2s) ([slides](https://docs.google.com/presentation/d/1pVk0-L-K0DrYWygAqPqHIjOBqCnoVQ90AegbqRG4-lw/edit?usp=sharing)) and [ARC-AGI-3 Action Efficiency Overview](https://www.youtube.com/watch?v=TK9MN22q6E0).

A key difference between interactive and static benchmarks is that interactive benchmarks allow you to track the number of actions/turns needed to complete a task.

This allows you to quantify a new measure of action efficiency to grade AI. ARC-AGI-3 will take advantage of this.

The ARC-AGI-3 scoring method is as follows, *score AI by its per-level action efficiency (as compared to a human baseline), normalized per game, across all games.*

## Summary

* AI will be scored by 2 criteria:  
  * How many levels did the AI complete in each ARC-AGI-3 game?  
  * How *efficient* (by number of actions) was the AI in completing the levels?  
* Scores will be normalized to human baselines as observed in first-time test-testers  
* AI will receive a score between 0%-100%  
  * 100% \- AI completes all games/levels while matching or surpasses human baseline efficiency  
  * \>75% \- Strong, AI almost always succeeds and rarely more than \~33% less efficient than the human baseline  
  * 25% \- Inconsistent performance  
  * \~1% \- AI may have completed a few levels, but inefficiently  
  * 0% \- Agent never completes a level across any game

## Introduction

Interactive Reasoning Benchmarks provide a new way to measure the learning efficiency of AI by counting the number of actions (defined below) it takes them to complete a task.

To complete ARC-AGI-3, test takers must use actions in two ways: **exploration** (learning the rules and building a strategy for completing the game) and **execution** (carry out a strategy) to complete a game. Counting the total number of actions taken to beat a game accounts for both of these.

This exploration & execution process is found in other tasks as well. For example, when an AI agent explores a new code base, it must first orient itself (explore) to the repository before it can complete a task (execute).

ARC Prize Foundation believes that AGI matches learning efficiency of humans, ARC-AGI-3 AI scores will be normalized to and directly compared with the number of actions that humans need to complete each level.

This scoring function is tentatively called RHAE (Relative Human Action Efficiency).

**Definition of an Action**

In ARC-AGI-3, an *action* is defined as a discrete interaction with the environment, i.e., a turn where the agent submits a command, move, or input that affects the game state.

Internal operations that do not alter the environment, such as tool calls, reasoning steps, or retries within the model itself, are **not counted** as actions. We make a best-effort attempt to filter out retries, null actions, and no-ops.

## Scoring Method Overview

**Broken down:**

* **“Score AI its per-level action efficiency”** \- For each level that an AI completes, count the number of actions that it took.  
* **“As compared to human baseline”** \- For each level that is counted, compare the AI action count to a human baseline. Ex: If a human completed a level in only 10 actions, but AI took 1,000 to complete it, then the AI scores 1% (10 / 1,000) for that level   
* **“Normalized per game”** \- Each level will be scored in isolation. Each individual level will get a score between 0% (very inefficient) 100% (matches or surpasses human level efficiency). The game score will be the average sub-score across all levels of that game  
* **“Across all games”** \- Total score will be the sum of individual game scores divided by the total number of games. Output will be a score between 0%-100%

For programmatic pseudo code, see the [appendix](?tab=t.0#bookmark=id.e5y0qagtmq7f).

This equation takes inspiration from the robotics navigation “[Shortest Path Length](?tab=t.0#bookmark=id.oplihu874wcw)” metric. With SPL, not only do you care if the robot got to its destination, but you want to know how direct was the path.

**Score Reporting**

ARC-AGI-3 scores will be reported on a similar 2x2 as the normal [ARC Prize leaderboard](https://arcprize.org/leaderboard). The Y-Axis will be performance (defined above). The X-Axis will continue to be cost for a given run.

Since playing ARC-AGI-3 requires a high number of actions (\>2,000), it will be time intensive and expensive to test a model against *all* games. Therefore we will likely tier games in sets and progressively test models on more games depending on performance.

For example, we’ll run a new model against our easiest 3 games, if there is progress, we’ll test against the next 5 and so on. For any lab that would like to test against all games, we will work on this with the lab directly.

Key Design Decisions

**Normalize AI scores with 2nd best (fewest actions) human score** 

A defining characteristic of ARC-AGI-3 scoring is that AI will be normalized to human level action efficiency. ARC Prize Foundation will conduct live human testing in a controlled environment to gather this data. A game will *only* be included in ARC-AGI-3 if it passes an “easy for humans” bar. An average of 10 members of the public will be tested on each game.

But a key question remains, which human testing results will we normalize AI actions to?

Our aim is to target proficient human efficiency. Proficient human efficiency is defined as the 2nd best (fewest actions) human. This removes the outlier winner while still demonstrating the upper bound of human capability.

This avoids penalizing later levels for early misclicks, keeps the baseline grounded in real play (not theoretical speed-runs), and gives a cleaner signal of where AI genuinely matches human capability.

**Per-Level vs Per-Game Aggregation**

AI performance will be compared to humans *per-level* then aggregated per game.

Per-level aggregation strips away noise from uneven level lengths. We’ve [observed](?tab=t.0#bookmark=id.onjuhjzclhae) that `vc33`‘s (ARC-AGI-3 public game) level 6 requires 10x actions of its level 1 (50 vs \<5). A 10% decrease in efficiency on vc33 level 6 (+5 actions) would drown out the efficiency observed on level 1\.

If we disregard level efficiency and simply look at actions across an entire game, the longest levels dominate the score and reduce signal from short levels.

Additionally, early levels are meant to be trivial, late levels are more difficult. If we collapse scoring into one game-denominator you can’t tell *where* the agent is weak. With per-level scores you immediately see “agent matches human efficiency on levels 1 through 3, but does not perform on levels 4 and 5”.

Lastly, by reinforcing efficiency per level, we won’t design games (or encourage AI) to waste actions on levels because they’re still “under budget” for a given game.

**Cap the max per-level score**

To stop a single glitch-level from distorting an entire game score, we cap the per-level efficiency an AI can receive at 1.0x human baseline.

For example, suppose the human baseline shows 20 actions needed to complete a level, but an AI discovers a 2-action exploit, the ratio (20/2 \= 10x) would overwhelm the game average. To counter this, we will cap the max AI score for a level at 1x the human baseline. 

Capping the score will incentivize developers to build AI that generalizes across games/levels rather than overscoring on a single level.