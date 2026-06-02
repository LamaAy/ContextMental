# 🧠 ContextMental

**ContextMental: A Sociocultural Benchmark for Arabic Mental Health Understanding**

[![Paper](https://img.shields.io/badge/Journal-Electronics%20(under%20review)-blue)]()
[![Dataset](https://img.shields.io/badge/Resource-Dataset-green)]()
[![Task](https://img.shields.io/badge/Task-Multi--label%20Classification-orange)]()
[![Language](https://img.shields.io/badge/Language-Arabic-lightgrey)]()
[![Use](https://img.shields.io/badge/Use-Research%20Only-red)]()

---

## 🌍 Overview

**ContextMental** is a benchmark dataset for identifying **sociocultural contextual factors** in Arabic mental health questions. The dataset focuses on how patient-authored questions express psychological distress through **social relationships**, **cultural norms and values**, and **religious framing**.

Most Arabic mental health NLP resources focus on clinical-condition detection, question answering, or general health classification. ContextMental instead provides explicit labels for contextual cues that shape how mental health concerns are described in Arabic-speaking settings.

---

## 📁 Repository Contents

| File | Description |
|---|---|
| `ContextMentalQA 2677.csv` | Arabic mental health questions with contextual labels. |
| `README.md` | Dataset description, schema, statistics, and citation information. |

---

## ✨ Key Contributions

- A **context-aware multi-label annotation schema** for social, cultural, and religious factors in Arabic mental health questions.
- A benchmark corpus of **2,677 Arabic mental health questions**.
- A manually annotated gold-standard subset of **500 questions**.
- A total of **552 context-positive instances** after pseudo-label expansion.
- Distributional analysis of social, cultural, and religious contextual factors.
- A research resource for socially, culturally, and religiously aware Arabic mental health NLP.

---

## 📌 Task Definition

The task is formulated as **multi-label classification**.

Each Arabic mental health question is first assessed for whether it contains contextual framing:

- `No Contextual Factors`: the question is limited to symptoms, diagnosis, treatment, or general medical advice without social, cultural, or religious framing.
- Context-positive labels: the question contains one or more social, cultural, or religious contextual cues.

A context-positive question may receive **multiple labels** because social, cultural, and religious cues can co-occur.

---

## 🧩 Annotation Schema

| Main Category | Label | Definition |
|---|---|---|
| **No** | `No Contextual Factors` | No explicit or implicit social, cultural, or religious contextual framing. |
| **Social** | `Social\|Relationship` | Interpersonal relations involving family, spouse, parents, children, friends, peers, workplace relations, or community interaction. |
| **Social** | `Social\|Demographics` | Personal or social-position attributes such as age, gender, marital status, nationality, income, education, employment, or family status. |
| **Social** | `Social\|Life Satisfaction` | Perceived quality of life, happiness, loneliness, dissatisfaction, hopelessness, social functioning, or overall well-being. |
| **Cultural** | `Culture\|Information` | Culturally shaped knowledge, assumptions, or explanations about mental health, symptoms, causes, treatment, or appropriate behavior. |
| **Cultural** | `Culture\|Values` | Culturally valued goals, expectations, or judgments, such as honor, family reputation, obedience, marriage expectations, independence, or acceptable roles. |
| **Cultural** | `Culture\|Norms and Morals` | Shared rules about proper or improper behavior, moral responsibility, shame, blame, obligation, or community acceptability. |
| **Religious** | `Religion` | Religious belief, practice, obligation, spiritual coping, sin, guilt, prayer, divine will, or faith-based interpretation of distress or treatment. |

---

## 📊 Dataset Summary

The dataset contains patient-authored Arabic mental health questions collected from an Arabic medical Q&A platform. Only the **questions** are used for contextual annotation; physician responses are excluded.

| Item | Count |
|---|---:|
| Total questions | 2,677 |
| Manually annotated gold-standard questions | 500 |
| Unlabeled questions used for pseudo-labeling | 2,177 |
| Human context-positive questions | 131 |
| Pseudo-labeled context-positive questions | 421 |
| Total context-positive questions | 552 |
| Total context-negative questions | 2,125 |

### Binary Context Distribution

| Label | Human | Pseudo | Total |
|---|---:|---:|---:|
| `No Contextual Factors` | 369 | 1,756 | 2,125 |
| Context-positive | 131 | 421 | 552 |
| **Total** | **500** | **2,177** | **2,677** |

---

## 🔗 Main-Category Overlap Distribution

Rows represent mutually exclusive combinations of main categories among context-positive questions.

| Annotation Source | Only Social | Only Culture | Only Religion | Social + Culture | Social + Religion | All Three | Total |
|---|---:|---:|---:|---:|---:|---:|---:|
| Human Annotators | 98 | 14 | 9 | 5 | 5 | 0 | 131 |
| Pseudo-labeling | 408 | 2 | 2 | 6 | 2 | 1 | 421 |
| **Total** | **506** | **16** | **11** | **11** | **7** | **1** | **552** |

---

## ✅ Annotation Reliability

The gold-standard subset was annotated by native Arabic-speaking Saudi annotators using predefined guidelines. Agreement was computed under an exact-match criterion for each annotation level.

| Annotation Level | Cohen's κ | Krippendorff's α |
|---|---:|---:|
| Yes/No | 0.80 | 0.80 |
| Main category | 0.76 | 0.68 |
| Sub-category | 0.58 | 0.58 |

---

## 📈 Reported Baseline Results

The associated manuscript reports held-out gold-standard evaluation results for the dataset.

| Configuration | Micro-F1 | Macro-F1 | Subset Accuracy | Jaccard | Hamming Loss |
|---|---:|---:|---:|---:|---:|
| Gold Only | 0.72 | 0.19 | 0.70 | 0.72 | 0.07 |
| Gold + Pseudo | **0.84** | **0.22** | **0.84** | **0.84** | **0.04** |

Pseudo-labeling improves overall performance, especially for high-support labels such as `No Contextual Factors` and `Social\|Relationship`. Minority cultural and religious labels remain challenging because they have limited positive support.

---

## 📌 Per-Label F1 Results

Support is the number of positive instances in each held-out gold-standard test fold. Values are reported as mean ± standard deviation across five folds.

| Label | Support | Gold Only F1 | Gold + Pseudo F1 |
|---|---:|---:|---:|
| `No Contextual Factors` | 71 | 0.85 ± 0.02 | **0.89 ± 0.01** |
| `Social\|Relationship` | 22 | 0.55 ± 0.05 | **0.68 ± 0.08** |
| `Social\|Demographics` | 3 | 0.16 ± 0.11 | **0.24 ± 0.17** |
| `Social\|Life Satisfaction` | 1 | 0.00 ± 0.00 | 0.00 ± 0.00 |
| `Culture\|Information` | 1 | 0.00 ± 0.00 | 0.00 ± 0.00 |
| `Culture\|Values` | 1 | 0.00 ± 0.00 | 0.00 ± 0.00 |
| `Culture\|Norms and Morals` | 4 | 0.18 ± 0.13 | **0.31 ± 0.10** |
| `Religion` | 3 | **0.20 ± 0.17** | 0.13 ± 0.18 |

### Why Retain Rare Categories?

Rare categories are retained because they are part of the proposed sociocultural schema and capture meaningful contextual dimensions that are underrepresented in existing Arabic mental health NLP resources. Low performance on these labels should therefore be interpreted as an important limitation and future-work direction, not as evidence that the categories are unimportant. Improving these categories requires targeted annotation, stronger imbalance-aware learning, and more robust evaluation with larger minority-label support.

---

## ⚠️ Responsible Use

ContextMental is intended for **research on Arabic mental health NLP and sociocultural language understanding**. It should not be used for clinical diagnosis, triage, automated counseling, or medical decision-making without expert oversight.

The labels describe contextual framing in text. They should not be interpreted as clinical judgments about individuals.

Any downstream use should include human review, transparency about limitations, and fairness evaluation across demographic and dialectal groups.

---

## 📚 Citation

```bibtex
@article{ayash2026contextmental,
  title={ContextMental: A Sociocultural Benchmark for Arabic Mental Health Understanding},
  author={Ayash, Lama and Alasmari, Ashwag and Alhuzali, Hassan},
  journal={Electronics},
  year={2026},
  note={Under review}
}
```

---

## 🙏 Acknowledgment

The authors acknowledge the support of the Deanship of Research and Graduate Studies at King Khalid University through small group research under grant number `RGP1/69/46`.

---

## 🌱 Closing

**ContextMental** supports socially, culturally, and religiously aware Arabic mental health NLP by documenting how distress is expressed through relationships, norms, values, and belief-related context.
