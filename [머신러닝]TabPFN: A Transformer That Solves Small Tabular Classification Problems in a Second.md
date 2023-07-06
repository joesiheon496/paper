[원문 주소](https://arxiv.org/abs/2207.01848)

Full Publication Link: 2207.01848.pdf (arxiv.org)

Tabular data analysis is a fundamental task in machine learning and data science, with numerous applications across various domains. In recent years, researchers have made significant advancements in developing models specifically designed for tabular data. One such model is the TabPFN (Tabular Prior-Data Fitted Network), which introduces novel architectural enhancements and a unique prior for tabular data. In this article, we will delve into the details of the TabPFN and its contributions compared to existing methods.

The TabPFN Architecture:
The TabPFN is a modified version of the original PFN architecture, designed to handle tabular data efficiently. Two key modifications have been made to the architecture. Firstly, slight adjustments have been made to the attention masks, resulting in shorter inference times. Secondly, the TabPFN has been equipped to handle datasets with varying numbers of features through zero-padding. These architectural enhancements, combined with the main contributions of the TabPFN, are discussed in detail in the appendix.

Training the TabPFN
In the prior-fitting phase, the TabPFN is trained on samples generated from a novel prior specifically designed for tabular data. The training process involves using a 12-layer Transformer and synthetic datasets. The training is computationally expensive, requiring significant time and computational resources. However, it is a one-time offline step done during algorithm development. The trained TabPFN model is then used for all subsequent experiments and evaluations.

Inference with the TabPFN
During the inference phase, the TabPFN approximates the Posterior Predictive Distribution (PPD) for the dataset prior. It captures the marginal predictions across different spaces of Structural Causal Models (SCMs) and Bayesian Neural Networks (BNNs), with a focus on simplicity and causal explanations for the data. The predictions are obtained through a single forward pass of the TabPFN, as well as an ensemble of 32 forward passes on modified datasets. These modifications involve power transformations and index rotations of feature columns and class labels.

A Prior for Tabular Data
The performance of the TabPFN relies on a suitable prior designed for tabular data. The prior incorporates distributions instead of point estimates for most hyperparameters. The notion of simplicity is at the core of the prior, aligning with Occam’s Razor and the Speed Prior. The prior leverages SCMs and BNNs as fundamental mechanisms for generating diverse data. SCMs capture causal relationships among columns in tabular data, while BNNs offer flexibility in modeling complex patterns. The prior also accounts for peculiarities of tabular data, such as correlated and categorical features, exponentially scaled data, and missing values.

Fundamentally Probabilistic Models
Unlike traditional models that rely on point estimates for hyperparameters, the TabPFN allows for full Bayesian treatment of hyperparameters. By defining a probability distribution over the hyperparameter space, including BNN architectures, the TabPFN integrates over the space and model weights. This probabilistic modeling approach extends to a mixture of hyperparameters and distinct priors, combining both SCMs and BNNs.

Multi-Class Prediction
To generate multi-class prediction the scalar labels obtained from the described priors are converted into discrete class labels. This transformation is done by dividing the values of the scalar labels into intervals that correspond to different class labels. It will automatically account for potential class imbalances as well.

TabPFN vs XGBoost Head-to-Head

Table 1: Results on the 18 numerical datasets in OpenML-CC18 for 60 minutes requested time per data-split. ± values indicate a metric’s standard deviation. If available, all baselines optimize ROC AUC. TabPFN n.e. is a faster version of our method, without ensembling, while TabPFN + AutoGluon is an ensemble of TabPFN and AutoGluon.
TabPFN achieves a better tradeoff between accuracy and training speed compared to all other methods, including XGBoost and other gradient boosting decision tree methods.
TabPFN makes predictions within less than a second on one GPU, which is comparable to the performance of the best competitors (AutoML systems) after training for one hour.
TabPFN outperforms tuned GBDT methods and performs better than XGBoost, CatBoost, and LightGBM.
The simple baselines (LogReg, KNN) have lower performance overall.
TabPFN is significantly faster than methods with comparable performance. It requires an average of 1.30s on a CPU and 0.05s on a GPU for prediction, resulting in a substantial speedup compared to baselines.
TabPFN performs exceptionally well on purely numerical datasets, as confirmed by results on OpenML-CC18 Benchmark and additional validation datasets.
In the OpenML-AutoML Benchmark, TabPFN outperforms all baselines in terms of mean cross-entropy, accuracy, and the OpenML Metric.

Code — Very Very Easy to Implement
It very much follows the basic Sklearn architecture.

```
from sklearn.metrics import accuracy_score
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split

from tabpfn import TabPFNClassifier

X, y = load_breast_cancer(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33, random_state=42)

# N_ensemble_configurations controls the number of model predictions that are ensembled with feature and class rotations (See our work for details).
# When N_ensemble_configurations > #features * #classes, no further averaging is applied.

classifier = TabPFNClassifier(device='cpu', N_ensemble_configurations=32)

classifier.fit(X_train, y_train)
y_eval, p_eval = classifier.predict(X_test, return_winning_probability=True)

print('Accuracy', accuracy_score(y_test, y_eval))
```
