---
site:
  outline_maxdepth: 2
---

# Lightweight classification

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Using embeddings for lightweight classification and change analysis
</div>
<!-- markdownlint-enable MD033 -->

---

You explored the embedding space without any labels on the previous page. This page adds a small number of labels back in, training a lightweight classifier on top of the same embeddings, then uses embedding comparison as a lightweight route to change detection.

---

## 1. Motivation

This page demonstrates the core economic advantage of embeddings directly: because Clay already extracted rich features during pre-training, a simple classifier trained on fewer than a hundred labeled points can achieve reasonable accuracy, without training anything resembling the segmentation or detection models from earlier lessons.

---

## 2. Core idea

The workflow is "embed first, classify later": match your labeled points to their corresponding embedding patches with a {term}`spatial join <Spatial Join>`, train a lightweight classifier directly on the matched embeddings, and separately, compare embeddings across locations or time as a lightweight signal for {term}`change detection <Change Detection>`.

---

## 3. Workflow

### A. Labels to embedding patches

```{code-cell} python
if labels_gdf.crs != embeddings_gdf.crs:
    labels_gdf = labels_gdf.to_crs(embeddings_gdf.crs)

joined = gpd.sjoin(labels_gdf, embeddings_gdf, how="inner", predicate="within")
print(f"Matched {len(joined)} labeled points to embedding patches")
```

Checking and matching CRS before the join is not optional; a mismatch here would silently produce wrong or empty matches rather than a clear error. `predicate="within"` matches each labeled point to whichever embedding patch's geometry contains it; the matched count can be slightly larger than the original label count if a point happens to fall inside more than one overlapping patch.

### B. Extract labeled embeddings

```{code-cell} python
labeled_embeddings = np.stack(
    [embeddings_gdf.iloc[idx]["embeddings"] for idx in joined["index_right"]]
)
class_labels = joined["class"].values

from sklearn.model_selection import train_test_split

X_train, X_val, y_train, y_val = train_test_split(
    labeled_embeddings, class_labels, test_size=0.3, random_state=42, stratify=class_labels,
)
```

`stratify=class_labels` preserves the original class balance in both the training and validation splits, important here since the two classes (baseball fields and marinas) are not evenly represented to begin with.

### C. Train and compare classifiers

```{code-cell} python
label_names = ["Baseball Field", "Marina"]

result = geoai.train_embedding_classifier(
    train_embeddings=X_train, train_labels=y_train,
    val_embeddings=X_val, val_labels=y_val,
    method="knn", n_neighbors=5, label_names=label_names,
)

print(f"Train accuracy: {result['train_accuracy']:.2%}")
print(f"Val accuracy: {result['val_accuracy']:.2%}")
```

`method="knn"` trains a {term}`k-nearest neighbors <k-Nearest Neighbors (k-NN)>` classifier, which predicts a new point's class based on the majority class among its `n_neighbors` closest embeddings. Comparing this against two alternatives is straightforward, since only the `method` argument changes:

```{code-cell} python
methods = ["knn", "random_forest", "logistic_regression"]
results_summary = []

for method in methods:
    res = geoai.train_embedding_classifier(
        train_embeddings=X_train, train_labels=y_train,
        val_embeddings=X_val, val_labels=y_val,
        method=method, label_names=label_names, verbose=False,
    )
    results_summary.append({
        "Method": method,
        "Train Acc": f"{res['train_accuracy']:.2%}",
        "Val Acc": f"{res['val_accuracy']:.2%}",
    })

pd.DataFrame(results_summary)
```

{term}`Random Forest` builds many decision trees and combines their votes; {term}`logistic regression <Logistic Regression>` fits a linear decision boundary in embedding space. None of these three methods needs more than a few seconds to train, precisely because the heavy feature-extraction work already happened during Clay's pre-training, not here.

### D. Visualize class separation

```{code-cell} python
fig = geoai.visualize_embeddings(
    labeled_embeddings, labels=class_labels, label_names=label_names,
    method="pca", figsize=(8, 8), s=30, alpha=0.8,
    title="PCA of Labeled Embeddings (Baseball vs Marina)",
)
plt.show()
```

If the two classes form visually distinct groups in this PCA projection, that is a strong sign the foundation model's embeddings already encode the distinction your classifier is learning to draw, which is exactly why so few labels are enough here.

### E. Embeddings for change detection

```{code-cell} python
similarity = geoai.compare_embeddings(emb_a, emb_b, metric="cosine")

fig, ax = plt.subplots(figsize=(10, 4))
ax.hist(similarity, bins=50, edgecolor="black", alpha=0.7)
ax.axvline(similarity.mean(), color="red", linestyle="--", label=f"Mean: {similarity.mean():.3f}")
ax.legend()
plt.show()
```

`compare_embeddings()` computes similarity between two matched sets of embeddings; in a genuine temporal comparison (the same locations at two different dates, unlike this demonstration comparing different spatial patches), low-similarity outliers would flag locations that likely changed between the two dates, a lightweight alternative to ChangeStar's per-pixel change map from L10 when a coarser, patch-level change signal is enough.

```{admonition} A signal, not a verified answer
:class: important
Low embedding similarity flags candidate change locations; it does not by itself confirm what changed or rule out a false alarm from misalignment or seasonal difference, the same comparability concerns from L10. Treat it as a fast way to prioritize where to look more closely, not a finished change detection product.
```

---

## 4. Python reactivation

`stratify=class_labels` is a keyword argument to `train_test_split()` that ensures each split's class proportions match the original data's, rather than potentially skewing by chance, especially important with the kind of small, imbalanced label set used here. The list-of-dictionaries-to-DataFrame pattern in step C (`results_summary.append({...})`, then `pd.DataFrame(results_summary)`) is a common way to collect results from a loop into a clean, displayable table.

---

## 5. Common pitfalls

- **Skipping the CRS check before a spatial join.** A mismatch produces silently wrong or empty results rather than a clear error; always verify or explicitly reproject first.
- **Over-trusting a validation accuracy from a very small validation set.** With only a few dozen points, a single accuracy number can swing considerably; treat it as an estimate, not a precise measurement.
- **Treating low embedding similarity as proof of change.** As emphasized above, it is a signal worth investigating, not a validated change detection result on its own.
- **Comparing classifier methods on only one train/validation split.** Given how small this example's dataset is, results can vary somewhat with a different random split; a more rigorous project comparison would check this across multiple splits.

---

## 6. Mini task

Your k-NN classifier achieves 75% validation accuracy on 32 held-out points, 24 baseball fields and 8 marinas. A classmate says this is clearly a strong result. What would you check before agreeing?

:::{dropdown} Sample solution
:class: note

With classes this imbalanced (24 versus 8), a baseline that always predicts "Baseball Field" would already achieve 75% accuracy without learning anything about marinas at all, so overall accuracy alone does not confirm the classifier is doing well on both classes. The next step would be checking per-class metrics (precision and recall for each class specifically), which would reveal whether the classifier is actually distinguishing marinas or simply defaulting to the majority class most of the time.
:::

---

## 7. Key takeaways

- Matching labels to embeddings via a spatial join, then training a lightweight classifier, is the core "embed first, classify later" pattern.
- k-NN, Random Forest, and logistic regression all train in seconds on embedding features, since the foundation model already did the expensive feature extraction.
- Comparing embeddings across locations or time offers a lightweight, patch-level alternative to per-pixel change detection.
- Low similarity or a plausible accuracy number is a signal worth investigating, not a substitute for closer verification, especially with small or imbalanced datasets.
