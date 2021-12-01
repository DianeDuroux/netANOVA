# netANOVA
Unsupervised statistical procedure to identify groups of networks.

## networkData.RData
**Description**

30 simulated networks: 10 networks with random structure, 10 scale-free networks, 10 hub networks.

**Usage**
```
load(networkData.RData)
```

**Format**

List of 30 adjacency matrices and their group membership (random, scale-free, hub).

**References**

> Haoming Jiang, Xinyu Fei, Han Liu, Kathryn Roeder, John Lafferty, Larry Wasserman, Xingguo Li and Tuo Zhao (2021). huge: High-Dimensional Undirected Graph Estimation. R package version 1.3.5. https://CRAN.R-project.org/package=huge.

**Example**
```
#Upload the simulated networks
load("networkData.RData")

#Select the 30 adjacency matrices
G=data[[1]]

#Plot a random network, a scale-free network and a hub network
plot(graph_from_adjacency_matrix(G[[1]]), vertex.label= NA, edge.arrow.size=0.02,vertex.size = 3, xlab = "Random network")
plot(graph_from_adjacency_matrix(G[[11]]), vertex.label= NA, edge.arrow.size=0.02,vertex.size = 3, xlab = "Scale free network")
plot(graph_from_adjacency_matrix(G[[21]]), vertex.label= NA, edge.arrow.size=0.02,vertex.size = 3, xlab = "Hub network")

#Select the group memberships
membership=data[[2]]

#Print an extract of the group memberships
head(membership)
```

## Initialization
**Description**

Initialization computes pairwise similarities and distances to a set of networks, and returns the distance and similarity matrices.

**Usage**
```
initialization(G, meth)
```

**Arguments**

- `G`:		A list of adjacency matrices.
- `Meth`: 		Type of distance method from “edd”, “gdd”, “wsd”, “hamming”, “rbf”,“shortestPathKernel”, "randomWalkKernel", "WLkernel", "graphletKernel", “Gaussian”.

**Details**

The function initialization computes the similarity and distance matrix from a set of networks. For kernel measures, the distance is obtained via the following formula $d=\sqrt{(s_ii-2s_ij+s_jj)}$  with $s_ij$ the similarity measure between network i and network j obtained from a kernel. Otherwise, s=1/(1+d).

**Value**

A list containing a matrix of the vectorized networks if all graphs have the same size, the similarity matrix and the distance matrix.

**References**
> Sugiyama, M., Ghisu, M. E., Llinares-López, F., & Borgwardt, K. (2018). graphkernels: R and Python packages for graph comparison. Bioinformatics, 34(3), 530-532.

> Kisung You (2020). NetworkDistance: Distance Measures for Networks. R package version 0.3.3. https://CRAN.R-project.org/package=NetworkDistance.

**Example**
```
#Upload the simulated networks
load("networkData.RData")
G=data[[1]]

#Compute the distance matrix
init=initialization(G, meth=”edd”)
```

## netANOVA
### Description

Applies an unsupervised hierarchical algorithm to identify latent classes of similar networks.

### Usage
```
netANOVA(Dist, t=NULL, method_clust=”complete”, MT=”tree”, p_threshold=0.05, permutations=99, perturbation=0.2, seed=2021)
```

### Arguments

- `Dist`:		A distance matrix, for example derived from the function initialization.
- `t`:		the threshold indicating the minimum size of a group of network to be statistically tested for difference with another group.
- `method_clust`:	method to compute the distance between each cluster in the hierarchical clustering: “complete” (default) or average.
- `MT`:		method to correct for multiple testing. Default “tree”, i.e. $p_adjusted=p\times\frac{N_j-1}{N-1}$, where N_j is the number of networks clustered at node j of the dendrogram.
- `p_threshold`:	maximum p-value for 2 groups of networks to be considered as significantly different, default 0.05.
- `permutation`:	number of permutations to derive the p-value, default 99.
- `perturbation`:	percentage of values permuted in the distance matrix, default 20%.
- `Seed`:		seed for replicability.

### Details

To determine the optimal number of clusters, we recursively test for distances between two groups of networks, progressing from the root node to the end nodes of the clustering dendrogram. The test itself is based on computing pairwise distances between graphs and is inspired by distance-wise ANOVA algorithms. Permutations are used to assess significance.

### Value

A list of two elements: table indicating the membership of each network, and details on the differences between pairs of groups along the hierarchical clustering, i.e statistics, ids of networks in group 1, id of networks in group 2, associated p-value.

### References
> Anderson, M. J. (2001). A new method for non‐parametric multivariate analysis of variance. Austral ecology, 26(1), 32-46.

> Kimes, P. K., Liu, Y., Neil Hayes, D., & Marron, J. S. (2017). Statistical significance for hierarchical clustering. Biometrics, 73(3), 811-821.

### Example
```
#Upload the simulated networks
load("networkData.RData")
G=data[[1]]

#Compute the matrix distance
init=initialization(G, meth=”edd”)

#netANOVA clustering algorithm to create group of networks that are statistically different
output=netANOVA(Dist=init[[3]],  t=5)
```

