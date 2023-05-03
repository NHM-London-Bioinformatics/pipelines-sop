# Guidelines for running nf-core pipelines on the NHM HPC

This repo comprises guidelines and Standard Operating Procedures for running [https://nf-co.re/](nf-core) nextflow bioinformatic pipelines on the NHM HPC cluster. It is designed to augment, not replace the comprehensive documentation available on the nf-core webpages for each pipeline. 

These guidelines assume that you have:
* Access to the NHM HPC
* Basic familiarity with the Linux command line interface
* Basic familiarity with conda

# Getting started with nextflow and nf-core

Nextflow is a programming language used to build workflows. Nf-core is a community of nextflow developers who build and curate bioinformatic pipelines in nextflow using common structures and modules. The only thing we need to do to get started is install the nextflow program. Everything else is handled by the `nextflow` command: downloading, installating, configuring and running pipelines is all done in one command. 

Nextflow runs as a "control" program that sets up and runs all of the individual steps in the pipeline itself. It automatically interacts with slurm, setting up each of the individual processes as their own slurm jobs. The "control" program uses very little resources itself, so you can run this on the head node of the HPC and observe its progress.

## Installation

We recommend installing nextflow using conda, and doing so in its own environment, as follows:
```
conda create -n nextflow -c conda-forge nextflow
```
This creates a new conda environment called `nextflow` and installs nextflow into it from the conda-forge channel. 

## Top tips for running a pipeline

When you are ready to run a pipeline, we suggest the following:

* Always run a pipeline for a specific set of data in its own working directory. While nextflow does most of its work in a `work` directory that you specify, it also creates a `.nextflow` and `.nextflow.log` files in the current working directory, and there's a chance these may clash if you have two different nextflow pipelines running in the same working directory.
* Always run a pipeline using `screen`. *Nextflow dispatches slurm jobs for you* while the main program continues running and reporting progress, but it can take from hours to days for a pipeline to complete so being able to close the screen and leave the pipeline running is invaluable.
* Always specify a version when you run a pipeline. When you want to compare a new run to a previous dataset, use the same version as the previous dataset. Otherwise, we generally recommend running the most recent stable version: if you're not sure what that is, head to the documentation for that pipeline and it will say on the main page.
* Always keep your own record of the exact command you run, so that you can replicate the analysis either with the same or new data.
* All pipelines require a samplesheet in tab-separated values (TSV) text file (also known as tab delimited table). You can create this table and save it as TSV in [Microsoft Excel](https://smallbusiness.chron.com/make-txt-tab-delimited-35511.html), [LibreOffice Calc](https://ask.libreoffice.org/t/how-to-generate-calc-tab-delimited-output/14591) or [Google Sheets](https://support.google.com/merchants/answer/160569?hl=en-GB).
* We recommend using [absolute file paths](https://www.linuxfoundation.org/blog/blog/classic-sysadmin-absolute-path-vs-relative-path-in-linux-unix) throughout any nextflow command, including in the samplesheet

## Queue selection and other configurations

We have written a configuration file that optimises how nextflow runs on the NHM HPC, including dispatching jobs with slurm. The main thing to remember is that this allows us to specify which slurm queue to run with. For ampliseq, this is done by the user, and by default we use the `hour` queue but this is easily changed - see the [metabarcoding page](metabarcoding.md) for more information. For eager, this is done automatically by a secondary configuration file - see the aDNA page for more information (coming soon). Otherwise, you don't need to understand profiles and caching to get started, but if you want to learn more you can see the documentation for the [NHM nextflow profile](profiles.md) and the [singularity cache](singularity_cache.md)

# Pipelines

## Metabarcoding

We have extensively tested the [nf-core ampliseq](https://nf-co.re/ampliseq) pipeline for metabarcoding. [Click here](metabarcoding.md) to read our guide to running ampliseq on the NHM HPC.

## Ancient DNA

We are in the process of extensively testing the nf-core eager pipeline for aDNA bioinformatics. Watch this space.

## Genome Skimming

We are in the process of developing a new nf-core pipeline, called genomeskim. Watch this space.
