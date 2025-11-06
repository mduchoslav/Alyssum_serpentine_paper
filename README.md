Annotation of *Alyssum gmelinii* genome
================
Miloš Duchoslav
2025-02

This repository contains scripts and data for annotation of the newly assembled genome of *Alyssum gmelinii* for the following publication:

> Celestini S., Duchoslav M., Nezamivand-Chegini M., Gerchen J.,
> Šrámková G., Wijfjes R., Krejčová A., Kuzmanović N., Španiel S.,
> Schneeberger K., Yant L., Kolář F. (2025) Genomic basis of adaptation
> to serpentine soil in two Alyssum species shows convergence with
> Arabidopsis across 20 million years of divergence. Annals of Botany:
> mcaf141. doi:
> [10.1093/aob/mcaf141](https://doi.org/10.1093/aob/mcaf141)
>
> Preprint at bioRxiv: <https://doi.org/10.1101/2025.02.27.640498>

## Structural annotation of genome of *Alyssum gmelinii*

### Scripts and documentation

The main file with scripts and other information for annotation:  
[GitHub md file](01_genome_annotation/annotation_Alyssum.md) | [original RMarkdown file](01_genome_annotation/annotation_Alyssum.rmd)

Installation and versions of tools used:  
[GitHub md file](01_genome_annotation/Installation_of_SW.md) | [original RMarkdown file](01_genome_annotation/Installation_of_SW.Rmd)

### Genome and annotation of *Alyssum gmelinii*

Genome with repeats softmasked by RepeatMasker:  
[Alyssum_gmelinii_genome_softmasked.fa.gz](Alyssum_gmelinii_genome_softmasked.fa.gz)

Genome annotation in GFF format:  
[Alyssum_gmelinii_genome_annotation.gff.gz](Alyssum_gmelinii_genome_annotation.gff.gz)

Coding sequences (CDS) extracted by AGAT:  
[Alyssum_gmelinii_genome_annotation_cds.fasta.gz](Alyssum_gmelinii_genome_annotation_cds.fasta.gz)

Protein sequences extracted by AGAT:  
[Alyssum_gmelinii_genome_annotation_proteins.fasta.gz](Alyssum_gmelinii_genome_annotation_proteins.fasta.gz)

## Orthology

To get some functional information about *Alyssum gmelinii* genes and to be able to compare them with genes from other species, I inferred *Arabidopsis thaliana* orthologues (or other kinds of homologues in some cases).

1. I ran [OrthoFinder](https://github.com/davidemms/OrthoFinder) with 18 Brassicaceae species to get the tables of orthologues.
	- Scripts and other information:  
		[GitHub md file](02_orthology/Orthofinder_Brassicaceae_2.md) | [original RMarkdown file](02_orthology/Orthofinder_Brassicaceae_2.Rmd)
	- [Folder with results from OrthoFinder](02_orthology/orthofinder_results) (only parts important for my further analysis)
	- [Folder with statistics from OrthoFinder](02_orthology/orthofinder_stats) visualized by my R script
2. I supplemented the OrthoFinder results with results from BLAST to obtain *Arabidopsis thaliana* homologues for genes not covered by OrthoFinder.
	- Scripts and other information:  
		[GitHub md file](02_orthology/Orthologues_all-A.thaliana.md) | [original RMarkdown file](02_orthology/Orthologues_all-A.thaliana.Rmd)
	- Resulting file:  
		[Alyssum_gmelinii__v__Arabidopsis_thaliana_supplemented.tsv](02_orthology/supplemented_orthologues/Alyssum_gmelinii__v__Arabidopsis_thaliana_supplemented.tsv)

In further analysis, Sonia used the columns `Alyssum_gmelinii` with *Alyssum gmelinii* gene IDs and `single_Arabidopsis_thaliana` with corresponding *Arabidopsis thaliana* orthologues/homologues.

Newer versions of these scripts and the resulting orthology tables and functional annotations for more Brassicaceae species can be found in my repository [Brassicaceae_orthology](https://github.com/mduchoslav/Brassicaceae_orthology).
		
