# Knota

## A General-Purpose AI-Based Skill Assessment and Learner Modelling Framework

**Version 0.1 — Concept Whitepaper**

---

## Abstract

Knota is an experimental AI-native framework for estimating what a learner actually understands and can apply.

Traditional learning systems frequently focus on content consumption, memorisation, or question answering. Flashcard and spaced-repetition systems are particularly effective for memory retention, but they do not necessarily measure whether a learner can explain, apply, analyse, or transfer a concept.

Knota instead treats learning as a problem of **latent learner-state estimation**.

The framework combines:

- AI-generated assessment questions
- rubric-based semantic answer evaluation
- multidimensional learner-state estimation
- adaptive assessment policies
- semantic concept representations
- uncertainty-aware skill visualisation

The initial implementation uses a domain-independent rule-based learner model.

Over time, Knota is designed to evolve toward a **General-Purpose Learner Model** trained across users, concepts, cognitive levels, question formats, and time.

The central research question is:

> **Can a shared learner model estimate and predict learner capability across heterogeneous knowledge domains?**

---

# 1. Motivation

Modern learning systems are increasingly capable of generating educational content using large language models.

A typical AI-assisted learning workflow is:

```text
Learning Material
      ↓
Question Generation
      ↓
Learner Answer
      ↓
Correct / Incorrect
      ↓
Progress
```

This approach is useful, but it does not necessarily answer a more important question:

> **What does the learner actually understand?**

A learner may correctly recall a definition while being unable to:

- explain the underlying mechanism
- distinguish it from similar concepts
- apply it in a new situation
- diagnose an incorrect implementation
- design a solution using the concept

Knota therefore focuses not primarily on content delivery, but on **measurement of learner capability**.

---

# 2. Core Hypotheses

Knota is based on four primary hypotheses.

## H1 — Skill is multidimensional

A concept should not be represented only by a binary mastered/not-mastered flag.

For a concept, learner capability may instead be represented across several dimensions.

For example:

```text
Retrieval-Augmented Generation

Recall          0.88
Understanding   0.76
Application     0.51
Analysis        0.62
Retention       0.79
Confidence      0.64
```

A learner may therefore demonstrate strong recall while having limited application ability.

---

## H2 — Learner state is latent

True understanding cannot be directly observed.

Instead, the system observes evidence such as:

- rubric performance
- question difficulty
- cognitive level
- question format
- response latency
- previous performance
- time since previous assessment
- consistency across repeated assessments

Knota therefore represents skill as an **estimate with uncertainty**, rather than as an absolute truth.

---

## H3 — Assessment should adapt to learner state

The next assessment should not simply be randomly selected from a static question bank.

It should depend on:

```text
Current Skill Estimate
+
Confidence
+
Retention Risk
+
Concept Importance
+
Knowledge Relationships
```

This creates a closed adaptive assessment loop.

---

## H4 — Some learning dynamics may generalise across domains

Knowledge domains differ, but some aspects of human learning may exhibit transferable structure.

Examples include:

- forgetting over time
- differences between recall and application
- effects of question difficulty
- effects of repeated evidence
- uncertainty reduction
- differences between question formats

Knota investigates whether such patterns can be captured by a **shared learner model**, rather than requiring an entirely independent model for every domain.

---

# 3. System Overview

The conceptual architecture of Knota is:

```text
Learning Material / Concepts
           ↓
Concept Representation
           ↓
Assessment Policy
           ↓
Question Specification
           ↓
AI Question Generator
           ↓
Question + Rubric
           ↓
Learner Response
           ↓
LLM Rubric Evaluator
           ↓
Structured Assessment Evidence
           ↓
Learner Model
           ↓
Learner State
           ↓
Skill Map / Next Assessment
           ↺
```

Knota separates three major computational responsibilities.

---

## 3.1 Semantic Intelligence

Large language models perform tasks such as:

- question generation
- rubric generation
- semantic answer evaluation
- concept extraction
- concept relationship inference
- explanation generation

---

## 3.2 Learner-State Estimation

The learner model estimates latent variables such as:

- mastery
- recall probability
- application ability
- retention
- future success probability
- uncertainty

---

## 3.3 Assessment Control

The Assessment Policy determines:

- what concept to assess
- at what cognitive level
- at what difficulty
- in which question format
- when reassessment is needed

The learner model and the assessment policy therefore serve different purposes:

```text
Learner Model
"What is the learner's state?"

Assessment Policy
"What should the system do next?"
```

---

# 4. Concept Representation

A **Concept** represents the knowledge or skill being assessed.

Example:

```text
Concept:
Retrieval-Augmented Generation
```

Concepts can initially be stored as symbolic entities.

Future versions may additionally represent concepts as semantic vectors.

For concept $c$:

$$
e_c \in \mathbb{R}^{d}
$$

where:

- $e_c$ is the semantic embedding of concept $c$
- $d$ is the embedding dimensionality
- $\mathbb{R}^{d}$ is a $d$-dimensional real-valued vector space

The purpose of the embedding is to provide semantic information about the concept to the learner model.

---

# 5. Cognitive Level

Knota distinguishes **cognitive level** from question difficulty.

An initial taxonomy is:

```text
Recall
Understand
Apply
Analyse
```

For example:

### Recall

> What is Retrieval-Augmented Generation?

### Understand

> Why can retrieval reduce hallucination in an LLM application?

### Apply

> Design a RAG architecture for an internal knowledge base.

### Analyse

> Identify weaknesses in the following RAG architecture.

Correct performance at one level does not imply equivalent capability at another.

---

# 6. Question Difficulty

Difficulty represents **item difficulty**, not overall domain difficulty.

For assessment item $q$:

$$
d_q \in [0,1]
$$

Alternatively, an ordinal representation may be used:

```text
1 — Very Easy
2 — Easy
3 — Medium
4 — Hard
5 — Very Hard
```

Initial difficulty may be estimated by an LLM under fixed generation criteria.

Over time, difficulty should be recalibrated from actual learner behaviour.

Conceptually:

```text
Generated Difficulty Estimate
           ↓
Real Learner Responses
           ↓
Observed Difficulty
           ↓
Statistical Calibration
```

Future implementations may use Item Response Theory or related psychometric approaches.

---

# 7. Question Format

Question format is treated separately from cognitive level.

Possible formats include:

```text
Multiple Choice
Short Answer
Explanation
Scenario
Case Analysis
Design Challenge
```

This distinction is important because correct answers from different formats do not necessarily provide equivalent evidence.

For example:

- multiple-choice questions may have a non-zero guessing probability
- short-answer questions require active recall
- explanation questions require semantic structure
- design challenges may provide stronger evidence of application ability

Question format therefore becomes an explicit learner-model input.

---

# 8. AI Question Generation

Questions are generated from a controlled specification.

Example:

```text
Concept:
Retrieval-Augmented Generation

Cognitive Level:
Apply

Difficulty:
3 / 5

Question Format:
Scenario

Grounding:
Provided learning material only
```

The AI generator returns both the assessment question and its scoring rubric.

Example:

```json
{
  "question": "...",
  "concept": "retrieval_augmented_generation",
  "cognitive_level": "apply",
  "difficulty": 3,
  "format": "scenario",
  "rubric": [
    "Identifies a retrieval component",
    "Explains grounding",
    "Addresses access control",
    "Considers retrieval quality"
  ]
}
```

Generated questions should not be accepted blindly.

A validation pipeline should check:

```text
Question Generation
       ↓
Schema Validation
       ↓
Concept Alignment
       ↓
Grounding Validation
       ↓
Difficulty Validation
       ↓
Rubric Alignment
       ↓
Duplicate Detection
```

---

# 9. AI-Based Answer Evaluation

Knota separates **semantic evaluation** from **numerical scoring**.

The LLM evaluates whether rubric criteria have been satisfied.

Example rubric:

```text
A. Retrieval component
B. Grounding mechanism
C. Access control
D. Evaluation strategy
```

An evaluator may return:

```json
{
  "A": true,
  "B": true,
  "C": false,
  "D": true,
  "misconception": false,
  "confidence": 0.91
}
```

The LLM does not directly determine the final Skill Score.

Instead:

```text
LLM
= Semantic Judgement

Deterministic System Logic
= Numerical Scoring
```

This separation is intended to reduce scoring drift and improve reproducibility.

---

# 10. Knota Learner Model v0

The MVP begins with an interpretable, domain-independent learner model.

For learner $u$, concept $c$, cognitive level $l$, and time $t$, the learner state is represented as:

```math
S_{u,c,l,t} \in [0,1]
```

where:

- $u$ represents the learner
- $c$ represents the concept
- $l$ represents the cognitive level
- $t$ represents time
- $S_{u,c,l,t}$ represents the estimated learner state

The learner state should not be interpreted as a direct measurement of true understanding.

It represents the system's current estimate based on accumulated assessment evidence.

---

# 11. Assessment Evidence

Each assessment item $q$ produces structured evidence about learner capability.

For the initial model, assessment evidence is defined as:

```math
E_q
=
R_q
\times
D_q
\times
F_q
\times
C_q
```

where:

- $R_q$ = rubric-based performance
- $D_q$ = difficulty adjustment
- $F_q$ = question-format adjustment
- $C_q$ = evaluator-confidence adjustment

The final evidence value is constrained to the interval:

```math
E_q \in [0,1]
```

If the raw calculation exceeds the interval, it can be clipped:

```math
E_q
=
\min
\left(
1,
\max(0,E_q)
\right)
```

This evidence function is intentionally simple and should be treated as an initial engineering baseline.

---

## 11.1 Rubric Score

For a question with $n$ rubric criteria, suppose the learner satisfies $m$ criteria.

The rubric score is:

```math
R_q = \frac{m}{n}
```

For example, if three of four rubric criteria are satisfied:

```math
R_q
=
\frac{3}{4}
=
0.75
```

More advanced versions may assign different weights to individual rubric criteria.

For weighted rubric criteria:

```math
R_q
=
\frac{
\sum_{i=1}^{n} w_i r_i
}{
\sum_{i=1}^{n} w_i
}
```

where:

- $w_i$ is the importance weight of rubric criterion $i$
- $r_i \in [0,1]$ represents the degree to which criterion $i$ was satisfied

---

# 12. Difficulty Adjustment

Question difficulty modifies the evidential strength of a response.

Let:

```math
d_q \in [0,1]
```

represent estimated item difficulty.

A simple initial difficulty adjustment is:

```math
D_q
=
1
+
\beta
\left(
d_q - 0.5
\right)
```

where $\beta$ controls how strongly question difficulty affects evidence.

For example, if:

```math
\beta = 0.4
```

and:

```math
d_q = 0.8
```

then:

```math
D_q
=
1
+
0.4
\left(
0.8 - 0.5
\right)
=
1.12
```

This means that performance on a relatively difficult question may provide slightly stronger evidence than equivalent performance on an easier question.

However, this formulation is only a provisional approximation.

A future learner model should estimate difficulty from observed learner behaviour rather than relying solely on generated difficulty labels.

---

# 13. Question Format Adjustment

Question format affects the evidential strength of an assessment.

The MVP may initialise format adjustment values manually.

For example:

```math
F_q
=
\begin{cases}
0.70 & \text{Multiple Choice} \\
0.90 & \text{Short Answer} \\
1.00 & \text{Explanation} \\
1.10 & \text{Case Analysis} \\
1.20 & \text{Design Challenge}
\end{cases}
```

These values are not intended as universal cognitive constants.

They are provisional priors.

The hypothesis is that different question formats provide different levels of evidence about learner capability.

For example:

- multiple-choice answers may be influenced by guessing
- short-answer questions require active retrieval
- explanation questions require semantic reconstruction
- case questions require contextual application
- design challenges may provide stronger evidence of transfer and application

Future versions should learn or statistically calibrate these effects from real interaction data.

---

# 14. Forgetting and Time

Learner-state estimates should account for the fact that evidence becomes less reliable as time passes without reassessment.

Before incorporating new evidence, the previous learner state is decayed according to elapsed time:

```math
S^{-}_{u,c,l,t}
=
S_{u,c,l,t-1}
e^{-\lambda \Delta t}
```

where:

- $\lambda$ = forgetting rate
- $\Delta t$ = elapsed time since the previous relevant assessment
- $S^{-}_{u,c,l,t}$ = learner-state estimate after time decay but before incorporating new evidence

The MVP may initially use a single global forgetting parameter:

```math
\lambda_{\mathrm{global}}
```

Future versions may estimate learner-specific forgetting:

```math
\lambda_u
```

concept-specific forgetting:

```math
\lambda_c
```

or learner-concept-specific forgetting:

```math
\lambda_{u,c}
```

This allows the system to represent differences in forgetting behaviour across learners and knowledge domains.

---

# 15. Learner-State Update

After time decay, the learner state is updated using the latest assessment evidence.

The update equation is:

```math
S_{u,c,l,t}
=
(1-\alpha)
S^{-}_{u,c,l,t}
+
\alpha E_q
```

Substituting the time-decay equation gives:

```math
S_{u,c,l,t}
=
(1-\alpha)
S_{u,c,l,t-1}
e^{-\lambda \Delta t}
+
\alpha E_q
```

where:

- $\alpha \in [0,1]$ controls the influence of new evidence
- $\lambda$ controls forgetting over time
- $E_q$ represents evidence from the current assessment

A larger $\alpha$ makes the learner-state estimate respond more quickly to new evidence.

A smaller $\alpha$ makes the estimate more stable and dependent on historical evidence.

This equation is intentionally simple.

It serves as a **general-purpose learner-model baseline**, not as a claim of a validated cognitive or psychometric model.

---

# 16. Confidence and Uncertainty

Knota maintains confidence separately from the estimated Skill Score.

A high Skill Score with little evidence should not be treated as equivalent to the same score supported by repeated assessments.

A simple evidence-count-based confidence function is:

```math
Conf_{u,c,l}
=
1
-
e^{-kn}
```

where:

- $n$ = number of relevant assessments
- $k$ = confidence-growth parameter

As evidence accumulates, confidence approaches 1.

A more advanced version may account for inconsistency in observed evidence:

```math
Conf_{u,c,l}
=
\left(
1-e^{-kn}
\right)
\left(
1-\sigma_E
\right)
```

where $\sigma_E$ represents normalised variability in assessment evidence.

This allows Knota to distinguish:

```text
Skill: 80
Confidence: Low
```

from:

```text
Skill: 80
Confidence: High
```

Confidence is therefore part of the learner-state representation rather than merely a user-interface decoration.

---

# 17. Overall Concept Skill

The multidimensional learner state should remain the authoritative internal representation.

However, the user interface may require a single summary score.

For learner $u$ and concept $c$, an aggregate Skill Score can be defined as:

```math
Skill_{u,c}
=
\sum_l
w_l
S_{u,c,l}
```

where $w_l$ represents the weight assigned to cognitive level $l$.

An example weighting is:

```math
Skill_{u,c}
=
0.15S_{\mathrm{Recall}}
+
0.30S_{\mathrm{Understand}}
+
0.35S_{\mathrm{Apply}}
+
0.20S_{\mathrm{Analyse}}
```

This weighting places greater importance on understanding and application than on simple recall.

The weights are product assumptions, not validated psychometric parameters.

They should therefore be tested and potentially recalibrated.

The aggregate Skill Score is primarily a presentation layer.

Internally, Knota should preserve the individual cognitive dimensions.

---

# 18. Skill Map

Knota exposes learner state through a **Skill Map**.

Example:

```text
AI Architecture

RAG               82
AI Agents         67
MCP               54
Evaluation        43
Governance        76
```

A concept may expose deeper dimensions:

```text
RAG

Recall           91
Understanding    82
Application      58
Analysis         61
Retention        74
Confidence       81
```

The Skill Map is not intended merely as gamification.

It visualises the system's current estimate of learner capability together with the strength of supporting evidence.

The map may also expose:

- weak concepts
- high-confidence strengths
- uncertain areas
- retention risks
- prerequisite gaps
- recently improved skills

---

# 19. Skill Scan

New learners and newly introduced domains create a cold-start problem.

Knota therefore introduces an initial diagnostic process called a **Skill Scan**.

A simple exploration policy may allocate assessments approximately as follows:

```text
50% — Unmeasured concepts
30% — Weak concepts
15% — Retention checks
 5% — Strong-skill challenges
```

The purpose of Skill Scan is not merely to produce an initial score.

Its purpose is to gather information that reduces uncertainty in the learner model.

The user interface may therefore present the process as:

```text
Skill Scan

12 / 20 complete

Mapping your knowledge...
```

As evidence accumulates, the Skill Map becomes increasingly well supported.

---

# 20. Assessment Policy

The learner model estimates the learner's state.

A separate Assessment Policy decides what should be measured next.

For concept $c$ and cognitive level $l$, a simple priority function may be:

```math
Priority(c,l)
=
w_1
\left(
1-S_{u,c,l}
\right)
+
w_2
\left(
1-Conf_{u,c,l}
\right)
+
w_3
R_{u,c,l}
+
w_4
I_c
```

where:

- $S_{u,c,l}$ = estimated skill
- $Conf_{u,c,l}$ = confidence
- $R_{u,c,l}$ = retention risk
- $I_c$ = concept importance
- $w_1,w_2,w_3,w_4$ = policy weights

A concept with low skill, low confidence, high retention risk, or high importance therefore receives higher assessment priority.

Example policy rules include:

```text
if confidence is low:
    run diagnostic assessment

elif understanding is high
and application is low:
    generate application question

elif retention risk is high:
    schedule delayed retrieval test

else:
    explore a related concept
```

This separation is fundamental:

```text
Learner Model
"What is the learner's state?"

Assessment Policy
"What should the system do next?"
```

The learner model estimates.

The policy acts.

---

# 21. Toward a General-Purpose Learner Model

The long-term research objective of Knota is to determine whether learner behaviour across heterogeneous knowledge domains can be represented by a shared computational model.

The v0 learner model uses manually defined parameters.

Future versions can replace these parameters with learned functions.

A future learner model may estimate the probability that learner $u$ succeeds on question $q$ at time $t$:

```math
P
\left(
Y_{u,q,t}=1
\right)
=
f_{\theta}
\left(
Z_{u,t},
e_c,
D_q,
L_q,
F_q,
\Delta t,
H_{u,t}
\right)
```

where:

- $Y_{u,q,t}$ = observed assessment outcome
- $Z_{u,t}$ = user-specific latent learner state
- $e_c$ = semantic representation of concept $c$
- $D_q$ = item difficulty
- $L_q$ = cognitive level
- $F_q$ = question format
- $\Delta t$ = elapsed time
- $H_{u,t}$ = learner interaction history
- $\theta$ = shared population-level model parameters

The conceptual architecture becomes:

```text
Concept Embedding
Difficulty
Cognitive Level
Question Format
Time Gap
Interaction History
        ↓
Shared Learner Model fθ
        ↓
User-Specific Latent State
        ↓
P(success)
Mastery Estimate
Retention Estimate
Uncertainty
```

The objective is not to train an independent model for every learner.

Instead, one shared model learns patterns across the population while individual interaction histories produce personalised learner-state estimates.

---

# 22. Semantic Concept Representation

A categorical Concept ID alone contains no semantic information.

For example:

```text
Concept 481
Concept 927
```

does not tell the model whether two concepts are related.

Knota therefore proposes representing concept $c$ using a semantic embedding:

```math
c
\rightarrow
e_c
```

where:

```math
e_c
\in
\mathbb{R}^{d}
```

and $d$ is the dimensionality of the embedding space.

For example:

```text
Retrieval-Augmented Generation
        ↓
Semantic Representation

Bayesian Inference
        ↓
Semantic Representation

Standard Costing
        ↓
Semantic Representation
```

The hypothesis is that semantic concept representations may support generalisation to concepts that were not observed during model training.

However, semantic similarity does not directly represent:

- learner mastery
- question difficulty
- prerequisite relationships
- forgetting rates
- cognitive complexity

The concept embedding should therefore be treated as one model input rather than as a complete representation of learning behaviour.

---

# 23. Hierarchical Personalisation

A general-purpose learner model should share information across learners while still allowing individual variation.

A future statistical implementation may therefore use hierarchical modelling.

Conceptually:

```text
Global Population
       ↓
Domain
       ↓
Concept
       ↓
Learner
       ↓
Learner × Concept
```

A parameter associated with learner $u$ and concept $c$ can be represented conceptually as:

```math
\theta_{u,c}
=
\theta_{\mathrm{global}}
+
\delta_{\mathrm{domain}}
+
\delta_c
+
\delta_u
+
\delta_{u,c}
```

where:

- $\theta_{\mathrm{global}}$ = shared population baseline
- $\delta_{\mathrm{domain}}$ = domain-specific effect
- $\delta_c$ = concept-specific effect
- $\delta_u$ = learner-specific effect
- $\delta_{u,c}$ = learner-concept interaction effect

This enables **partial pooling**.

When little individual evidence exists, estimates remain closer to global or domain-level priors.

As more evidence is collected, estimates become increasingly personalised.

---

# 24. Model Evolution

The proposed development path is:

```text
Knota v0
Rule-Based Domain-Independent Model
        ↓
Knota v1
Hierarchical Statistical Learner Model
        ↓
Knota v2
Semantic Cross-Domain Learner Model
        ↓
Knota v3
General-Purpose Learner Model
```

## v0

The first model uses:

- manually defined parameters
- deterministic learner-state updates
- explicit confidence
- rule-based assessment policy

Its objective is interpretability and rapid validation.

## v1

The model introduces:

- population-level calibration
- learner-specific effects
- concept-specific effects
- empirical item difficulty
- hierarchical statistical modelling

## v2

The model introduces:

- semantic concept embeddings
- transfer across related concepts
- cross-domain prediction
- learned forgetting effects
- learned question-format effects
- richer learner-history representation

## v3

The research target is a shared learner model capable of operating across heterogeneous domains.

Potential methods include:

- neural Knowledge Tracing
- semantic Knowledge Tracing
- transformer-based sequence models
- KARL-like learner models
- hybrid psychometric and neural models

---

# 25. Structured Interaction Data

Each assessment attempt generates structured evidence.

A Study Attempt may include:

```text
User ID
Concept ID
Concept Embedding
Question ID
Question Format
Estimated Difficulty
Calibrated Difficulty
Cognitive Level
Rubric Score
Response Time
Time Gap
Evaluator Confidence
Timestamp
```

These interactions form the empirical basis for future learner-model training and calibration.

A learner history can be represented as:

```math
H_{u,t}
=
\left\{
x_{u,1},
x_{u,2},
\dots,
x_{u,t-1}
\right\}
```

where each interaction $x_{u,i}$ contains assessment features and observed learner behaviour.

---

# 26. Model Improvement Loops

Knota contains at least two distinct improvement loops.

## 26.1 Learner Model Loop

```text
Prediction
    ↓
Observed Response
    ↓
Prediction Error
    ↓
Model Training
    ↓
Improved Prediction
```

Suppose the model predicts:

```math
\hat{p}_{u,q,t}
=
P
\left(
Y_{u,q,t}=1
\right)
```

and the observed outcome is:

```math
Y_{u,q,t}
\in
\{0,1\}
```

The difference between prediction and outcome provides training evidence for future models.

---

## 26.2 Assessment Calibration Loop

```text
AI-Estimated Difficulty
        ↓
Real Learner Performance
        ↓
Observed Difficulty
        ↓
Calibration
        ↓
Improved Question Generation
```

Generated difficulty becomes a prior rather than a permanent truth.

As response data accumulates, observed item difficulty can replace or adjust the initial LLM estimate.

The resulting system combines elements of:

- learner modelling
- adaptive assessment
- psychometrics
- MLOps
- LLMOps
- semantic retrieval

---

# 27. Relationship to Existing Research

Knota draws on several established research directions.

## 27.1 Knowledge Tracing

Knowledge Tracing attempts to infer latent learner state from sequences of interactions.

Relevant approaches include:

- Bayesian Knowledge Tracing
- Deep Knowledge Tracing
- semantic Knowledge Tracing

Knowledge Tracing provides a conceptual basis for Knota's learner-state estimation.

---

## 27.2 Item Response Theory

Item Response Theory provides a statistical framework for modelling the interaction between learner ability and item difficulty.

A simple one-parameter logistic IRT model is:

```math
P
\left(
Y_{u,q}=1
\right)
=
\frac{
1
}{
1+
e^{-(\theta_u-b_q)}
}
```

where:

- $\theta_u$ = learner ability
- $b_q$ = item difficulty

Future versions of Knota may use IRT-like calibration for question difficulty and learner ability.

---

## 27.3 Forgetting and Memory Models

Human recall typically changes as a function of time.

Knota's v0 exponential decay:

```math
e^{-\lambda \Delta t}
```

is intentionally simplistic.

More sophisticated memory models may eventually replace this approximation.

---

## 27.4 Bloom's Taxonomy

Knota uses cognitive levels to distinguish simple recall from deeper forms of understanding and application.

Bloom-style categories are used as assessment-design metadata rather than as a direct psychometric model.

---

## 27.5 KARL

KARL demonstrates the potential value of combining semantic representations of learning content with learner-history modelling and teaching policies.

Knota extends this direction toward:

- multidimensional skill estimation
- arbitrary user-provided domains
- semantic concept representations
- adaptive assessment generation

---

## 27.6 AI-Based Question Generation

Recent research has explored learner-aware and Knowledge-Tracing-informed question generation.

Knota incorporates question generation into a closed learner-state estimation loop:

```text
Learner State
      ↓
Target Selection
      ↓
Question Generation
      ↓
Response
      ↓
Evidence
      ↓
Updated Learner State
```

---

# 28. Research Questions

The Knota framework raises several empirical research questions.

## RQ1

Can multidimensional learner capability be estimated more accurately than using raw quiz accuracy alone?

## RQ2

Does explicit question-format information improve learner-state prediction?

## RQ3

Does cognitive-level information improve prediction of future learner performance?

## RQ4

How reliably can large language models generate questions at controlled difficulty levels?

## RQ5

How stable is rubric-based LLM evaluation compared with expert human grading?

## RQ6

How much learner interaction data is required before personalised estimates outperform population priors?

## RQ7

Can Knota Skill Scores predict future learner performance?

## RQ8

Can a shared learner model outperform domain-specific baselines across heterogeneous domains?

## RQ9

Do semantic Concept representations improve prediction on previously unseen concepts?

## RQ10

Which learner-model parameters transfer across domains, and which require domain-specific or learner-specific adaptation?

## RQ11

Can uncertainty-aware Skill Maps improve learners' awareness of their own knowledge gaps?

---

# 29. Evaluation Strategy

Knota should not be evaluated solely using engagement metrics.

The learner model itself requires quantitative validation.

---

## 29.1 Future Response Prediction

The model predicts:

```math
\hat{p}_{u,q,t}
=
P
\left(
Y_{u,q,t}=1
\right)
```

Possible evaluation metrics include:

- Accuracy
- ROC-AUC
- Log Loss
- Brier Score

---

## 29.2 Probability Calibration

A learner model should produce calibrated probabilities.

If Knota predicts:

```math
P(\text{success}) = 0.8
```

for a sufficiently large set of comparable assessment events, approximately 80% of those events should result in success.

Calibration can be evaluated using:

- Expected Calibration Error
- Brier Score
- reliability diagrams

Calibration is particularly important because Knota presents learner state probabilistically.

---

## 29.3 Cross-Domain Generalisation

One of Knota's central experiments is to train the learner model on several knowledge domains and evaluate it on held-out domains.

For example:

```text
Training Domains

AI
Statistics
Finance

Held-Out Domains

Enterprise Software
Organic Chemistry
Networking
```

Possible model comparisons include:

```text
Baseline A
Global learner model without semantic concept information

Baseline B
Domain-specific learner model

Model C
Semantic cross-domain learner model
```

If Model C performs well on previously unseen domains, this would provide evidence for the General-Purpose Learner Model hypothesis.

---

## 29.4 Ablation Studies

The contribution of individual model inputs should also be evaluated.

For example:

```text
Full Model
Concept Embedding
+ Difficulty
+ Cognitive Level
+ Question Format
+ Time Gap
+ Interaction History
```

Compare against versions with individual variables removed.

Examples:

```text
Without Concept Embedding
Without Question Format
Without Cognitive Level
Without Time Gap
```

This helps determine which variables genuinely improve prediction.

---

# 30. MVP Scope

The initial MVP deliberately avoids advanced machine learning.

The first implementation is:

```text
Defined Concepts
      ↓
AI Question + Rubric
      ↓
Learner Answer
      ↓
LLM Rubric Evaluation
      ↓
Rule-Based Learner Model
      ↓
Skill Map
      ↓
Rule-Based Next Assessment
```

The MVP does not require:

- reinforcement learning
- custom neural-network training
- KARL implementation
- automated Knowledge Graph generation
- universal PDF understanding
- large-scale cross-domain model training

The MVP should primarily answer:

> **Does an evidence-based, uncertainty-aware Skill Map create meaningful value for learners?**

A secondary objective is to begin collecting structured Study Attempt data for future model calibration.

---

# 31. Long-Term Vision

Knota aims to evolve from an assessment application into a continuously updated **Personal Skill Model**.

Traditional systems often answer:

> What have you studied?

or:

> What should you review?

Knota aims to answer:

> **What can you currently recall, understand, apply, and analyse — and how confident should we be in that estimate?**

The long-term feedback loop is:

```text
Learning
   ↓
Assessment
   ↓
Evidence
   ↓
Learner Model
   ↓
Skill Map
   ↓
Adaptive Challenge
   ↓
New Evidence
   ↺
```

If a sufficiently general learner model can be developed, Knota may eventually provide a common representation of learning state across multiple domains.

The objective is not to produce a definitive measurement of human knowledge.

It is to produce an:

> **Increasingly calibrated estimate of learner capability from structured assessment evidence.**

---

# 32. Current Status and Limitations

Knota is currently a proposed experimental framework.

The learner models, parameters, weighting functions, and policies described in this document are **research hypotheses and engineering baselines**.

They have not yet been empirically validated.

Important limitations include:

- manually selected parameters are not psychometrically calibrated
- LLM grading reliability requires empirical evaluation
- generated question difficulty may be unstable
- semantic similarity does not imply equivalent learning behaviour
- forgetting rates may differ substantially across learners and concepts
- question-format effects are currently hypothetical
- cognitive-level classification may itself contain uncertainty
- cross-domain learner-model generalisation remains an open research question
- Skill Scores should not initially be interpreted as absolute measures of competence
- the v0 learner-state update does not model all dependencies between repeated assessments

The purpose of Knota v0.1 is therefore not to claim that a validated General-Purpose Learner Model already exists.

Its purpose is to define a **testable architecture and research programme** for investigating whether such a model can be developed.

---

# 33. Proposed Development Roadmap

```text
Whitepaper v0.1
      ↓
Knota MVP
      ↓
Structured Study Attempts
      ↓
Parameter Calibration
      ↓
Whitepaper v0.2
      ↓
Hierarchical Learner Model
      ↓
Cross-Domain Evaluation
      ↓
Semantic Learner Model
      ↓
General-Purpose Learner Model Experiments
      ↓
Preprint / Research Publication
```

The transition between stages should be evidence-driven rather than feature-driven.

In particular, advanced modelling should be introduced only after sufficient learner interaction data has been collected.

---

# 34. Conclusion

Knota proposes a shift from learning-content management toward **learner-state modelling**.

The framework combines:

- dynamically generated assessments
- structured rubric evaluation
- multidimensional skill estimation
- uncertainty modelling
- adaptive assessment policies
- semantic concept representations
- cross-domain learner modelling

The initial Knota v0 model is deliberately simple and interpretable.

Its purpose is to establish a baseline from which increasingly sophisticated statistical and machine-learning models can be developed.

The long-term research objective is to investigate whether a shared model can represent and predict learning behaviour across heterogeneous concepts, learners, and domains.

Knota therefore progresses conceptually from:

```text
Rule-Based Learner Model
        ↓
Statistical Learner Model
        ↓
Semantic Cross-Domain Learner Model
        ↓
General-Purpose Learner Model
```

In this sense, Knota is not primarily a flashcard system or quiz generator.

It is an experimental framework for estimating:

> **What a learner can currently do, how certain that estimate is, and what evidence should be collected next.**
