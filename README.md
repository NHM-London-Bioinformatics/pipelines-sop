# Guidelines for running nf-core pipelines on the NHM HPC

This repo comprises guidelines and Standard Operating Procedures for running [nf-core](https://nf-co.re/) nextflow bioinformatic pipelines on the NHM HPC cluster. It is designed to augment, not replace the comprehensive documentation available on the nf-core webpages for each pipeline. 

## Pre-requisites

You will require access to the NHM HPC cluster to run these pipelines. Contact TS if you do not have access to an account on the HPC cluster (this is separate from franklin access)

**Running these pipelines requires familiarity with the Linux command-line interface (CLI) and using text files in Linux**. It may also help to have basic familiarity with [conda](https://docs.conda.io/en/latest/) and with the [Slurm Workload Manager](https://slurm.schedmd.com/documentation.html). 

# Getting started with nextflow and nf-core

Nextflow is a programming language used to build workflows. Nf-core is a community of nextflow developers who build and curate bioinformatic pipelines in nextflow using common structures and modules. The only thing we need to do to get started is install the nextflow program. Everything else is handled by the `nextflow` command: downloading, installing, configuring and running pipelines is all done in one command. 

Nextflow runs as a "control" program that sets up and runs all of the individual steps in the pipeline itself. It automatically interacts with Slurm, setting up each of the individual processes as their own Slurm jobs. The "control" program uses very little resources itself, so you can run this on the head node of the HPC and observe its progress.

## Installation

We recommend installing nextflow using conda, and doing so in its own environment, as follows:
```
conda create -n nextflow -c conda-forge nextflow
```
This creates a new conda environment called `nextflow` and installs nextflow into it from the conda-forge channel. 

## Top tips for running a pipeline

When you are ready to run a pipeline, we suggest the following:

* Always run a pipeline for a specific set of data in its own working directory. While nextflow does most of its work in a `work` directory that you specify, it also creates a `.nextflow` and `.nextflow.log` files in the current working directory, and there's a chance these may clash if you have two different nextflow pipelines running in the same working directory.
* Always run a pipeline using `screen`. **Nextflow dispatches slurm jobs for you** while the main program continues running and reporting progress, but it can take from hours to days for a pipeline to complete so being able to close the screen and leave the pipeline running is invaluable.
* Always specify a pipeline version (`-r`) when you run a pipeline. When you want to compare a new run to a previous dataset, use the same version as the previous dataset. Otherwise, we generally recommend running the most recent stable version: if you're not sure what that is, head to the documentation for that pipeline and it will say on the main page.
* Always keep your own record of the exact command you run, so that you can replicate the analysis either with the same or new data.
* All pipelines require a samplesheet in tab-separated values (TSV) text file (also known as tab delimited table). You can create this table and save it as TSV in [Microsoft Excel](https://smallbusiness.chron.com/make-txt-tab-delimited-35511.html), [LibreOffice Calc](https://ask.libreoffice.org/t/how-to-generate-calc-tab-delimited-output/14591) or [Google Sheets](https://support.google.com/merchants/answer/160569?hl=en-GB). You may need to use the [`dos2unix`](https://linux.die.net/man/1/dos2unix) command on the Linux CLI to remove hidden formatting. 
* We recommend using [absolute file paths](https://www.linuxfoundation.org/blog/blog/classic-sysadmin-absolute-path-vs-relative-path-in-linux-unix) throughout any nextflow command, including in the input samplesheet.

## Queue selection and other configurations

We have written a configuration file that optimises how nextflow runs on the NHM HPC, including dispatching jobs with Slurm. The main thing to remember is that this allows us to specify which Slurm queue to run with. When running any of these pipelines on the NHM HPC, you **must** make sure to include the NHM config file as follows:
```
 -c /mbl/share/workspaces/groups/nextflow/config/NHM.config
 ```
This is included in all of the example commands for the pipelines.

This config file creates a set of default profiles that instruct the pipeline to dispatch all jobs to a specific slurm queue. For pipelines that use this default approach (i.e. ampliseq), all you need to know is that by default, the example commands use the `genericcpuhour` profile, dispatching jobs to CPU nodes on the `hour` queue. If your dataset is large and the pipeline needs more time, you can change to the `day`, `week` or `month` queue by just swapping out `genericcpuhour` for `genericcpuday`, etc. 

For eager, queue selection is instead done automatically by a secondary configuration file - see the [aDNA](ancientDNA.md) page for more information. Dispatching to `genericcpuhour` or equivalent profiles is not recommended.

Otherwise, you don't need to understand profiles and caching to get started, but if you want to learn more you can see the documentation for the [NHM nextflow configs and profiles](configs.md) and the [singularity cache](singularity_cache.md)

# Pipelines

## Metabarcoding

We have extensively tested the [nf-core ampliseq](https://nf-co.re/ampliseq) pipeline for metabarcoding. [Click here](metabarcoding.md) to read our guide to running ampliseq on the NHM HPC.

## Ancient DNA

We have extensively tested the [nf-core eager](https://nf-co.re/eager) pipeline for ancient DNA analysis. [Click here](ancientDNA.md) to read our guide to running eager on the NHM HPC.

## Genome Skimming

We are in the process of developing a new nf-core pipeline, called [genomeskim](https://nf-co.re/genomeskim). Watch this space.
