03/11/2025 - Discovery

Started the scMARK challenge to predict four cell types including Cancer and T-cells using single-cell gene expression data. The dataset is challenging because it has very high dimensionality with 14,000 genes but only 1,000 training samples. I observed that the data matrix is extremely sparse and contains mostly zeros, which means I cannot feed the raw data into a model without significant cleaning.

21/12/2025 - Research & Preprocessing 

I analyzed the statistical properties of the data and confirmed it follows a Negative Binomial distribution rather than a Poisson distribution. To fix this, I implemented log-normalization and selected only the top 2,000 highly variable genes to reduce noise. I trained a Random Forest baseline, but the error analysis showed it was confusing NK cells with CD8+ T-cells because their gene profiles are too similar.

28/12/2025 - Testing PCA & Error Analysis
I hypothesized that a Support Vector Machine (SVM) would handle the overlapping cell clusters better than decision trees. The SVM with balanced class weights successfully separated the NK cells and improved the overall recall significantly. I fixed a critical bug where the PCA was processing noise instead of signal and wrapped the final solution into a robust pipeline for submission.
