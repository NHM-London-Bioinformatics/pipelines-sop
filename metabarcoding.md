# Metabarcoding with ampliseq

We have extensively tested the [nf-core ampliseq](https://nf-co.re/ampliseq) pipeline and validated its efficacy with published NHM metabarcoding data. Here we give a brief overview of getting started with the pipeline and some commands for different barcode sequences. The [ampliseq documentation](https://nf-co.re/ampliseq/usage) is very good and you should review it alongside this guide.

## Getting started

First, check our [top tips for running a pipeline](README.md#top-tips-for-running-a-pipeline)

The following is a checklist of information you should know before running ampliseq:

1. The forward and reverse primers used to amplify your target region. These should not include any index or adapter sequences (aka "overhang")
2. The length of your reads (e.g. 300bp, 250bp, etc)
3. The location of your demultiplexed reads files. Each pair of files should correspond to a single sample or replicate.

Once you have these, you need to prepare a samplesheet. See the [ampliseq samplesheet documentation](https://nf-co.re/ampliseq/2.5.0/usage#samplesheet-input) for the columns that are required in your table, and don't forget to use absolute file paths and save it as a tab-separated values file.

## Commands

Before you run ampliseq, remember to open a `screen` and activate the nextflow conda environment:
```
conda activate nextflow
```
Below are starting commands for running ampliseq for four different markers. Before you run any of these commands, you should check over the parameters, reviewing the [ampliseq documentation](https://nf-co.re/ampliseq/parameters). Several parameters will require you to fill in project-specific information - some of these are obvious, more information is given for some below. Other parameters have default information already filled in, you should consider whether these are the best options for your project. 

### `--trunclenf` / `--trunclenr`
The conservative approach is to set these to one less than your read length, i.e. `--trunclenf 249` and `--trunclenr 249` for 2x250 reads. If your sequencing is not ideal and this removes too many reads, you could instead set this to a lower value, remembering that you need to leave enough read length left for merging!

### `--cutadapt_min_overlap`
This sets the minimum length of primer sequence to match for primers to be removed and the read retained. This should never be less than 3, and where primers are self-complementary at the ends, should be increased. Review the beginning and end of each of your primer sequences and if they overlap, set this to one greater than the maximum number of overlapping bases for either of your primers. For example, the primer sequence `CGGTAAYTCCAGCTCYV` overlaps with itself by 3 bases since the ambiguities `Y` and `V` both include `G`:
```
CGGTAAYTCCAGCTCYV
              CGGTAAYTCCAGCTCYV
```
Assuming the other primer overlaps by 3 or fewer bases, runs including this primer should be set to `--cutadapt_min_overlap 4`

### `-w` / `--outdir`
We recommend running ampliseq in its own working directory, so by default these arguments are set to generic directory names. Change as desired

### Taxonomic and location filtering
Ampliseq has two ways to filter out ASVs from non-target taxa or genetic compartments. Firstly, for 16S and 18S only, ampliseq can use barrnap to check ASVs are in one or more taxa and/or compartments. Use [`--filter_ssu`](https://nf-co.re/ampliseq/parameters#filter_ssu) to specify kingdoms and or compartments - any ASVs in the specified taxa/compartments will be kept. Secondly, for any marker, after taxonomic assignment ampliseq can filter *out* specified compartments and/or any arbitrary taxonomic levels using [`--exclude_taxa`](https://nf-co.re/ampliseq/parameters#exclude_taxa). Note that the default for `--exclude_taxa` removes mitochondrial sequences, so make sure that if no other filter is set, `"none"` is sepecified if running on COX1.


* Fill in any parameters with arguments enclosed in `<>`. For example, <FORSEQ> should be replaced with the forward primer sequence. See below for explanation
* Check all of the parameters on the  and make sure you know what they are doing.

# 16S

```
nextflow run nf-core/ampliseq -r <version> \
  -c /mbl/share/workspaces/groups/nextflow/config/NHM.config \
  -profile genericcpuhour,singularity \
  -w work \
  --input </path/to/samplesheet.tsv> \
  --FW_primer <FORSEQ> \
  --RV_primer <REVSEQ> \
  --trunclenf <FTRUNC> \
  --trunclenr 199 \
  --cutadapt_min_overlap 3 \
  --min_frequency 2 \
  --max_ee 2 \
  --dada_ref_taxonomy "silva=138" \
  --cut_dada_ref_taxonomy \
  --outdir out \
  --skip_diversity_indices \
  --skip_ancom \
  --skip_barplot \
  --skip_alpha_rarefaction \
  --exclude_taxa "mitochondria,chloroplast" \
  --email "t.creedy@nhm.ac.uk"


## Important arguments











