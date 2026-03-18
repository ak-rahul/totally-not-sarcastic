# 😏 totally-not-sarcastic

> Context-aware sarcasm and irony detection powered by RoBERTa.  
> Trained on 100k Reddit comments so you don't have to read them.

[![Model](https://img.shields.io/badge/🤗%20Model-AK--Rahul%2Fsarcasm--roberta-yellow)](https://huggingface.co/AK-Rahul/sarcasm-roberta)
[![Space](https://img.shields.io/badge/🤗%20Space-totally--not--sarcastic-blue)](https://huggingface.co/spaces/AK-Rahul/totally-not-sarcastic)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

---

## 📊 Performance

| Metric    | Score  |
|-----------|--------|
| Accuracy  | 80.57% |
| F1        | 80.39% |
| Precision | 81.14% |
| Recall    | 79.66% |

---

## 🗂️ Repository Structure

```
totally-not-sarcastic/
├── colab_1_train.py      # Train RoBERTa and push to HuggingFace Hub
├── colab_2_dashboard.py  # Gradio dashboard — runs on HF Spaces or Colab
├── requirements.txt      # Dependencies for HF Spaces
└── README.md
```

---

## 🚀 Quick Start

### Run the dashboard locally
```bash
pip install transformers gradio plotly lime torch
python colab_2_dashboard.py
```

### Use the model directly
```python
from transformers import pipeline

classifier = pipeline("text-classification", model="AK-Rahul/sarcasm-roberta")

# Without context
classifier("Oh absolutely, I love waiting 3 hours at the DMV.")

# With context — improves accuracy for conversational sarcasm
classifier("How was the flight? </s></s> Oh wonderful, only delayed by 4 hours.")
```

---

## 🧠 Model

**Base:** `roberta-base` (125M parameters)  
**Task:** Binary classification — Sarcastic / Not Sarcastic  
**Context:** Supports optional `parent_comment` as context for conversational input

### Training Data
| Dataset | Rows | Context |
|---|---|---|
| Reddit SARC (danofer) | ~90,000 | ✅ parent_comment pairs |
| TweetEval Irony | ~3,600 | ❌ |
| News Headlines | ~28,600 | ❌ |
| **Total (balanced)** | **107,058** | |

### Training Details
- 3 epochs · batch=32 · lr=2e-5 · fp16
- Label smoothing=0.05
- Embeddings frozen for epoch 1 (prevents catastrophic forgetting)
- Cosine LR decay · Early stopping (patience=2)
- Best checkpoint selected by validation loss

---

## 🗃️ Files

### `colab_1_train.py`
Run this in Google Colab (T4 GPU) to train from scratch and push to HuggingFace Hub.

**Requirements before running:**
- `danofer-sarcasm.zip` in the root of your Google Drive
- `HF_TOKEN` added to Colab Secrets (write access)

**Steps:**
1. Runtime → Change runtime type → T4 GPU
2. Add `HF_TOKEN` to Colab Secrets
3. Paste entire file into one cell → Run
4. ~38 min training time

### `colab_2_dashboard.py`
Gradio dashboard with three tabs — single prediction with LIME explanations, batch classification, and model stats.

**To deploy on HuggingFace Spaces:**
1. Create a new Space → SDK: Gradio
2. Upload this file as `app.py`
3. Upload `requirements.txt`
4. Set `MODEL_REPO = "AK-Rahul/sarcasm-roberta"` (already set)

**To run in Colab:**
- Paste into a cell and run — it will share a public Gradio link automatically

---

## 🖥️ Dashboard Features

- **Sarcasm probability dial** — semicircular gauge showing exact confidence
- **Intensity bands** — from ✅ Clearly Sincere to 🔥 Scorching Sarcasm
- **LIME word attribution** — highlights which words pushed the prediction
- **Batch mode** — classify multiple messages at once with shared context
- **Export history** — download all predictions as CSV
- **Model stats tab** — confusion matrix, radar chart, architecture details

---

## 📦 Requirements

```
transformers>=4.40
torch
gradio>=4.0
plotly
lime
```

---

## 📄 License

MIT — see [LICENSE](LICENSE)

---

## 🙏 Credits

- [danofer/sarcasm](https://www.kaggle.com/datasets/danofer/sarcasm) — Reddit SARC dataset
- [TweetEval](https://huggingface.co/datasets/tweet_eval) — Irony benchmark
- [raquiba/Sarcasm_News_Headline](https://huggingface.co/datasets/raquiba/Sarcasm_News_Headline) — News headlines
- [RoBERTa](https://huggingface.co/roberta-base) — Base model by Meta AI