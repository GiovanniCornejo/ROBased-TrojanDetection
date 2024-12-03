# Machine Learning-Based Anomaly Detection for Hardware Trojans using Ring Oscillator Networks

## Overview

This project develops machine learning-based classifiers to detect hardware Trojans by analyzing ring oscillator (RO) frequency variations. Trojans in integrated circuits (ICs) can result in malicious modifications that compromise chip security and functionality. Our classifiers leverage RO frequency data to identify anomalies caused by Trojan activity, such as localized power consumption variations.

## Project Structure

### Dataset

`ROFReq` contains RO frequency data for 33 chips, where:

- Rows correspond to Trojan-free or Trojan-inserted states.
    - **Golden (Trojan-free) Data**: Rows 1 and 25 in each chip file.
    - **Trojan-inserted Data**: Rows 2 through 24 in each chip file.
- Columns represent frequencies of eight ROS (RO1-RO8) in the network.

### Notebooks

`case_2.ipynb` handles the scenario where only golden (Trojan-free) chip data is available for training. An Isolation Forest algorithm is employed to detect anomalies indicative of Trojan presence. The implementation includes:

- Feature subset experimentation (e.g., RO1-RO4, RO5-RO8, all ROs)
- Metrics such as accuracy, precision, and recall across 20 trials with different training sample sizes (6, 12, 24 chips).

`case_3.ipynb` addresses the scenario where all samples are unlabeled, using an SVM + KNN ensemble approach:

- **One-Class SVM** detects primary anomalies, identifying Trojan-inserted or otherwise unusual samples.
- **K-Nearest Neighbors** refines the classification by considering the proximity of points to differentiate between Trojan-inserted samples and normal anomalies.
- **K-Means Clustering** is applied as a post-processing step to further segment data into groups.

## Methodology

### Case 2: Isolation Forest

- **Objective**: Train solely on golden data to detect anomalies.
- **Process**:
  - Golden data is extracted and split into training and testing sets.
  - An Isolation Forest classifier is trained with contamination and feature subset settings.
  - Evaluation over 20 trials per training sample size, with metrics aggregated and visualized.
- **Output**: Classification summary with performance metrics including accuracy, precision, F1 score, TNR, TPR, FPR, and FNR for different RO subsets (RO5-RO8), as well as confusion matrices.

### Case 3: SVM + KNN Ensemble

- **Objective**: Handle unlabeled data using a hybrid model.
- **Process**:
  - One-Class SVM detects primary anomalies in scaled, imputed RO data.
  - KNN evaluates the proximity of anomalies to labeled data to improve differentiation.
  - K-Means clustering segments anomalies into distinct categories.
- **Output**: Classification summary with performance metrics including TNR, TPR, FPR, and FNR, as well as confusion matrices.

## Usage

1. Clone the repository:

```bash
git clone https://github.com/GiovanniCornejo/ROBased-TrojanDetection.git
```

2. Ensure you have [Anaconda](https://www.anaconda.com) installed, as it provides the necessary libraries (e.g., `scikit-learn`, `numpy`, `matplotlib`, `seaborn`)

3. Launch the Jupyter notebook environment:

```bash
jupyter notebook
```

- `case_2.ipynb` for training and evaluating an Isolation Forest with golden data.
- `case_3.ipynb` for an SVM + KNN ensemble on unlabeled data.

## Results

### Comparison of Performance Metrics

| **Metric**     | **Case 2 (Isolation Forest)**  | **Case 3 (SVM + KNN Ensemble)**      |
| -------------- | ------------------------------ | ------------------------------------ |
| **TNR**        | 92.41%                         | 64.41%                               |
| **TPR**        | 4.17%                          | 97.69%                               |
| **FPR**        | 7.59%                          | 35.59%                               |
| **FNR**        | 95.83%                         | 2.31%                                |
| **Accuracy**   | 90.26%                         | 81.85%                               |
| **Precision**  | 1.79%                          | 75.15%                               |
| **F1 Score**   | 2.60%                          | 84.95%                               |

### Observations

- **True Positive Rate (TPR)**: The SVM + KNN ensemble in Case 3 demonstrates superior performance in detecting Trojan-inserted chips, achieving a high TPR. In contrast, Case 2 with Isolation Forest performs poorly in this regard, with a much lower TPR.

- **False Positive Rate (FPR)**: Although Case 3 excels in identifying Trojan-inserted chips, it also introduces more false positives, as shown by its higher FPR. Case 2, which is trained solely on golden data, is more conservative, resulting in a lower FPR.

- **Accuracy**: The Isolation Forest in Case 2 attains better overall accuracy than Case 3, likely because of fewer instances of misclassifying Trojan-free chips as infected.

## References

This work is based on:

- Shane Kelly, Xuehui Zhang, Mohammed Tehranipoor, and Andrew Ferraiuolo. _"Detecting Hardware Trojans using On-chip Sensors in an ASIC Design."_ Journal of Electronic Testing 31, no. 1 (2015): 11-26.
