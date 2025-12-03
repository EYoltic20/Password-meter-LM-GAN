# 🛡️ GAN+LM Password Strength Meter  
### *A Hybrid Discriminator + Language Model for Modern Password Guessability Estimation*

This repository contains the final implementation used in the MSc thesis:

> **Improving Password Strength Estimation Using a GAN-Based Discriminator and LSTM Language Models**  
> *Emilio Yoltic Martínez, National College of Ireland (2025)*

The project introduces a next-generation password strength meter that combines:

- A **Wasserstein GAN (WGAN) Discriminator**  
- A **Character-Level LSTM Language Model (LM)**  
- A **Logistic Regression Calibrator**  

to generate **probabilistic password risk estimations** that align more closely with real attacker capabilities than traditional strength meters such as **zxcvbn**, **NIST rules**, or **LUDS heuristics**.

The model outputs three core metrics per password:

- **`D_score`** — realism score from the GAN discriminator  
- **`neglogp_per_char`** — statistical likelihood from the LSTM LM  
- **`p_guessable`** — calibrated probability of being guessable  

---

## 📂 Pre-Trained Models (Download Required)

Because the full model is ~15 GB, it is hosted externally.

👉 **Download all model checkpoints here:**  
https://drive.google.com/drive/folders/1YI7onUkWtLAmrDLZUHY1chbWql2C3SQa?usp=sharing

Place the downloaded files in your project directory or update the command-line paths accordingly.

Included in the folder:

- `encoder_epoch19.pth`  
- `D_epoch140.pth` (GAN Discriminator)  
- `train.pt` (LSTM Language Model)  
- `tokenizer.json`  
- `calibrator.pkl` (optional, for identical calibrated scoring)  

---

## ⚙️ Installation

### 1. Create a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

Typical requirements include:

```
torch
numpy
pandas
scikit-learn
matplotlib
joblib
```

---

## 🚀 Usage

All functionality is contained in the main script (`evaluation_new.py`).

---

### 🔥 1. Scoring a CSV or TXT dataset

```bash
python evaluation_new.py   --lm-load ./lm.pt   --disc-ckpt ./D_epoch140.pth   --enc-ckpt ./encoder_epoch19.pth   --disc-tokenizer-json ./tokenizer.json   --score-csv ./mixed_passwords.csv   --password-col password   --out-csv ./scored.csv   --reliability-png ./reliability.png
```

Outputs:

- `scored.csv` with:
  - `password`
  - `D_score`
  - `neglogp_per_char`
  - `p_guessable`
- Reliability plot (`reliability.png`)
- Model performance metrics (if labels exist)

---

### 🔥 2. Single Password Mode

```bash
python gan_lm_scorer.py    --single-password "qwerty2024"
```

Example Output:

```
[Single password result]
  password        : qwerty2024
  D_score         : 11.815453
  neglogp_per_char: 3.133637
  p_guessable     : 0.384047
```

Thresholds:

- `< 0.20` → STRONG  
- `0.20–0.40` → MEDIUM-STRONG  
- `0.40–0.60` → MEDIUM / RISKY  
- `0.60–0.80` → WEAK  
- `> 0.80` → VERY WEAK  

---

### 🔥 3. Training the Language Model (Optional)

```bash
python gan_lm_scorer.py   --train-lm   --train-text ./rockyou_clean.txt   --lm-save ./lm.pt
```

---

## 📁 Repository Structure

```
Final_Product/
│
├── evaluation_new.py
├── tokenizer.json
├── requirements.txt
├── scored.csv
├── latent.csv
├── mixed_passwords.csv
├── just_rockyou.csv
├── generate_wordlist.py
├── compare_with_zxcbvn_nist.py
└── README.md

```

---

## 🧠 How It Works

Three components are combined:

1. **Encoder–Decoder LSTM** — builds latent vectors  
2. **WGAN Discriminator** — learns human-like structure  
3. **LSTM Language Model** — estimates statistical likelihood  

A logistic regression calibrator merges both signals to compute:

```
p_guessable = P(password is guessable)
```

---

## 🧪 Academic Context

The model is evaluated using:

- ROC-AUC  
- PR-AUC  
- Brier Score  
- Reliability Diagrams  
- False-Negative Rate  

Results support modern adversarial + probabilistic modeling for password strength estimation.

---

## 📜 Citation

```
Martínez, E. Y. (2025).
Improving Password Strength Estimation Using a GAN-Based Discriminator and LSTM Language Models.
Master’s Thesis, National College of Ireland.
```

---

## 🛑 Disclaimer

This tool is for research and security evaluation purposes only.  
It must not be used for unauthorized password cracking or malicious activity.
