# Checking Singh and Mantri (2024) against the public datasets

Singh, K.N. and Mantri, J.K. (2024) 'A clinical decision support system using rough set theory and machine learning
for disease prediction', *Intelligent Medicine*, 4(3), pp. 200–208.
doi: [10.1016/j.imed.2023.08.002](https://doi.org/10.1016/j.imed.2023.08.002)

The paper reports accuracies for six classifiers on four public datasets but doesn't say how it tested them. I wanted
to see what the labels in those datasets actually are and what the models score under a test setup that's stated and
can be repeated. The work is in [`singh_mantri_check.ipynb`](singh_mantri_check.ipynb), with the outputs from a full
run saved in it.

Every model is scored with stratified ten-fold cross-validation repeated five times. Imputation and scaling are
inside the pipeline, so they only see the training folds. Results are the mean of the 50 fold scores +/- their
standard deviation.

## Results

Hepatitis: the label records whether each patient lived or died. This means the task is predicting survival, not
diagnosing hepatitis. We report only a majority baseline to make the label clear, rather than compare model accuracy.

Autism: the label is YES when the ten screening answers add up to at least 7. A decision tree using that total and a
linear SVM using the ten answers both get 100%. If those answers are left out, the other fields are only weakly
predictive: no model beats always predicting NO on accuracy. Balanced accuracy is 56–59%, compared with 50% for a
coin-flip baseline.

Liver: the dataset labels 583 people as liver patients or not. Of these, 416 (71.4%) are patients, so simply
predicting that everyone is a patient gets 71.4% accuracy. A random forest gets 70.1% (the paper reports 91.58%),
and logistic regression gets 71.5% with an AUC of 0.72. The best random forest result across all 120 combinations
of seven features is 72.8%. Grouping feature values into ranges or tuning the forest does not improve the results.
Removing the 13 duplicate rows makes no difference.

Dermatology: a random forest gets 97.5% accuracy, close to the paper's 97.29%. A linear SVM also gets 97.5%,
whereas the paper reports 72.35% for it.

## Running it

```sh
uv sync
uv run --with jupyter jupyter lab singh_mantri_check.ipynb
```

In VS Code, `uv sync` is enough: select the `.venv` kernel.

The 120-subset search takes about ten minutes.

## Data

The paper took three datasets from UCI and the autism one from a Kaggle copy, which holds the same 704 rows as the UCI
file (only the two missing ages are written differently). The paper reports 703 and never says why. All the files
here are from the UCI Machine Learning Repository, under CC BY 4.0.

- `ilpd.csv` (20 October 2026): Ramana, B. and Venkateswarlu, N. ILPD (Indian Liver Patient Dataset).
  doi: 10.24432/C5D02C
- `dermatology.data` (20 October 2026) and `dermatology.names` (25 October 2026): Ilter, N. and Güvenir, H.
  Dermatology. doi: 10.24432/C5FK5P
- `autism.arff` (20 October 2026): Thabtah, F. Autism Screening Adult. doi: 10.24432/C5F019
- `hepatitis.data` and `hepatitis.names` (23 October 2026): Gong, G. Hepatitis. doi: 10.24432/C5Q59J
