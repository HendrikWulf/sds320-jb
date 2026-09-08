---
site:
  outline_maxdepth: 2
---

# Visualising embeddings

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Visualising embeddings and using them for clustering and similarity search
</div>
<!-- markdownlint-enable MD033 -->

---

You have a matrix of 36,024 embeddings, each with 768 dimensions. This page makes that matrix explorable: projecting it down to something you can see, discovering natural groupings within it, and searching it for locations similar to one you choose.

---

## 1. Motivation

A 768-dimensional space cannot be plotted directly, and eyeballing raw numbers tells you nothing useful. These three techniques, projection, clustering, and similarity search, are the standard toolkit for making an embedding space genuinely usable, and you will likely reach for at least one of them in any embedding-based project.

---

## 2. Core idea

**{term}`Principal Component Analysis (PCA) <Principal Component Analysis (PCA)>`** projects high-dimensional embeddings down to two dimensions for visualization, preserving as much of the original structure as possible. **{term}`K-means clustering <K-Means Clustering>`** partitions embeddings into a fixed number of groups based on proximity, discovering structure without any labels. **{term}`Cosine similarity <Cosine Similarity>`** measures how alike two embedding vectors are, the basis for similarity search: given one location, finding others that look similar to the foundation model.

---

## 3. Workflow

### A. Visualize structure with PCA

```{code-cell} python
fig = geoai.visualize_embeddings(
    embeddings,
    method="pca",
    figsize=(10, 8),
    s=3,
    alpha=0.4,
    title="PCA of Clay Embeddings (SF Bay Area)",
)
plt.show()
```

Points close together in this plot represent tiles with similar spectral and spatial characteristics; distinct clusters often correspond to different land cover types or geographic regions. The separation you see between groups is a rough visual indicator of how well the foundation model distinguishes different kinds of scenes, though the two PCA axes themselves are mathematical projections, not directly interpretable physical quantities.

### B. Discover groups with K-means

```{code-cell} python
result = geoai.cluster_embeddings(embeddings, n_clusters=8, method="kmeans")
cluster_labels = result["labels"]

print(f"Cluster sizes: {np.bincount(cluster_labels)}")
```

`cluster_embeddings()` partitions the data into `n_clusters` groups by minimizing within-cluster variance, entirely unsupervised. Coloring the PCA plot from step A by cluster assignment shows how these groups relate to the overall structure:

```{code-cell} python
fig = geoai.visualize_embeddings(
    embeddings, labels=cluster_labels, method="pca", figsize=(10, 8), s=5, alpha=0.5,
    title="K-Means Clusters of Clay Embeddings",
)
plt.show()
```

Mapping the same cluster assignments back onto real coordinates is where clustering becomes genuinely informative:

```{code-cell} python
fig, ax = plt.subplots(figsize=(10, 8))
scatter = ax.scatter(coords_x, coords_y, c=cluster_labels, cmap="tab10", s=3, alpha=0.6)
plt.colorbar(scatter, ax=ax, label="Cluster")
ax.set_aspect("equal")
plt.show()
```

Spatial coherence in this map, clusters forming recognizable geographic regions rather than scattering randomly, is a good sign the foundation model's embeddings capture real land-cover patterns, not just noise.

### C. Find similar locations

```{code-cell} python
query_idx = 0
query = embeddings[query_idx]

results = geoai.embedding_similarity(query=query, embeddings=embeddings, metric="cosine", top_k=10)

for rank, (idx, score) in enumerate(zip(results["indices"], results["scores"]), start=1):
    print(f"{rank}. Index {idx}: similarity={score:.4f}")
```

`embedding_similarity()` ranks every other embedding by cosine similarity to your chosen query, with a score of 1.0 meaning identical and lower scores meaning progressively less similar. Plotting the query and its top matches geographically, using the same `coords_x`/`coords_y` extracted two pages ago, lets you see directly whether similar embeddings also cluster spatially, often a sign of shared land cover or environmental conditions.

---

## 4. Python reactivation

`zip(results["indices"], results["scores"])` pairs two parallel lists together for iteration, the same pattern used for coordinated lists throughout this course; wrapping it in `enumerate(..., start=1)` additionally numbers each pair starting from 1 rather than 0, useful here for a human-readable ranked list. `np.bincount(cluster_labels)` counts how many times each cluster ID appears, a quick way to check whether K-means produced reasonably balanced groups or a few dominant clusters with several tiny ones.

---

## 5. Common pitfalls

- **Interpreting PCA axes as meaningful physical quantities.** They are mathematical directions of maximum variance, not something like "greenness" or "urbanization" unless you specifically verify that connection.
- **Choosing `n_clusters` without checking interpretability.** Too few clusters can merge genuinely distinct land-cover types; too many can fragment one type into meaningless subgroups. Try a few values and check whether the geographic pattern makes sense, rather than accepting a default.
- **Treating a high similarity score as a guaranteed match.** A 0.85 cosine similarity is a strong signal worth investigating, not proof that two locations share every relevant characteristic; verify a sample of results visually before relying on them.
- **Forgetting the query point itself appears in its own results.** The top result for any query is typically the query itself, with a similarity of 1.0; the genuinely informative matches start from the second result onward.

---

## 6. Mini task

You run K-means with `n_clusters=8` and notice one cluster contains over half of all patches, while three other clusters each contain fewer than 50 patches. What would you check before concluding this is a problem with your clustering, and what might explain it?

:::{dropdown} Sample solution
:class: note

First, check whether the dominant cluster corresponds to a genuinely common land-cover type across the Bay Area (open water, dense urban fabric, or similar), which could legitimately account for a large share of tiles rather than indicating an error. Second, map the tiny clusters geographically to see whether they correspond to small but real, distinct features (a specific industrial area, a small wetland) rather than noise. If the small clusters look geographically scattered and inconsistent rather than spatially coherent, that would suggest `n_clusters=8` may not be well suited to this data, and trying a different value would be a reasonable next step.
:::

---

## 7. Key takeaways

- PCA projects high-dimensional embeddings into two dimensions for visualization; proximity in the plot reflects similarity, but the axes themselves are not directly interpretable.
- K-means clustering discovers natural groupings without labels; mapping clusters geographically is what reveals whether they correspond to real spatial patterns.
- Cosine similarity search ranks locations by how alike their embeddings are to a query, with 1.0 meaning identical.
- High similarity scores and clean cluster boundaries are strong signals, not guarantees; spot-check results before relying on them for a project conclusion.

### Further reading

- [Similarity Search with Xarray and Embeddings](https://www.geopythontutorials.com/notebooks/xarray_embeddings_similarity_search.html) — an alternative, `xarray`-based approach to the similarity search workflow covered above.
