Note finale : 

- Mahir : 9/20

03/11/2025 - Discovery

Started the scMARK challenge to predict four cell types including Cancer and T-cells using single-cell gene expression data. The dataset is challenging because it has very high dimensionality with 14,000 genes but only 1,000 training samples. I observed that the data matrix is extremely sparse and contains mostly zeros, which means I cannot feed the raw data into a model without significant cleaning.

21/12/2025 - Research & Preprocessing 

I analyzed the statistical properties of the data and confirmed it follows a Negative Binomial distribution rather than a Poisson distribution. To fix this, I implemented log-normalization and selected only the top 2,000 highly variable genes to reduce noise. I trained a Random Forest baseline, but the error analysis showed it was confusing NK cells with CD8+ T-cells because their gene profiles are too similar.

28/12/2025 - Testing PCA & Error Analysis

I hypothesized that a Support Vector Machine (SVM) would handle the overlapping cell clusters better than decision trees. The SVM with balanced class weights successfully separated the NK cells and improved the overall recall significantly. I fixed a critical bug where the PCA was processing noise instead of signal and wrapped the final solution into a robust pipeline for submission.

30/12/2025 - Investigation & Reality Check

I wasn't satisfied with just guessing why the model keeps confusing NK cells with CD8+ T-cells, so I approached the problem from two angles: theory and practice.

First, I went back to the scMARK benchmark paper, which confirmed that this isn't just a bug in my code, it is a fundamental challenge in the field. The authors explicitly note that these cell types are so biologically similar that standard supervised models frequently fail to separate them, especially on datasets generated with certain technologies (like Azizi et al. 2018).

To verify this in my own data, I followed the standard Seurat - Guided Clustering Tutorial to manually extract and compare the top marker genes. The result was shocking: 5 out of the top 10 genes for NK cells and CD8+ T-cells are identical (Indices: 1829, 7449, 1017, 5000, 1827).

Insight: The model isn't "confused", it is actually mathematically correct to be struggling. If 50% of the strongest features are the same, the signal overlap is massive. Since I cannot use heavy deep learning tools (like scVI) to fix this on such a small dataset, I have to accept this difficulty as part of the problem nature.

05/01/2026 - Final Submission

After realizing that the cell types overlapped so much, I discovered that my standard feature selection (picking "Highly Variable Genes") was actually the root cause. It was prioritizing "loud" genes (like cancer markers) and throwing away the quiet, subtle genes that distinguish NK cells.

To fix this, I built a "Focused Feature Selector" that explicitly looks for these subtle subtype markers and "injects" them into the dataset. When I fed this enriched data into the SVM, the results were clear: the model stopped guessing. I achieved a 90% balanced accuracy, and most importantly, the ability to correctly identify NK cells jumped by 10%. It turns out the key wasn't a more complex model, but simply giving the SVM the right biology-aware features. 
