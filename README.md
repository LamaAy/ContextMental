# 🧠 ContextMental

**ContextMental: A Sociocultural Benchmark for Arabic Mental Health Understanding**

[![Paper](https://img.shields.io/badge/Journal-Electronics%20(under%20review)-blue)]()
[![Model](https://img.shields.io/badge/Model-AraBERT--v02-orange.svg)](https://huggingface.co/aubmindlab/bert-base-arabertv02)
[![Task](https://img.shields.io/badge/Task-Multi--label%20Classification-green)]()
[![Language](https://img.shields.io/badge/Language-Arabic-lightgrey)]()
[![Use](https://img.shields.io/badge/Use-Research%20Only-red)]()

---

## 🌍 Overview

**ContextMental** is a benchmark dataset and modeling framework for identifying **sociocultural contextual factors** in Arabic mental health questions. The benchmark focuses on how patient-authored questions express psychological distress through **social relationships**, **cultural norms and values**, and **religious framing**.

Most Arabic mental health NLP work focuses on clinical-condition detection, question answering, or general health classification. ContextMental instead models the contextual cues that shape how mental health concerns are described in Arabic-speaking settings.

The repository supports training, evaluation, pseudo-labeling, inference, and validation-based threshold calibration for an AraBERT-based multi-label classification framework.

---

## ✨ Key Contributions

- A **context-aware multi-label annotation schema** for social, cultural, and religious factors in Arabic mental health questions.
- A benchmark corpus of **2,677 Arabic mental health questions**.
- A manually annotated gold-standard subset of **500 questions**.
- A total of **552 context-positive instances** after pseudo-label expansion.
- An **AraBERT-based multi-label classification pipeline** for contextual-factor prediction.
- Imbalance-aware training using **weighted binary cross-entropy**.
- Semi-supervised expansion through **pseudo-labeling**.
- Validation-based **class-specific threshold calibration** for imbalanced labels.

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

The dataset contains patient-authored Arabic mental health questions collected from an Arabic medical Q&A platform. Only the **questions** are used for contextual classification; physician responses are excluded.

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

### Main-Category Overlap Distribution

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

## 🧠 Model Architecture

The classification framework uses **AraBERT v0.2** as the backbone encoder.

### Pipeline

1. Arabic question input
2. AraBERT tokenization
3. Transformer encoding
4. `[CLS]` sequence representation
5. Linear classification head
6. Sigmoid probability for each label
7. Class-specific thresholding
8. Final multi-label prediction

### Core Components

- `aubmindlab/bert-base-arabertv02`
- Weighted BCE loss
- Semi-supervised pseudo-labeling
- Validation-calibrated adaptive thresholds
- Five-fold multi-label stratified cross-validation

---

## 🖼️ Architecture Figure

<p align="center">
  <img src="figures/contextmental_architecture.png" width="85%" alt="ContextMental architecture">
</p>

---

## 🧪 Experimental Setup

Two training configurations are evaluated:

| Setting | Description |
|---|---|
| **Gold Only** | Training uses only the 500 manually annotated questions. |
| **Gold + Pseudo** | Training uses the gold-standard data plus pseudo-labeled questions. |

Evaluation is performed using **five-fold multi-label stratified cross-validation**. In each fold, the held-out test split contains only manually annotated gold-standard samples.

### Fold Design

| Split | Count per Fold | Purpose |
|---|---:|---|
| Train | 360 | Model fitting |
| Validation | 40 | Model selection and threshold calibration |
| Test | 100 | Held-out gold-standard evaluation |

### Training Configuration

| Hyperparameter | Value |
|---|---|
| Base model | `bert-base-arabertv02` |
| Max sequence length | 192 |
| Epochs | 15 |
| Batch size | 8 |
| Learning rate | `2e-5` |
| Weight decay | `0.01` |
| Optimizer | AdamW |
| Loss function | Weighted BCE |
| Cross-validation | 5-fold MLSKF |
| Mixed precision | fp16 |
| Random seed | 42 |

---

## 🏷️ Pseudo-Labeling Protocol

A seed model is first trained on the manually annotated gold-standard subset. The model then generates label probabilities for the remaining **2,177 unlabeled questions**.

Pseudo-labels are assigned using validation-calibrated thresholds:

- if `p(label) >= threshold`, the label is treated as positive;
- if `p(label) < threshold`, the label is treated as negative.

No sample-level exclusion is applied. The complete unlabeled subset contributes to weak supervision through threshold-based positive and negative label assignment.

---

## 🎯 Adaptive Thresholding

A fixed threshold of `0.5` is often unsuitable for imbalanced multi-label classification. ContextMental therefore uses **validation-calibrated class-specific thresholds**.

| Label | Threshold |
|---|---:|
| `No Contextual Factors` | 0.94 |
| `Social\|Relationship` | 0.52 |
| `Social\|Demographics` | 0.40 |
| `Social\|Life Satisfaction` | 0.40 |
| `Culture\|Information` | 0.40 |
| `Culture\|Values` | 0.40 |
| `Culture\|Norms and Morals` | 0.40 |
| `Religion` | 0.40 |

---

## 📈 Main Results

Mean performance across five held-out gold-standard test folds:

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

## 🧪 Ablation Results

| Configuration | Micro-F1 | Macro-F1 | Subset Accuracy | Jaccard |
|---|---:|---:|---:|---:|
| AraBERT | 0.78 | 0.19 | 0.76 | 0.76 |
| AraBERT + Weighted BCE | 0.73 | 0.21 | 0.70 | 0.72 |
| AraBERT + Pseudo-labels | 0.83 | 0.21 | 0.82 | 0.83 |
| AraBERT + Weighted BCE + Pseudo-labels | **0.84** | **0.22** | **0.84** | **0.84** |

---

## 📏 Evaluation Metrics

| Metric | Purpose |
|---|---|
| **Micro-F1** | Overall performance across all label decisions. |
| **Macro-F1** | Average performance across labels, including rare labels. |
| **Subset Accuracy** | Exact match between predicted and true label sets. |
| **Jaccard Index** | Partial overlap between predicted and true label sets. |
| **Hamming Loss** | Label-wise prediction error rate. |

---

## 🧩 Repository Structure

```text
ContextMental/
├── configs/
│   └── default.yaml
│
├── data/
│   ├── MentalQA_500_multilabel_final.csv
│   └── sample_infer.csv
│
├── src/contextmental/
│   ├── dataset.py
│   ├── model.py
│   ├── train.py
│   ├── infer.py
│   ├── thresholds.py
│   └── utils.py
│
├── figures/
│   └── contextmental_architecture.png
│
├── train.py
├── predict.py
├── requirements.txt
├── README.md
└── LICENSE
```

---

## ⚙️ Installation

```bash
git clone https://github.com/LamaAy/ContextMental-A-Benchmark-for-Sociocultural-Context-Understanding-in-Arabic-Mental-Health-Questions.git
cd ContextMental-A-Benchmark-for-Sociocultural-Context-Understanding-in-Arabic-Mental-Health-Questions
pip install -r requirements.txt
```

---

## 🚀 Training

```bash
python train.py --config configs/default.yaml
```

The training pipeline supports:

- Five-fold multi-label stratified cross-validation
- Weighted BCE loss
- Gold-only training
- Gold + pseudo-labeled training
- Validation-based threshold calibration

---

## 🔍 Inference

```bash
python predict.py \
  --input data/sample_infer.csv \
  --checkpoints_dir checkpoints/ \
  --output outputs/inference.csv
```

Expected outputs include:

- Per-label probabilities
- Binary label predictions
- Final contextual category predictions

---

## 🧾 Example Configuration

```yaml
seed: 42
model_name: aubmindlab/bert-base-arabertv02
max_len: 192
batch_size: 8
epochs: 15
num_folds: 5
lr: 2e-5
weight_decay: 0.01
warmup_ratio: 0.06
grad_clip_norm: 1.0
save_dir: checkpoints
metric: micro_f1

train_csv: data/MentalQA_500_multilabel_final.csv
text_col: question
labels_col: labels
```

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

**ContextMental** supports socially, culturally, and religiously aware Arabic mental health NLP by modeling how distress is expressed through relationships, norms, values, and belief-related context.
