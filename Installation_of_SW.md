SW installation for Annotation of *Alyssum gmelinii* genome
================
Milos Duchoslav
2025-02-24

- [FastQC](#fastqc)
- [MultiQC](#multiqc)
- [Hisat2](#hisat2)
- [Samtools](#samtools)
- [Braker](#braker)
- [Running IGV on Metacentrum](#running-igv-on-metacentrum)
- [AGAT](#agat)
- [Busco](#busco)
- [Bedtools](#bedtools)

## FastQC

### FastQC installation

``` sh
# FastQC installation
cd SW
mkdir fastqc
cd fastqc
wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.12.1.zip
unzip fastqc_v0.12.1.zip

# check
/storage/brno12-cerit/home/duchmil/SW/fastqc/FastQC/fastqc --help
```

### FastQC testing

``` sh
# add module with Java
module add openjdk/

# test on 2 samples
/storage/brno12-cerit/home/duchmil/SW/fastqc/FastQC/fastqc -o /storage/brno12-cerit/home/duchmil/lyrata_vcf/lyrata_results/report/fastqc_outside_Snakemake/fastqc -f fastq /storage/brno12-cerit/home/duchmil/lyrata_vcf/lyrata_results/fastq_trimmed/AHM-01dl_1_trimmed_U1.fastq.gz

/storage/brno12-cerit/home/duchmil/SW/fastqc/FastQC/fastqc -o /storage/brno12-cerit/home/duchmil/lyrata_vcf/lyrata_results/report/fastqc_outside_Snakemake/fastqc -f fastq /storage/brno12-cerit/home/duchmil/lyrata_vcf/lyrata_results/fastq_trimmed/AHM-01dl_1_trimmed_U2.fastq.gz
```

## MultiQC

### MultiQC installation

Installed MultiQC version: 1.21

``` sh
# MultiQC installation
# Using locally installed Mamba (installed for running Snakemake)
source /storage/brno2/home/duchmil/SW/mambaforge/bin/activate
conda create --name Multiqc python=3.11
conda activate Multiqc
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
conda config --set channel_priority strict
conda install multiqc
```

### MultiQC testing

``` sh
# Running MultiQC
# activation
source /storage/brno2/home/duchmil/SW/mambaforge/bin/activate Multiqc

# check version
multiqc --version


cd /storage/brno12-cerit/home/duchmil/lyrata_vcf/lyrata_results/report/fastqc_outside_Snakemake/fastqc

# test on 2 samples
multiqc --filename test_report --outdir /storage/brno12-cerit/home/duchmil/lyrata_vcf/lyrata_results/report/fastqc_outside_Snakemake/multiqc /storage/brno12-cerit/home/duchmil/lyrata_vcf/lyrata_results/report/fastqc_outside_Snakemake/fastqc
```

## Hisat2

I used Hisat2 installed as module on Metacentrum.

Version: 2.1.0

[HISAT2 manual](http://daehwankimlab.github.io/hisat2/manual/)

``` sh
# Load hisat2
module load hisat2 # Note: The hisat2 does not work on CentOS on Metacentrum (zuphux frontend).

hisat2 --version
# /cvmfs/software.metacentrum.cz/spack1/software/hisat2/linux-debian10-x86_64/2.1.0-intel-itifoz/bin/hisat2-align-s # version 2.1.0
# 64-bit
# Built on metasw11.grid.cesnet.cz
# Thu Oct 27 11:46:07 CEST 2022
# Compiler: gcc version 8.3.0 (Debian 8.3.0-6)
# Options: -O3 -m64 -msse2 -funroll-loops -g3 -DPOPCNT_CAPABILITY
# Sizeof {int, long, long long, void*, size_t, off_t}: {4, 8, 8, 8, 8, 8}
```

## Samtools

I used Samtools installed as module on Metacentrum.

Version: 1.14

[Samtools GitHub](https://github.com/samtools/samtools) [Samtools
manual](http://www.htslib.org/doc/samtools.html)

``` sh
# Load samtools
module load samtools

samtools --version
# samtools 1.14
# Using htslib 1.14
```

## Braker

### Installation of Braker

Braker version used: 3.0.8

[Instructions](https://github.com/Gaius-Augustus/BRAKER?tab=readme-ov-file#container)
for Braker installation as Singularity container.

``` sh
cd /storage/brno12-cerit/home/duchmil/annotations/alyssum_2024_Mahnaz_assembly
mkdir braker_sw
cd braker_sw

singularity build braker3.sif docker://teambraker/braker3:latest

# version
singularity exec -B $PWD:$PWD braker3.sif braker.pl --version
```

### Running tests of Braker

``` sh
# Run the tests
# (see https://github.com/Gaius-Augustus/BRAKER#container)
singularity exec -B $PWD:$PWD braker3.sif cp /opt/BRAKER/example/singularity-tests/test1.sh .
singularity exec -B $PWD:$PWD braker3.sif cp /opt/BRAKER/example/singularity-tests/test2.sh .
singularity exec -B $PWD:$PWD braker3.sif cp /opt/BRAKER/example/singularity-tests/test3.sh .

# Interactive job for tests
qsub -I -l select=1:ncpus=2:mem=16gb -l walltime=2:00:00

cd /storage/brno12-cerit/home/duchmil/annotations/alyssum_2024_Mahnaz_assembly/braker_sw

export BRAKER_SIF=/storage/brno12-cerit/home/duchmil/annotations/alyssum_2024_Mahnaz_assembly/braker_sw/braker3.sif

bash test1.sh
bash test2.sh
bash test3.sh

# In the end I ran the 3rd test as Metacentrum job:
qsub test3_for_Metacentrum.sh
# It ran 11 minutes.
```

#### Script for running test 3 of Braker as batch job

``` sh
#!/bin/bash

#PBS -N BRAKER_test3
#PBS -l select=1:ncpus=8:mem=32gb:scratch_local=20gb
#PBS -l walltime=1:00:00 
#PBS -m ae

cd /storage/brno12-cerit/home/duchmil/annotations/alyssum_2024_Mahnaz_assembly/braker_sw
export BRAKER_SIF=/storage/brno12-cerit/home/duchmil/annotations/alyssum_2024_Mahnaz_assembly/braker_sw/braker3.sif

## original test3 script:

# Author: Katharina J. hoff
# Contact: katharina.hoff@uni-greifswald.de
# Date: Jan 12th 2023

# Copy this script into the folder where you want to execute it, e.g.:
# singularity exec -B $PWD:$PWD braker3.sif cp /opt/BRAKER/example/singularity-tests/test3.sh .
# Then run "bash test3.sh".

# Check whether braker3.sif is available

if [[ -z "${BRAKER_SIF}" ]]; then
    echo ""
    echo "Variable BRAKER_SIF is undefined."
    echo "First, build the sif-file with \"singularity build braker3.sif docker://teambraker/braker3:latest\""
    echo ""
    echo "After building, export the BRAKER_SIF environment variable on the host as follows:"
    echo ""
    echo "export BRAKER_SIF=\$PWD/braker3.sif"
    echo ""
    echo "You will have to modify the export statement if braker3.sif does not reside in \$PWD."
    echo ""
    exit 1
fi

# Check whether singularity exists

if ! command -v singularity &> /dev/null
then
    echo "Singularity could not be found."
    echo "On some HPC systems you can load it with \"module load singularity\"."
    echo "If that fails, please install singularity."
    echo "Possibly you misunderstood how to run this script. Before running it, please copy it to the directory where you want to execute it by e.g.:"
    echo "singularity exec -B \$PWD:\$PWD braker3.sif cp /opt/BRAKER/example/singularity-tests/test3.sh ."
    echo "Then execute on the host with \"bash test3.sh\"".
    exit 1
fi

# remove output directory if it already exists

wd=test3

if [ -d $wd ]; then
    rm -r $wd
fi

singularity exec -B ${PWD}:${PWD} ${BRAKER_SIF} braker.pl --genome=/opt/BRAKER/example/genome.fa --prot_seq=/opt/BRAKER/example/proteins.fa --bam=/opt/BRAKER/example/RNAseq.bam --workingdir=${wd} \
        --threads 8 --gm_max_intergenic 10000 --skipOptimize --busco_lineage eukaryota_odb10 &> test3.log

        # Important: the options --gm_max_intergenic 10000 --skipOptimize should never be applied to a real life run!!!                                   
        # They were only introduced to speed up the test. Please delete them from the script if you use it for real data analysis. 



# Resources: The job ran 11 minutes.
```

## Running IGV on Metacentrum

``` sh
# Download IGV
wget https://data.broadinstitute.org/igv/projects/downloads/2.16/IGV_2.16.2.zip
# Unzip IGV
unzip IGV_2.16.2.zip
# Start interactive job
qsub -I -l select=1:ncpus=2:mem=32gb:scratch_local=40gb -l walltime=5:00:00
# load and start GUI
module add gui
gui start
# Open the URL in browser.
# Open terminal in GUI.
# Run these commands:
# load Java
module load openjdk
# run IGV
/storage/brno12-cerit/home/duchmil/SW/igv/IGV_2.16.2/igv.sh
```

## AGAT

AGAT-v1.4.0

``` sh
cd ~/SW
mkdir agat
cd agat
singularity pull docker://quay.io/biocontainers/agat:1.4.0--pl5321hdfd78af_0

# run the container
singularity run /storage/brno12-cerit/home/duchmil/SW/agat/agat_1.4.0--pl5321hdfd78af_0.sif
```

## Busco

BUSCO 5.7.1

### Installation

``` sh
source /storage/brno2/home/duchmil/SW/mambaforge/bin/activate

conda create -n busco_5_7_1 -c conda-forge -c bioconda busco=5.7.1
conda activate busco_5_7_1
```

### Testing

``` sh
cd /storage/brno12-cerit/home/duchmil/annotations/alyssum_2024_Mahnaz_assembly/
mkdir busco_results
cd busco_results

# Busco activation
source /storage/brno2/home/duchmil/SW/mambaforge/bin/activate busco_5_7_1

busco --version
# BUSCO 5.7.1

busco --list-datasets

# main command
busco --in ../results_braker_alyssum_01/braker.aa --mode proteins --lineage_dataset brassicales_odb10 --cpu 16
```

## Bedtools

[Bedtools manual](https://bedtools.readthedocs.io)

I used Bedtools installed as module on Metacentrum.

bedtools v2.30.0

``` sh
cd /storage/brno12-cerit/home/duchmil/annotations/alyssum_2024_Mahnaz_assembly
mkdir intersects
cd intersects

module avail bedtools
module avail bedtools2/
module load bedtools2/2.30.0-gcc-10.2.1-5acjqve
# Loading bedtools2/2.30.0-gcc-10.2.1-5acjqve
#  Loading requirement: bzip2/1.0.8-gcc-10.2.1-ydytecx xz/5.2.5-gcc-10.2.1-fteagxc zlib/1.2.12-gcc-10.2.1-7qmmk4c

bedtools --version
# bedtools v2.30.0
```
