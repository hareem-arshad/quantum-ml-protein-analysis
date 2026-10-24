# ProteinQ: A Variational Quantum Classifier for SIRT6 Binding

**Team:** ProteinQ
**Members:** Hareem Arshad, Alaa Osman, Ayden, Colson Asher
**Track / Problem:** Track 2 (Quantum Machine Learning), Problem 3: Build and benchmark a variational quantum classifier
**Python version:** [fill in, e.g. 3.11.x]

## Summary

SIRT6 is a protein studied as a cancer drug target, and screening molecules against it in the lab is slow and costly. We built a variational quantum classifier (VQC) from scratch in Qiskit that predicts whether a molecule from the UCI Sirtuin6 dataset (100 molecules, 6 descriptors) has a High or Low binding free energy (BFE). We tuned the VQC on a validation split, then benchmarked it on 25 held-out molecules against Logistic Regression, an RBF-SVM and a Random Forest, using all 6 features and reduced sets of 4 and 2. The VQC scored 0.88 (22/25) with 2 features, but the 95% confidence intervals of all four models overlap, so we claim no quantum advantage.

## Main result

![Main result](figures/main_result.png)

Test accuracy of the VQC and three classical models on 25 held-out molecules, with 95% Wilson confidence intervals. With 2 features the VQC reaches 0.88 versus 0.80 to 0.84 for the classical models, but the intervals overlap almost completely. With 6 and 4 features the classical models are equal or better (best classical result: RBF-SVM, 0.96 at 4 features).

| Features | VQC (5 seeds) | Logistic Regression | RBF-SVM | Random Forest |
|---|---|---|---|---|
| 6 | 0.768 | 0.80 | 0.84 | 0.84 |
| 4 | 0.760 | 0.84 | 0.96 | 0.80 |
| 2 | 0.880 | 0.80 | 0.84 | 0.84 |

The classical models are the reference values for this comparison. The majority-class guess is 0.52.

## How to reproduce (clean start)

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook ProteinQ_QML.ipynb   # then choose Kernel > Restart and Run All
```

- Expected runtime: [fill in, e.g. about N minutes on a laptop]
- Random seeds used: [fill in the split seed]. VQC tuning used seeds 7, 21 and 42 (42 was selected). The feature-reduction study ran the VQC with 5 seeds: [list them].
- Output: the figures are written to `figures/`. [Confirm that the main figure is saved as `figures/main_result.png`.]
- Internet: the first run downloads the dataset from the UCI Machine Learning Repository. [Confirm how the notebook loads it.]
- Needs an IBM Quantum account? [No for the simulator results. Fill in if a hardware run is included.]

## Repository contents

| Path | What it is |
|------|------------|
| `WRITEUP.pdf` | Write-up (2 to 4 pages): Problem, Method, Results, Honest limits |
| `AI_USE_NOTE.md` | Tools used, one thing the AI got wrong, how we caught it |
| `ProteinQ_QML.ipynb` | Full notebook: data split, VQC, tuning, baselines, feature-reduction study, plots |
| `results/` | Exported CSV results [confirm folder name] |
| `figures/` | All figures used in the write-up, including training curves and confusion matrices |
| `requirements.txt` | Pinned package versions |
| [project plan file] | Project plan [confirm file name] |

## What is simulated

All VQC results come from exact statevector simulation on a classical computer. There is no shot noise, no noise model and no real quantum hardware in the main results. Nothing is a statistical shortcut: the circuit is simulated exactly and the model reads out the probability that the last qubit measures 1. The classical baselines are standard scikit-learn models.

The VQC itself uses angle encoding (each standardized feature becomes the angle 2*arctan(x) of an RY rotation on its own qubit, so 6 features use 6 qubits), then 2 layers of trainable RY rotations with a chain of CNOT gates, trained with COBYLA for 100 iterations on binary cross-entropy loss. With 6 qubits it has 12 trainable weights.

## Hardware run (optional bonus)

[Backend name, date, job ID, and a side-by-side comparison with the simulator. Delete this section if you did not run on hardware.]

## Honest limits (short version)

- No quantum advantage is claimed. The classical models were equal or better at 6 and 4 features, and the 2-feature result is a statistical tie, since one test molecule is worth 4 points and the intervals overlap.
- Everything uses a single train/test split of 100 molecules. Features were ranked once, and settings tuned at 6 features were reused at 4 and 2.
- We did not test shot noise, hardware noise, other feature maps, a quantum kernel or cross-validation.

The full discussion is in `WRITEUP.pdf`.

## Credits

- Dataset: SIRTUIN6 small molecules, UC Irvine Machine Learning Repository.
- Background on SIRT6: Fiorentino, F., Mai, A., & Rotili, D. (2021). Emerging therapeutic potential of SIRT6 modulators. *Journal of Medicinal Chemistry*, 64(14), 9732-9758.
- Libraries: Qiskit (circuits and statevector simulation), SciPy (COBYLA optimizer), scikit-learn (splits, baselines, metrics). [Add NumPy, pandas and Matplotlib if used.]
- AI assistance: ChatGPT and Claude were used for explanations, documentation and code suggestions. See `AI_USE_NOTE.md`.