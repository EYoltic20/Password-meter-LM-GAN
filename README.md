🛡️ GAN+LM Password Strength Meter
A Hybrid Discriminator + Language Model for Modern Password Guessability Estimation

This repository contains the final implementation used in the MSc thesis:

Improving Password Strength Estimation Using a GAN-Based Discriminator and LSTM Language Models
Emilio Yoltic Martínez, National College of Ireland (2025)

The project introduces a next-generation password strength meter that combines:

A Wasserstein GAN (WGAN) Discriminator

A Character-Level LSTM Language Model (LM)

A Logistic Regression Calibrator

to generate probabilistic password risk estimations that align more closely with real attacker capabilities than traditional strength meters such as zxcvbn, NIST heuristics, or LUDS rules.

The model outputs three core metrics per password:

D_score → GAN discriminator realism

neglogp_per_char → statistical likelihood from LSTM LM

p_guessable → calibrated probability of being guessable

📂 Pre-Trained Models (Download Required)

Because the full model is ~15 GB, it is hosted externally.

👉 Download all model checkpoints here:
https://drive.google.com/drive/folders/1YI7onUkWtLAmrDLZUHY1chbWql2C3SQa?usp=sharing

Place the downloaded files in your project folder or modify the script paths accordingly.

You will find:

encoder_epochxx.pth

D_epochxx.pth (WGAN Discriminator)

lm.pt (LSTM Language Model)

tokenizer.json

calibrator.pkl (optional — used for identical single-password scoring)

⚙️ Installation
1. Create environment (recommended)
python3 -m venv .venv
source .venv/bin/activate

2. Install dependencies
pip install -r requirements.txt


Typical requirements include:

torch
numpy
pandas
scikit-learn
matplotlib
joblib

🚀 Usage

All functionality is contained in the main script (e.g., gan_lm_scorer.py).

🔥 1. Scoring a CSV or TXT Dataset

This mode processes many passwords at once and optionally calibrates using labels (y).

Example:
python gan_lm_scorer.py \
  --lm-load ./lm.pt \
  --disc-ckpt ./D_epoch140.pth \
  --enc-ckpt ./encoder_epoch19.pth \
  --disc-tokenizer-json ./tokenizer.json \
  --score-csv ./mixed_passwords.csv \
  --password-col password \
  --out-csv ./scored.csv \
  --reliability-png ./reliability.png

What this produces:

scored.csv containing:

password

D_score

neglogp_per_char

p_guessable

Reliability plot (reliability.png)

Calibration and metrics (ROC-AUC, PR-AUC, Brier) if labels exist

If your input is a .txt file (one password per line), the script auto-detects it.

🔥 2. Single Password Mode (Interactive Scoring)

Use this when you want the same calibrated behavior used during evaluation.

Example:
python gan_lm_scorer.py \
  --lm-load ./lm.pt \
  --disc-ckpt ./D_epoch140.pth \
  --enc-ckpt ./encoder_epoch19.pth \
  --disc-tokenizer-json ./tokenizer.json \
  --load-calibrator ./calibrator.pkl \
  --single-password "qwerty2024"

Output example:
[Single password result]
  password        : qwerty2024
  D_score         : 11.815453
  neglogp_per_char: 3.133637
  p_guessable     : 0.384047

Classification thresholds:

< 0.20 → STRONG

0.20–0.40 → MEDIUM-STRONG

0.40–0.60 → MEDIUM / RISKY

0.60–0.80 → WEAK

> 0.80 → VERY WEAK

🔥 3. Training the Language Model (Optional)

If you want to retrain the LM on a custom dataset:

python gan_lm_scorer.py \
  --train-lm \
  --train-text ./rockyou_clean.txt \
  --lm-save ./lm.pt


Input: one password per line# Password-meter-LM-GAN
