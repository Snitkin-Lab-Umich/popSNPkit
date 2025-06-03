# popSNPkit
Variant calling pipeline for population data

## Summary
This pipeline perfroms the following steps:
- 
- 
-

The workflow generates all the output in the `prefix` folder set in  `config/config.yaml`. Each workflow steps gets its own individual folder as shown below. This structure provides a general view of how outputs are organized, with each tool or workflow step having its own directory. **_Note that this overview does not capture all possible outputs from each tool; it only highlights the primary directories and _SOME_ of their contents._** 

```
(base) [dhatrib@gl-login1 popSNPkit]$ tree results/2025-06-02_Project_Test_Pipeline_popSNPkit/
results/2025-06-02_Project_Test_Pipeline_popSNPkit/
├── align_reads
│   ├── Rush_KPC_POP_27
│   │   ├── Rush_KPC_POP_27_aln.sam
│   │   ├── Rush_KPC_POP_27_noclips.bam
│   │   ├── Rush_KPC_POP_27_noclip_sort.bam
│   │   └── Rush_KPC_POP_27_noclip_sort.bam.bai
│   ├── Rush_KPC_POP_28
│   │   ├── Rush_KPC_POP_28_aln.sam
│   │   ├── Rush_KPC_POP_28_noclips.bam
│   │   ├── Rush_KPC_POP_28_noclip_sort.bam
│   │   └── Rush_KPC_POP_28_noclip_sort.bam.bai
│   └── Rush_KPC_POP_29
│       ├── Rush_KPC_POP_29_aln.sam
│       ├── Rush_KPC_POP_29_noclips.bam
│       ├── Rush_KPC_POP_29_noclip_sort.bam
│       └── Rush_KPC_POP_29_noclip_sort.bam.bai
└── gatk_varcall
    ├── Rush_KPC_POP_27
    │   ├── Rush_KPC_POP_27.vcf.gz
    │   ├── Rush_KPC_POP_27.vcf.gz.stats
    │   └── Rush_KPC_POP_27.vcf.gz.tbi
    ├── Rush_KPC_POP_28
    │   ├── Rush_KPC_POP_28.vcf.gz
    │   ├── Rush_KPC_POP_28.vcf.gz.stats
    │   └── Rush_KPC_POP_28.vcf.gz.tbi
    └── Rush_KPC_POP_29
        ├── Rush_KPC_POP_29.vcf.gz
        ├── Rush_KPC_POP_29.vcf.gz.stats
        └── Rush_KPC_POP_29.vcf.gz.tbi

```


## Quick start

### Run pipeline on a set of samples.
>Preview the steps in popSNPkit by performing a dryrun of the pipeline.

```

snakemake -s workflow/Snakefile --dryrun

```

> Submit the pipeline as a batch job. Change these SBATCH commands: `--job-name` to a more descriptive name like `run_popsnpkit`, `--mail-user` to your email address, `--time` depending on the number of samples you have (should be more than what you specified in `config/cluster.json`). Feel free to make changes to the other flags if you are comfortable doing so. The sbat script can be found in the current directory—it's called `popSNPkit.sbat`. Don't forget to submit the script to Slurm! `sbatch popSNPkit.sbat`.

```
#!/bin/bash

#SBATCH --job-name=popSNPkit
#SBATCH --mail-user=youremail@umich.edu
#SBATCH --mail-type=BEGIN,END,FAIL,REQUEUE
#SBATCH --export=ALL
#SBATCH --partition=standard
#SBATCH --account=esnitkin1
#SBATCH --nodes=1 --ntasks=1 --cpus-per-task=3 --mem=10g --time=08:15:00

# Load necessary modules
module load Bioinformatics snakemake singularity 

# Run pipeline
snakemake -s workflow/Snakefile --use-envmodules -j 999 --cluster "sbatch -A {cluster.account} -p {cluster.partition} -N {cluster.nodes}  -t {cluster.walltime} -c {cluster.procs} --mem-per-cpu {cluster.pmem} --output=slurm_out/slurm-%j.out" --cluster-config config/cluster.json --configfile config/config.yaml --latency-wait 100 --nolock
```


