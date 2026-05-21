# 🧠 ContextMental

**A Benchmark for Sociocultural Context Understanding in Arabic Mental Health Questions**

[![Paper](https://img.shields.io/badge/Journal-Electronics%20(under%20review)-blue)]()
[![Model](https://img.shields.io/badge/Model-AraBERT--v02-orange.svg)](https://huggingface.co/aubmindlab/bert-base-arabertv02)
[![Task](https://img.shields.io/badge/Task-Multi--label%20Classification-green)]()
[![Language](https://img.shields.io/badge/Language-Arabic-lightgrey)]()

---

## 🌍 Overview

**ContextMental** is a benchmark dataset and modeling framework for identifying **sociocultural contextual factors** in Arabic mental health questions. The benchmark focuses on how patient-authored questions express distress through **social relationships**, **cultural norms**, and **religious or belief-related framing**.

Most Arabic mental health NLP work focuses on clinical-condition detection, question answering, or general health classification. ContextMental instead models the contextual cues that shape how mental health concerns are described in Arabic-speaking settings.

The repository supports training, evaluation, pseudo-labeling, inference, and threshold calibration for an AraBERT-based multi-label classification framework.

---

## ✨ Key Contributions

- A **multi-label annotation schema** for social, cultural, and religious context in Arabic mental health questions.
- A benchmark corpus of **2,677 Arabic mental health questions**.
- A manually annotated gold subset of **500 questions**.
- A total of **552 context-positive instances** after pseudo-label expansion.
- An **AraBERT-based multi-label classification pipeline**.
- Imbalance-aware training using **weighted binary cross-entropy**.
- Semi-supervised expansion through **pseudo-labeling**.
- Validation-based **class-specific threshold calibration**.

---

## 📌 Task Definition

The task is formulated as **multi-label classification**.

Each Arabic mental health question is first assessed for whether it contains contextual framing. If contextual factors are present, the question may receive one or more labels from the schema below.

### Main Categories

| Category | Description |
|---|---|
| **Social** | Interpersonal, family, demographic, relational, or life-satisfaction context. |
| **Cultural** | Cultural values, norms, expectations, morality, or culturally shaped health beliefs. |
| **Religious** | Religious belief, spiritual coping, guilt, faith-based interpretation, or religious practice. |

### Sub-categories

| Main Category | Sub-category |
|---|---|
| Cultural | `Culture|Information` |
| Cultural | `Culture|Values` |
| Cultural | `Culture|Norms and Morals` |
| Social | `Social|Relationship` |
| Social | `Social|Demographics` |
| Social | `Social|Life Satisfaction` |
| Religious | `Religion` |

---

## 📊 Dataset Summary

| Item | Count |
|---|---:|
| Total questions | 2,677 |
| Manually annotated gold questions | 500 |
| Unlabeled questions used for pseudo-labeling | 2,177 |
| Human context-positive questions | 131 |
| Pseudo-labeled context-positive questions | 421 |
| Total context-positive questions | 552 |
| Total context-negative questions | 2,125 |

The dataset contains patient-authored Arabic mental health questions collected from an Arabic medical Q&A platform. The released task focuses on questions only; physician responses are not used for contextual classification.

---

## 🧩 Annotation Schema

The annotation process uses two levels:

1. **Binary contextual relevance**
   - `Yes`: the question contains social, cultural, or religious contextual framing.
   - `No`: the question is limited to symptoms, treatment, diagnosis, or general medical advice without contextual framing.

2. **Multi-label contextual annotation**
   - Context-positive questions can receive one or more social, cultural, or religious labels.

---

## 🧠 Model Architecture

The classification framework uses **AraBERT v0.2** as the backbone encoder.

### Pipeline

1. Arabic question input
2. AraBERT tokenization
3. Transformer encoding
4. `[CLS]` representation
5. Linear classification head
6. Sigmoid probability per label
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

The experiments compare two main settings:

| Setting | Description |
|---|---|
| **Gold Only** | Training uses only the 500 manually annotated questions. |
| **Gold + Pseudo** | Training uses gold data plus pseudo-labeled questions. |

Evaluation is performed using **five-fold multi-label stratified cross-validation**. In each fold, the test split contains only manually annotated gold-standard samples.

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
| Loss | Weighted BCE |
| Cross-validation | 5-fold MLSKF |
| Mixed precision | fp16 |
| Random seed | 42 |

---

## 📈 Main Results

Mean performance across five held-out gold-standard test folds:

| Configuration | Micro-F1 | Macro-F1 | Subset Accuracy | Jaccard | Hamming Loss |
|---|---:|---:|---:|---:|---:|
| Gold Only | 0.72 | 0.19 | 0.70 | 0.72 | 0.07 |
| Gold + Pseudo | **0.84** | **0.22** | **0.84** | **0.84** | **0.04** |

Pseudo-labeling improves overall performance, especially for dominant contextual patterns such as social relationship cues. Minority categories remain challenging because cultural and religious labels have limited positive support.

---

## 🧪 Ablation Results

| Configuration | Micro-F1 | Macro-F1 | Subset Accuracy | Jaccard |
|---|---:|---:|---:|---:|
| AraBERT | 0.78 | 0.19 | 0.76 | 0.76 |
| AraBERT + Weighted BCE | 0.73 | 0.21 | 0.70 | 0.72 |
| AraBERT + Pseudo-labels | 0.83 | 0.21 | 0.82 | 0.83 |
| AraBERT + Weighted BCE + Pseudo-labels | **0.84** | **0.22** | **0.84** | **0.84** |

---

## 🎯 Adaptive Thresholding

A fixed threshold of `0.5` is often unsuitable for imbalanced multi-label classification. ContextMental therefore uses **validation-calibrated class-specific thresholds**.

| Label | Threshold |
|---|---:|
| No Contextual Factors | 0.94 |
| Social\|Relationship | 0.52 |
| Social\|Demographics | 0.40 |
| Social\|Life Satisfaction | 0.40 |
| Culture\|Information | 0.40 |
| Culture\|Values | 0.40 |
| Culture\|Norms and Morals | 0.40 |
| Religion | 0.40 |

---

## 📈 Evaluation Metrics

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
git clone https://github.com/LamaAy/ContextMental.git
cd ContextMental
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

## ⚠️ Responsible Use

ContextMental is intended for **research on Arabic mental health NLP and sociocultural language understanding**. It should not be used for clinical diagnosis, triage, automated counseling, or medical decision-making without expert oversight.

The labels describe contextual framing in text. They should not be interpreted as clinical judgments about individuals.

---

## 📚 Citation

```bibtex
@article{ayash2026contextmental,
  title={ContextMental: A Benchmark for Sociocultural Context Understanding in Arabic Mental Health Questions},
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

**ContextMental** supports culturally and socially aware Arabic mental health NLP by modeling how distress is expressed through relationships, norms, values, and belief-related context.
