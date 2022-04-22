# RFD-seq

![Release](https://img.shields.io/github/v/release/SansamLab/RFD-seq)
![ReleaseDate](https://img.shields.io/github/release-date/SansamLab/RFD-seq)
![Size](https://img.shields.io/github/repo-size/SansamLab/RFD-seq)
![License](https://img.shields.io/github/license/SansamLab/RFD-seq)
![LastCommit](https://img.shields.io/github/last-commit/SansamLab/RFD-seq)
![Downloads](https://img.shields.io/github/downloads/SansamLab/RFD-seq/total)
![OpenIssues](https://img.shields.io/github/issues-raw/SansamLab/RFD-seq)
[![DOI](https://zenodo.org/badge/472901755.svg)](https://zenodo.org/badge/latestdoi/472901755)


Pipeline for calculating replication fork directionality from methods such as OK-seq or GLOE-seq

### 1.  Load slurm and miniconda
Note. The commands to do this will be different on your machine. These commands are specific to an HPC using slurm with these modules installed.

```bash
ml slurm
ml miniconda
```
#### 3A.  FIRST TIME ONLY:  Setup conda environment with snakemake
```bash
# -f is the location of the environment .yml file. 
## The relative path assumes that you are in the root directory of this repository.
# -p is the path where you want to install this environment
conda env create -f workflow/envs/SnakemakeEnv.yml -p /s/sansam-lab/SnakemakeEnv 
```

#### 3B.  Activate conda environment with snakemake
```bash
conda activate /s/sansam-lab/SnakemakeEnv
```


```bash
git clone https://github.com/sansamcl/SnakemakeRepositoryTemplate.git
```

#### 7A. Use conda environments
If conda is to be used for rule-specific environments, you may find it useful to create the environments first. Running 'snakemake' with the '--conda-create-envs-only' option will create the environments without running the pipeline. The '--conda-prefix' option is used to set a directory in which the ‘conda’ and ‘conda-archive’ directories are created. This directory may be changed to a stable or shared location.
```bash
sbatch --mem 32G \
--wrap="\
snakemake \
--cores all \
--use-conda \
--conda-prefix ../condEnvs/ \
--conda-create-envs-only \
--conda-frontend conda"
```

Once the environments are setup, you may execute pipeline with conda environments using the following command:
```bash
sbatch --constraint=westmere \
--wrap="\
snakemake \
-R \
-j 999 \
--use-conda \
--conda-prefix ../condEnvs/ \
--conda-frontend conda \
--latency-wait 100 \
--cluster-config config/cluster_config.yml \
--cluster '\
sbatch \
-A {cluster.account} \
-p {cluster.partition} \
--cpus-per-task {cluster.cpus-per-task}  \
--mem {cluster.mem} \
--output {cluster.output}'"
```

#### 7B. Use environment modules.
Rather than using conda environments, you may prefer to use modules installed on your computing cluster. These modules are defined for each rule in 'workflow/Snakefile'. This must be customized for your environment, and you must modify the Snakefile yourself.

To execute the pipeline with environment modules, enter the following:
```bash
sbatch --constraint=westmere \
--wrap="\
snakemake \
-R \
-j 999 \
--use-envmodules \
--latency-wait 100 \
--cluster-config config/cluster_config.yml \
--cluster '\
sbatch \
-A {cluster.account} \
-p {cluster.partition} \
--cpus-per-task {cluster.cpus-per-task}  \
--mem {cluster.mem} \
--output {cluster.output}'"
```
