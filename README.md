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

Training a plASgraph2 model requires (1) assembled graphs in Ggzipped FA format for the training samples and (2) labllig of the training samples contigs as either *plasmid*, *chromosome*, *ambiguous* (contigs that appear in both a plasmid and the chromosome) or *unlabeled* (typically very short contigs).

The training input consists of two files:
- a **configuration file** in <a href="https://yaml.org/">YAML</a> format, that specifies training parameters (default: `model/condig_default.yaml`);
- a **CSV samples file** that contains one line per sample, specifying (1) the path to the GFA assembly file for the sample, (2) the path for a contig labels CSV file, nd (3) a sample name.

Files path in the CSV training file are assumed to be relative, with the prefix of the path for each file being provided as a command-line parameters. This assumption implies that all GFA and CSV training files are located in the same directory (although they can be located in subdirectories).

For example, the first training sample is defined by the line `boostrom/abau-SAMEA12292436/short.gfa.gz,boostrom/abau-SAMEA12292436/short.gfa.csv,boostrom_abau-SAMEA12292436-u`:
- the gzipped GFA assembly graph file is the file `short.gfa.gz` in the subdirectory `boostrom/abau-SAMEA12292436/` of the data directory;
- the contig labels file is the file `short.gfa.csv` in the subdirectory `boostrom/abau-SAMEA12292436/` of the data directory; **TODO: describe format of CSV contig labels file**
- the sample name is `boostrom_abau-SAMEA12292436-u`.

Given a samples file `training_samples.csv` and a configuration file `training_config.yaml`, assuming that the directory containing data is `training_data_dir`, a plASgraph2 model can be trained by the command

```
python3 ./src/train.py training_config.yaml training_samples.csv training_data_dir output_model_dir > model_train.log 2> model_train.err
```

The result is created in the directory `output_model_dir`, while files `model_train.log`, `model_train.err` record the log and possible errors that occured duing training.

In the directory `output_model_dir`, the model is provided as the GML file `training_samples.gml`.

## Classification

### Input 
The input for plASgraph consists in a trained model file and either a single assembly graph from a single bacterial isolate in <a href="http://gfa-spec.github.io/GFA-spec/">GFA (.gfa) format</a> or a CSV file with a list of GFA files to analyze.

PlASgraph has been trained and tested on assembly graphs generated by the assemblers <a href="https://github.com/rrwick/Unicycler">Unicycler</a> and <a href="https://github.com/ncbi/SKESA">SKESA</a>.

### Command
PlASgraph is a command-line program. 


### Output
PlASgraph predicts each contig as either plasmidic, chromosomal or ambiguous. The output file contains four columns: I) the name of the contig, II) the plasmid score, III) the chromosome score and IV) the predicted label.

### Example


## Citation
Janik Sielemann, Katharina Sielemann, Broňa Brejová, Tomas Vinar, Cedric Chauve; "plASgraph: Using Graph Neural Networks to Detect Plasmid Contigs from an Assembly Graph", in preparation, 2023.
