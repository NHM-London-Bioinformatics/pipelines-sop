# Metabarcoding with ampliseq

We have extensively tested the [nf-core ampliseq](https://nf-co.re/ampliseq) pipeline and validated its efficacy with published NHM metabarcoding data. Here we give a brief overview of getting started with the pipeline and some commands for different barcode sequences. The [ampliseq documentation](https://nf-co.re/ampliseq/2.5.0/usage) is very good and you should review it alongside this guide.

## Getting started

The following is a checklist of information you should know before running ampliseq:

1. The forward and reverse primers used to amplify your target region. These should not include any index or adapter sequences (aka "overhang")
2. The length of your reads (e.g. 300bp, 250bp, etc)
3. The location of your demultiplexed reads files. Each pair of files should correspond to a single sample or replicate.

Once you have these, you need to prepare a samplesheet. See the [ampliseq samplesheet documentation](https://nf-co.re/ampliseq/2.5.0/usage#samplesheet-input) for the columns that are required in your table, and the 

### Samplesheet









