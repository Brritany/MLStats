![PyPI - Version](https://img.shields.io/pypi/v/MLstatkit)
![PyPI - License](https://img.shields.io/pypi/l/MLstatkit)
![PyPI - Status](https://img.shields.io/pypi/status/MLstatkit)
![PyPI - Wheel](https://img.shields.io/pypi/wheel/MLstatkit)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/MLstatkit)
![PyPI - Download](https://img.shields.io/pypi/dm/MLstatkit)
[![Downloads](https://static.pepy.tech/badge/MLstatkit)](https://pepy.tech/project/MLstatkit)

# MLstatkit

MLstatkit is a comprehensive Python library designed to seamlessly integrate established statistical methods into machine learning projects. It encompasses a variety of tools, including **Delong's test** for comparing areas under two correlated Receiver Operating Characteristic (ROC) curves and **Bootstrapping** for calculating confidence intervals, among others. With its modular design, MLstatkit offers researchers and data scientists a flexible and powerful toolkit to augment their analyses and model evaluations, catering to a broad spectrum of statistical testing needs within the domain of machine learning.

## Installation

Install MLstatkit directly from PyPI using pip:

```bash
pip install MLstatkit
```

## Usage

### Delong's Test

`Delong_test` function enables a statistical evaluation of the differences between the **areas under two correlated Receiver Operating Characteristic (ROC) curves derived from distinct models**. This facilitates a deeper understanding of comparative model performance.

#### Parameters:
- **true** : array-like of shape (n_samples,)  
    True binary labels in range {0, 1}.

- **prob_A** : array-like of shape (n_samples,)  
    Predicted probabilities by the first model.

- **prob_B** : array-like of shape (n_samples,)  
    Predicted probabilities by the second model.

#### Returns:
- **z_score** : float  
    The z score from comparing the AUCs of two models.

- **p_value** : float  
    The p value from comparing the AUCs of two models.

#### Example:

```python
from MLstatkit.stats import Delong_test

# Example data
true = np.array([0, 1, 0, 1])
prob_A = np.array([0.1, 0.4, 0.35, 0.8])
prob_B = np.array([0.2, 0.3, 0.4, 0.7])

# Perform DeLong's test
z_score, p_value = Delong_test(true, prob_A, prob_B)

print(f"Z-Score: {z_score}, P-Value: {p_value}")
```

This demonstrates the usage of `Delong_test` to statistically compare the AUCs of two models based on their probabilities and the true labels. The returned z-score and p-value help in understanding if the difference in model performances is statistically significant.

### Bootstrapping for Confidence Intervals

The `Bootstrapping` function calculates confidence intervals for specified performance metrics using bootstrapping, providing a measure of the estimation's reliability. It supports calculation for AUROC (area under the ROC curve), AUPRC (area under the precision-recall curve), and F1 score metrics.

#### Parameters:
- **true** : array-like of shape (n_samples,)  
    True binary labels, where the labels are either {0, 1}.
- **prob** : array-like of shape (n_samples,)  
    Predicted probabilities, as returned by a classifier's predict_proba method, or binary predictions based on the specified scoring function and threshold.
- **metric_str** : str, default='f1'  
    Identifier for the scoring function to use. Supported values include 'f1', 'accuracy', 'recall', 'precision', 'roc_auc', 'pr_auc', and 'average_precision'.
- **n_bootstraps** : int, default=1000  
    The number of bootstrap iterations to perform. Increasing this number improves the reliability of the confidence interval estimation but also increases computational time.
- **confidence_level** : float, default=0.95  
    The confidence level for the interval estimation. For instance, 0.95 represents a 95% confidence interval.
- **threshold** : float, default=0.5  
    A threshold value used for converting probabilities to binary labels for metrics like 'f1', where applicable.
- **average** : str, default='macro'  
    Specifies the method of averaging to apply to multi-class/multi-label targets. Other options include 'micro', 'samples', 'weighted', and 'binary'.
- **random_state** : int, default=0  
    Seed for the random number generator. This parameter ensures reproducibility of results.

#### Returns:
- **original_score** : float  
    The score calculated from the original dataset without bootstrapping.
- **confidence_lower** : float  
    The lower bound of the confidence interval.
- **confidence_upper** : float  
    The upper bound of the confidence interval.

#### Examples:

```python
from MLstatkit.stats import Bootstrapping

# Example data
y_true = np.array([0, 1, 0, 0, 1, 1, 0, 1, 0])
y_prob = np.array([0.1, 0.4, 0.35, 0.8, 0.2, 0.3, 0.4, 0.7, 0.05])

# Calculate confidence intervals for AUROC
original_score, confidence_lower, confidence_upper = Bootstrapping(y_true, y_prob, 'roc_auc')
print(f"AUROC: {original_score:.3f}, Confidence interval: [{confidence_lower:.3f} - {confidence_upper:.3f}]")

# Calculate confidence intervals for AUPRC
original_score, confidence_lower, confidence_upper = Bootstrapping(y_true, y_prob, 'pr_auc')
print(f"AUPRC: {original_score:.3f}, Confidence interval: [{confidence_lower:.3f} - {confidence_upper:.3f}]")

# Calculate confidence intervals for F1 score with a custom threshold
original_score, confidence_lower, confidence_upper = Bootstrapping(y_true, y_prob, 'f1', threshold=0.5)
print(f"F1 Score: {original_score:.3f}, Confidence interval: [{confidence_lower:.3f} - {confidence_upper:.3f}]")

# Calculate confidence intervals for AUROC, AUPRC, F1 score
for score in ['roc_auc', 'pr_auc', 'f1']:
    original_score, conf_lower, conf_upper = Bootstrapping(y_true, y_prob, score, threshold=0.5)
    print(f"{score.upper()} original score: {original_score:.3f}, confidence interval: [{conf_lower:.3f} - {conf_upper:.3f}]")
```

### Permutation Test for Statistical Significance

The `Permutation_test` function assesses the statistical significance of the difference between two models' metrics by randomly shuffling the data and recalculating the metrics to create a distribution of differences. This method does not assume a specific distribution of the data, making it a robust choice for comparing model performance.

#### Parameters:
- **y_true** : array-like of shape (n_samples,)  
    True binary labels, where the labels are either {0, 1}.
- **prob_model_A** : array-like of shape (n_samples,)  
    Predicted probabilities from the first model.
- **prob_model_B** : array-like of shape (n_samples,)  
    Predicted probabilities from the second model.
- **metric_str** : str, default='f1'  
    The metric for comparison. Supported metrics include 'f1', 'accuracy', 'recall', 'precision', 'roc_auc', 'pr_auc', and 'average_precision'.
- **n_bootstraps** : int, default=1000  
    The number of permutation samples to generate.
- **threshold** : float, default=0.5  
    A threshold value used for converting probabilities to binary labels for metrics like 'f1', where applicable.
- **average** : str, default='macro'  
    Specifies the method of averaging to apply to multi-class/multi-label targets. Other options include 'micro', 'samples', 'weighted', and 'binary'.
- **random_state** : int, default=0  
    Seed for the random number generator. This parameter ensures reproducibility of results.

#### Returns:
- **metric_a** : float  
    The calculated metric for model A using the original data.
- **metric_b** : float  
    The calculated metric for model B using the original data.
- **p_value** : float  
    The p-value from the permutation test, indicating the probability of observing a difference as extreme as, or more extreme than, the observed difference under the null hypothesis.
- **benchmark** : float  
    The observed difference between the metrics of model A and model B.
- **samples_mean** : float  
    The mean of the permuted differences.
- **samples_std** : float  
    The standard deviation of the permuted differences.

#### Examples:
```python
from MLstatkit.stats import Permutation_test

y_true = np.array([0, 1, 0, 0, 1, 1, 0, 1, 0])
prob_model_A = np.array([0.1, 0.4, 0.35, 0.8, 0.2, 0.3, 0.4, 0.7, 0.05])
prob_model_B = np.array([0.2, 0.3, 0.25, 0.85, 0.15, 0.35, 0.45, 0.65, 0.01])

# Conduct a permutation test to compare F1 scores
metric_a, metric_b, p_value, benchmark, samples_mean, samples_std = Permutation_test(
    y_true, prob_model_A, prob_model_B, 'f1'
)

print(f"F1 Score Model A: {metric_a:.5f}, Model B: {metric_b:.5f}")
print(f"Observed Difference: {benchmark:.5f}, p-value: {p_value:.5f}")
print(f"Permuted Differences Mean: {samples_mean:.5f}, Std: {samples_std:.5f}")
```

## References

### Delong's Test
The implementation of `Delong_test` in MLstatkit is based on the following publication:
- Xu Sun and Weichao Xu, "Fast implementation of DeLong’s algorithm for comparing the areas under correlated receiver operating characteristic curves," in *IEEE Signal Processing Letters*, vol. 21, no. 11, pp. 1389-1393, 2014, IEEE.

### Bootstrapping
The `Bootstrapping` method for calculating confidence intervals does not directly reference a single publication but is a widely accepted statistical technique for estimating the distribution of a metric by resampling with replacement. For a comprehensive overview of bootstrapping methods, see:
- B. Efron and R. Tibshirani, "An Introduction to the Bootstrap," Chapman & Hall/CRC Monographs on Statistics & Applied Probability, 1994.

### Permutation Test
The `Permutation_tests` are utilized to assess the significance of the difference in performance metrics between two models by randomly reallocating observations to groups and computing the metric. This approach does not make specific distributional assumptions, making it versatile for various data types. For a foundational discussion on permutation tests, refer to:
- P. Good, "Permutation Tests: A Practical Guide to Resampling Methods for Testing Hypotheses," Springer Series in Statistics, 2000.

These references lay the groundwork for the statistical tests and methodologies implemented in MLstatkit, providing users with a deep understanding of their scientific basis and applicability.

## Contributing

We welcome contributions to MLstatkit! Please see our contribution guidelines for more details.

## License

MLstatkit is distributed under the MIT License. For more information, see the LICENSE file in the GitHub repository.

### Update log
- `0.1.4`   Update `README.md`, Add `Permutation_tests` function, Re-do `Bootstrapping` Parameters.
- `0.1.3`   Update `README.md`.
- `0.1.2`   Add `Bootstrapping` operation process progress display.
- `0.1.1`   Update `README.md`, `setup.py`. Add `CONTRIBUTING.md`.
- `0.1.0`   First edition
