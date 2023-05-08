# NHM HPC nextflow configuration files

We have written configuration files that optimise how nextflow runs on the NHM HPC. You can find these files in /mbl/share/workspaces/groups/nextflow/config/. This page documents the contents of these files in case you want to learn more or use these as a starting point for your own configuration. Read more about configuration files [here](https://www.nextflow.io/docs/latest/config.html).

# NHM.config

This profile sets the path to the [singularity cache](singularity_cache.md) directory and specifies that all processes should be executed by slurm. It then sets four **profiles**, corresponding to the four different NHM HPC queues, all using CPUs rather than GPUs. As the NHM HPC slurm configuration is set to dispatch jobs to CPUs by default, no specific CPU dispatch instructions are included. We intend to add GPU profiles if needed.

# eager.config

This profile sets configuration for individual tasks in eager. All nf-core pipelines have the capability to retry failed tasks, and generally when this happens the pipelines increase the computational resources made available to a task for each retry. The ancient DNA pipeline comprises several steps that may take substantial amounts of time to run, in particular mapping, and this config alters the default resources made available to tasks so that these tasks are more likely to complete successfully the first time they are run, or are supplied with suitably increased resources on subsequent attempts. The aim here is to reduce wasted computational resources, and we take the approach of providing plenty of resources on the first attempt, rather than wasting time slowly increasing resources until a task succeds.
