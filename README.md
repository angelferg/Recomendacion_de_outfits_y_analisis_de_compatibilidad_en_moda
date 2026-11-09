# Multimodal AI for Outfit Recommendation and Compatibility Analysis in Fashion

Master's Thesis (TFM) in Artificial Intelligence · Universidad Internacional de Valencia · 2025–26

Three state-of-the-art fashion compatibility models (SCE-Net, HGNN and Recommendation Engine) trained and compared under one homogeneous protocol on Polyvore Outfits, plus a **Text-to-Outfit** module that retrieves complete outfits from a natural-language description.

- **Author:** Ángel Fernández García
- **Supervisor:** Mónica Díaz Hernández
- **Thesis (PDF, in Spanish):** see the [`TFM - latex`](<TFM%20-%20latex/>) folder of this repository

---

## Why is fashion recommendation hard?

Most recommenders assume that "close in the embedding space" means "good match". In fashion that breaks down for two reasons:

1. **Similarity is not compatibility.** A good outfit is usually made of *different* items (a skirt, a top, a pair of shoes), not of near-duplicates.
2. **Compatibility is not transitive.** If a top goes well with a skirt, and the skirt goes well with a jacket, the top and the jacket may still clash.

Because of this, traditional semantic spaces are not enough, and compatibility has to be learned explicitly.

## What this project does

The goal is to build and validate a system that generates and combines **image and text embeddings** of garments, and that generalises across several outfit-recommendation tasks.

- Re-implements and compares **SCE-Net**, **Hypergraph Neural Networks (HGNN)** and **Recommendation Engine (RE)** on the *Fill-In-The-Blank* (FITB) and *Compatibility Prediction* tasks.
- Builds and compares three **Text-to-Outfit** retrievers: a lightweight adaptation of RE's latent space (zero-shot and fine-tuned) versus the dedicated contrastive architecture **CLIP4Outfit**.

## Results

All numbers are on the **Polyvore Outfits** test set (*nondisjoint* split).

### Compatibility (FITB accuracy and AUC)

| Model | FITB (this work) | FITB (paper) | AUC (this work) | AUC (paper) |
|-------|:---:|:---:|:---:|:---:|
| SCE-Net | 0.509 | 0.616 | 0.793 | 0.910 |
| HGNN | 0.637 | 0.390 | 0.602 | 0.760 |
| **Recommendation Engine** | 0.566 | 0.625 | **0.896** | 0.910 |

Recommendation Engine is the most robust and balanced model and the one that gets closest to the published numbers. SCE-Net and HGNN turned out to be more sensitive to the training setup and to the size of the dataset, respectively.

### Text-to-Outfit retrieval

| Model | R@1 (%) | R@5 (%) | R@10 (%) | MRR (%) |
|-------|:---:|:---:|:---:|:---:|
| RE (zero-shot) | 2.3 | 8.1 | 11.7 | 5.9 |
| **RE (fine-tuned)** | 3.2 | **10.4** | **14.9** | **7.4** |
| CLIP4Outfit | 3.2 | 5.8 | 8.2 | 5.0 |
| *CLIP4Outfit (paper)* | – | *7.59* | *11.12* | – |

The compatibility space learned by RE transfers surprisingly well to text search, beating CLIP4Outfit with a fraction of its size.

### Efficiency

| Model | Parameters | Inference |
|-------|:---:|:---:|
| SCE-Net | 12.3 M | 165 ms (FITB) |
| HGNN | 95 k | 16 ms (FITB)* |
| Recommendation Engine | 3.3 M | 57 ms (FITB) |
| RE Text-to-Outfit (fine-tuned) | 3.5 M | ~260 ms / query |
| CLIP4Outfit | 163.9 M | ~32 s / query |

\*HGNN works on pre-computed features, so its latency does not include feature extraction for new items.

## Repository structure

```text
.
├── TFM - codigo/     # Source code and requirements.txt
├── TFM - latex/      # LaTeX source and PDF of the thesis
└── README.md
```

> [COMPLETAR: add a short description of the main subfolders of `TFM - codigo/` (e.g. one per model).]

## Getting started

**Requirements**
- Dependencies: listed in `TFM - codigo/requirements.txt`
- Hardware: all experiments were run locally on an **NVIDIA GeForce RTX 3050 Laptop GPU (6 GB VRAM)**, using gradient accumulation and mixed precision (AMP) to fit in memory.

## Data

The dataset is **not included** in this repository.

- **Polyvore Outfits** (*nondisjoint* split, Vasileva et al., 2018): 68,306 outfits and 251,008 unique items.
- Download it from Hugging Face: [`mvasil/polyvore-outfits`](https://huggingface.co/datasets/mvasil/polyvore-outfits).
- Outfit definitions and metadata also come from [`xthan/polyvore-dataset`](https://github.com/xthan/polyvore-dataset).
- [COMPLETAR: folder where the data should be placed.]

## Limitations and future work

**Limitations**

- Out-of-domain validation on real commercial catalogues could not be done: the stores' terms of service forbid scraping their images.
- No human evaluation of aesthetic quality (it was left out of scope by design).
- Polyvore is user-generated, so compatibility labels are noisy.
- 71.3% of the items have no textual description.
- Hyperparameters were taken from the original papers rather than tuned, because of the available compute.

**Future work**

- Validate on real e-commerce catalogues through research agreements or a curated subset.
- Run a study with professional stylists to compare offline metrics with human judgement.
- Replace the classic text features with vision-language models (CLIP-like).
- Reduce over-smoothing in HGNN (e.g. DropEdge, residual connections).
- Handle negation and exclusions in Text-to-Outfit queries (e.g. "without black").
- Systematic hyperparameter search (Bayesian optimisation).

## Contact

**Ángel Fernández** · [angelfdezgarc@gmail.com](mailto:angelfdezgarc@gmail.com)
