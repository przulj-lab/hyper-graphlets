<div align="center">

<img src="images/header02.jpg" width="100%">

# Higher Order Molecular Organisation as a Source of Biological Function

**Thomas Gaudelet, Noël Malod-Dognin and Nataša Pržulj**

Corresponding author: Prof. N. Pržulj, natasa.przulj@mbzuai.ac.ae

<img src="images/hypergraphlets.png" width="400">

</div>

## Downloads

- [Dataset: biological hypernetworks](https://github.com/przulj-lab/hyper-graphlets/releases/download/v1.0/hypernetworks.zip)
- [Result: functional predictions](https://github.com/przulj-lab/hyper-graphlets/releases/download/v1.0/all_predictions.zip)
- [Hypergraphlets counter](https://github.com/przulj-lab/hyper-graphlets/releases/download/v1.0/hypergraphlets_counter.zip)

## Hypergraphlets counter

To compile, set the path to your local Boost installation in the `Makefile`, then run:
```bash
make
```

To run:
```bash
./run_hypercounter -g <hyperedge list> -o <output name> -t <threads> -b <blocks>
```

- `-t`: number of threads (OpenMP), default 1
- `-b`: number of vertex blocks distributed across the threads, default 50. Vertices are assigned to blocks at random, so densely connected vertices don't end up in the same block.

**Input:** tab-separated hyperedge list. Each row starts with the hyperedge index (0 to m-1), followed by the vertices it contains (indexed 0 to n-1).

**Output:** `.svml` file with one row per vertex. The first and last numbers are the vertex index; in between are the orbit counts as `orbit:count`.

## Citation

T. Gaudelet, N. Malod-Dognin and N. Pržulj. Higher-order molecular organization as a source of biological function. *Bioinformatics* 34(17), i944–i953 (2018). https://doi.org/10.1093/bioinformatics/bty570
