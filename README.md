# Hypergraphlet Counter

A parallelized tool for counting hypergraphlets in hypergraphs using OpenMP.

## Overview

The Hypergraphlet Counter efficiently computes hypergraphlet orbit counts for vertices in hypergraphs. It uses parallel processing to handle large-scale hypergraph datasets.

## Resources

### Datasets
- **[Biological Hypernetworks](http://www0.cs.ucl.ac.uk/staff/natasa/hypergraphlets/hypernetworks.zip)** - Biological hypergraph datasets for testing and analysis

### Results
- **[Functional Predictions](http://www0.cs.ucl.ac.uk/staff/natasa/hypergraphlets/all_predictions.zip)** - Example functional predictions using hypergraphlet analysis

*Note: If download links don't work when clicked, copy and paste the URLs directly into your browser's address bar.*

## Features

- **Parallel Processing** - Uses OpenMP for efficient multi-threaded computation
- **Scalable** - Handles large hypergraphs through block-based processing
- **Optimized Partitioning** - Randomized vertex partitioning prevents thread imbalance
- **Standard Output Format** - Produces .svml format for easy integration with analysis pipelines

## Installation

### Prerequisites

- C++ compiler with OpenMP support (gcc/g++ recommended)
- Boost library
- Make

### Compilation

1. **Configure Boost path** in the `Makefile`:
   ```makefile
   # Update this line with your local Boost installation path
   BOOST_PATH = /path/to/your/boost
   ```

2. **Compile**:
   ```bash
   make
   ```

This creates the `run_hypercounter` executable.

## Usage

```bash
./run_hypercounter -g <hyperedge_file> -o <output_file> [OPTIONS]
```

### Required Parameters

- `-g` : Path to input hyperedge list file
- `-o` : Path and name for output file

### Optional Parameters

- `-t` : Number of threads (default: 1)
- `-b` : Number of vertex groups/blocks for parallel processing (default: 50)

### Example

```bash
./run_hypercounter -g data/hyperedge.list -o results/counts -t 8 -b 100
```

This processes `data/hyperedge.list` using 8 threads with 100 vertex blocks.

## Input Format

The hyperedge list file must be **tab-separated** with the following structure:

```
<hyperedge_index>    <vertex_1>    <vertex_2>    ...    <vertex_k>
```

**Requirements:**
- Each row starts with a hyperedge index (0 to m-1, where m = total hyperedges)
- Followed by all vertices in that hyperedge
- Vertices must be indexed from 0 to n-1 (n = total vertices)
- Tab-separated values

**Example:**
```
0    0    1    2    3
1    1    2    4
2    0    3    5    6
3    2    4    5
```

## Output Format

The output is in **.svml format** with hypergraphlet orbit counts:

```
<vertex_index> <orbit_1>:<count_1> <orbit_2>:<count_2> ... <vertex_index>
```

**Structure:**
- One row per vertex
- First number: vertex index
- Middle: orbit counts in format `orbit:count`
- Last number: vertex index (repeated)

**Example:**
```
0 0:5 1:3 2:7 0
1 0:4 1:6 2:2 1
2 0:8 1:1 2:5 2
```

## Parallelization Strategy

### Thread Management

The tool uses OpenMP for parallelization:
- Set number of threads with `-t` flag
- Recommended: Use number of available CPU cores

### Block-Based Processing

Vertices are partitioned into blocks for efficient parallel processing:

1. **Vertex Partitioning**: Dataset divided into blocks (specified by `-b`)
2. **Random Assignment**: Vertices randomly assigned to blocks
3. **Load Balancing**: Prevents densely connected vertices in same block
4. **Thread Processing**: Each thread processes one block at a time

**Why Randomization?**
- Avoids thread imbalance from consecutive dense vertices
- Ensures even workload distribution
- Improves parallel efficiency

### Performance Tuning

```bash
# For small hypergraphs (< 1000 vertices)
./run_hypercounter -g input.list -o output -t 4 -b 20

# For medium hypergraphs (1000-10000 vertices)
./run_hypercounter -g input.list -o output -t 8 -b 50

# For large hypergraphs (> 10000 vertices)
./run_hypercounter -g input.list -o output -t 16 -b 100
```

## Example Workflow

```bash
# 1. Compile the code
make

# 2. Prepare your hyperedge list
cat data/my_hypergraph.list
# 0    0    1    2
# 1    1    3    4    5
# 2    0    2    3

# 3. Run with 4 threads and 50 blocks
./run_hypercounter -g data/my_hypergraph.list -o results/counts -t 4 -b 50

# 4. View results
cat results/counts.svml
```

## Performance Considerations

### Thread Count (`-t`)
- **Recommended**: Number of CPU cores
- **Too high**: Overhead from thread management
- **Too low**: Underutilized hardware

### Block Count (`-b`)
- **Higher values**: Better load balancing but more overhead
- **Lower values**: Less overhead but potential imbalance
- **Rule of thumb**: 5-10x the number of threads

### Memory Usage
- Scales with hypergraph size and number of threads
- Large hypergraphs may require significant RAM

## Troubleshooting

### Compilation Errors

**Boost library not found:**
```bash
# Ubuntu/Debian
sudo apt-get install libboost-all-dev

# macOS
brew install boost

# Then update Makefile with correct path
```

**OpenMP not available:**
```bash
# Install gcc with OpenMP support
# Ubuntu/Debian
sudo apt-get install gcc g++

# macOS
brew install gcc
```

### Runtime Issues

**Slow performance:**
- Increase number of threads: `-t 8`
- Increase number of blocks: `-b 100`
- Check CPU utilization

**Out of memory:**
- Reduce number of threads
- Process hypergraph in smaller batches

**Incorrect results:**
- Verify input format (tab-separated, correct indexing)
- Check vertex indices start from 0
- Ensure hyperedge indices are sequential

## Input Validation

Before running, verify your input:

```bash
# Check tab separation
cat -A hyperedge.list

# Count vertices and hyperedges
awk '{print $1}' hyperedge.list | sort -n | tail -1  # max hyperedge index
awk '{for(i=2;i<=NF;i++) print $i}' hyperedge.list | sort -nu | tail -1  # max vertex index
```

## Applications

- **Biological Networks** - Protein complexes, gene regulatory networks
- **Social Networks** - Group interactions, collaborations
- **Chemical Structures** - Molecular complexes
- **Recommender Systems** - Multi-way user-item relationships

## Contact

For questions or issues, please open an issue on GitHub or contact the repository maintainers.