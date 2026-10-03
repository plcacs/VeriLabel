# VeriLabel Artifact

This artifact contains the data, scripts, human-level annotations, and experimental results for vulnerability fix classification using VeriLabel.

---

## 📁 CSV Files

### 1. `300data_humanlevel.csv`

This file contains the human-level analysis of **300 function-level code changes** associated with vulnerability-fixing commits. The manually annotated dataset serves as the ground truth for evaluating VeriLabel's ability to identify genuine local vulnerability fixes and characterize the semantic contribution of guard strengthening.

Each sample was analyzed to determine whether the function-level change genuinely contributes to vulnerability mitigation, identify the mitigation strategy, and establish whether guard strengthening occurs.

**Annotation fields:**

| Column | Description |
|---|---|
| `index` | Unique sample index |
| `CVE ID` | CVE identifier associated with the vulnerability |
| `CWE ID` | CWE classification of the vulnerability |
| `project` | Project associated with the sample |
| `actual_class` | Human-annotated classification (`Fixing` / `Not_Fixing`) |
| `mitigation_strategy` | Human analysis describing the vulnerability mitigation mechanism or the nature of the change |
| `is_guard_strengthened` | Human annotation indicating whether guard strengthening occurs (`True` / `False`) |
| `codeLink` | Source-code reference for examining the change |
| `commit_id` | Commit identifier associated with the change |

**Human annotation results:**

| Actual class | Guard strengthened (`True`) | Not strengthened (`False`) | Total |
|---|---:|---:|---:|
| Fixing | 135 | 61 | 196 |
| Not_Fixing | 1 | 103 | 104 |
| **Total** | **136** | **164** | **300** |

**Key observations:**

- **196 of 300 samples (65.33%)** were identified as genuine local vulnerability fixes.
- **135 of 196 genuine fixes (68.88%)** exhibit guard strengthening.
- **61 of 196 genuine fixes (31.12%)** do not exhibit guard strengthening, indicating that genuine vulnerability fixes can involve other semantic mitigation mechanisms.
- **135 of 136 guard-strengthened samples (99.26%)** were classified as genuine fixes.
- Only **1 of 104 non-fixing samples (0.96%)** exhibits guard strengthening.

These findings demonstrate a strong association between guard strengthening and genuine local vulnerability fixes within the annotated sample. However, guard strengthening is not necessary for every vulnerability fix, as some vulnerabilities are mitigated through other semantic changes.

**Purpose in VeriLabel evaluation:**

The annotations support two complementary analyses:

1. **Ground-truth evaluation:** The `actual_class` field provides manually established reference labels against which VeriLabel's `Fixing` and `Not_Fixing` predictions can be compared.
2. **Semantic characterization:** The `mitigation_strategy` and `is_guard_strengthened` fields help characterize the relationship between guard strengthening and vulnerability mitigation, including genuine fixes that fall outside VeriLabel's guard-strengthening criterion.

The human annotation results characterize the dataset independently of VeriLabel's predictions.

---
### 2. `comparison_VeriLabel.csv`

This file contains a record-level comparison between **VeriLabel's predictions and the human-annotated ground truth for 300 function-level code changes**. It is used to evaluate VeriLabel's ability to distinguish genuine local vulnerability fixes from non-fixing changes.

Each record includes the manually assigned vulnerability-fixing classification, VeriLabel's predicted classification, prediction correctness, and the human annotation indicating whether guard strengthening occurs.

**Comparison fields:**

| Column | Description |
|---|---|
| `index` | Sample index corresponding to the human-annotated dataset |
| `actual_class` | Human-annotated ground-truth classification (`Fixing` / `Not_Fixing`) |
| `prediction` | VeriLabel's predicted classification (`Fixing` / `Not_Fixing`) |
| `is_correct` | Indicates whether VeriLabel's prediction matches the human-annotated classification (`True` / `False`) |
| `is_guard_strengthened` | Human annotation indicating whether guard strengthening occurs (`True` / `False`) |

**Evaluation results:**

We evaluate VeriLabel against the manually annotated `actual_class` labels, treating `Fixing` as the positive class.

The resulting confusion matrix is:

| Actual class | Predicted `Fixing` | Predicted `Not_Fixing` | Total |
|---|---:|---:|---:|
| Fixing | 146 (TP) | 50 (FN) | 196 |
| Not_Fixing | 11 (FP) | 93 (TN) | 104 |
| **Total** | **157** | **143** | **300** |

The corresponding classification performance is:

| Metric | Result |
|---|---:|
| Precision | 92.99% |
| Recall | 74.49% |
| F1-score | 82.72% |
| Accuracy | 79.67% |

These results are calculated by comparing the `prediction` column against the manually established `actual_class` labels.

The `is_guard_strengthened` column provides an additional annotation dimension for examining the relationship between guard strengthening and vulnerability mitigation. It is not used as the ground-truth classification when computing the reported evaluation metrics.

---

### 3. `BigVul_result.csv`

VeriLabel fix-or-not predictions for **6,438 entries** from the BigVul dataset.

| Column | Description |
|---|---|
| `id` | Unique sample identifier |
| `project` | Project the sample belongs to |
| `prediction` | VeriLabel output (`Fixing` / `Not_Fixing`) |
| `before_func` | Function code before the change |
| `after_func` | Function code after the change |

---

### 4. `PrimeVul_result.csv`

VeriLabel fix-or-not predictions for **4,446 entries** from the PrimeVul dataset.

| Column | Description |
|---|---|
| `id` | Unique sample identifier |
| `prediction` | VeriLabel output (`Fixing` / `Not_Fixing`) |
| `before_func` | Function code before the change |
| `after_func` | Function code after the change |

---

## 🔁 Reproducing the Results

### Option A: Easier Method (Recommended)

For convenience, two ready-to-use folders are provided:

- **`test_on_BigVul/`** — contains input pairs for all 10 BigVul projects.
- **`test_on_PrimeVul/`** — contains input pairs for PrimeVul (test, train, and validation splits).

**Steps:**

1. Clone the repository:

   ```bash
   git clone <repository-url>
   ```

2. Navigate to any project folder inside `test_on_BigVul/` or to a subfolder inside `test_on_PrimeVul/`.

3. Run the analysis script:

   ```bash
   python Verilabel_beta3_test.py
   ```

4. A `Vulresult.csv` file will be generated in the corresponding directory.

> Tables 1–5 reported in the paper can be reproduced using the generated `Vulresult.csv` files.

---

### Option B: From Scratch

1. From `BigVul_result.csv` or `PrimeVul_result.csv`, extract the `before_func` column and save each entry as a separate file in a folder named `before/`, using the corresponding `id` as the filename.

2. Similarly, extract the `after_func` column and save each entry as a separate file in a folder named `after/`, using the corresponding `id` as the filename.

3. Run the analysis script:

   ```bash
   python Verilabel_beta3_test.py
   ```

4. The script will generate several output files. The final fix classification results can be found in **`Vulresult.csv`**.

---

## 📊 Reproducing Table 6: Baseline Model Evaluation

### 5. Baseline Model Predictions

The artifact includes prediction results produced by four baseline vulnerability detection models across three datasets and five random seeds. These results are used to reproduce the averaged baseline performance reported in **Table 6**.

| Item | Description |
|---|---|
| Evaluated models | LineVul, DeepDFA, CodeBERT, and ReVeal |
| Evaluation datasets | Setting 1, Setting 2, and the original BigVul dataset |
| Random seeds | Five independent training runs for each model–dataset combination |
| Reported metrics | F1-score, Recall, and Precision |
| Results archive | `Four_Models_Predictions.zip` |
| Output usage | Computing the averaged baseline results reported in Table 6 |

### Baseline Implementations

The baseline implementations and resources were obtained from the following sources:

| Model | Source |
|---|---|
| LineVul | [LineVul GitHub repository](https://github.com/awsm-research/LineVul) |
| DeepDFA | [DeepDFA GitHub repository](https://github.com/ISU-PAAL/DeepDFA) |
| CodeBERT and ReVeal | [Figshare resources](https://doi.org/10.6084/m9.figshare.20791240) |

### Evaluation Datasets

Each baseline model was evaluated using the following three datasets:

| Dataset | File / Source |
|---|---|
| Setting 1 | `CWE_IDs_Vulnerable_plus_k_times_non_vulnerable_split.zip` |
| Setting 2 | `CWE_IDs_Vulnerable_plus_k_times_non_vulnerable_refiltered_by_Bigvul_Fixing_split.tar.gz` |
| Original BigVul dataset | Original BigVul dataset used by prior baseline studies |

The **original BigVul dataset** can be downloaded from the original authors' Google Drive:

[Download original BigVul dataset](https://drive.google.com/uc?id=10-kjbsA806Zdk54Ax8J3WvLKGTzN8CMX)

This link refers to the original dataset resource and is not intended to reveal the authorship of the current paper.

### Data Preprocessing and Model Training

For **Setting 1** and **Setting 2**, we first applied the required preprocessing procedures to convert the datasets into the input formats expected by each baseline model.

Detailed preprocessing procedures are available in the corresponding baseline repositories listed above.

In our evaluation, we followed those preprocessing pipelines and replaced the original input datasets with our Setting 1 and Setting 2 datasets. After preprocessing, each model was retrained on the corresponding processed dataset.

For the **original BigVul dataset**, we followed the original baseline settings and used it as the reference dataset for comparison.

### Evaluation Procedure

For each model–dataset combination, we conducted five independent training runs using different random seeds.

The trained models were then used to generate predictions on their corresponding test sets.

For each run, we calculated the following evaluation metrics:

- F1-score
- Recall
- Precision

We report the average metric values across the five random seeds for each model–dataset combination.

### Prediction Results

The prediction outputs from the four baseline models are provided in:

**`Four_Models_Predictions.zip`**

These files contain the prediction results used to calculate the averaged F1-score, Recall, and Precision values reported in Table 6.

To reproduce the reported baseline results, extract the archive, compute the evaluation metrics for each model–dataset–seed combination, and average the corresponding metric values across the five random seeds.