# 📡 Jammer Detection & Classification in Wireless Communications

> **CNN-based detection and classification of jamming signals in 5G wireless systems using synthetically generated heatmap datasets.**

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1RkXjn7eI8KjGxUUToZFLjNp5EVQXmSWC?usp=sharing)

---

## 📌 Project Overview

This project develops and evaluates **Convolutional Neural Networks (CNNs)** to detect and classify jamming signals in wireless communication systems. The model can:

- **Detect** whether a communication signal is being jammed (binary classification)
- **Identify** the specific type of jammer present (multi-class classification)

The full pipeline covers synthetic dataset generation, data augmentation, CNN model design, training, evaluation, and improvement analysis.

---

## 🔬 Pipeline at a Glance

```
Sionna Simulation
      ↓
Generate Heatmap Images (133×14)
      ↓
5G Scheduler Masking (Data Augmentation)
      ↓
Build Binary & Multi-Class Datasets
      ↓
Train PaperCNN Model
      ↓
Evaluate & Analyze
      ↓
Improvement Recommendations
```

---

## 📂 Project Stages

### 1. 🗂️ Dataset Generation

Synthetic heatmap images were generated using [**Sionna**](https://nvlabs.github.io/sionna/), NVIDIA's open-source TensorFlow-based library for link-level simulations.

**Resource Grid Parameters:**

| Parameter | Value |
|---|---|
| Physical Resource Blocks (PRBs) | 133 |
| Subcarriers per PRB | 12 |
| FFT Size | 1596 |
| OFDM Symbols | 14 |
| Subcarrier Spacing | 30 kHz |
| Modulation | 16-QAM |
| Channel Model | TDL-C |
| Delay Spread | 300 ns |
| Carrier Frequency | 3.5 GHz |
| Fixed SNR | 10 dB |

**Jammer Types (5 Classes):**

| Class | Description |
|---|---|
| 🟢 **Noise** | Standard AWGN — no jamming signal |
| 🔴 **Barrage** | Wideband, continuous jamming across a large spectrum portion |
| 🟠 **Sweep** | Jamming signal that sweeps across frequencies over time |
| 🟡 **Spot** | Narrowband, continuous jamming fixed at one frequency |
| 🔵 **Pulse** | Intermittent jamming appearing for short bursts |

**Generation Process (per sample):**
1. Define jammer-specific parameters (bandwidth, offset, width, spacing, etc.)
2. Fill a `ResourceGrid` with jammer symbols
3. Scale signal power using a random **JNR value** (−10 dB to +10 dB)
4. Apply TDL channel model + AWGN noise
5. Convert received signal → **133×14 heatmap** (power averaged per PRB & OFDM symbol)

**Dataset Sizes:**

| Dataset | Description | Samples |
|---|---|---|
| `multi_dataset` | All 5 classes (Noise, Barrage, Sweep, Spot, Pulse) | 10,500 |
| `binary_dataset` | Not-jammed (Noise) vs. Jammed (all 4 jammers) | Balanced |

> 5 classes × 21 JNR values × 100 samples = **10,500 initial samples**

---

### 2. 🔄 Data Augmentation

To simulate real-world conditions where received signals can be **incomplete or partially blocked**, a **5G scheduler mask** was applied to a subset of heatmaps.

- Randomly selects contiguous blocks of PRBs and OFDM symbols
- Sets selected values to **zero**, mimicking unallocated or obstructed resource blocks
- Increases model robustness to data incompleteness

---

### 3. 🧠 Model Architecture — PaperCNN

A custom CNN architecture (`PaperCNN`) was designed for both classification tasks:

- **Input:** 133×14 heatmap images
- **Task A:** Binary classification → `jammed` / `not jammed`
- **Task B:** Multi-class classification → `Noise` / `Barrage` / `Sweep` / `Spot` / `Pulse`

---

### 4. 📊 Training & Evaluation

Models were trained on the generated and augmented datasets. Training trends were analyzed for:
- Accuracy and loss curves
- Overfitting detection
- Per-class performance

---

### 5. 🔍 Analysis & Improvement Suggestions

Post-training analysis identified potential issues including **overfitting** and proposed concrete improvement steps such as:
- Regularization techniques (Dropout, L2)
- Learning rate scheduling
- Expanding dataset diversity
- Cross-validation strategies

---

## ⚙️ Getting Started

### Run in Google Colab (Recommended)

The notebook is large and may not render directly on GitHub. Click the badge below to open it directly in Colab:

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1RkXjn7eI8KjGxUUToZFLjNp5EVQXmSWC?usp=sharing)

### Dependencies

```bash
pip install sionna tensorflow numpy matplotlib
```

> **Note:** Sionna requires TensorFlow. A GPU runtime is strongly recommended in Colab (`Runtime → Change runtime type → T4 GPU`).

---

## 📦 Key Libraries

| Library | Purpose |
|---|---|
| `sionna` | Link-level wireless simulation & heatmap generation |
| `tensorflow` / `keras` | CNN model building and training |
| `numpy` | Numerical operations and data handling |
| `matplotlib` | Heatmap and training curve visualization |

---

## 📈 Results Summary

| Task | Model | Notes |
|---|---|---|
| Binary Detection | PaperCNN | Jammed vs. Not Jammed |
| Multi-Class Classification | PaperCNN | 5 jammer types |

> Detailed results, confusion matrices, and training curves are available inside the notebook.

---

## 🧪 Dataset Summary

```
JNR Range:     -10 dB to +10 dB (21 values)
Samples/class: 100 per JNR value
Heatmap size:  133 × 14 (PRBs × OFDM symbols)
Total samples: 10,500 (multi) | Balanced (binary)
Augmentation:  5G Scheduler Masking
```

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

> Built with Sionna, TensorFlow, and Python. Designed for research in 5G physical-layer security.
