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

## Profiles and cache

You don't need to understand profiles and caching to get started, but if you want to learn more you can see the documentation for the NHM nextflow profile and the singularity cache

# Pipelines

## Metabarcoding

We have extensively tested the nf-core ampliseq pipeline for metabarcoding. Click here to read more about ampliseq.

## Ancient DNA

We are in the process of extensively testing the nf-core eager pipeline for aDNA bioinformatics. Watch this space.

## Genome Skimming

We are in the process of developing a new nf-core pipeline, called genomeskim. Watch this space.
