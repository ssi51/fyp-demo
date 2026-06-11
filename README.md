# AUTONOMOUS MALWARE CONTAINMENT VIA SMALL LANGUAGE MODEL (SLM) URL DETECTION

**BAXU3973 Final Year Project** · Semester 6 · Session 2025/2026

---

## Overview

This project implements a self-healing network defence system that combines a fine-tuned DistilBERT model with automated VLAN orchestration. When a malicious URL is detected in real time, the system automatically isolates the infected host into a quarantine VLAN — reducing Mean Time to Respond (MTTR) from ~15 minutes of manual SOC intervention to under 200 milliseconds.

The live demo replaces the GNS3 simulation environment with an interactive web dashboard that visualises the full detection-to-isolation pipeline.

---

## Demo

**Live dashboard:** `https://ssi51.github.io/fyp-demo/`

The dashboard runs in heuristic mode by default. To connect the real DistilBERT model, run `app.py` locally and click **Connect model** in the dashboard.

---

## System Architecture

```
Internet Traffic / DNS Queries
         │
   DistilBERT Classifier  ←── Fine-tuned on malicious URL dataset
         │
   Orchestration Engine
         │
    ┌────┴────┐
  Benign    Malicious
  (pass)       │
               ▼
        VLAN Migration
        Host → VLAN 999 Quarantine
               │
        Remediation Server
        (Captive portal / walled garden)
```

**Network simulation:** GNS3 with Open vSwitch and vyOS Router  
**Detection model:** DistilBERT (fine-tuned, binary classification — malicious / benign)  
**Orchestration:** Python-based automated VLAN reconfiguration  
**Frontend:** Vanilla HTML/CSS/JS dashboard hosted on GitHub Pages  
**Backend:** Flask API hosted on Hugging Face Spaces  

---

## Project Objectives

| # | Objective |
|---|-----------|
| PO1 | Design and implement an automated network orchestration framework within a GNS3 simulation environment |
| PO2 | Develop and evaluate a specialised SLM classification engine for real-time semantic analysis of URLs from live traffic |
| PO3 | Validate the reduction in MTTR through automatic migration of infected hosts into a restricted Quarantine VLAN |

---

## Repository Structure

```
fyp-demo/
├── index.html          # Live demo dashboard (frontend)
├── app.py              # Flask classification API (backend)
├── requirements.txt    # Python dependencies
└── your_saved_model/   # Fine-tuned DistilBERT model (not tracked by Git)
    ├── config.json
    ├── tokenizer_config.json
    ├── vocab.txt
    └── pytorch_model.bin
```

> The `your_saved_model/` folder is excluded from version control via `.gitignore`. Upload it directly to Hugging Face Spaces alongside `app.py`.

---

## Running Locally

**1. Install dependencies**

```bash
pip install flask flask-cors transformers torch
```

**2. Place your saved model**

Ensure your fine-tuned model is saved in `./your_saved_model/` using the HuggingFace `save_pretrained()` format.

**3. Start the backend**

```bash
python app.py
```

The server starts on `http://localhost:7860`. Open `index.html` in your browser, click **Connect model**, and the dashboard switches from heuristic mode to live DistilBERT inference.

---

## API Reference

### `POST /predict`

Classifies a URL as malicious or benign.

**Request**
```json
{ "url": "https://example.com/path" }
```

**Response**
```json
{
  "label": "malicious",
  "confidence": 0.9823,
  "infer_ms": 142.5,
  "url": "https://example.com/path"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `label` | string | `"malicious"` or `"benign"` |
| `confidence` | float | Model confidence score (0.0 – 1.0) |
| `infer_ms` | float | Inference latency in milliseconds |

### `GET /`

Health check — returns model load status.

---

## Dashboard Features

- **Real-time classification** — URL input with DistilBERT inference or heuristic fallback
- **Confidence visualisation** — animated score bar per classification
- **Network topology** — animated SVG diagram showing all VLANs; infected host migrates to VLAN 999 on detection
- **Walled garden indicator** — tooltip on quarantined host confirms captive portal is active
- **MTTR comparison chart** — logarithmic bar chart comparing automated (ms) vs manual SOC (~15 min) response
- **Event log** — timestamped detection and isolation events

---

## VLAN Layout

| VLAN | Name | Subnet | Purpose |
|------|------|--------|---------|
| 10 | Engineering | 192.168.10.0/24 | User workstations |
| 20 | Finance | 192.168.20.0/24 | User workstations |
| 30 | HR | 192.168.30.0/24 | User workstations |
| 100 | Management | 192.168.100.0/24 | Remediation server, MGMT server |
| 999 | Quarantine | 192.168.99.0/24 | Isolated infected hosts (walled garden) |

Quarantine hosts have all traffic blocked except HTTP/HTTPS to the Remediation Server (`192.168.100.10`), which serves a static captive portal warning page.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Detection model | DistilBERT (HuggingFace Transformers) |
| Backend | Python 3, Flask, flask-cors |
| Frontend | HTML5, CSS3, Vanilla JS, inline SVG |
| Network simulation | GNS3, Open vSwitch, vyOS |
| Frontend hosting | GitHub Pages |
| Backend hosting | Hugging Face Spaces |

---

## References

- Sanh, V. et al. (2019). *DistilBERT, a distilled version of BERT.* arXiv:1910.01108
- HuggingFace Transformers — https://huggingface.co/docs/transformers

---

*BAXU3973 · Faculty of Artificial Intelligence and Cyber Security · 2025/2026*
