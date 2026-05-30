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

---

## Additional figures

### Classification problem

![Classification problem](https://user-images.githubusercontent.com/83819024/236599165-c0d95c0f-dcd1-41e1-a307-a376479f204d.png)

### Implementation planning

![Implementation planning](https://user-images.githubusercontent.com/83819024/236599172-6d080401-30e6-42ec-a1b2-21921eed07b0.png)

### Build data

![Build data](https://user-images.githubusercontent.com/83819024/236599177-19d68535-0ac4-4374-a653-9cdbac35d873.png)

### Model structure

![Model structure](https://user-images.githubusercontent.com/83819024/236599227-c4ac61c5-38ff-485c-a9b3-18472feb7516.png)

![Model structure 2](https://user-images.githubusercontent.com/83819024/236599249-2f1d781b-0f73-48f7-9f14-b1233e1b10f0.png)

### Evaluation

![Evaluation](https://user-images.githubusercontent.com/83819024/236599264-bdd776b2-a072-4a44-9e29-97bbdbd7e501.png)

### Experiment

![Experiment](https://user-images.githubusercontent.com/83819024/236599320-279f8d31-0b83-4fcb-a81d-a6b3ce3d4355.png)

### Result

![Result](https://user-images.githubusercontent.com/83819024/236599350-3967a6b2-cd92-417b-a729-1bb35794b05d.png)

### Improvement

![Improvement](https://user-images.githubusercontent.com/83819024/236599365-e4e29f7b-a514-41da-93d8-5db31ffbc814.png)

### Mechanical model

![Mechanical model](https://user-images.githubusercontent.com/83819024/236599390-b852b9cd-c97b-4e6e-906f-d481cfc01068.png)

![Mechanical model 2](https://user-images.githubusercontent.com/83819024/236599400-4c175a54-4d4d-4b3f-993d-757f636c35cb.png)

### Electrical model

![Electrical model](https://user-images.githubusercontent.com/83819024/236599413-a086f840-9c2a-4db3-a22d-b5091e196978.png)

![Electrical model 2](https://user-images.githubusercontent.com/83819024/236599418-8426ff5e-8bee-4c1e-abec-1ce3520dbddf.png)

### Summary

![Summary](https://user-images.githubusercontent.com/83819024/236599430-29a9bca2-e795-425b-be51-ad8f2b58dfe4.png)
