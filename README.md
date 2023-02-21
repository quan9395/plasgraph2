# plASgraph - Classifying Plasmid Contigs From Bacterial Assembly Graphs Using Graph Neural Networks

## Overview

Identification of plasmids and plasmid genes from sequencing data is an important question regarding antimicrobial resistance spread and other One-Health issues. PlASgraph2 is a deep-learning tool that classifies contigs from a short-read assembly as originating either from a plasmid, the chromosome or being ambiguous (i.e. could originate from both, e.g. in the case of a shared repeated contig). 

PlASgraph2 is built on a graph neural network (GNN) and analysis the **assembly graph** (provided in <a href="http://gfa-spec.github.io/GFA-spec/">GFA format</a>) generated by an assembler such as <a href="https://github.com/rrwick/Unicycler">Unicycler</a> or <a href="https://github.com/ncbi/SKESA">SKESA</a>. 

<p align="center">
  <img src="/doc/plASgraph2_architecture.png" alt="drawing" width="600"/>
</p>

This distribution of PlASgraph2 is provided with a model trained on data from the ESKAPEE group of bacterial pathogens (*Enterococcus faecium*, *Staphylococcus aureus*, *Klebsiella pneumoniae*, *Acinetobacter baumannii*, *Pseudomonas aeruginosa*, *Enterobacter spp.*, and *Escherichia coli*). PlASgraph2 is species-agnostic, so the provided trained model can be applied to analyse data from other pathogen species.

## Installation

~~~
git clone https://github.com/cchauve/plASgraph2.git
~~~

PlASgraph2 is written in python 3 and requires the following python modules, and has been tested on the listed versions of these modules. 
  - NetworkX  2.6.3+
  - Pandas  1.3.5+
  - NumPy  1.21.5+
  - Scikit-learn  0.23.1+
  - Scipy 1.8.0+
  - Biopython  1.79+
  - Matplotlib  3.5.1+
  - TensorFlow  2.8.0+
  - Spektral  1.0.8+
All modules can be installed using pip.

## Training

### Input

### Command

### Output

## Classification

### Input 
The input for plASgraph consists in a trained model file and either a single assembly graph from a single bacterial isolate in <a href="http://gfa-spec.github.io/GFA-spec/">GFA (.gfa) format</a> or a CSV file with a list of GFA files to analyze.

PlASgraph has been trained and tested on assembly graphs generated by the assemblers <a href="https://github.com/rrwick/Unicycler">Unicycler</a> and <a href="https://github.com/ncbi/SKESA">SKESA</a>.

### Command
PlASgraph is a command-line program. 


### Output
PlASgraph predicts each contig as either plasmidic, chromosomal or ambiguous. The output file contains four columns: I) the name of the contig, II) the plasmid score, III) the chromosome score and IV) the predicted label.

### Example

~~~
python3 plASgraph.py -i example_data/c_freundii_assembly.gfa -o output.csv --draw_graph
~~~

## Citation
Janik Sielemann, Katharina Sielemann, Broňa Brejová, Tomas Vinar, Cedric Chauve; "plASgraph: Using Graph Neural Networks to Detect Plasmid Contigs from an Assembly Graph", in preparation, 2023.
