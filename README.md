# Adversarial Attack & Defense on ML-Based Network Intrusion Detection

## Developer
Christian de Guzman

## Purpose
Create a machine learning model that detects network instructions (cyberattacks in network traffic). Attack the model itself using adversarial techniques and then fix it <br>
This project is to help improve my understanding of AI/ML. Also, providing an opportunity to grow as a developer by combining my passion for both Computer Science and Cybersecurity.

---

## Results at a Glance

| Model | Clean Accuracy | False Positive Rate | Detection under PGD (ε=0.10) | Detection under PGD (ε=0.30) |
|---|---|---|---|---|
| Original | 97.0% | 3.5% | 54.5% | 26.5% |
| Hardened (fine-tuned, 30% adversarial) | 96.3% | 4.4% | 88.8% | 61.7% |
| Hardened (from scratch, 50% adversarial) | 94.9% | 6.1% | 99.0% | 75.1% |

All numbers are measured on a held-out test set (157,361 flows, 27,767 attacks) that is never used for training or model selection.

**Key finding:** 
- Created a 97% threat detection model with 99.2% attack recall on clean traffic.
- White-box PGD drops attack detection from 99.2% to 26.5% at ε=0.30.
- Adversarial training raises detection under PGD (ε=0.30) from 26.5% to 61.7%, at a cost of 0.7 points of clean accuracy.

---

## Objectives
### Threat Detection
Train a neural network on real-world labeled network traffic (CICIDS-2017 dataset) to classify traffic as benign or malicious (97.0% accuracy, Attack Recall of 99.2%, 86.0% attack precision on the held-out test set).

### Adversarial Attacks
Simulate a real attacker using FGSM and PGD attacks to craft malicious network traffic that the model misclassifies as safe. PGD reduces detection accuracy from 99.2% → 26.5% at ε=0.30.

### Adversarial Defense
Retrain the model with adversarial training (PGD-perturbed attack flows mixed into each training batch). The fine-tuned hardened model detects 61.7% of attacks under the same PGD attack (up from 26.5%), with only a 0.7-point drop in clean accuracy.

---

## Model Architecture

```
Network Flow Features (78)
         │
    [Linear 256] → BatchNorm → ReLU → Dropout(0.3)
         │
    [Linear 128] → BatchNorm → ReLU → Dropout(0.3)
         │
    [Linear  64] → BatchNorm → ReLU → Dropout(0.3)
         │
    [Linear   2] → Benign (0) / Attack (1)
```

---

## Attack Methods

**FGSM (Fast Gradient Sign Method)** — Single-step white-box attack that changes the input to try to trick/fool the model. Reduces detection from 99.2% → 43.8% at ε=0.30.

**PGD (Projected Gradient Descent)** — Iterative attack (40 steps, α=0.01) using a margin loss to directly push the benign logit above the attack logit, projected back into the ε-ball after each step.. Stronger than FGSM: 99.2% → 26.5% at ε=0.30.

---

## Defense Method

**Adversarial Training via Fine-tuning:**
- Initialize from pre-trained weights (preserves clean accuracy)
- In 30% of each training batch, attack-class flows are replaced with PGD adversarial examples (ε=0.10, 10 steps) generated on the fly against the current model
- Lower learning rate (1e-4) to avoid overwriting learned features
- Result: 88.8% detection under PGD ε=0.10 vs 54.5% for the original, and 61.7% vs 26.5% at ε=0.30

---
### Limitations
- Perturbations are applied in standardized feature space and are not constrained to physically valid traffic (e.g. packet counts can become fractional or negative). This measures model robustness, not a guaranteed real-world evasion.
- Attacks are white-box (full access to model gradients). Black-box attacks would be weaker.
- Robustness is measured as attack-class recall; the false positive rate is reported alongside to show the hardened model isn't just flagging everything.
---

## Project Structure
```
src/adversec/
    └── __init__.py
notebooks/
    ├── threat_detection.ipynb
    ├── defenses.ipynb
    └── adversarial_attacks.ipynb
results/
    ├── training_curves.png
    ├── Final_Eval.png
    ├── attack_comparison.png
    └── defense_comparison.png
.gitignore
pyproject.toml
README.md
```
---

## Visualizations

### Training — Loss & F1
![Training Curves](results/training_curves.png)
- Loss decreases steadily each epoch. Training loss falls from ~0.12 to ~0.06 over 10 epochs
- Validation F1 stays stable at ~0.89–0.93 (peak ~0.93)

### Adversarial Attack — FGSM vs PGD
![Attack Comparison](results/attack_comparison.png)
- Baseline attack detection (no attack): 99.2%
- PGD is consistently stronger than FGSM at every ε above 0.01
- At ε=0.30, detection drops to 43.8% under FGSM and 26.5% under PGD

### Defense — Original vs Hardened
![Defense Comparison](results/defense_comparison.png)
- The hardened model (fine-tuned, 30% adversarial) stays above the original at every ε
- Detection under PGD: 54.5% → 88.8% at ε=0.10, and 26.5% → 61.7% at ε=0.30
- Small clean accuracy tradeoff (97.0% → 96.3%)

---

## Setup
### Dataset
**CICIDS-2017** (Canadian Institute for Cybersecurity)
- ~2.8M network flow records, 78 features per flow
- Labels: BENIGN, DDoS, PortScan, Bot, Web Attack, DoS, and more
- Binary classification: BENIGN (0) vs. any attack (1)

Download: https://www.unb.ca/cic/datasets/ids-2017.html → MachineLearningCSV.zip → place in `data/MachineLearningCVE/`

```bash
git clone https://github.com/ccdeguzman/Adversarial-Attack-Defense-on-ML-Based-Network-Intrusion-Detection.git
cd Adversarial-Attack-Defense-on-ML-Based-Network-Intrusion-Detection

python -m venv venv          # Python 3.10–3.12
venv\Scripts\activate        # Windows
    OR
source venv/bin/activate     # Mac/Linux

pip install -e ".[dev,notebooks,robustness]"
```

Dependencies are pinned to exact versions in `pyproject.toml`. Extras: `notebooks` (Jupyter, matplotlib), `robustness` (IBM ART, for cross-checking our PGD), `dev` (ruff, pytest). A plain `pip install -e .` installs only the core library.

```bash
ruff check .     # lint
pytest           # tests (added in Phase 2)
```

Run notebooks in order: `threat_detection` → `adversarial_attacks` → `defenses`

---

## Tech Stack

PyTorch · scikit-learn · pandas · NumPy · CICIDS-2017

---

## What I learned
- I learned how to run jupyter notebook from Visual Studio Code terminal. I usually run jupyter notebook on Google Drive.
- I learned that I had to only take 30% of each file which is still hundreds of thousands of rows. The full dataset is too big for my current RAM. 
- I learned that F1 score is better for imbalanced classes because it balances both recall and precision. False positives and false negatives affect the score. <br>
F1 uses a harmonic mean, the F1 score is only high if both precision and recall are high. 
- FGSM initially looked weak (99.8% → 94.6%), but that was because I was evaluating on training data the model was extremely confident on, where cross-entropy gradients nearly vanish. On a proper held-out set, FGSM is effective (99.2% → 43.8%), and PGD is stronger still.
- Always call `model.eval()` before evaluating. My hardened models were benchmarked in training mode, so dropout randomized the attack's gradients and BatchNorm used per-batch statistics. That made the results meaningless (robust accuracy went *up* as the attack got stronger).
- Data leakage is easy to miss: fitting the scaler before splitting, validating on the test set, duplicate rows appearing in both train and test, and evaluating attacks on training samples all inflate results.
- An off-by-one in intent (`X_batch[:split]` as the clean part) meant "30% adversarial" was actually 70%. Checking what a ratio parameter really does is worth it.

---

## Resources and Works Cited
- Iman Sharafaldin, Arash Habibi Lashkari, and Ali A. Ghorbani, “Toward Generating a New Intrusion Detection Dataset and Intrusion Traffic Characterization”, 4th International Conference on Information Systems Security and Privacy (ICISSP), Portugal, January 2018.
- Adversarial Robustness Tutorial: FGSM vs PGD Attacks in PyTorch (Hands-on Code): https://www.youtube.com/watch?v=uX7IkTlyfuc
- Adversarial Robustness Toolbox How to attack and defend your machine learning models: https://www.youtube.com/watch?v=tTN_BqTv440
