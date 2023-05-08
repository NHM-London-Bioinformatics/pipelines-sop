# NHM HPC nextflow configuration files

We have written configuration files that optimise how nextflow runs on the NHM HPC. You can find these files in /mbl/share/workspaces/groups/nextflow/config/. This page documents the contents of these files in case you want to learn more or use these as a starting point for your own configuration. Read more about configuration files [here](https://www.nextflow.io/docs/latest/config.html).

# NHM.config

This config sets the path to the [singularity cache](singularity_cache.md) directory and specifies that all processes should be executed by slurm. It then sets four **profiles**, corresponding to the four different NHM HPC queues, all using CPUs rather than GPUs. As the NHM HPC slurm configuration is set to dispatch jobs to CPUs by default, no specific CPU dispatch instructions are included. We intend to add GPU profiles if needed.

# eager.config

This config sets configuration for individual tasks in eager. All nf-core pipelines have the capability to retry failed tasks, and generally when this happens the pipelines increase the computational resources made available to a task for each retry. The ancient DNA pipeline comprises several steps that may take substantial amounts of time to run, in particular mapping, and this config alters the default resources made available to tasks so that these tasks are more likely to complete successfully the first time they are run, or are supplied with suitably increased resources on subsequent attempts. The aim here is to reduce wasted computational resources, and we take the approach of providing plenty of resources on the first attempt, rather than wasting time slowly increasing resources until a task succeeds.

## Queue specification

The config file specifies a map (aka dict or hash) that comprises 3 unique queue options, such that successive attempts will be dispatched to queues with longer running times. Namely, attempts 1 and 2 are dispatched to the hour queue, 3 and 4 the day queue and 5 the week queue. 

## Task specifications

The config file overwrites the default starting cpus, memory and time for different processes (tasks) - processes are classified by labels denoting broad levels of computational usage. All processes in eager are marked as belonging to one of these labels, and inherit that label's resource configuration. 
