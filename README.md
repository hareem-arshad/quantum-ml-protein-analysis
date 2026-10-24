# ProteinQ: A Variational Quantum Classifier for SIRT6 Binding Prediction

Can a small variational quantum classifier (VQC) learn a real drug-discovery task today, and how does it compare with standard classical models?

ProteinQ builds and benchmarks a VQC that predicts whether a small molecule is a **high** or **low** binder of the SIRT6 protein, and compares it against logistic regression, an RBF-SVM, and a random forest.

**Track:** Quantum Machine Learning, Problem 3 (Build and Benchmark a Variational Quantum Classifier)
**Team:** Hareem Arshad, Alaa Osman, Ayden, Colson Asher

> **TL;DR: No quantum advantage is claimed.** Classical models beat the VQC with 6 and 4 features and are statistically indistinguishable from it with 2 features. With only 25 test molecules, the results are suggestive at best.

---

## Background

SIRT6 is a protein studied as a drug target. Its expression can promote oncogenesis, in part because it helps cancer cells evade apoptosis by deacetylating histone H3K9 [2]. Screening candidate inhibitors in the lab is slow and costly, so a fast model that ranks candidates first can save time.

Binding free energy (BFE) measures how tightly a molecule binds. Low BFE means a strong binder and a better drug candidate.

## Dataset

[UCI SIRTUIN6 small molecules dataset](https://archive.ics.uci.edu/) [1]

| Property | Value |
|---|---|
| Molecules | 100 |
| Descriptors | 6 (`SC-5`, `SP-6`, `SHBd`, `minHaaCH`, `maxwHBa`, `FMF`) |
| Classes | `High_BFE` / `Low_BFE` |
| Majority-class baseline | 52% |
| Split | Stratified 75 train / 25 test (inner 60 / 15 train / validation for tuning) |

Random forest importance ranking of the features: `SP-6 > FMF > minHaaCH > SC-5 > maxwHBa > SHBd`.

## Method

- **Preprocessing:** standardize inputs, then encode as rotation angles via `2·arctan(x)`
- **Circuit:** RY angle encoding, trainable RY layers with a CNOT entangling chain, one measured qubit
- **Optimizer:** COBYLA, 100 iterations
- **Baselines:** RBF-SVM, logistic regression, random forest (all on the same split)
- **Uncertainty:** 95% Wilson confidence intervals on test accuracy

### Hyperparameter selection (validation set, seed 42 unless noted)

| Setting | Values tried | Result |
|---|---|---|
| COBYLA initial step (`rhobeg`) | 0.1, 0.5, 1.0 | 0.1 worst (acc 0.60); 0.5 and 1.0 both 0.80; **0.5 chosen** on validation macro F1 |
| Circuit depth | 1, 2, 3 layers | **2 layers** best (0.80); 1 and 3 layers 0.60 |
| Random seed | 7, 21, 42 | 0.667, 0.800, 0.800, so seed alone moves accuracy by more than 13 points |

Final VQC: **2 layers, `rhobeg` = 0.5, seed 42** (validation macro F1 = 0.7847).

## Results

### All 6 features (final held-out test, single run)

| Model | Accuracy | Macro F1 |
|---|---|---|
| VQC | 0.72 | 0.72 |
| RBF-SVM | 0.84 | 0.838 |
| Logistic regression | 0.84 | 0.84 |

The classical models classified 3 more of the 25 test molecules correctly.

### Feature reduction (top 6 / 4 / 2 features by importance)

| Model | 6 features | 4 features | 2 features |
|---|---|---|---|
| VQC | 0.768 | 0.760 | **0.880** |
| Logistic regression | 0.80 | 0.84 | 0.80 |
| RBF-SVM | 0.84 | 0.96 | 0.84 |
| Random forest | 0.84 | 0.80 | 0.84 |

*Test accuracy. Values for the classical models are read from the results plots; check exact numbers in the notebook outputs.*

### How certain are the results?

95% Wilson intervals at 2 features (n = 25):

| Model | Test accuracy | 95% CI |
|---|---|---|
| VQC | 0.88 (22/25) | 0.700 – 0.958 |
| Logistic regression | 0.80 (20/25) | 0.609 – 0.911 |
| RBF-SVM | 0.84 (21/25) | 0.653 – 0.936 |
| Random forest | 0.84 (21/25) | 0.653 – 0.936 |

All intervals overlap, so the VQC's higher score at 2 features is **not** strong evidence that it outperforms classical models.

## Honest limits

- No quantum advantage is claimed.
- Each test molecule is worth 4 accuracy points; the test set is tiny.
- VQC results depend heavily on the random initialization.
- Classical models train in milliseconds with no random start to tune.
- Feature ranking and feature-count comparisons were evaluated on the same 25-molecule test set, so the best numbers should be read as optimistic.

## Hardware

A hardware check was run on the IBM Quantum platform (see `src/proteinq_with_hardware_check.ipynb`).

| Item | Value |
|---|---|
| Backend | _fill in_ |
| Shots | _fill in_ |
| Simulator vs. hardware accuracy | _fill in_ |

## Repository structure

```
.
├── README.md
├── figures/                                 # plots used in the report
│   ├── Confusion Matrix.png
│   ├── Experiment 1 - COBYLA.png
│   ├── Experiment 2.png
│   ├── Experiment 3.png
│   ├── VQC Stability.png
│   ├── VQC Training Loss.png
│   └── VQC vs SVM Accuracy Comparison.png
├── src/
│   ├── proteinq-notebook-qml-vs-classical-ml.ipynb   # main experiments: training, baselines, plots
│   └── proteinq_with_hardware_check.ipynb            # IBM hardware run
├── ProteinQ - QML Model to Predict SIRT6 Protein Bindings.pdf   # project write-up
├── AI_USE_NOTE.md
├── SUBMISSION_CHECKLIST.md
├── WRITEUP_TEMPLATE.md
├── requirements.txt
├── LICENSE
└── .gitignore
```

## Getting started

```bash
git clone https://github.com/hareem-arshad/quantum-ml-protein-analysis.git
cd quantum-ml-protein-analysis
pip install -r requirements.txt
jupyter notebook src/proteinq-notebook-qml-vs-classical-ml.ipynb
```

Typical dependencies: `qiskit`, `qiskit-machine-learning`, `qiskit-ibm-runtime`, `scikit-learn`, `numpy`, `pandas`, `matplotlib`. Both notebooks run on the free Colab tier. Running on IBM hardware requires an IBM Quantum account and API token (never commit your token).

## Reproducing the results

1. Open the main notebook and run all cells; the split uses a fixed stratified seed.
2. Set the VQC seed to 42 to reproduce the reported final model. Other seeds will give different accuracies.
3. Figures (accuracy vs. feature count, ROC curves, confusion matrices) are saved to `figures/`.

## Possible next steps

- Repeat the VQC over many seeds and report mean ± standard deviation
- Use repeated stratified k-fold or leave-one-out cross-validation on all 100 molecules
- Select features and feature count using training/validation data only
- Compare against classical models matched in parameter count

## AI usage

ChatGPT and Claude chat features were used to clarify concepts, improve documentation, and suggest code. The team reviewed every AI suggestion by hand (see [AI_USE_NOTE.md](AI_USE_NOTE.md)). Some suggestions were generic and not specific to our setup; these were caught during review and testing.

## References

1. SIRTUIN6 small molecules dataset, UC Irvine Machine Learning Repository.
2. Fiorentino, F., Mai, A., & Rotili, D. (2021). Emerging therapeutic potential of SIRT6 modulators. *Journal of Medicinal Chemistry*, 64(14), 9732–9758. https://doi.org/10.1021/acs.jmedchem.1c00601

## License

See [LICENSE](LICENSE).