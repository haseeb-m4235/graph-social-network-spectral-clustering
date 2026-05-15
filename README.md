# Social Network Community Detection Using Spectral Clustering

A Python-based framework for identifying and analyzing community structures in social networks using spectral clustering on graph-based datasets. This project leverages the eigenvalue decomposition of the graph Laplacian matrix combined with the k-means algorithm to detect optimal clustering boundaries in network graphs.

## Overview

Social networks exhibit inherent community structures where nodes (individuals) are more densely connected within groups than between them. This project implements **spectral clustering** to automatically discover these communities by analyzing the spectral properties of the graph's Laplacian matrix.

### Key Features

- **Spectral Clustering Algorithm**: Automatically determines the optimal number of clusters using the eigenvalue gap heuristic
- **Weighted Adjacency Matrix**: Constructs edge weights using Gaussian kernel similarity on node features
- **Comprehensive Evaluation**: Measures clustering quality using multiple metrics (Silhouette Score, NMI, ARI, Modularity)
- **Graph Visualization**: Interactive visualization of detected communities with color-coded clusters
- **Multi-node Analysis**: Batch processing of multiple central nodes to identify consistent patterns

## Project Structure

```
.
├── SpectralClusteringGraph.py   # Core implementation of spectral clustering
├── visulaize.ipynb              # Jupyter notebook for visualization and analysis
└── dataset/                     # Graph data files (edges, features, ground truth)
    ├── {node_id}.edges         # Edge list for the graph
    ├── {node_id}.feat          # Node feature vectors
    └── {node_id}.circles       # Ground truth community labels
```

## Algorithm Overview

### 1. **Weighted Adjacency Matrix Construction**
   - Computes edge weights using Gaussian kernel similarity between node features
   - Formula: $w_{ij} = \exp(-\sigma \|\mathbf{f}_i - \mathbf{f}_j\|^2)$
   - Weight parameter: `sigma = 0.1` for controlled similarity decay

### 2. **Laplacian Matrix Computation**
   - Constructs the unnormalized Laplacian: $\mathcal{L} = D - W$
   - Where $D$ is the degree matrix and $W$ is the adjacency matrix

### 3. **Eigenvalue Decomposition**
   - Performs eigenvalue decomposition of the Laplacian matrix
   - Analyzes the spectral gap (largest eigenvalue difference)
   - The number of clusters $k$ is determined at the index of the largest gap

### 4. **Clustering**
   - Selects the first $k$ eigenvectors as features
   - Applies K-means clustering on the eigenvector matrix
   - Assigns nodes to clusters based on k-means labels

### 5. **Evaluation**
   - **Silhouette Score**: Measures within-cluster cohesion (-1 to 1 scale)
   - **NMI (Normalized Mutual Information)**: Alignment with ground truth (0 to 1 scale)
   - **ARI (Adjusted Rand Index)**: Similarity to true labels, corrected for chance
   - **Modularity**: Network community strength metric

## Installation

### Prerequisites
- Python 3.8+
- NumPy
- NetworkX
- Matplotlib
- scikit-learn
- SciPy
- pandas
- Jupyter

### Setup

```bash
# Install required packages
pip install numpy networkx matplotlib scikit-learn scipy pandas jupyter

# Clone or download the repository
cd social-network-clustering

# Run the Jupyter notebook
jupyter notebook visulaize.ipynb
```

## Usage

### Basic Usage (Python)

```python
from SpectralClusteringGraph import SpectralClusteringGraph

# Initialize the clustering object
graph = SpectralClusteringGraph(central_node=0, sigma=0.1)

# Load data
graph.read_edges("dataset/0.edges")
graph.read_features("dataset/0.feat")

# Create node mapping
graph.node_mapping = {node: idx for idx, node in enumerate(graph.features.keys())}

# Perform clustering steps
graph.create_weighted_adjacency_matrix()
graph.create_degree_matrix()
graph.create_laplacian_matrix()
graph.analyze_laplacian()
graph.perform_clustering()

# Visualize results
graph.build_and_visualize_graph()

# Evaluate clustering
results = graph.evaluate_clustering("dataset/0.circles")
print(results)
```

### Batch Processing

The included Jupyter notebook (`visulaize.ipynb`) demonstrates batch processing of multiple central nodes:

```python
central_nodes = [0, 107, 348, 414, 686, 698, 1684, 1912, 3437, 3980]

for central_node in central_nodes:
    # ... initialization and clustering steps ...
    evaluation_results = graph.evaluate_clustering(f"dataset/{central_node}.circles")
```

## Results & Insights

### Performance Metrics Across Central Nodes

| Metric | Range | Best Performer |
|--------|-------|----------------|
| Silhouette Score | 0.02 - 0.44 | Node 1912 (0.44) |
| NMI | 0.18 - 0.71 | Node 3980 (0.71) |
| ARI | -0.08 - 0.05 | Near-zero across all nodes |
| Modularity | 0.10 - 0.65 | Node 3437 (0.65) |

### Key Findings

1. **Variable Clustering Quality**: Performance varies significantly across nodes, with some nodes exhibiting stronger community structure than others
2. **Strong Community Separation**: Node 3437 demonstrates well-defined community boundaries with modularity of 0.65
3. **Best Ground Truth Alignment**: Node 3980 shows the strongest correspondence to ground truth labels (NMI: 0.71)
4. **Metric Trade-offs**: Nodes with high silhouette scores tend to have lower NMI, indicating trade-offs between cluster cohesion and ground truth alignment
5. **Sparse Data Challenge**: Low ARI values across all nodes suggest noisy or sparse network data

## Data Format

### Edge Format (.edges)
```
node1 node2
node1 node3
...
```

### Features Format (.feat)
```
node_id feature1 feature2 feature3 ...
node_id feature1 feature2 feature3 ...
...
```

### Ground Truth Format (.circles)
```
circle_id member1 member2 member3 ...
circle_id member1 member2 member3 ...
...
```

## Visualization

The project generates several visualizations:

1. **Eigenvalue Plot**: Shows the spectrum of the Laplacian matrix with annotated gaps
2. **Network Graph**: Spring-layout visualization of the network with nodes colored by cluster
3. **Metrics Comparison**: Bar charts comparing performance across multiple central nodes
4. **Results Table**: Formatted table with styling for quick metric comparison

## Customization

### Adjustable Parameters

```python
# Gaussian kernel bandwidth
graph = SpectralClusteringGraph(central_node=0, sigma=0.1)  # Adjust sigma

# Number of eigenvectors (default: 100)
self.eigenvalues = eigenvalues[sorted_indices][:100]

# K-means random state (for reproducibility)
kmeans = KMeans(n_clusters=k, random_state=42)
```

## Methodology Strengths

✓ **Automatic cluster detection** via eigenvalue gap heuristic  
✓ **Feature-aware clustering** using Gaussian kernel similarity  
✓ **Comprehensive evaluation** with multiple quality metrics  
✓ **Scalable approach** for multiple network samples  
✓ **Interpretable results** with clear visualizations  

## Challenges & Limitations

- Low ARI values indicate difficulty aligning with ground truth in sparse networks
- Trade-offs between different evaluation metrics
- Performance depends heavily on feature quality and network sparsity
- Gaussian kernel parameter (sigma) requires tuning for optimal results
- High computational complexity for very large graphs

## Future Improvements

- Implement adaptive sigma parameter selection
- Explore normalized Laplacian variants
- Add support for directed graphs
- Implement alternative clustering methods (spectral co-clustering, etc.)
- Optimize for large-scale network analysis
- Add community significance testing

## References

- Von Luxburg, U. (2007). "A tutorial on spectral clustering." Statistics and computing, 17(4), 395-416.
- Newman, M. E. (2004). "Finding community structure in networks using the eigenvectors of matrices." Physical review E, 74(3), 036104.
- Ng, A. Y., Jordan, M. I., & Weiss, Y. (2002). "On spectral clustering: Analysis and an algorithm."

## License

This project is open source and available under the MIT License.

## Author

Haseeb Muhammad

---

For questions or contributions, feel free to open an issue or submit a pull request!
