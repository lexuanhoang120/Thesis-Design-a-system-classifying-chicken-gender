# Chicken Gender Classification System

> **Undergraduate Thesis** — HCMC University of Technology (Bách Khoa), Mechatronics, 2022

A CNN-powered system that automatically sorts chickens by gender. Built from scratch: collected the dataset on a farm, trained the classifier, designed the mechanical singulation mechanism, wired the control electronics, and ran live experiments with real chickens.

---

## Highlights

- **95.9% classification accuracy** using a custom CNN with multi-frame majority voting — up from 91.5% single-frame
- **Self-collected dataset** — photographed hundreds of chickens one-by-one on a local farm, published on Kaggle
- **Full mechatronic system** — mechanical design (CAD), electrical wiring, embedded control (ATMega 2560), and computer vision — all integrated into a working prototype
- **~0.22s single-frame inference** for real-time use; **4.56s** with 20-frame voting for near-96% accuracy
- **4.4 pp accuracy gain** from a simple ensemble strategy that costs nothing but a few extra frames

---

## Results

**Experiment 1 — Single frame (200 chickens):**

| | Actual Cock | Actual Hen |
|---|---|---|
| **Predicted Cock** | **92** | 9 |
| **Predicted Hen** | 8 | **91** |

→ **91.5% accuracy**, ~0.22s per prediction

**Experiment 2 — Multi-frame voting (171 chickens):**

Take 20 frames, predict each independently, majority vote. Sum > 10 → hen, < 10 → cock.

| | Actual Cock | Actual Hen |
|---|---|---|
| **Predicted Cock** | **84** | 4 |
| **Predicted Hen** | 3 | **80** |

→ **95.9% accuracy** (+4.4 pp), ~4.56s per prediction  
→ Cock: 96.55%, Hen: 95.24%

The voting strategy trades speed for accuracy — worth it because the mechanical singulation is the bottleneck, not the classifier.

---

## How it works

```
Flock → Singulation mechanism → One chicken in the cage
                                       ↓
                              Camera captures image(s)
                                       ↓
                              CNN classifier → Cock or Hen
                                       ↓
                              Sorting gate → Correct bin
```

**Three integrated subsystems:**

| Subsystem | What it does | Key components |
|---|---|---|
| **Computer Vision** | Classifies gender from images | Custom CNN (4 conv + 2 dense), 256×256 RGB input |
| **Mechanical** | Isolates one chicken at a time | Gates, pusher plate, timing belt, stepper motor, servo |
| **Electrical & Control** | Orchestrates the sequence | ATMega 2560, IR sensors, motor drivers, 12V supply |

---

## The CNN classifier

### Data

Collected in person at a local chicken farm. Each bird was placed in a fixed cage under controlled LED lighting and photographed with a smartphone. The dataset was split into train/validation/test with augmentation.

<!-- 📦 [Kaggle Dataset](https://www.kaggle.com/datasets/hongxun/dataset-chicken-gender) · [Raw Data](https://www.kaggle.com/datasets/hongxun/raw-data-chicken-gender) · [Trained Model](https://www.kaggle.com/datasets/hongxun/classify-chicken-gender) · [All Materials](https://www.kaggle.com/datasets/hongxun/material-classify-chicken-gender) -->

### Architecture

| Layer | Details |
|---|---|
| Input | 256×256 RGB |
| Conv Block 1–4 | Increasing filters, ReLU, MaxPooling, Dropout |
| Dense | 2 fully-connected layers |
| Output | Sigmoid → binary (0 = Cock, 1 = Hen) |

Tested multiple architectures iteratively — selected the best balance of accuracy and inference speed.

### Key lessons

- **Environmental consistency matters.** Training images looked slightly different from on-site captures, which caused the real-world accuracy drop from training performance. Re-collecting data to match deployment conditions was the fix.
- **Chicken movement degrades single-frame accuracy.** A 20-frame voting strategy was a zero-cost fix — no model retraining, just capturing more frames and taking the majority.
- **Data collection is the hardest part.** Every sample means catching a chicken, placing it correctly, and labeling it. No shortcut for this.

---

## Demos & drawings

**Experiments:**

1. Building the model + initial testing — https://youtu.be/ve8OjjkXJxA
2. Results from experiment 1 — https://youtu.be/6x_dHk-QXaI
3. Results from experiment 2 + chicken measurements — https://youtu.be/hFi8iOCUlrs

**System animation:** https://youtu.be/mFdrJw7o6yE

**Design drawings:** [Algorithm](Drawing/algorithm.pdf) · [Mechanical](Drawing/mechanic.pdf) · [Electrical](Drawing/electric.pdf) · [Component selection](Drawing/selections.pdf)

---

## Project structure

```
.
├── VP ... HĐ11.pdf               # Full thesis (Vietnamese)
├── Drawing/                      # CAD drawings (DWG + PDF)
│   ├── algorithm.{dwg,pdf}
│   ├── mechanic.{dwg,pdf}
│   ├── electric.{dwg,pdf}
│   └── selections.{dwg,pdf}
├── #Kinematic diagram/           # Kinematic mechanism diagrams (Visio)
├── clip/                         # Experiment YouTube links
└── README.md
```
