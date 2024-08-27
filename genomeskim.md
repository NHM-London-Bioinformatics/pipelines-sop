# Genome skim analysis with genomeskim

We have developed the [nf-core genomeskim](https://nf-co.re/genomeskim) pipeline. Here we give a brief overview of getting started with the pipeline and some commands for different sorts of skims. This page is based on the [genomeskim documentation](https://nf-co.re/genomeskim/usage), which also goes into more detail about the parameters - you should review it alongside this guide.

## Getting started

First, check our [top tips for running a pipeline](README.md#top-tips-for-running-a-pipeline)

The following is a checklist of information you should know before running ampliseq:

1. The taxon (ideally, the [NCBI taxid](https://www.ncbi.nlm.nih.gov/taxonomy)) that you've skimmed and the organelle you're targeting
2. The location of your demultiplexed reads files. Each pair of files should correspond to a single sample or replicate.

Once you have these, you need to prepare a samplesheet. See the [genomeskim samplesheet documentation](https://nf-co.re/genomeskim/usage#samplesheet-input) for the columns that are required in your table, and don't forget to use absolute file paths and save it as a comma separated values file.

## Commands

Before you run ampliseq, remember to open a `screen` and activate the nextflow conda environment:
```
conda activate nextflow
```
Below are example commands for running genomeskim for three different situations. Before you run any of these commands, you should check over the parameters, reviewing the [genomeskim documentation](https://nf-co.re/genomeskim/parameters). Several parameters will require you to fill in project-specific information - some of these are obvious, more information is given for some below. Other parameters have default information already filled in, you should consider whether these are the best options for your project. 

#### `-w` / `--outdir`
We recommend running ampliseq in its own working directory, so by default these arguments are set to generic directory names. Change as desired

#### Run time (`-profile`)
These commands use the `day` queue on the HPC and can generally process a MiSeq's worth of genome skims without running out of time. If your runs are failing with out of time errors, you could use `genericcpuweek` instead of `genericcpuday`. Remember, each individual process is run on the `day` queue - adding up all of these individual processes, the overall run time of the pipeline may be much longer.

#### Taxonomic assignment using BLAST (`--blastdbpath`, `--taxdumppath`)
Genomeskim estimates the taxonomy of contigs using BLAST to aid in validation. The first two example commands below point to local copies of NCBI nt and taxdump that are available on the HPC. You should check to see if there are more up-to-date versions than the dates given. This step is slow; you might want to consider instead creating your own smaller BLAST database to speed things up, [using `makeblastdb`](https://www.ncbi.nlm.nih.gov/books/NBK569841/) - ensure you include a taxon map so that the taxonomy can be retrieved from taxdump. This database should include any taxa you might expect to see in your sample - including contaminants. See the third example for how to specify this in a genomeskim run.

### Organelle assembly (`--getorganelle_wordsize`, `--getorganell_kmers`)

genomeskim always performs organelle assembly using the [GetOrganelle](https://github.com/Kinggerm/GetOrganelle) toolkit; the only parameterisation of this process that is *required* is to specify the type of organelle expected using [`--getorganelle_genometype`](LINK). However, it is recommended to review the guidance for running GetOrganelle and configure this to your dataset, in particular the `--getorganelle_wordsize` and `--getorganelle_kmers`.

#### Reference datasets (`--organellrefs`, `--gofetch_taxon`, `--gofetch_target`, `--getorganelle_ref_action`)

GetOrganelle uses two reference datasets to aid in assembly: complete reference organelle sequences ("seeds") and sequences of protein coding genes expected to be present ("labels"). By default, genomeskim will use GetOrganelle's own reference database for each of the genome types it supports. However, these databases are very generic and improved assemblies can be achieved by using references from close relatives of the target. genomeskim provides two additional or alternative ways of supplying references for GetOrganelle.

**File**:  A custom set of references can be supplied as a file to `--organellerefs`. If this file is a genbank flat file, the complete sequences will be used for "seed" references and any protein coding gene sequences will be used as "label" references,

**Fetch**: A taxon name or NCBI taxid can be supplied to `--gofetch_taxon` and organelle type to `--gofetch_target`, in which case the pipeline will use the GoFetch script to retrieve a set of the taxonomically closest available sequences from GenBank or RefSeq. For more details on how GoFetch works, see [its github](https://github.com/o-william-white/go_fetch)

The pipeline can combine any of the GetOrganelle default, File, or Fetched references into each of the "seeds" and "labels" required by GetOrganelle using the `--getorganelle_ref_action` argument.

#### Annotation (`--mitos_geneticcode`, `--mitos_refdbid`, `--skip_annotation`)

genomeskim performs annotation of any contigs; currently, only mitochondrial sequences are supported using MITOS. You must specify the genetic code and the database to use for annotation. This step can be skipped using `--skip_annotation`, which currently should be used for non-mitochondrial organelles.


### 16S

```
nextflow run nf-core/ampliseq -r <version> \
  -c /mbl/share/workspaces/groups/nextflow/config/NHM.config \
  -profile genericcpuhour,singularity \
  -w work \
  --outdir out \
  --input </path/to/samplesheet.tsv> \
  --FW_primer <FORSEQ> \
  --RV_primer <REVSEQ> \
  --cutadapt_min_overlap 3 \
  --trunclenf <FTRUNC> \
  --trunclenr <RTRUNC> \
  --max_ee 1 \
  --min_frequency 2 \
  --dada_ref_taxonomy "silva=138" \
  --cut_dada_ref_taxonomy \
  --exclude_taxa "mitochondria,chloroplast" \
  --skip_diversity_indices --skip_ancom --skip_barplot --skip_alpha_rarefaction \
  --email <you@nhm.ac.uk>
```

Notes:
* Add `--filter_ssu "bac"` if you're only interested in bacteria, or `--filter_ssu "bac,arc"` for bacteria and archaea. See [above](#taxonomic-and-location-filtering)

### 18S

```
nextflow run nf-core/ampliseq -r <version> \
  -c /mbl/share/workspaces/groups/nextflow/config/NHM.config \
  -profile genericcpuhour,singularity \
  -w work \
  --outdir out \
  --input </path/to/samplesheet.tsv> \
  --FW_primer <FORSEQ> \
  --RV_primer <REVSEQ> \
  --cutadapt_min_overlap 3 \
  --trunclenf <FTRUNC> \
  --trunclenr <RTRUNC> \
  --max_ee 1 \
  --min_frequency 2 \
  --dada_ref_taxonomy "pr2=4.14.0" \
  --cut_dada_ref_taxonomy \
  --skip_diversity_indices --skip_ancom --skip_barplot --skip_alpha_rarefaction \
  --email <you@nhm.ac.uk>
```

### ITS

```
nextflow run nf-core/ampliseq -r <version> \
  -c /mbl/share/workspaces/groups/nextflow/config/NHM.config \
  -profile genericcpuhour,singularity \
  -w work \
  --outdir out \
  --input </path/to/samplesheet.tsv> \
  --illumina_pe_its \
  --FW_primer <FORSEQ> \
  --RV_primer <REVSEQ> \
  --cutadapt_min_overlap 3 \
  --trunclenf <FTRUNC> \
  --trunclenr <RTRUNC> \
  --max_ee 1 \
  --cut_its <"full"/"its1"/"its2"> \
  --min_frequency 2 \
  --dada_ref_taxonomy "unite-alleuk=8.3" \
  --cut_dada_ref_taxonomy \
  --skip_diversity_indices --skip_ancom --skip_barplot --skip_alpha_rarefaction \
  --email <you@nhm.ac.uk>
```

Note: we recommend using `--cut_its` for ITS, which runs ITSx on your ASVS, filtering out non-ITS sequences and optionally retaining only parts of the ITS regions. If your primers amplify only ITS1 or ITS2, specifying "its1" or "its2" respectively will return only those regions, trimming off other sequence data. If you use "full", ITSx will be run and filter out non-ITS sequences, but no trimming will be run.

### COX1

```
nextflow run nf-core/ampliseq -r <version> \
  -c /mbl/share/workspaces/groups/nextflow/config/NHM.config \
  -profile genericcpuhour,singularity \
  -w work \
  --outdir out \
  --input </path/to/samplesheet.tsv> \
  --illumina_pe_its \
  --FW_primer <FORSEQ> \
  --RV_primer <REVSEQ> \
  --cutadapt_min_overlap 3 \
  --trunclenf <FTRUNC> \
  --trunclenr <RTRUNC> \
  --max_ee 1 \
  --min_frequency 2 \
  --min_len_asv <MINEXPLENGTH> \
  --max_len_asv <MAXEXPLENGTH> \
  --dada_ref_taxonomy "midori2-co1" \
  --cut_dada_ref_taxonomy \
  --skip_diversity_indices --skip_ancom --skip_barplot --skip_alpha_rarefaction \
  --email <you@nhm.ac.uk>
```

Note: as COX1 is a protein-coding gene, we can apply a strict length filter (`--min_len_asv`, `--max_len_asv`) as we don't expect valid ASVs to vary much. Note that you may wish to apply further filtering not currently available in ampliseq, such as translation filtering and removal of out-of-frame length variants. 

## Outputs

For complete documentation of all the outputs, see [here](https://nf-co.re/ampliseq/output). Here is a highlight set of outputs

* QC reports are in `multiqc/multiqc_report.html`
* ASVs (before `--exclude_taxa`) are in `dada2/ASV_seqs.fasta`
* Taxonomy with species is in `dada2/ASV_tax_species.tsv`
* Raw read counts are in `dada2/DADA2_table.tsv`
* Taxonomic filtered ASVs are in `qiime2/representative_sequences/rep-seq.fasta`
* Taxnomically collapsed read count tables are in `qiime2/abundance_tables/`

