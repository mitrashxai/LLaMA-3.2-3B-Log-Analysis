# Fine-Tuning LLaMA-3.2-3B for Log Analysis Using LLaMA-Factory

This project fine-tunes **LLaMA-3.2-3B-Instruct** (4-bit quantized) with **LoRA** for multi-task log analysis, using the **LLaMA-Factory** framework. The goal was to evaluate how a fine-tuned open-source LLM performs against the published **LogLM** baseline across log parsing, anomaly detection, log interpretation, root cause analysis, and solution recommendation, including generalization to log domains not seen during training.

### Project Highlights

- **Base Model:** LLaMA-3.2-3B-Instruct, loaded in 4-bit quantization for efficient fine-tuning.
- **Fine-Tuning Method:** LoRA (parameter-efficient fine-tuning) via the LLaMA-Factory framework, trained for 6 epochs.
- **Dataset:** The instruction dataset introduced in the LogLM paper (Liu et al., *LogLM: From Task-Based to Instruction-Based Automated Log Analysis*, arXiv:2410.09352), 2,632 instruction–input–output pairs spanning log parsing, anomaly detection, log interpretation, root cause analysis, and solution recommendation. Split 85% train+validation / 15% test (10% of the training portion held out for validation).
- **Beyond the Original Benchmark:** In addition to reproducing the LogLM evaluation, the model was tested on a custom cross-domain dataset that includes Apache logs, a domain not present in the original training data, to probe generalization to unseen log formats.

---

### Key Findings

**1. Overall Generation Performance vs. LogLM**

| Method | BLEU | ROUGE-L | BERTScore |
|---|---|---|---|
| **Our Model** | **24.69** | **68.21** | **95.0** |


**2. Anomaly Detection**

| Method | F1 Score |
|---|---|
| **Our Model** | **73** |

Evaluated on 24 logs combining the original BGL domain with Thunderbird, a domain not seen during training.

**3. Cross-Domain Log Parsing (Custom Dataset)**

A new 30-sample dataset was built across BGL, Hadoop, Linux, HDFS, and Apache (a domain not in the original dataset), 6 samples per domain.

| Evaluation Set | F1 | Levenshtein Ratio |
|---|---|---|
| All 5 domains (30 samples) | 50.0 | – |
| Excluding Apache (24 samples) | 58.33 | 94.95 |

The drop when including Apache reflects the expected difficulty of generalizing to a genuinely unseen log format; the high Levenshtein ratio on familiar domains shows the model's outputs are very close to ground truth even when not an exact match.

**4. Log Interpretation, Root Cause Analysis & Solution Recommendation**

Evaluated on Apache domain logs, using GPT-5-generated references as ground truth:

| BLEU | ROUGE-L | BERTScore |
|---|---|---|
| 3.8 | 19.17 | 87.38 |

The low BLEU/ROUGE-L alongside a high BERTScore suggests the model's responses are semantically correct but lexically different from the reference text — a common pattern for open-ended generation tasks where many valid phrasings exist.

---

### Project Structure

- `Dataset/`: The LogLM instruction dataset (train/validation/test splits) and the custom cross-domain evaluation set (BGL, Hadoop, Linux, HDFS, Apache).
- `Results/`: Full evaluation outputs and comparison tables.

---

### Notes

- Fine-tuning was performed using LLaMA-Factory's training pipeline (web UI) rather than a custom training script; this repository documents the exact configuration and results rather than the training code itself.
- All comparisons against "LogLM (Paper)" refer to the numbers reported in the original LogLM paper (Liu et al., arXiv:2410.09352).
- Manual inspection of near-miss predictions (e.g., in log parsing) confirmed that most discrepancies are minor formatting differences (spacing, punctuation) rather than semantic errors.
