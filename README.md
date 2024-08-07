# Snakemake (and sbatch) Demo for Phoenix Cluster
This repo provides recommendations for running Snakemake workflows on the UCSC Genomics Institute Phoenix Cluster.

## Usage
1. Setup conda env and activate:
```
conda create -n snakemake-demo -c bioconda "snakemake>=8"
conda activate snakemake-demo
```
2. Install SLURM plugin for Snakemake. And matplotlib for silly plotting script.
```
pip install snakemake-executor-plugin-slurm matplotlib
```
3. Run the workflow on SLURM:
```
snakemake --workflow-profile profiles/slurm
```

## SLURM Profile
Snakemake allows command line options to be specified via YAML file called a profile. Read the docs [here](https://snakemake.readthedocs.io/en/stable/executing/cli.html#profiles).

Feel free to copy the SLURM profile from this repo (`profiles/slurm/config.yaml`). Below is an annotated version:

```yaml
executor: slurm  # Specifies SLURM as the job scheduler for running Snakemake workflows
use-conda: True  # Enables the use of conda environments for reproducibility and dependency management
jobs: 50  # Allows up to 50 jobs to be submitted at any given time to manage cluster load and resource allocation efficiently
latency-wait: 30  # Waits 30 seconds for output files to account for potential latency issues, ensuring all files are properly written before the next step
retries: 2  # Retries failed jobs up to 2 times to handle transient errors without manual intervention
max-jobs-per-second: 5  # Limits the rate of job submissions to 5 per second to prevent overloading the scheduler
max-status-checks-per-second: 5  # Limits the rate of status checks to 5 per second to avoid excessive querying of the scheduler

# These resources will be applied to all rules. Can be overridden on a per-rule basis below.
default-resources:
  mem_mb: attempt * 16000  # Allocates 16GB of memory per attempt, scaling with the number of attempts to ensure sufficient resources are available
  slurm_partition: "short"  # Assigns jobs to the "short" partition, typically used for jobs with shorter runtimes
  runtime: 60  # Sets the maximum runtime to 60 minutes for each job to ensure jobs complete within an acceptable time frame. This can be adjusted based on the specific requirements of the workflow

set-resources:
    bwa_mem: #overwrite default resources for bwa mem rule.
        mem_mb: 32000
        slurm_partition: "medium"
        runtime: 120
```

The data in this repo is completely fake and meaningless.  