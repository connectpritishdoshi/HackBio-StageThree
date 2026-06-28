# Brain Tumor Segmentation with U-Net
**HackBio Stage 3 — Final Submission**

> Teaching a computer to find brain tumors in MRI scans — automatically, accurately, and without human effort.

---

## What is this project?

When a doctor suspects a brain tumor, they order an **MRI scan** — a detailed photograph taken from inside your head. A trained radiologist then looks at that photograph and carefully draws a boundary around any tumor they find. This process is slow, expensive, and depends on expert availability.

This project teaches a computer to do the same thing — automatically.

We show the model thousands of MRI scans that doctors have already marked up. Over time, it learns to recognise what a tumor looks like and can outline one in a brand-new scan it has never seen before.

> **Analogy:** Think of teaching a child to find Waldo in *Where's Waldo?* You show them hundreds of examples until they spot him instantly in any new book. We're doing exactly that — except instead of Waldo, it's brain tumors.

---

## What does the model look at?

MRI machines can photograph the brain in several different "modes" — each one highlights different tissue types, like switching filters on a camera. This project uses the **FLAIR mode**, which makes abnormal tissue (such as tumors) appear much brighter than the surrounding healthy brain.

> **Analogy:** It's like shining a UV light in a dark room. Healthy tissue stays dim. Anything unusual glows.

---

## How does the model work?

The model is called a **U-Net** — named after its U-shaped structure. It works in two stages:

**Stage 1 — Zoom in (Encoder)**
The model progressively compresses the image, looking for broad patterns. It's asking: *"Is something wrong here?"*

**Stage 2 — Zoom back out (Decoder)**
Using what it learned while zooming in, it zooms back out and draws a precise pixel-level outline around the tumor.

> **Analogy:** Imagine squinting at a photo to decide if something looks off (Stage 1), then putting on glasses to trace a precise circle around it (Stage 2). The U-Net does both.

Skip connections between the two stages let the model combine its big-picture understanding with fine spatial detail — so the outlines are both accurate *and* sharp.

---

## Results

| Metric | Score | What it means |
|---|---|---|
| **Dice Score** | `0.XX` | Overlap between the model's outline and the doctor's outline (1.0 = perfect) |
| **IoU Score** | `0.XX` | Intersection over union — a stricter version of Dice |

A Dice score of 0.85 means the model and a human radiologist agree 85% of the time — comparable to agreement between two experienced doctors reviewing the same scan independently.

---

## Dataset

**BraTS2020** — Brain Tumour Segmentation 2020 Challenge

| Detail | Value |
|---|---|
| Total slices available | 57,195 |
| Slices used | 15,000 |
| Train / Val split | 90% / 10% by patient volume (no data leakage) |
| Input | FLAIR channel, 240×240 pixels |
| Output | Binary mask (0 = healthy, 1 = tumour) |

Each `.h5` file contains one 2D slice from a 3D MRI volume, paired with a mask annotated by expert radiologists. The train/val split is performed at the **volume level** — all slices from a given patient appear in only one set — preventing data leakage between splits.

---

## Training Setup

| Setting | Value |
|---|---|
| Model | U-Net (encoder–decoder with skip connections) |
| Channels | `[64, 128, 256, 512, 1024]` |
| Loss | Dice + Binary Cross-Entropy (50/50) |
| Optimiser | Adam, lr = 1e-3 |
| LR Scheduler | ReduceLROnPlateau (factor=0.5, patience=2) |
| Gradient clipping | max norm 1.0 |
| Augmentation | Random horizontal + vertical flips (train only) |
| Early stopping | Patience = 3 epochs |
| Max epochs | 10 |
| Hardware | Google Colab T4 GPU |

---

## How to Run

1. Open `brain_tumor_segmentation.ipynb` in [Google Colab](https://colab.google)
2. Go to **Runtime → Change runtime type → T4 GPU → Save**
3. Run all cells from top to bottom
4. A `brain_tumor_segmentation.zip` will be automatically downloaded at the end

> **Running on a laptop (no GPU)?** See the CPU instructions at the top of the notebook. Reduce the dataset size and image resolution to complete training in a reasonable time — results will be approximate.

---

## Tech Stack

| Library | Purpose |
|---|---|
| PyTorch | Model architecture and training |
| h5py | Reading MRI `.h5` data files |
| kagglehub | Automatic dataset download |
| tqdm | Live training progress bars |
| matplotlib | Visualising predictions vs ground truth |

---

## Output Files

All outputs are saved to a structured folder and bundled into a single zip for download:

```
brain_tumor_segmentation/
├── models/
│   ├── best_model.pt                    ← best validation checkpoint
│   └── checkpoint.pt                    ← latest epoch checkpoint
├── plots/
│   ├── loss_curve.png                   ← training and validation loss over epochs
│   └── prediction_visualisation.png     ← MRI / ground truth / predicted mask
└── results_summary.txt                  ← final Dice and IoU scores
```
