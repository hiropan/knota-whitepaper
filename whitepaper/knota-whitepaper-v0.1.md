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

For learner $u$, concept $c$, cognitive level $l$, and time $t$:

$$
S_{u,c,l,t} \in [0,1]
$$

where $S_{u,c,l,t}$ represents the estimated learner state.

---

# 11. Assessment Evidence

For assessment item $q$, Knota derives an evidence value $E_q$.

A simple formulation is:

$$
E_q =
R_q
\times
D_q
\times
F_q
\times
C_q
$$

where:

- $R_q$ = rubric score
- $D_q$ = difficulty adjustment
- $F_q$ = question-format adjustment
- $C_q$ = evaluator confidence adjustment

The resulting evidence value is clipped to:

$$
E_q \in [0,1]
$$

---

## 11.1 Rubric Score

If $m$ of $n$ rubric criteria are satisfied:

$$
R_q = \frac{m}{n}
$$

For example:

$$
R_q = \frac{3}{4} = 0.75
$$

---

# 12. Difficulty Adjustment

Question difficulty modifies the strength of the evidence.

A simple initial formulation is:

$$
D_q =
1 + \beta(d_q - 0.5)
$$

where:

- $d_q \in [0,1]$
- $\beta$ controls the magnitude of difficulty adjustment

For example, with:

$$
\beta = 0.4
$$

a relatively difficult item with:

$$
d_q = 0.8
$$

produces:

$$
D_q = 1 + 0.4(0.8 - 0.5) = 1.12
$$

These values are provisional engineering parameters and should later be empirically calibrated.

---

# 13. Question Format Adjustment

The MVP may initialise question-format weights manually.

For example:

$$
F_q =
\begin{cases}
0.70 & \text{Multiple Choice} \\
0.90 & \text{Short Answer} \\
1.00 & \text{Explanation} \\
1.10 & \text{Case Analysis} \\
1.20 & \text{Design Challenge}
\end{cases}
$$

These values should not be interpreted as universal constants.

They are initial priors intended for later empirical calibration.

---

# 14. Forgetting and Time

Skill evidence should weaken over time if it is not revalidated.

Before processing new evidence:

$$
S^{-}_{u,c,l,t}
=
S_{u,c,l,t-1}
e^{-\lambda \Delta t}
$$

where:

- $\lambda$ = forgetting rate
- $\Delta t$ = elapsed time since relevant evidence

The MVP may initially use a globally shared forgetting rate:

$$
\lambda_{\text{global}}
$$

Future versions may estimate learner-specific or concept-specific parameters:

$$
\lambda_u
$$

or:

$$
\lambda_{u,c}
$$

---

# 15. Learner-State Update

The updated learner state is defined as:

$$
S_{u,c,l,t}
=
(1-\alpha)
S^{-}_{u,c,l,t}
+
\alpha E_q
$$

Substituting the forgetting function gives:

$$
\boxed{
S_{u,c,l,t}
=
(1-\alpha)
S_{u,c,l,t-1}
e^{-\lambda \Delta t}
+
\alpha E_q
}
$$

where:

- $\alpha$ determines how strongly new evidence affects the learner-state estimate
- $\lambda$ determines the effect of time
- $E_q$ represents the latest assessment evidence

This equation is intentionally simple.

It serves as a **general-purpose learner-model baseline**, not as a claim of a validated cognitive or psychometric model.

---

# 16. Confidence

Knota maintains confidence separately from the estimated learner state.

A simple evidence-count-based confidence function is:

$$
Conf_{u,c,l}
=
1 - e^{-kn}
$$

where:

- $n$ is the number of relevant assessments
- $k$ controls how quickly confidence increases

A more robust version can include evidence inconsistency:

$$
Conf_{u,c,l}
=
(1-e^{-kn})(1-\sigma_E)
$$

where $\sigma_E$ represents variability in observed evidence.

This allows Knota to distinguish between:

```text
Skill: 80
Confidence: Low
```

and:

```text
Skill: 80
Confidence: High
```

This distinction is central to Knota's design.

---

# 17. Overall Concept Skill

The multidimensional learner state should remain the authoritative internal representation.

However, the user interface may require a single summary score.

For concept $c$:

$$
Skill_{u,c}
=
\sum_l w_l S_{u,c,l}
$$

An example weighting is:

$$
Skill_{u,c}
=
0.15S_{\text{Recall}}
+
0.30S_{\text{Understand}}
+
0.35S_{\text{Apply}}
+
0.20S_{\text{Analyse}}
$$

The weighting reflects product priorities and should eventually be empirically validated.

The aggregate score is intended primarily for presentation.

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

The purpose of the Skill Map is not merely gamification.

It visualises the system's current belief about learner capability and the uncertainty associated with that estimate.

---

# 19. Skill Scan

New users and new knowledge domains create a cold-start problem.

Knota therefore introduces a diagnostic process called a **Skill Scan**.

An initial exploration policy may allocate assessments approximately as follows:

```text
50% — Unmeasured concepts
30% — Weak concepts
15% — Retention checks
 5% — Strong-skill challenges
```

The purpose is to increase the resolution of the learner model.

Example user interface:

```text
Skill Scan

12 / 20 complete

Mapping your knowledge...
```

As evidence accumulates, the Skill Map becomes increasingly reliable.

---

# 20. Assessment Policy

The learner model estimates state.

A separate policy determines the next assessment action.

One simple priority function is:

$$
Priority(c,l)
=
w_1(1-S_{u,c,l})
+
w_2(1-Conf_{u,c,l})
+
w_3R_{u,c,l}
+
w_4I_c
$$

where:

- $S_{u,c,l}$ = estimated skill
- $Conf_{u,c,l}$ = confidence
- $R_{u,c,l}$ = retention risk
- $I_c$ = concept importance

Example policy rules:

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

The separation remains:

```text
Learner Model
"What is the learner's state?"

Assessment Policy
"What should the system do next?"
```

---

# 21. Toward a General-Purpose Learner Model

The long-term research objective of Knota is to determine whether learner behaviour across heterogeneous knowledge domains can be represented by a shared computational model.

The v0 model uses manually defined parameters.

Future versions may replace these values with learned model parameters.

The learner model can then be represented as:

$$
\boxed{
P(Y_{u,q,t}=1)
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
}
$$

where:

- $Y_{u,q,t}$ = future assessment outcome
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

Instead, a shared model learns population-level patterns while individual histories produce personalised latent learner states.

---

# 22. Why Concept Embeddings Matter

A categorical Concept ID provides little information about an unseen concept.

For example:

```text
Concept 481
Concept 927
```

contains no semantic information.

A concept embedding instead provides:

$$
c \rightarrow e_c
$$

with:

$$
e_c \in \mathbb{R}^{d}
$$

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

The hypothesis is that semantic representations may support partial transfer to previously unseen concepts.

However, semantic similarity does not imply identical:

- learner mastery
- difficulty
- prerequisite structure
- forgetting behaviour

Concept embeddings therefore represent only one input to the learner model.

---

# 23. Hierarchical Personalisation

A shared learner model should not assume that every learner or every concept behaves identically.

A future statistical implementation may use hierarchical structure:

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

When little evidence exists, estimates remain close to population priors.

As evidence accumulates, estimates become increasingly personalised.

Conceptually:

$$
\theta_{u,c}
=
\theta_{\text{global}}
+
\delta_{\text{domain}}
+
\delta_c
+
\delta_u
+
\delta_{u,c}
$$

where the correction terms represent domain-, concept-, learner-, and learner-concept-specific effects.

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

- manually defined parameters
- deterministic updates
- confidence tracking
- rule-based assessment policy

## v1

- population-level calibration
- user-specific effects
- concept-specific effects
- item difficulty estimation
- hierarchical statistical modelling

## v2

- semantic concept embeddings
- cross-domain transfer
- learned forgetting effects
- learned question-format effects
- richer interaction-history modelling

## v3

- shared learner model across heterogeneous domains
- domain-generalisation evaluation
- neural Knowledge Tracing or KARL-like architectures where appropriate

---

# 25. Data Model

Each assessment attempt generates structured evidence.

A Study Attempt may contain:

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

This interaction history forms the empirical basis for future learner-model development.

---

# 26. Learning and MLOps Loops

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

The resulting system combines elements of:

- learner modelling
- adaptive assessment
- MLOps
- LLMOps
- semantic retrieval
- psychometrics

---

# 27. Relationship to Existing Research

Knota draws on several established research directions.

## 27.1 Knowledge Tracing

Knowledge Tracing attempts to infer latent learner state from sequences of learner interactions.

Relevant approaches include:

- Bayesian Knowledge Tracing
- Deep Knowledge Tracing
- semantic Knowledge Tracing

---

## 27.2 Item Response Theory

Item Response Theory provides a framework for jointly modelling learner ability and item difficulty.

It may be useful for future calibration of Knota assessment items.

---

## 27.3 Forgetting and Memory Models

Spaced-repetition and memory models provide methods for modelling retention over time.

These approaches may inform Knota's future retention component.

---

## 27.4 Bloom's Taxonomy

Knota uses cognitive levels to distinguish simple recall from deeper forms of understanding and application.

The taxonomy is used as an assessment-design abstraction rather than as a direct psychometric model.

---

## 27.5 KARL

KARL demonstrates the potential value of combining semantic representations of learning content with learner-history modelling and teaching policies.

Knota extends this direction toward multidimensional assessment and heterogeneous knowledge domains.

---

## 27.6 AI-Based Question Generation

Recent research has explored learner-aware and Knowledge-Tracing-informed question generation.

Knota incorporates question generation into a closed learner-state estimation loop.

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

How much interaction data is required before personalised estimates outperform population priors?

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

Knota should not be evaluated solely on engagement or user retention.

The learner model itself requires quantitative evaluation.

---

## 29.1 Future Response Prediction

Possible metrics include:

- Accuracy
- ROC-AUC
- Log Loss
- Brier Score

---

## 29.2 Probability Calibration

If Knota predicts:

$$
P(\text{success}) = 0.8
$$

then approximately 80% of comparable assessment attempts should eventually succeed.

Calibration may be evaluated using:

- Expected Calibration Error
- Brier Score
- reliability diagrams

---

## 29.3 Cross-Domain Generalisation

One key experiment is to train on several domains while holding out an unrelated domain.

For example:

```text
Training Domains
- AI
- Statistics
- Finance

Held-Out Domains
- Enterprise Software
- Organic Chemistry
- Networking
```

Performance can then be compared between:

1. global baseline
2. domain-specific model
3. semantic cross-domain model

This provides a direct test of the General-Purpose Learner Model hypothesis.

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
- automated Knowledge Graph construction
- universal PDF understanding
- large-scale cross-domain model training

The MVP should primarily answer:

> **Does an evidence-based, uncertainty-aware Skill Map create meaningful value for learners?**

---

# 31. Long-Term Vision

Knota aims to evolve from an assessment application into a continuously updated **Personal Skill Model**.

Traditional learning systems often answer questions such as:

> What have you studied?

or:

> What should you review next?

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

The objective is not to produce a definitive measure of human knowledge.

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
- forgetting rates may differ substantially between learners and concepts
- question-format effects are currently hypothetical
- cross-domain learner-model generalisation remains an open research question
- Skill Scores should not initially be interpreted as absolute measures of competence

The purpose of Knota v0.1 is therefore not to claim a validated General-Purpose Learner Model.

Its purpose is to define a **testable architecture and research programme** for investigating whether such a model can be developed.

---

# 33. Proposed Development Roadmap

```text
Whitepaper v0.1
      ↓
Knota MVP
      ↓
Initial Study Attempts
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
Preprint / Research Publication
```

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

The long-term research objective is to investigate whether a shared model can represent and predict human learning behaviour across heterogeneous concepts, users, and domains.

In this sense, Knota is not primarily a flashcard system or quiz generator.

It is an experimental framework for building a:

> **General-Purpose Learner Model.**
