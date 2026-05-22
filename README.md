# 5G Network Automation Using Local LLMs and RAG

[![arXiv](https://img.shields.io/badge/arXiv-2511.21084-b31b1b.svg)](https://arxiv.org/abs/2511.21084)
[![Conference](https://img.shields.io/badge/6G%20Summit-Abu%20Dhabi%202024-blue)](https://eucnc.eu/)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/)

> **Demo accepted at the 3rd 6G Summit, Abu Dhabi, UAE — November 2024**

Automating private 5G network management using a locally-deployed, quantized **LLaMA-3 8B (Q4)** enhanced with **Retrieval-Augmented Generation (RAG)**. The system translates natural language into accurate 5G CLI commands — with no cloud APIs and no data leakage.

---

## Highlights

- **94.3% classification accuracy** across 11 command categories using RAG-augmented retrieval
- **Fully local inference** on consumer hardware (RTX 2060 / 6 GB VRAM)
- **Two-stage pipeline**: command classification → command generation
- **Privacy-first**: sensitive network data never leaves the device
- **Open5GS compatible**: tested on a real private 5G core network

---

## System Architecture

```
Natural Language Input
        │
        ▼
┌─────────────────────┐     top-8 similar examples
│  Stage 1            │◄──────────────────────────┐
│  Command Classifier │                            │
│  (LLaMA-3 8B Q4)   │                     ┌──────┴──────┐
└────────┬────────────┘                     │  ChromaDB   │
         │ category label (1 of 11)         │  Vector DB  │
         ▼                                  │  BAAI/bge   │
┌─────────────────────┐                    └──────┬──────┘
│  Stage 2            │     top-5 similar examples │
│  Command Generator  │◄──────────────────────────┘
│  (LLaMA-3 8B Q4)   │
└────────┬────────────┘
         │
         ▼
   5G CLI Command
```

---

## Results

### Classification Accuracy

| Method | Accuracy |
|--------|----------|
| **RAG — Classifier Retriever** | **94.29%** |
| RAG — Base Retriever | 91.43% |
| Zero-Shot (no RAG) | 74.29% |

### End-to-End Pipeline

| Metric | Score |
|--------|-------|
| Command generation accuracy (exact match) | ~45–46% |
| Mean IoU (token-level semantic overlap) | 65–68% |

> The gap between classification and exact-match generation reflects the strictness of CLI syntax. IoU scores show the semantic intent is largely captured correctly.

---

## Repository Structure

```
Automation-in-5G/
├── RAG_implementation.ipynb   # Full pipeline: setup → embedding → classify → generate → eval
├── LLMapp_dataset.zip         # 5G command corpus (descriptions + CLI commands + labels)
└── demo_RAG_Auto5G_6GSummit.pdf  # Demo paper submitted to 6G Summit 2024
```

---

## Setup

### Requirements

```bash
pip install chromadb llama-index llama-index-vector-stores-chroma llama-index-embeddings-huggingface
```

### Model (via Ollama)

```bash
# Install Ollama: https://ollama.com
ollama pull llama3:8b-instruct-q4_0
```

### Hardware

| Component | Minimum | Used in Paper |
|-----------|---------|---------------|
| GPU VRAM  | 6 GB    | RTX 2060 (6 GB) + offsite lab server |
| RAM       | 16 GB   | 32 GB |

---

## Usage

1. **Download the dataset** — run the `gdown` cell in the notebook, or place `LLM_app_dataset_v2.zip` in the working directory
2. **Start Ollama** — `ollama serve`
3. **Open the notebook** — `jupyter notebook RAG_implementation.ipynb`
4. **Run all cells** in order

The notebook is organized into clear sections:

| Section | Description |
|---------|-------------|
| 1. Setup | Install packages, imports |
| 2. Dataset | Download and extract the 5G command corpus |
| 3. Embedding | Build ChromaDB vector stores (RAG + Classifier) |
| 4. Retrieval | Configure retrievers (top-k = 8) |
| 5. Classification | Stage 1 — classify command type via RAG few-shot |
| 6. Generation | Stage 2 — generate CLI command via RAG prompting |
| 7. Evaluation | Accuracy + Mean IoU on held-out test set |

---

## Dataset

The command corpus covers **12 network operation categories**:

`classifier` · `completion` · `diag` · `extract` · `help` · `list` · `login` · `network` · `observe` · `remove` · `test` · `user`

Each entry pairs a natural language description with its ground-truth CLI command and category label.

---

## Citation

```bibtex
@article{majlesara2024_5g_rag,
  title   = {5G Network Automation Using Local Large Language Models
             and Retrieval-Augmented Generation},
  author  = {Majlesara, Ahmadreza and Majlesi, Ali and Mamaghani, Ali
             and Shokrani, Alireza and Khalaj, Babak Hossein},
  journal = {arXiv preprint arXiv:2511.21084},
  year    = {2024},
  note    = {Demo at 3rd 6G Summit, Abu Dhabi, UAE, Nov 2024}
}
```

---

## Related Work

**[Automated Fault Detection in 5G Core Networks Using LLMs](https://arxiv.org/abs/2512.19697)**  
Follow-up paper at EuCNC/6G Summit 2025. Kubernetes-based 5G core, intentional fault injection, LLM-based fault classification and root-cause analysis.

---

## Authors

| Name | Affiliation |
|------|-------------|
| **Ahmadreza Majlesara** | Sharif University of Technology |
| Ali Majlesi | Sharif University of Technology |
| Ali Mamaghani | Sharif University of Technology |
| Alireza Shokrani | Sharif University of Technology |
| Prof. Babak Hossein Khalaj | Sharif University of Technology |

---

*Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)*
