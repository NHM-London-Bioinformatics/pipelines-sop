# aDNA data analysis with eager

We have extensively tested the [nf-core eager](https://nf-co.re/eager) pipeline and validated its efficacy with published NHM aDNA data. Here we give a brief overview of getting started with the pipeline and some commands for different barcode sequences. The [eager documentation](https://nf-co.re/eager/2.4.6/usage) is very good and you should review it alongside this guide.

Eager has a range of analysis pathways. Make sure you check out what these are at the eager documentation before starting, so you know exactly what you want to do. This documentation is based on the "Eukaryotic Nuclear Genome" pathway, as this is the most comprehensive and encompasses most steps of the other pathways, and hence it will largely apply for any pathway. The most important distinction is that the "Eukayotic Nuclear Genome" and "Microbial or Organelle Genome" pathways focus on samples of a single species, for which the "reference" should be a genome for this species, while the "Metagenomic Profiling" pathway focuses on multiple species that might be present in the input sample(s).

Each run of eager is based on a single reference species or host species - if you have samples of multiple species or multiple hosts, you should run eager separately for each. However, each run encompass multiple individual samples of the target species/host, as well as multiple libries sequenced and prepared in different ways of each sample. Eager will merge data from different sequence runs, libraries and samples at appropriate points in the pipeline, running different analyses at different, appropriate levels of merging. You can see an illustration of this [here](https://nf-co.re/eager/usage#tsv-input-method). Thus we recommend running eager once with many samples, rather than separately with different samples.

## Getting started

First, check our [top tips for running a pipeline](README.md#top-tips-for-running-a-pipeline)

### References

Second, decide on your reference genome - either the species of interest, or the host species. 

If your reference genome is one of the [iGenomes](https://emea.support.illumina.com/sequencing/sequencing_software/igenome.html), check to see if it's already available directly in Eager by checking [this file](https://github.com/nf-core/eager/blob/2.4.6/conf/igenomes.config). The supported genomes are those that are listed as 'titles' of each section, followed by a list of file paths: e.g. the first two are 'GRCh37' and 'GRCh38'. If your reference genome is in iGenomes and supported by Eager, great, you don't need to do anything else. 

If not, you simply need to make sure you have a fasta of the reference genome. If it doesn't come with an index, you should index it using `samtools faidx <reference.fasta>`, replacing `<reference.fasta>` with the name of the file. Once you have both the fasta and the index, ensure that the index has the same name as the uncompressed reference, with `.fai` at the end. For example if your reference is called `ref.fasta`, your index should be called `ref.fasta.fai`. If your reference is compressed, e.g. `ref.fasta.gz`, your index should still be called `ref.fasta.fai`. You should also make sure the modification date of the index is after that of the reference, you can do this by running `touch <ref.fasta.fai>`, replacing <ref.fasta.fai> with the name of the index file.

### Sample information

Finally, ensure you have the following information for each library

1. The colour chemistry - this is just a matter of knowing what sequencer was used for each library, '2' for NextSeq or NovaSeq, '4' for HiSeq or MiSeq
2. The sequence type - 'PE' or 'SE'
3. The strandedness of the DNA sequenced - 'double' or 'single'
4. The UDG treatment - 'full', 'part' or 'none'

If you have raw demultiplexed reads files, you should know the paths for these fastq files. Alternatively, if you've already mapped against your reference, you can use BAM files as your input, and you should know the paths to these files. 

Once you have all of the above information, you need to prepare a samplesheet. See the [eager samplesheet documentation](https://nf-co.re/eager/usage#tsv-input-method) for the columns that are required in your table, and don't forget to use absolute file paths and save it as a tab-separated values file. Note that if you have a single library, or a set of libraries all prepared the same way, you could use the the direct input method, but we don't recommend it - a samplesheet keeps a better record and simplifies the input command.

If you're doing Metagenomic Profiling, you will also need a Kraken or Malt database for classification, and know the path to this. This is supplied as its own argument.

## Commands

Before you run eager, remember to open a `screen` and activate the nextflow conda environment:
```
conda activate nextflow
```
Below are starter commands for running eager on a set of libraries for each of the three pathways. Before you run any of these commands, you should check over the parameters, reviewing the [eager documentation](https://nf-co.re/eager/parameters). Several parameters will require you to fill in project-specific information - some of these are obvious, more information is given for some below. Other parameters have default information already filled in, you should consider whether these are the best options for your project. 

#### Configuration

We have written a configuration file to optimise the running of eager on the NHM HPC, included in the commands below. For more information about what this configuration file does, see the [configs documentation](configs.md).

#### Reference
If your reference is one of the iGenomes and it's supported by Eager, simply input its as the value for `--genome` in place of `<iGenomeID>` in the command. Otherwise, you should remove the `--genome` line and instead add the following lines:
```
--fasta </path/to/ref.fasta.gz> \
--fasta_index </path/to/ref.fasta.fai> \
```
As always, replace the value in `<>` with the path, removing the `<>`.

#### Mapping

The commands below follow the eager suggestions for mapping, namely they use the default BWA aln method for the Eukaryotic Nuclear Genome and Metagenomic Profiling pathways, and CircularMapper for Microbial or Organell Genome pathway, but there are several others available. You can change the mapper used, as well as configure the mapper: check out [this section](https://nf-co.re/eager/parameters#read-mapping-to-reference-genome) of the eager documentation.

#### Genotyping

The commands below use the default GATK UnifiedGenotyper ('ug') option for genotyping, but others are available. You can change the genotyper used, as well as configure the genotyper: check out [this section](https://nf-co.re/eager/parameters#genotyping) of the eager documentation.

### Eukaryotic Nuclear Genome

```
nextflow run nf-core/eager -r <version> \
  -c /mbl/share/workspaces/groups/nextflow/config/NHM.config \
  -c /mbl/share/workspaces/groups/nextflow/configs/eager.config \
  -profile singularity \
  -w work \
  --outdir out \
  --input </path/to/samplesheet.tsv> \
  --genome <iGenomeID> \
  --mapper bwaaln \
  --run_genotyping \
  --genotyping_tool 'ug' \
  --run_mtnucratio \
  --run_bcftools_stats \
  --email <you@nhm.ac.uk>
```

Note: if you're running on humans, you may also want to add [`--run_sexdeterrmine`](https://nf-co.re/eager/parameters#human-sex-determination) and [`--run_nuclear_contamination`](https://nf-co.re/eager/parameters#human-sex-determination), both options only available with human sequencing. 

### Microbial or Organelle Genome
Note that we haven't specifically tested eager with this sort of data, but this should be a good starting point.

```
nextflow run nf-core/eager -r <version> \
  -c /mbl/share/workspaces/groups/nextflow/config/NHM.config \
  -c /mbl/share/workspaces/groups/nextflow/configs/eager.config \
  -profile singularity \
  -w work \
  --outdir out \
  --input </path/to/samplesheet.tsv> \
  --genome <iGenomeID> \
  --mapper bwaaln \
  --run_genotyping \
  --genotyping_tool 'ug' \
  --run_bcftools_stats \
  --run_multivcfanalyzer \
  --email <you@nhm.ac.uk>
```
Note that MALT is also supported for taxonomic classification, see [here](https://nf-co.re/eager/parameters#metagenomic-screening)

### Metagenomic Profiling
Note that we haven't specifically tested eager with this sort of data, but this should be a good starting point.

```
nextflow run nf-core/eager -r <version> \
  -c /mbl/share/workspaces/groups/nextflow/config/NHM.config \
  -c /mbl/share/workspaces/groups/nextflow/configs/eager.config \
  -profile singularity \
  -w work \
  --outdir out \
  --input </path/to/samplesheet.tsv> \
  --genome <iGenomeID> \
  --mapper circularmapper \
  --metagenomic_tool 'kraken' \
  --database </path/to/classifer_database> \
  --email <you@nhm.ac.uk>
```
Note that MALT is also supported for taxonomic classification, see [here](https://nf-co.re/eager/parameters#metagenomic-screening)


## Outputs

For complete documentation of all the outputs, see [here](https://nf-co.re/eager/output). We strongly recommend you check out the MultiQC report (`multiqc/multiqc_report.html`) first - it has detailed summaries of the results of mapping and downstream analyses and is a really handy one-stop overview of the run. Otherwise, the output directory is pretty intuitive and you can find the outputs from each tool in the relevant named folder.
