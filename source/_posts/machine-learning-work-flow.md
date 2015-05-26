title: "machine-learning-work-flow"
date: 2015-05-14 18:57:33
tags: [machine learning]
---

## 数据预处理
去掉零方差、多重共线性等
在 scikit-learn 中，可以使用 sklearn.feature_selection 模块中的相应函数：
### Removing features with low variance
VarianceThreshold 
### Univariate feature selection
* **SelectKBest** removes all but the k highest scoring features
* **SelectPercentile** removes all but a user-specified highest scoring percentage of features
* using common univariate statistical tests for each feature: false positive rate **SelectFpr**, false discovery rate **SelectFdr**, or family wise error **SelectFwe**.
* **GenericUnivariateSelect** allows to perform univariate feature
selection with a configurable strategy. This allows to select the best univariate selection strategy with hyper-parameter search estimator.

### Recursive feature elimination
* RFE
* RFECV

### L1-based feature selection
* linear_model.Lasso 
* linear_model.LogisticRegression
*  svm.LinearSVC
*  RandomizedLasso
*  RandomizedLogisticRegression
*  lasso_stability_path

###Tree-based feature selection
* ExtraTreesClassifier in sklearn.ensemble










