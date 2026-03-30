# pLM4Alg Protocol: A protein language model based framework for allergen prediction.

A reproducible protocol for predicting allergenic proteins and peptides using pretrained protein language model (pLM) embeddings and deep learning.

---

## Overview

This repository provides a step-by-step workflow for allergen prediction using the **pLM4Alg framework**.

It integrates:

* Protein Language Model: ESM2 (esm2_t6_8M_UR50D)
* Deep Learning Model: Convolutional Neural Network (CNN)
* Feature Representation: 320-dimensional sequence embeddings

The workflow includes:

* Dataset preparation
* Sequence embedding generation
* Feature normalization
* Model training and hyperparameter optimization
* Prediction on new datasets

---

## Repository Contents

* `pLM4Alg_Protocol_allergen_prediction_ESM2_320.ipynb` → Complete workflow notebook
* `best_model_grid_320.keras` → Final trained model
* `pLM4Alg_protocol_example_allergens_short_dataset.xlsx` → Small demo dataset
* `pLM4Alg_protocol_allergens_dataset.xlsx` → Full allergen dataset
* `new_data.xlsx` → Example input dataset
* `new_data_embeddings_320.csv` → Example embeddings
* `whole_sample_dataset_esm2_t6_8M_UR50D_unified_320_dimension.csv` → Training embeddings
* `new_data_prediction_result.xlsx` → Example prediction output

---

## Installation

Install required packages:

```
pip install tensorflow torch fair-esm scikit-learn pandas numpy h5py keras
```

---

## How to Use

### Step 1: Prepare your dataset

Create an Excel file with a column:

```
sequence
```

* Use uppercase amino acid sequences
* No missing values or non-standard residues

Example:

```
new_data.xlsx
```

---

### Step 2: Generate embeddings

Run the notebook:

```
pLM4Alg_Protocol_allergen_prediction_ESM2_320.ipynb
```

This will generate:

```
new_data_embeddings_320.csv
```

---

### Step 3: Run prediction

```python
from keras.models import load_model
import numpy as np
import pandas as pd

# load model
model = load_model('best_model_grid_320.keras')

# load embeddings
X_new = pd.read_csv('new_data_embeddings_320.csv').to_numpy()
X_new = X_new.reshape((X_new.shape[0], 320, 1))

# predict
predicted_probability = model.predict(X_new)

predicted_class = np.argmax(predicted_probability, axis=1)
confidence_score = np.max(predicted_probability, axis=1)

# load original dataset
dataset = pd.read_excel('new_data.xlsx')

dataset['predicted_label'] = predicted_class
dataset['confidence_score'] = confidence_score

dataset.to_excel('new_data_prediction_result.xlsx', index=False)

print(dataset.head())
```

---

## Output

* `predicted_label` → 1 = allergen, 0 = non-allergen
* `confidence_score` → model confidence (0–1)

---

## Important Notes

* The same embedding model (**ESM2**) must be used for both training and prediction
* Input embeddings must be 320-dimensional
* The example dataset is small and intended only for demonstration
* For real applications, use larger curated datasets
* Precomputed embeddings are provided to reduce computational cost

---

## Model Flexibility

This protocol uses 320-dimensional embeddings generated from the ESM2 model (esm2_t6_8M_UR50D).

Alternative ESM2 variants may also be used depending on computational resources; however, larger models require higher memory and computation time.

For short peptide sequences (≤ 50 residues), our lightweight models such as PepBERT can be considered as efficient alternatives (https://github.com/dzjxzyd/PepBERT). Other frameworks such as UniDL4BioPep (https://github.com/drkumarnandan/LLM_book_chapter) and pLM4CPPs (https://github.com/drkumarnandan/pLM4CPPs) can also be explored for extended applications.

---

## Citation

If you use this protocol, please cite:

Kumar, N. et al. Predicting Allergenic Proteins and Peptides Using Protein Language Models and Deep Learning. (Submitted).

