# TAI-figures
Codes for reproducing figures of the TAI-seq article, organized in R markdown files and html documents (compiled from markdown).

## System requirements ##
At least 128 GB memory, and 200 GB disk space. The source data is very large, and cannot be uploaded

## Software dependencies ##
Dependencies  | Version
------------- | -------------
nextflow | 25.10.2
openjdk | 17.0.8
Perl | 5.32.1
python | 3.10.12
cutadapt | 4.5
bzip2 | 1.0.8
bedtools | 2.31.0
bowtie2 | 2.5.1
pigz | 2.6
samtools | 1.18
star | 2.7.11a
subread | 2.0.6
mixcr | 4.7.0
numpy | 1.26.0
pandas | 2.1.1
scipy | 1.11.3
matplotlib | 3.8.0
python-levenshtein | 0.22.0

## Installation ##
Installation should finish within 10 minutes (Epyc 7542).  
(1) Setup the environment using conda:  
```
conda create -n taiseq -c bioconda python=3.10.12 openjdk=17.0.8 nextflow=25.10.2 cutadapt=4.5 bzip2=1.0.8 bedtools=2.31.0 bowtie2=2.5.1 pigz=2.6 samtools=1.18 star=2.7.11a subread=2.0.6 numpy=1.26.0 pandas=2.1.1 scipy=1.11.3 matplotlib=3.8.0 python-levenshtein=0.22.0
```    
Then activate the conda environment:  
```
conda activate taiseq
```
(2) Clone the repository with `git clone`, and execute
```
cd TAI-seq
```
(3) To run the TCR or BCR pipeline, you also need to install mixcr 4.7.0. Please install mixcr according to its instructions.  
(4) You also need to prepare bowtie2 (for ATAC) and star (for RNA) index files for your genome. Please refer to their manuals to generate indexed genome files.

## Usage ##
### RNA ###
(1) Prepare the `samplesheet.csv`. The csv file __must__ contain 4 columns with defined column names:  
`sample`: Name of the sequenced library. For example, `demo-1`. It will be the prefix of the output. Note: Different fastqs with same sample name will be merged before processing.  
`fastq_1`: Path to read 1.  
`fastq_2`: Path to read 2.  
`pattern`: During the TAI-seq experiments, samples are loaded in a column-wise manner into plate 1. This column defines how many columns a sample should use. For example, `3 3 6` indicates 3 samples are loaded to plate 1, with sample1 in column 1-3 (round1 barcode 1-24), sample2 in column 4-6 (round1 barcode 25-48) and sample3 in column 7-12 (round1 barcode 49-96). The numbers must be separated by space, and the sum of numbers must be 12. Can be left empty.  
`annotation`: The names of each sample separated by space, and they will be added to the prefix of the output (after the library name). For example, `Sp Ln Blood` will make the final output prefixed by `demo-1_Sp, demo-1_Ln and demo-1_Blood`. Can be left empty.  
Refer to `samplesheet_rna.csv` for example.

(2) Run the pipeline. Change directory to nfTAI-rna with `cd nfTAI-rna`, and execute:
```
nextflow run nfTAI-rna.nf \
  -output-dir your_output_dir \
  --gtf your_path_to_gtf \
  --genomeDir your_path_to_star_index \
  --input_csv samplesheet.csv \
  --n6 false \
  --minumis 50 \
  -with-report nfTAI-rna_report.html \
  -with-timeline nfTAI-rna_timeline.html
```
`-output-dir`: Path to the output directory.  
`--gtf`: Path to the gtf annotation file. Must be the same one used when building star index.  
`--genomeDir`: Path to the star index.  
`--input_csv`: Path to samplesheet.csv as described in step (1).  
`--n6`: Whether random hexamers are used in RT. Default is false. Valid choice: true, false.  
`--minumis`: Minimum umis detected for a barcode to be considered as a valid barcode. Barcodes with less umis will be removed from the pipeline. Default value is 1, but we strongly recommand setting this value to 50 to reduce output size.  

#### Expected output ####
Go to `-output-dir`. The pipeline will create folders named according to the `sample` column in you csv file. Each folder contain 5 subfolders:  
`fastqs`: Merged, barcoded and adaptor-trimmed fastqs.  
`bams`: Aligned and filtered bam files.  
`tsvs`: UMI-collapsed tsv files.  
`qc`: Barcode rank plots.  
`gex`: Gene-count matrix. <strong>Decompresse the \*_tar.gz file, and load it into Seurat with `Seurat::Read10X`.</strong>  


### ATAC ###
(1) Prepare the `samplesheet.csv`. The csv file __must__ contain 4 columns with defined column names:  
`sample`: Name of the sequenced library. For example, `demo-1`. It will be the prefix of the output. Note: Different fastqs with same sample name will be merged before processing.  
`fastq_1`: Path to read 1.  
`fastq_2`: Path to read 2.  
`pattern`: During the TAI-seq experiments, samples are loaded in a column-wise manner into plate 1. This column defines how many columns a sample should use. For example, `3 3 6` indicates 3 samples are loaded to plate 1, with sample1 in column 1-3 (round1 barcode 1-24), sample2 in column 4-6 (round1 barcode 25-48) and sample3 in column 7-12 (round1 barcode 49-96). The numbers must be separated by space, and the sum of numbers must be 12. Can be left empty.  
`annotation`: The names of each sample separated by space, and they will be added to the prefix of the output (after the library name). For example, `Sp Ln Blood` will make the final output prefixed by `demo-1_Sp, demo-1_Ln and demo-1_Blood`. Can be left empty.  
Refer to `samplesheet_atac.csv` for example.

(2) Run the pipeline. Change directory to nfTAI-atac with `cd nfTAI-atac`, and execute:
```
nextflow run nfTAI-atac.nf \
  -output-dir your_output_dir \
  --genomeDir your_path_to_bowtie2_index \
  --input_csv samplesheet.csv \
  --minfrags 50 \
  -with-report nfTAI-atac_report.html \
  -with-timeline nfTAI-atac_timeline.html
```
`-output-dir`: Path to the output directory.  
`--genomeDir`: Path to the bowtie2 index.  
`--input_csv`: Path to samplesheet.csv as described in step (1).  
`--minfrags`: Minimum fragments detected for a barcode to be considered as a valid barcode. Barcodes with less fragments will be removed from the pipeline. Default value is 1, but we strongly recommand setting this value to 50 to reduce output size.  

#### Expected output ####
Go to `-output-dir`. The pipeline will create folders named according to the `sample` column in you csv file. Each folder contain 4 subfolders:  
`fastqs`: Merged, barcoded and adaptor-trimmed fastqs.  
`bams`: Aligned and filtered bam files.  
`qc`: Barcode rank plots.  
`fragments`: Output fragment tsv files. <strong>The \*_ArchR.tsv.gz file can be loaded into ArchR.</strong>  

### TCR, full-length library ###
(1) Prepare the `samplesheet.csv`. The csv file __must__ contain 4 columns with defined column names:  
`sample`: Name of the sequenced library. For example, `demo-1`. It will be the prefix of the output. Note: Different fastqs with same sample name will be merged before processing.  
`fastq_1`: Path to read 1.  
`fastq_2`: Path to read 2.  
`pattern`: During the TAI-seq experiments, samples are loaded in a column-wise manner into plate 1. This column defines how many columns a sample should use. For example, `3 3 6` indicates 3 samples are loaded to plate 1, with sample1 in column 1-3 (round1 barcode 1-24), sample2 in column 4-6 (round1 barcode 25-48) and sample3 in column 7-12 (round1 barcode 49-96). The numbers must be separated by space, and the sum of numbers must be 12. Can be left empty.  
`annotation`: The names of each sample separated by space, and they will be added to the prefix of the output (after the library name). For example, `Sp Ln Blood` will make the final output prefixed by `demo-1_Sp, demo-1_Ln and demo-1_Blood`. Can be left empty.  
Refer to `samplesheet_circ.csv` for example.

(2) Run the pipeline. Change directory to nfTAI-circ_mixcr47 with `cd nfTAI-circ_mixcr47`, and execute:
```
nextflow run nfTAI-circ_mixcr47.nf \
  -output-dir your_output_dir \
  --species mixcr_species \
  --input_csv samplesheet.csv \
  --fl true \
  --umi true \
  --noumi true \
  --mixcr path_to_mixcr \
  -with-report nfTAI-circ_report.html \
  -with-timeline nfTAI-circ_timeline.html
```
`-output-dir`: Path to the output directory.  
`--species`: The species parameter used by mixcr. `hsa` for human, and `mmu` for mouse.  
`--input_csv`: Path to samplesheet.csv as described in step (1).  
`--fl`: Whether to assemble full-length contigs. Default is true. Valid choice: true, false.  
`--umi`: Run pipeline that uses the `generic-ht-single-cell-fragmented-with-umi` preset of mixcr. Default is true. Valid choice: true, false.  
`--noumi`: Run pipeline that uses the `generic-ht-single-cell-fragmented` preset of mixcr. Default is true. Valid choice: true, false.  
`--mixcr`: Path to mixcr. Can be acquired by `which mixcr`.  

#### Expected output ####
Go to `-output-dir`. The pipeline will create folders named according to the `sample` column in you csv file. Each folder contain 4 subfolders:  
`fastqs`: Merged, barcoded and adaptor-trimmed fastqs.  
`mixcr`: mixcr outputs (.vdjca, .clna and .clns files) during pipeline running. 
`qc`: Barcode rank plots.  
`clones`: Output fragment tsv files. <strong>The \*_mixcr47_clones_noumi.tsv (if using --noumi true) and \*_mixcr47_clones_umi.tsv (if using --umi true) are mixcr-exported clonotypes.</strong>  

### TCR, V gene primer amplified library ###
(1) Prepare the `samplesheet.csv`. The csv file __must__ contain 4 columns with defined column names:  
`sample`: Name of the sequenced library. For example, `demo-1`. It will be the prefix of the output. Note: Different fastqs with same sample name will be merged before processing.  
`fastq_1`: Path to read 1.  
`fastq_2`: Path to read 2.  
`pattern`: During the TAI-seq experiments, samples are loaded in a column-wise manner into plate 1. This column defines how many columns a sample should use. For example, `3 3 6` indicates 3 samples are loaded to plate 1, with sample1 in column 1-3 (round1 barcode 1-24), sample2 in column 4-6 (round1 barcode 25-48) and sample3 in column 7-12 (round1 barcode 49-96). The numbers must be separated by space, and the sum of numbers must be 12. Can be left empty.  
`annotation`: The names of each sample separated by space, and they will be added to the prefix of the output (after the library name). For example, `Sp Ln Blood` will make the final output prefixed by `demo-1_Sp, demo-1_Ln and demo-1_Blood`. Can be left empty.  
Refer to `samplesheet_vmix.csv` for example.

(2) Run the pipeline. Change directory to nfTAI-vmix_mixcr47 with `cd nfTAI-vmix_mixcr47`, and execute:
```
nextflow run nfTAI-vmix_mixcr47.nf \
  -output-dir your_output_dir \
  --species mixcr_species \
  --input_csv samplesheet.csv \
  --umi true \
  --noumi true \
  --mixcr path_to_mixcr \
  -with-report nfTAI-vmix_report.html \
  -with-timeline nfTAI-vmix_timeline.html
```
`-output-dir`: Path to the output directory.  
`--species`: The species parameter used by mixcr. `hsa` for human, and `mmu` for mouse.  
`--input_csv`: Path to samplesheet.csv as described in step (1).  
`--umi`: Run pipeline that uses the `generic-ht-single-cell-fragmented-with-umi` preset of mixcr. Default is true. Valid choice: true, false.  
`--noumi`: Run pipeline that uses the `generic-ht-single-cell-fragmented` preset of mixcr. Default is true. Valid choice: true, false.  
`--mixcr`: Path to mixcr. Can be acquired by `which mixcr`.  

#### Expected output ####
Go to `-output-dir`. The pipeline will create folders named according to the `sample` column in you csv file. Each folder contain 4 subfolders:  
`fastqs`: Merged, barcoded and adaptor-trimmed fastqs.  
`mixcr`: mixcr outputs (.vdjca, .clna and .clns files) during pipeline running. 
`qc`: Barcode rank plots.  
`clones`: Output fragment tsv files. <strong>The \*_mixcr47_clones_noumi.tsv (if using --noumi true) and \*_mixcr47_clones_umi.tsv (if using --umi true) are mixcr-exported clonotypes.</strong>  

## Run demo ##
Demos of ATAC and RNA should finish within 5 minutes, and demos of TCR should finish within 10 minutes.  
Each demo contains 3 sequenced libraries: `demo-1`, `demo-2` and `demo-22`. `demo-2` and `demo-22` are two batches of sequencing of the same sample `demo-2`. `demo-1` contains only 1 subsample, whereas `demo-2` contains 2 subsamples (1-48 on plate 1 for subsample:`Ln`, and 49-96 on plate 1 for subsample:`Sp`). In the demo, you will see `demo-2` and `demo-22` are merged into single fastqs, and the output of `demo-2` is splitted to `demo-2_Ln` and `demo-2_Sp`.  
The demo uses mm10 genome as references. Prepare bowtie2 and star index accordingly.

### RNA ###
Change directory to nfTAI-rna with 
```
cd nfTAI-rna
```
and execute:
```
nextflow run nfTAI-rna.nf \
  -output-dir your_output_dir \
  --gtf your_path_to_gtf \
  --genomeDir your_path_to_mm10_star_index \
  --input_csv samplesheet_rna.csv \
  --minumis 1 \
  -with-report nfTAI-rna_report.html \
  -with-timeline nfTAI-rna_timeline.html
```
`-output-dir`: Path to your output directory.  
`--gtf`: Path to the gtf annotation file. Must be the same one used when building star index.  
`--genomeDir`: Path to the mm10 star index.  

### ATAC ###
Change directory to nfTAI-atac with 
```
cd nfTAI-atac
```
and execute:
```
nextflow run nfTAI-atac.nf \
  -output-dir your_output_dir \
  --genomeDir your_path_to_mm10_bowtie2_index \
  --input_csv samplesheet_atac.csv \
  --minfrags 1 \
  -with-report nfTAI-atac_report.html \
  -with-timeline nfTAI-atac_timeline.html
```
`-output-dir`: Path to the output directory.  
`--genomeDir`: Path to the mm10 bowtie2 index.  

### TCR, full-length library ###
Change directory to nfTAI-circ_mixcr47 with 
```
cd nfTAI-circ_mixcr47
```
and execute:
```
nextflow run nfTAI-circ_mixcr47.nf \
  -output-dir your_output_dir \
  --species mmu \
  --input_csv samplesheet_circ.csv \
  --mixcr path_to_mixcr \
  -with-report nfTAI-circ_report.html \
  -with-timeline nfTAI-circ_timeline.html
```
`-output-dir`: Path to the output directory.  
`--mixcr`: Path to mixcr. Can be acquired by `which mixcr`.  

### TCR, V gene primer amplified library ###
Change directory to nfTAI-vmix_mixcr47 with 
```
cd nfTAI-vmix_mixcr47
```
and execute:
```
nextflow run nfTAI-vmix_mixcr47.nf \
  -output-dir your_output_dir \
  --species mmu \
  --input_csv samplesheet_vmix.csv \
  --mixcr path_to_mixcr \
  -with-report nfTAI-vmix_report.html \
  -with-timeline nfTAI-vmix_timeline.html
```
`-output-dir`: Path to the output directory.  
`--mixcr`: Path to mixcr. Can be acquired by `which mixcr`.  







