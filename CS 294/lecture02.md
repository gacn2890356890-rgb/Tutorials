# Learning to Self-Improve & Reason with LLMs

Goal: An AI that "trains" itself as much as possible
- Creates new tasks to train on (challenges itself)
- Evaluates whether it gets them right ("self-rewarding")
- Updates itself based on what it understood

System 1: reactive and relies on
associations

System 1: reactive and relies on
associations

Standard (pre-training) trains by predicting the next token only on "positive examples" of language

LLM Post-training

## Improving reasoning via System 2 (LLMs)

System 1 failures: Factuality & hallucination

Chain-of Verification Reduces Hallucination in Large Language Models

More failure modes of System 1
Problem: whole context affects LLM
output even irrelevant parts!
Hypothesis: soft-attention inherently
spreads attention thin over everything.
Also, LM objective favors correlations.

System 2 Attention (S2A)
Decide what to attend explicitly (system 2) by rewriting the input
Problem: whole context affects LLM
output even irrelevant parts!
Hypothesis: soft-attention inherently
spreads attention thin over everything.
Also, LM objective favors correlations.
Solution: Make attention more explicit &
effortful → Prompt LLM to extract
relevant context

## Better reasoning via Self-Improvement
