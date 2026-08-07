# An Empirical Evaluation of K-Means-Based Outlier Detection for Zero-Day Intrusion Detection in Industrial IoT Networks

## Authors

* **Noushin Subah** — Department of Computer Science, Asian University for Women, Bangladesh
* **Sarah Azam Khan** — Department of Computer Science, Asian University for Women, Bangladesh
* **Nazhat Nashrah** — Department of Computer Science, Asian University for Women, Bangladesh

---

## Project Overview

Industrial Internet of Things (IIoT) networks are increasingly exposed to cyberattacks, including **zero-day attacks** that exploit previously unknown vulnerabilities and therefore cannot be reliably detected using conventional signature-based intrusion detection systems.

This project presents an empirical evaluation of a lightweight hybrid intrusion detection framework that combines an **Attention-based Bidirectional Gated Recurrent Unit (ABi-GRU)** classifier with **centroid-distance-based outlier detection using MiniBatch K-Means**.

The study focuses on how the number of clusters (**K**) and the centroid-distance threshold (**d_min**) affect zero-day detection performance. A **Leave-One-Class-Out (LOCO)** evaluation protocol is used to simulate previously unseen attacks, allowing the system to be evaluated on attack classes that are completely excluded from training and validation.

---

## Problem Statement

Traditional intrusion detection systems and supervised machine-learning approaches generally rely on attack patterns that are represented in their training data. This creates a significant challenge when previously unseen or **zero-day attacks** occur.

Although clustering-based anomaly detection can potentially identify unfamiliar network behavior without requiring labeled examples of the new attack, the effectiveness of centroid-based detection depends strongly on the clustering configuration and anomaly threshold.

This project therefore investigates how **K-Means clustering and centroid-distance thresholds influence the balance between detecting unseen attacks and generating false alarms in IIoT networks**.

---

## Research Questions

### RQ1

How does the integration of centroid-distance-based outlier detection affect the **Zero-Day Detection Rate (Z-DR)** of a deep-learning classifier when identifying unseen malicious flows in an IIoT environment?

### RQ2

How can the **Z-DR/False Positive Rate (FPR)** trade-off be balanced based on the centroid-distance threshold (`d_min`) and number of clusters (`K`), and which configuration provides the best balance under a Leave-One-Class-Out evaluation?

---

## Research Objectives

The project aims to:

* Develop a lightweight hybrid intrusion detection framework combining **ABi-GRU** classification with **MiniBatch K-Means** outlier detection.
* Evaluate centroid-distance-based detection for identifying previously unseen attacks.
* Simulate zero-day attacks using a **Leave-One-Class-Out (LOCO)** evaluation protocol.
* Investigate the effect of different numbers of clusters (`K`) on zero-day detection.
* Analyze the effect of the centroid-distance threshold (`d_min`) on detection performance.
* Evaluate the trade-off between **Zero-Day Detection Rate (Z-DR)** and **False Positive Rate (FPR)**.
* Determine the best `K` and `d_min` configurations for the held-out attack types.

---

## Dataset

The experiments use the **ToN-IoT `train_test_network` dataset**, a benchmark network intrusion dataset for IoT/IIoT environments.

The original dataset contains:

* **211,043 network-flow records**
* **44 features**
* One benign class
* Nine attack categories

The dataset contains the following classes:

| Class      | Type   |   Instances |
| ---------- | ------ | ----------: |
| Normal     | Benign |      50,000 |
| Backdoor   | Attack |      20,000 |
| DoS        | Attack |      20,000 |
| DDoS       | Attack |      20,000 |
| Injection  | Attack |      20,000 |
| Password   | Attack |      20,000 |
| Ransomware | Attack |      20,000 |
| Scanning   | Attack |      20,000 |
| XSS        | Attack |      20,000 |
| MITM       | Attack |       1,043 |
| **Total**  |        | **211,043** |

The dataset contains network-flow characteristics including flow duration, packet and byte counts, connection state, and protocol-level attributes such as DNS, SSL, and HTTP information.

---

## Data Preprocessing

The preprocessing pipeline consists of the following steps:

1. **Duplicate removal**
   20,569 duplicate records were identified and removed from the original 211,043 records, resulting in **190,474 records**.

2. **Missing-value handling**
   No missing values were found in the dataset.

3. **Feature selection**
   Identifier-based features such as source/destination IP addresses, ports, and timestamps were removed to reduce the risk of overfitting to specific hosts or connections.

4. **Categorical encoding**
   Categorical features were converted into numerical representations.

5. **Leave-One-Class-Out (LOCO)**
   Specific attack classes were completely withheld from training and validation and used only for zero-day testing.

6. **Train-validation split**
   The remaining known-class data was divided into training and validation sets using an 80/20 stratified split.

7. **Feature normalization**
   Features were standardized using z-score normalization, with scaling parameters estimated only from the training data to avoid information leakage.

8. **SMOTE**
   Synthetic Minority Over-sampling Technique (SMOTE) was applied to the training data to address class imbalance. Validation and zero-day test sets were not resampled.

---

## Proposed Methodology

The proposed framework consists of two main components:

### 1. ABi-GRU Classifier

An **Attention-based Bidirectional Gated Recurrent Unit (ABi-GRU)** model is used to classify network traffic belonging to known classes.

The bidirectional GRU processes traffic representations in both directions, while the attention mechanism helps emphasize informative features or representations for classification.

### 2. MiniBatch K-Means Outlier Detection

A **MiniBatch K-Means** clustering model is trained on the preprocessed known traffic.

For each network flow, the distance to every cluster centroid is calculated. The minimum distance to the nearest centroid is used as the anomaly score.

If:

```text
minimum centroid distance > d_min
```

the flow is flagged as a potential zero-day attack.

Otherwise, the flow is passed to the ABi-GRU classifier for known-traffic classification.

MiniBatch K-Means is used instead of standard K-Means to reduce computational cost and support more efficient processing in resource-constrained IIoT environments.

---

## Zero-Day Evaluation

The project uses a **Leave-One-Class-Out (LOCO)** protocol to simulate zero-day attacks.

Two attack types are evaluated as unseen attacks:

* **DDoS**
* **Ransomware**

These attack classes are withheld from training and validation and are introduced only during testing.

The number of clusters is investigated across:

```text
K ∈ {2, 3, 5, 7, 9}
```

The centroid-distance threshold is also varied across multiple `d_min` values to study its effect on zero-day detection.

---

## Evaluation Metrics

The project evaluates both known-attack classification and zero-day detection.

### Classification Metrics

* Accuracy
* Precision
* Recall
* Macro F1-score

### Zero-Day Detection Metrics

**Zero-Day Detection Rate (Z-DR)** measures the proportion of zero-day attack flows correctly identified as anomalous.

**False Positive Rate (FPR)** measures the proportion of benign validation flows incorrectly flagged as zero-day attacks.

A **Combined F1-score** is also used to evaluate the balance between Z-DR and false positives.

---

## Results

### ABi-GRU Classification Performance

For the known-class validation data:

| Held-Out Attack | Validation Accuracy | Macro F1 |
| --------------- | ------------------: | -------: |
| DDoS            |             **94%** | **0.88** |
| Ransomware      |             **93%** | **0.87** |

The proposed ABi-GRU model achieved competitive known-class classification performance. Backdoor, DoS, Normal, and Password traffic achieved F1-scores above 0.90 in both configurations, while MITM was the weakest class due in part to its severe class imbalance and overlap with other traffic classes.

### Zero-Day Detection Performance

The best Combined F1 configurations were:

| Held-Out Attack | Best K | Best d_min |       Z-DR |        FPR | Combined F1 |
| --------------- | -----: | ---------: | ---------: | ---------: | ----------: |
| **DDoS**        |  **3** |    **5.0** | **26.37%** | **25.71%** |  **0.3893** |
| **Ransomware**  |  **7** |    **2.0** |  **7.57%** | **72.03%** |  **0.1191** |

The results demonstrate that the performance of centroid-distance-based zero-day detection depends strongly on the characteristics of the unseen attack.

DDoS traffic produced a substantially better Z-DR/FPR trade-off because its high packet and byte volumes make it more distinguishable from known traffic. In contrast, Ransomware traffic more closely resembles legitimate and known traffic, making distance-based detection substantially less effective.

The experiments also show that **higher zero-day detection does not necessarily mean better detection performance**. At low `d_min` thresholds, Z-DR can become very high, but the system may simultaneously flag almost all traffic as anomalous, producing an impractically high FPR.

### Key Findings

* The choice of **K** significantly affects zero-day detection performance.
* The choice of **d_min** creates a strong trade-off between Z-DR and FPR.
* The silhouette-optimal number of clusters does **not necessarily produce the best zero-day detection performance**.
* Centroid-distance detection is more effective for **volumetric attacks such as DDoS** than for behavioral attacks such as Ransomware.
* No single `(K, d_min)` configuration performs optimally for both held-out attack types.
* The proposed approach is more appropriate as an **auxiliary zero-day detection mechanism**, particularly for attacks that form sufficiently distinct patterns in feature space.

---

## Repository Structure

```text
Zero-Day-IDS-IIoT/
│
├── README.md
├── requirements.txt
│
├── Models/
│   └── Trained model files
│
├── Results/
│   ├── kmeans_results.csv
│   ├── validation_kmeans_results.csv
│   ├── zero_day_kmeans_results.csv
│   ├── k_dmin_sweep.csv
│   └── f1_pivot_table.csv
│
├── Figures/
│   └── Generated figures and plots
│
├── Presentation/
│   └── Project presentation
│
├── Report/
│   └── Final project report
│
└── src/
    └── Source code / Jupyter notebooks
```

> The exact filenames inside each directory may vary depending on the final files uploaded to the repository.

---

## Installation

The project is implemented in Python and can be executed using **Google Colab** or a compatible Python/Jupyter environment.

Install the required dependencies using:

```bash
pip install -r requirements.txt
```

The complete dependency list and package versions are provided in `requirements.txt`.

---

## Execution

### Using Google Colab

1. Open the project notebook from the `src/` directory in Google Colab.
2. Upload the required ToN-IoT `train_test_network` dataset.
3. Run the notebook cells sequentially.
4. The notebook performs data preprocessing and normalization.
5. The known-class data is balanced using SMOTE.
6. The ABi-GRU classifier is trained/evaluated on the known classes.
7. MiniBatch K-Means clustering is performed on the processed feature space.
8. Centroid distances are calculated for validation and held-out zero-day samples.
9. Different `K` and `d_min` configurations are evaluated.
10. Z-DR, FPR, and Combined F1 are calculated for the evaluated configurations.
11. The generated CSV files and figures can be saved to the corresponding `Results/` and `Figures/` directories.

---

## Results Files

The experiment generates CSV files containing the numerical results of the analysis.

| File                            | Description                                                  |
| ------------------------------- | ------------------------------------------------------------ |
| `kmeans_results.csv`            | K-Means clustering results                                   |
| `validation_kmeans_results.csv` | Results obtained on the validation data                      |
| `zero_day_kmeans_results.csv`   | Results obtained on the held-out zero-day data               |
| `k_dmin_sweep.csv`              | Z-DR/FPR results across different K and d_min configurations |
| `f1_pivot_table.csv`            | Combined F1-score comparison across configurations           |

Large intermediate datasets are not required for understanding the reported results and should only be included in the repository if necessary for reproducibility.

---

## Technologies Used

* **Python**
* **Google Colab**
* **Pandas**
* **NumPy**
* **Scikit-learn**
* **Imbalanced-learn**
* **TensorFlow / Keras**
* **Matplotlib**
* **Seaborn**
* **ABi-GRU**
* **MiniBatch K-Means**
* **SMOTE**

---

## Limitations

Several limitations should be considered when interpreting the results:

* The ToN-IoT dataset is simulated, and the framework was not externally validated on additional real-world datasets.
* Only K-Means was investigated as the clustering algorithm.
* The zero-day evaluation was performed using the DDoS and Ransomware attack types from the dataset and therefore may not represent all possible real-world zero-day attacks.
* SMOTE may shift cluster centroids toward the zero-day distribution and consequently influence outlier detection.
* High-dimensional feature spaces and overlapping attack characteristics can reduce the effectiveness of centroid-distance-based detection.
* The ABi-GRU and K-Means outputs were evaluated independently rather than being combined through a fully implemented decision-fusion rule.

---

## Conclusion

This project evaluates a lightweight hybrid intrusion detection framework that combines **ABi-GRU classification** with **centroid-distance-based MiniBatch K-Means outlier detection** for zero-day attack identification in IIoT networks.

The experiments demonstrate that the number of clusters and centroid-distance threshold have a significant influence on zero-day detection performance. The results also show that centroid-distance-based detection is more effective for attacks that form distinct patterns in feature space, such as DDoS, while it performs poorly when the unseen attack resembles legitimate traffic, as observed for Ransomware.

Overall, the study demonstrates the importance of evaluating zero-day detection using both detection rate and false-positive behavior rather than relying solely on conventional classification accuracy.
