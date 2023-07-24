# plASgraph2 - Classifying Plasmid Contigs From Bacterial Assembly Graphs Using Graph Neural Networks

## Overview

Identification of plasmids and plasmid genes from sequencing data is an important question regarding antimicrobial resistance spread and other One-Health issues. PlASgraph2 is a deep-learning tool that classifies contigs from a short-read assembly as originating either from a plasmid, the chromosome or being ambiguous (i.e. could originate from both, e.g. in the case of a shared repeated contig). 

PlASgraph2 is built on a graph neural network (GNN) and analysis the **assembly graph** (provided in <a href="http://gfa-spec.github.io/GFA-spec/">GFA format</a>) generated by an assembler such as <a href="https://github.com/rrwick/Unicycler">Unicycler</a> or <a href="https://github.com/ncbi/SKESA">SKESA</a>. 

<p align="center">
  <img src="/doc/plASgraph2_architecture.png" alt="drawing" width="600"/>
</p>

This distribution of PlASgraph2 is provided with a model trained on data from the ESKAPEE group of bacterial pathogens (*Enterococcus faecium*, *Staphylococcus aureus*, *Klebsiella pneumoniae*, *Acinetobacter baumannii*, *Pseudomonas aeruginosa*, *Enterobacter spp.*, and *Escherichia coli*). PlASgraph2 is species-agnostic, so the provided trained model can be applied to analyse data from other pathogen species. Alternativly, plASgraph can be trained on a new training dataset (see section **Training** below).

## Installation
PlASgraph2 can be installed from this repository 

~~~
git clone https://github.com/cchauve/plASgraph2.git
~~~

PlASgraph2 is written in Python 3. It has been developed and tested with Python 3.8.10 and the modules listed in the `requirements.txt` file. 
All modules can be installed using pip (https://docs.python.org/3.8/installing/index.html) and we strongly recommand to run plASgraph2 using a dedicated python virtual environment (see https://docs.python.org/3.8/library/venv.html).

The enironment can be created e.g. using the commands below:
```bash
python3 -m venv venv
source venv/bin/activate
pip3 install -r plASgraph2/requirements.txt
```

## Training

Training a plASgraph2 model requires (1) assembly graphs in gzipped GFA format for the training samples and (2) a labeling of the training samples contigs as either *plasmid*, *chromosome*, *ambiguous* (contigs that appear in both a plasmid and the chromosome) or *unlabeled* (typically very short contigs).

The training input consists of two files:
- a *configuration file* in <a href="https://yaml.org/">YAML</a> format, that specifies training parameters
(default file: [model/config_default.yaml](./model/config_default.yaml));
- a *CSV samples file*, with no header line, that contains one line per sample, specifying (1) the path to the gzipped GFA assembly file for the sample, (2) the path for a contig labels CSV file, and (3) a sample name (example: [ESKAPEE training file](https://github.com/fmfi-compbio/plasgraph2-datasets/blob/master/eskapee-train.csv), from the github repo that contains all training data used to train plASgraph2 models, [plasgraph2-datasets](https://github.com/fmfi-compbio/plasgraph2-datasets)).

Files path in the CSV training file are assumed to be relative, with the prefix of the path for each file being provided as a command-line parameter (see example of command-line below). This assumption implies that all GFA and CSV training files are located in the same directory (although they can be located in different subdirectories).

For example, to re-train the ESKAPEE plASgraph2 model, one would run the commands
```
cd model
git clone https://github.com/fmfi-compbio/plasgraph2-datasets.git
python ../src/plASgraph2_train.py ./config_default.yaml plasgraph2-datasets/eskapee-train.csv plasgraph2-datasets/ ./ESKAPEE_model > ./ESKAPEE_model.log 2> ./ESKAPEE_model.err
```

**Remark.** PlASgraph2 has been trained and tested on assembly graphs generated by the assemblers <a href="https://github.com/rrwick/Unicycler">Unicycler</a> and <a href="https://github.com/ncbi/SKESA">SKESA</a>. The file formats are described in
[https://github.com/fmfi-compbio/plasgraph2-datasets](https://github.com/fmfi-compbio/plasgraph2-datasets).

The result is created in the directory `../ESKAPEE_model`, while files `../ESKAPEE_model.log`, `../ESKAPEE_model.err` record the log and possible errors that occured duing training. The model is provided in the file `../ESKAPEE_model/saved_model.pb`.

Additional options `-g` and `-l` allow respectively to generate the GNN as a file in GML format and to generate additional log files.

**QUESTION.** Are-there other technical details we should mention (GPU, GFA format?)?

## Classification

The input for plASgraph2 consists in a trained model and either a single assembly graph from a single bacterial sample in gzipped <a href="http://gfa-spec.github.io/GFA-spec/">GFA (.gfa) format</a> or a CSV file with a list of gzipped GFA files to analyze.

Given a single gzipped GFA file `assembly_graph.gfa.gz`, located in directory `data_dir` and a model located in directory `model_dir`, the contigs of the sample can be classified using the command

```
python ./src/plASgraph2_classify.py gfa assembly_graph.gfa.gz model_dir output.csv
```

The result is written in a file `output.csv` that contains one line per contig, recording its length, plasmid score, chromosome score and final label.

For example, you can run plASgraph2 on one of the GFA files provided in the directory `example`:
```bash
python ../src/plASgraph2_classify.py gfa SAMN15148288_SKESA.gfa.gz ../model/ESKAPEE_model/ SAMN15148288_SKESA_output.csv
```

To classify contigs of several samples at once, the input file is a CSV file `input.csv`, with one line per sample, the first field being the name of the gzippeed assembly graph file, the second is currently not used, and the last field is the name of the sample. 
All assembly graphs files listed in the file are assumed to be located in the same directory `data_dir`. 
The samples contigs can then be classified using the command

```
python ./src/plASgraph2_classify.py set input.csv data_dir model_dir output.csv
```

As in the previous case, `output.csv` is a CSV file containing the results for all contigs of all samples.

The directory `example` contains an example that has been generated by the command

```
python ../src/plASgraph2_classify.py set SAMN15148288_input.csv ./ ../model/ESKAPEE_model/ SAMN15148288_output.csv
```

The file generated by plASgraph2 ([example/SAMN15148288_output.csv](example/SAMN15148288_output.csv)) is a CSV file with five fields:
sample name, contig name, contig length, plasmid and chromosome scores (both numbers in [0,1]) and contig label ('plasmid,chromosome,ambiguous,unlabeled').

**QUESTIONS:** can we have unabeled contigs? are all contigs labeled or is-there a length threshold? is the labeling threshold fixed? GPU needed? what is the `thresholds.py` file about? should-we keep `plASgraph2_visualize_graphs.py`?

## Citation
Janik Sielemann, Katharina Sielemann, Broňa Brejová, Tomas Vinar, Cedric Chauve; "plASgraph2: Using Graph Neural Networks to Detect Plasmid Contigs from an Assembly Graph", submitted, 2023.
