![PyPI - Version](https://img.shields.io/pypi/v/MLstatkit)
![PyPI - License](https://img.shields.io/pypi/l/MLstatkit)
![PyPI - Status](https://img.shields.io/pypi/status/MLstatkit)
![PyPI - Wheel](https://img.shields.io/pypi/wheel/MLstatkit)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/MLstatkit)

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
    True binary labels in range {0, 1}.
- **prob** : array-like of shape (n_samples,)  
    Predicted probabilities or binary predictions depending on the score function.
- **score_func_str** : str  
    Scoring function identifier: 'auroc', 'auprc', or 'f1'.
- **n_bootstraps** : int, optional  
    Number of bootstrapping samples to use (default is 1000).
- **confidence_level** : float, optional  
    The confidence interval level (e.g., 0.95 for 95% confidence interval, default is 0.95).
- **threshold** : float, optional  
    Threshold to convert probabilities to binary labels for 'f1' scoring function (default is 0.5).
- **average** : str, optional  
    This parameter is required for multiclass/multilabel targets. (default is 'macro').  
    If None, the scores for each class are returned. Otherwise, this determines the type of averaging performed on the data.

#### Returns:
- **original_score** : float  
    The original score calculated without bootstrapping.
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
original_score, confidence_lower, confidence_upper = Bootstrapping(y_true, y_prob, 'auroc')
print(f"AUROC: {original_score:.3f}, Confidence interval: [{confidence_lower:.3f} - {confidence_upper:.3f}]")

# Calculate confidence intervals for AUPRC
original_score, confidence_lower, confidence_upper = Bootstrapping(y_true, y_prob, 'auprc')
print(f"AUPRC: {original_score:.3f}, Confidence interval: [{confidence_lower:.3f} - {confidence_upper:.3f}]")

# Calculate confidence intervals for F1 score with a custom threshold
original_score, confidence_lower, confidence_upper = Bootstrapping(y_true, y_prob, 'f1', threshold=0.5)
print(f"F1 Score: {original_score:.3f}, Confidence interval: [{confidence_lower:.3f} - {confidence_upper:.3f}]")

# Calculate confidence intervals for AUROC, AUPRC, F1 score
for score in ['auroc', 'auprc', 'f1']:
    original_score, conf_lower, conf_upper = Bootstrapping(y_true, y_prob, score, threshold=0.5)
    print(f"{score.upper()} original score: {original_score:.3f}, confidence interval: [{conf_lower:.3f} - {conf_upper:.3f}]")
```

## References

### Delong's Test
The implementation of `Delong_test` in MLstatkit is based on the following publication:
- Xu Sun and Weichao Xu, "Fast implementation of DeLong’s algorithm for comparing the areas under correlated receiver operating characteristic curves," in *IEEE Signal Processing Letters*, vol. 21, no. 11, pp. 1389-1393, 2014, IEEE.

### Bootstrapping
The `Bootstrapping` method for calculating confidence intervals does not directly reference a single publication but is a widely accepted statistical technique for estimating the distribution of a metric by resampling with replacement. For a comprehensive overview of bootstrapping methods, see:
- B. Efron and R. Tibshirani, "An Introduction to the Bootstrap," Chapman & Hall/CRC Monographs on Statistics & Applied Probability, 1994.

These references provide the foundational methodologies behind the statistical tests and techniques implemented in MLstatkit, offering users insights into their theoretical underpinnings.

## Contributing

We welcome contributions to MLstatkit! Please see our contribution guidelines for more details.

## License

MLstatkit is distributed under the MIT License. For more information, see the LICENSE file in the GitHub repository.

### Update log
- `0.1.3`  Update `README.md`.
- `0.1.2`  Add `Bootstrapping` operation process progress display.
- `0.1.1`  Update `README.md`, `setup.py`. Add `CONTRIBUTING.md`.
- `0.1.0`  First edition
