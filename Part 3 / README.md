
Part 3 — Technical Documentation & Analysis

 1. Decision Tree Baseline Variance Analysis
The baseline decision tree displays classic high-variance behavior, achieving nearly 100% training accuracy while dropping significantly on the test data split. Decision trees are described as high-variance algorithms because they fit the training data greedily at each splitting node. Since they prioritize immediate information gain without looking back or checking global impacts, they build deep, complex branches that memorize noise and outliers rather than tracking general patterns.

 2. Controlled Decision Tree Regularization Impact
* max_depth=5: Limits how deep the tree grows, reducing overall model variance at the cost of some added bias. This keeps the model from over-customizing leaf splits to small pockets of training instances.
* min_samples_split=20: Stops splitting a node if it contains fewer than 20 samples, preventing the model from creating rules based on tiny, noisy subsets of data.
* Comparison: The gap between training accuracy and testing accuracy shrinks significantly in the controlled tree compared to the unconstrained baseline, showing that regularization effectively reduces overfitting.

3. Gini vs. Entropy Mathematical Formulations
* Gini Impurity Formula: 
  $$1 - \sum_{i=1}^{C} p_i^2$$
* Entropy Formula:
  $$-\sum_{i=1}^{C} p_i \log_2(p_i)$$

Where $p_i$ is the probability of a sample belonging to class $i$ within that node. 
A node with a **Gini value of 0** means it is perfectly pure—all samples inside that node belong to exactly one class, resulting in maximum classification certainty.

 4. Ensemble Concepts & Feature Ablation Insights
* Feature Importance Computations:Random Forest calculates feature importance by measuring the average reduction in Gini impurity across all splits using that feature, averaged over all trees in the ensemble. Unlike a linear regression coefficient—which assumes a straight-line trend and can be distorted by correlated variables—Random Forest importance captures non-linear relationships and feature interactions without assuming a specific data distribution.
* Bagging Concept: Bootstrap aggregating (bagging) trains each individual tree on a random sample of the data drawn with replacement. Additionally, at each split, it only looks at a random subset of $\sqrt{\text{total features}}$. Averaging the predictions of these diverse, un-correlated trees cancels out individual errors, significantly reducing variance compared to a single deep decision tree.
* Feature Ablation Study Conclusion:When the 5 lowest-importance features are removed, the test ROC-AUC remains highly stable (showing a change below a tight tolerance of 0.005). This indicates these dropped features were mostly noise. Deploying this simplified model in production reduces inference latency, saves compute overhead, and minimizes long-term maintenance costs without compromising model accuracy.

5. Cross-Validation Reliability Advantages
Cross-validation provides a much more stable estimate of how a model will perform on unseen data than a single train-test split. A single split can be lucky or unlucky based on how the data happens to land. By shuffling and evaluating across 5 distinct iterations, StratifiedKFold ensures every sample is used for both training and testing, capturing a reliable mean and standard deviation of model performance.

6. Hyperparameter Grid Space Trade-offs
* Total Configurations Evaluated:3 (n_estimators) × 3 (max_depth) × 2 (min_samples_leaf) × 5 (CV Folds) = 90 total model fits.
* Grid Search vs. Randomized Search:Exhaustive `GridSearchCV` guarantees finding the absolute best combination within the specified grid parameters, but it scales poorly and demands high compute time as more options are added. `RandomizedSearchCV` samples a fixed number of parameter settings from a distribution. It runs much faster and saves compute power while often finding a comparably optimal model, making it a better choice for large hyperparameter spaces.

7. Manual Learning Curve & Capacity Diagnostic
Based on our empirical learning curve table:
1. The training AUC begins extremely high on small data slices and gradually decreases as the dataset grows and becomes more complex to memorize.
2. The test AUC increases steadily alongside larger training fractions, showing the benefit of more data.
3. Capacity Conclusion:Because the test ROC-AUC continues to climb steadily up to the 100% fraction mark rather than flattening out completely, the system remains **data-limited**. Providing more training instances will likely continue to improve its performance.
