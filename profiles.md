# NHM HPC nextflow profiles

We have written configuration files that optimise how nextflow runs on the NHM HPC. You can find these files in /mbl/share/workspaces/groups/nextflow/config/. This page documents the contents of these files in case you want to learn more or use these as a starting point for your own configuration. Read more about configuration files [here](https://www.nextflow.io/docs/latest/config.html).

# NHM.config

This profile sets the path to the [singularity cache](singularity_cache.md) directory and specifies that all processes should be executed by slurm. It then sets four profiles, corresponding to the four different NHM HPC queues, all using CPUs rather than GPUs. As the NHM HPC slurm configuration is set to dispatch jobs to CPUs by default, no specific CPU dispatch instructions are included. We intend to add GPU profiles if needed.

# eager.config

(Coming soon)
