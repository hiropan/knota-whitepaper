# Knota  
## A General-Purpose AI-Based Skill Assessment and Learner Modelling Framework

### Version 0.1 — Concept Whitepaper

---

## Abstract

Knota is an experimental AI-native framework for estimating what a learner actually understands and can apply.

Traditional learning systems frequently focus on content consumption, memorisation, or question answering. Flashcard and spaced-repetition systems are particularly effective for memory retention, but they do not necessarily measure whether a learner can explain, apply, analyse, or transfer a concept.

Knota instead treats learning as a problem of **latent learner-state estimation**.

The framework combines:

- AI-generated assessment questions,
- rubric-based semantic answer evaluation,
- multidimensional learner-state estimation,
- adaptive assessment policies,
- semantic concept representations,
- and uncertainty-aware skill visualisation.

The initial implementation uses a domain-independent rule-based learner model. Over time, Knota is designed to evolve toward a **general-purpose learner model** trained across users, concepts, cognitive levels, question formats, and time.

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

- explain the underlying mechanism,
- distinguish it from similar concepts,
- apply it in a new situation,
- diagnose an incorrect implementation,
- or design a solution using the concept.

Knota therefore focuses not primarily on content delivery, but on **measurement of learner capability**.

---

# 2. Core Hypothesis

Knota is based on four primary hypotheses.

## H1 — Skill is multidimensional

A concept should not be represented by a single binary mastered/not-mastered flag.

For a concept \(c\), learner \(u\) may instead have a state such as:

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

- rubric performance,
- question difficulty,
- cognitive level,
- question format,
- response latency,
- previous performance,
- time since previous assessment,
- consistency across repeated assessments.

Knota therefore represents skill as an **estimate with uncertainty**, rather than an absolute truth.

---

## H3 — Assessment should adapt to the learner state

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

The system therefore creates a closed assessment loop.

---

## H4 — Some learning dynamics may generalise across domains

Although knowledge domains differ, some patterns of learning may be transferable.

For example:

- forgetting over time,
- differences between recall and application,
- effects of question difficulty,
- effects of repeated evidence,
- effects of uncertainty,
- differences between question formats.

Knota investigates whether these patterns can be captured by a **shared learner model** rather than requiring a completely independent model for every domain.

---

# 3. System Overview

The conceptual Knota architecture is:

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

Knota separates three major responsibilities.

## Semantic Intelligence

Large language models perform tasks such as:

- question generation,
- rubric generation,
- semantic answer evaluation,
- concept extraction,
- concept relationship inference,
- explanation generation.

## Learner-State Estimation

The learner model estimates latent variables such as:

- mastery,
- recall probability,
- application ability,
- retention,
- future success probability,
- uncertainty.

## Assessment Control

The Teaching or Assessment Policy determines:

- what concept to assess,
- at what cognitive level,
- at what difficulty,
- in which question format,
- and when reassessment is needed.

---

# 4. Assessment Dimensions

Knota separates several properties of an assessment item.

---

## 4.1 Concept

A Concept represents the knowledge or skill being assessed.

Example:

```text
Concept:
Retrieval-Augmented Generation
```

Concepts can initially be stored as symbolic entities.

Future versions may additionally represent them as semantic vectors:

\[
e_c \in \mathbb{R}^{d}
\]

where \(e_c\) is the Concept embedding.

The embedding enables the model to capture semantic relationships between previously unseen concepts.

---

# 5. Cognitive Level

Knota distinguishes cognitive depth from question difficulty.

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

A question may initially receive an estimated difficulty:

\[
d_q \in [0,1]
\]

or an ordinal scale such as:

```text
1 — Very Easy
2 — Easy
3 — Medium
4 — Hard
5 — Very Hard
```

Initial difficulty may be estimated by an LLM under fixed criteria.

Over time, difficulty should be recalibrated from actual response data.

Conceptually:

```text
Generated Difficulty Estimate
           ↓
Real User Responses
           ↓
Observed Difficulty
           ↓
Statistical Calibration
```

Future implementations may use Item Response Theory or related models.

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

This distinction matters because correct answers from different formats do not provide equivalent evidence.

For example, multiple-choice questions have a non-zero guessing probability, while open-ended design questions may provide stronger evidence of application ability.

Question format therefore becomes an explicit model input.

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

The AI generator returns both a question and a scoring rubric.

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

Question generation should not be accepted blindly.

A validation stage should check:

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

Knota separates semantic evaluation from numerical scoring.

The LLM evaluates whether rubric criteria are satisfied.

Example rubric:

```text
A. Retrieval component
B. Grounding mechanism
C. Access control
D. Evaluation strategy
```

The evaluator may return:

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

The LLM does **not** directly determine the final Skill Score.

Instead:

```text
LLM
= Semantic Judgement

Deterministic System Logic
= Numerical Scoring
```

This design reduces scoring drift and improves reproducibility.

---

# 10. Knota Learner Model v0

The MVP begins with an interpretable domain-independent learner model.

For learner \(u\), concept \(c\), cognitive level \(l\), and time \(t\):

\[
S_{u,c,l,t} \in [0,1]
\]

where \(S\) represents the estimated learner state.

---

## 10.1 Evidence from an Assessment

For assessment item \(q\), define assessment evidence:

\[
E_q =
R_q
\times
D_q
\times
F_q
\]

where:

- \(R_q\): rubric score,
- \(D_q\): difficulty adjustment,
- \(F_q\): question-format adjustment.

A confidence term may additionally be used:

\[
E_q =
R_q
\times
D_q
\times
F_q
\times
C_q
\]

where \(C_q\) is evaluator confidence.

---

## 10.2 Rubric Score

If \(m\) of \(n\) rubric criteria are satisfied:

\[
R_q = \frac{m}{n}
\]

For example:

\[
R_q = \frac{3}{4} = 0.75
\]

---

# 11. Difficulty Adjustment

Question difficulty modifies the strength of the evidence.

One simple formulation is:

\[
D_q =
1 + \beta(d_q - 0.5)
\]

where:

- \(d_q \in [0,1]\),
- \(\beta\) controls the effect of difficulty.

For example, harder questions may produce slightly stronger positive evidence when answered correctly.

The exact values are provisional and should later be statistically calibrated.

---

# 12. Question Format Adjustment

Initial format weights can be manually defined.

For example:

\[
F_q =
\begin{cases}
0.70 & \text{Multiple Choice}\\
0.90 & \text{Short Answer}\\
1.00 & \text{Explanation}\\
1.10 & \text{Case Analysis}\\
1.20 & \text{Design Challenge}
\end{cases}
\]

These values should not be interpreted as universal constants.

They are initial priors intended for later empirical calibration.

---

# 13. Forgetting and Time

Skill evidence should decay with time if it is not revalidated.

Before processing new evidence:

\[
S^-_{u,c,l,t}
=
S_{u,c,l,t-1}
e^{-\lambda \Delta t}
\]

where:

- \(\lambda\): forgetting rate,
- \(\Delta t\): elapsed time.

The MVP may use a globally shared \(\lambda\).

Future versions may estimate:

\[
\lambda_u
\]

or:

\[
\lambda_{u,c}
\]

to represent learner- or concept-specific forgetting.

---

# 14. Learner-State Update

The updated learner state is:

\[
\boxed{
S_{u,c,l,t}
=
(1-\alpha)
S^-_{u,c,l,t}
+
\alpha E_q
}
\]

or equivalently:

\[
\boxed{
S_{u,c,l,t}
=
(1-\alpha)
S_{u,c,l,t-1}
e^{-\lambda \Delta t}
+
\alpha E_q
}
\]

where \(\alpha\) determines how strongly new evidence changes the state estimate.

This equation is intentionally simple.

It serves as a **general-purpose learner-model baseline**, not as a claim of a validated cognitive model.

---

# 15. Confidence

Knota maintains uncertainty separately from the estimated Skill Score.

A simple confidence function is:

\[
Conf_{u,c,l}
=
1-e^{-kn}
\]

where:

- \(n\): number of relevant assessments,
- \(k\): confidence growth parameter.

A more robust version includes inconsistency:

\[
Conf_{u,c,l}
=
(1-e^{-kn})
(1-\sigma_E)
\]

where \(\sigma_E\) represents variation in observed evidence.

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

---

# 16. Overall Concept Skill

Internal learner-state dimensions should remain separate.

However, the UI may require a single summary value.

For concept \(c\):

\[
Skill_{u,c}
=
\sum_l w_l S_{u,c,l}
\]

For example:

\[
Skill_{u,c}
=
0.15S_{Recall}
+
0.30S_{Understand}
+
0.35S_{Apply}
+
0.20S_{Analyse}
\]

The weighting reflects product priorities and should eventually be configurable or empirically validated.

The aggregate score is intended primarily for presentation.

The underlying multidimensional state remains the authoritative model.

---

# 17. Skill Map

Knota exposes learner state through a Skill Map.

Example:

```text
AI Architecture

RAG               82
AI Agents         67
MCP               54
Evaluation        43
Governance        76
```

A concept may expose detailed dimensions:

```text
RAG

Recall           91
Understanding    82
Application      58
Analysis         61
Retention        74
Confidence       81
```

The purpose of the map is not merely gamification.

It is intended to visualise the system's current belief about the learner's capabilities.

---

# 18. Skill Scan

New users and new domains create a cold-start problem.

Knota therefore introduces a diagnostic assessment process called a **Skill Scan**.

A simple exploration policy might allocate questions as follows:

```text
50% — Unmeasured concepts
30% — Weak concepts
15% — Retention checks
 5% — Strong-skill challenge
```

The objective is to improve the resolution of the learner model.

The UI may therefore present:

```text
Skill Scan

12 / 20 complete

Mapping your knowledge...
```

As evidence increases, the Skill Map becomes more precise.

---

# 19. Assessment Policy

The learner model estimates state.

A separate policy determines the next assessment action.

For example:

\[
Priority(c,l)
=
w_1(1-S_{u,c,l})
+
w_2(1-Conf_{u,c,l})
+
w_3RetentionRisk
+
w_4Importance_c
\]

The highest-priority Concept × Cognitive Level becomes a candidate for the next assessment.

Example rules:

```text
if confidence is low:
    run diagnostic assessment

elif understanding is high
and application is low:
    generate application question

elif retention risk is high:
    schedule delayed retrieval test

else:
    explore related concept
```

This separation is fundamental:

```text
Learner Model:
"What is the learner's state?"

Assessment Policy:
"What should the system do next?"
```

---

# 20. Toward a General-Purpose Learner Model

The long-term research objective of Knota is to determine whether learner behaviour across heterogeneous knowledge domains can be represented by a shared model.

The v0 equation uses manually defined parameters.

Future versions replace these fixed values with learned behaviour.

The learner model may eventually estimate:

\[
\boxed{
P(Y_{u,q,t}=1)
=
f_{\theta}
(
Z_{u,t},
E_c,
D_q,
L_q,
F_q,
\Delta t,
H_{u,t}
)
}
\]

where:

- \(Y_{u,q,t}\): future assessment outcome,
- \(Z_{u,t}\): user-specific latent learner state,
- \(E_c\): Concept embedding,
- \(D_q\): item difficulty,
- \(L_q\): cognitive level,
- \(F_q\): question format,
- \(\Delta t\): time since relevant evidence,
- \(H_{u,t}\): interaction history,
- \(\theta\): shared population-level model parameters.

The architecture is therefore:

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
Mastery
Retention
Uncertainty
```

---

# 21. Why Concept Embeddings Matter

A categorical Concept ID alone provides no information about an unseen concept.

For example:

```text
Concept 481
Concept 927
```

does not tell the learner model whether the concepts are related.

Semantic embeddings instead represent concept meaning:

\[
c \rightarrow E_c
\]

Examples:

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

The hypothesis is that semantic representations may allow partial transfer of learner-model behaviour to unseen concepts.

This does not imply that embeddings directly encode:

- mastery,
- difficulty,
- prerequisite relationships,
- or forgetting rates.

They are one source of information within the broader learner model.

---

# 22. Hierarchical Personalisation

A shared learner model should not assume that all users or concepts behave identically.

A future statistical model may use hierarchical structure:

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

This enables **partial pooling**.

When little evidence exists, estimates remain closer to population priors.

As evidence accumulates, estimates become increasingly personalised.

This improves cold-start behaviour while still allowing individual differences.

---

# 23. Model Evolution

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

### v0

- manually defined parameters,
- deterministic updating,
- confidence tracking.

### v1

- population-level calibration,
- user effects,
- concept effects,
- item difficulty estimation.

### v2

- semantic Concept embeddings,
- cross-domain transfer,
- learned forgetting and interaction effects.

### v3

- shared learner model across heterogeneous domains,
- domain-generalisation evaluation,
- potentially neural knowledge tracing or KARL-like architectures.

---

# 24. Data Model

Each assessment attempt creates structured evidence.

Example:

```text
User
Concept
Question
Question Format
Difficulty
Cognitive Level
Rubric Score
Response Time
Time Gap
Evaluator Confidence
Timestamp
```

These Study Attempts form the empirical basis for future model development.

---

# 25. Learning Loops

Knota contains two distinct improvement loops.

## Learner Model Loop

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

## Assessment Calibration Loop

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

The resulting system combines elements of:

- MLOps,
- LLMOps,
- learner modelling,
- adaptive assessment.

---

# 26. Relationship to Existing Research

Knota draws on several established research areas.

## Knowledge Tracing

Knowledge Tracing models infer latent learner state from sequences of learner interactions.

Relevant approaches include:

- Bayesian Knowledge Tracing,
- Deep Knowledge Tracing,
- semantic Knowledge Tracing.

## Item Response Theory

IRT provides a framework for jointly modelling learner ability and item difficulty.

It is particularly relevant to future calibration of Knota assessment items.

## Forgetting Models

Spaced repetition and memory models provide methods for estimating retention over time.

## Bloom's Taxonomy

Knota uses cognitive levels to distinguish recall from deeper forms of understanding and application.

## KARL

KARL demonstrates the potential value of combining semantic content representations with learner-history modelling and teaching policies.

## AI-Based Question Generation

Recent research explores Knowledge Tracing-informed and learner-aware question generation.

Knota extends this idea into a closed assessment and skill-estimation loop.

---

# 27. Research Questions

The proposed framework raises several empirical questions.

### RQ1

Can learner capability across multiple cognitive levels be estimated more accurately than using raw quiz accuracy alone?

### RQ2

Does explicit question format improve learner-state prediction?

### RQ3

Does cognitive-level information improve future-performance prediction?

### RQ4

How reliably can LLMs generate questions at controlled difficulty levels?

### RQ5

How stable is rubric-based LLM evaluation compared with expert human grading?

### RQ6

How much learner interaction data is required before personalised estimates outperform population priors?

### RQ7

Can Knota Skill Scores predict future learner performance?

### RQ8

Can a shared learner model outperform domain-specific baselines across heterogeneous domains?

### RQ9

Do semantic Concept representations improve prediction on previously unseen concepts?

### RQ10

Which learner-model parameters transfer across domains, and which require domain-specific adaptation?

### RQ11

Can uncertainty-aware Skill Maps improve learners' awareness of their own knowledge gaps?

---

# 28. Evaluation Strategy

Knota should not be evaluated solely on user engagement.

The learner model itself requires quantitative evaluation.

Possible metrics include:

### Future response prediction

- Accuracy
- Log Loss
- ROC-AUC
- Brier Score

### Probability calibration

If Knota predicts:

\[
P(success)=0.8
\]

approximately 80% of comparable responses should succeed.

Calibration can be measured using:

- Expected Calibration Error,
- Brier Score,
- reliability diagrams.

### Cross-domain generalisation

Train on selected domains and evaluate on held-out domains.

For example:

```text
Training:
AI
Statistics
Finance

Testing:
SAP
Organic Chemistry
Networking
```

This is one of the most important experiments for the general-purpose learner-model hypothesis.

---

# 29. MVP Scope

The MVP deliberately avoids advanced machine learning.

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

- reinforcement learning,
- custom neural models,
- KARL implementation,
- automated Knowledge Graph generation,
- universal PDF understanding,
- large-scale model training.

The MVP should primarily answer:

> **Does an evidence-based, uncertainty-aware Skill Map create meaningful value for learners?**

---

# 30. Long-Term Vision

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

The central objective is not to produce a definitive measure of human knowledge.

It is to produce an **increasingly calibrated estimate of capability from structured learning evidence**.

---

# 31. Current Status and Limitations

Knota is currently a proposed framework.

The learner models, parameters, and weighting functions described in this document are **research hypotheses and engineering baselines**.

They have not yet been empirically validated.

In particular:

- manually selected parameters are not psychometrically calibrated,
- LLM grading reliability must be experimentally evaluated,
- cross-domain learner-model generalisation remains an open research question,
- semantic similarity does not imply equivalent learning behaviour,
- Skill Scores should not initially be interpreted as absolute measures of competence.

The purpose of Knota v0.1 is to define a testable architecture from which these questions can be investigated.
