# Optimal Follow-Up of Gravitational-Wave Events with the UltraViolet EXplorer (UVEX)

This repository contains re-runs of the simulations from the paper _Optimal Follow-Up of Gravitational-Wave Events with the UltraViolet EXplorer (UVEX)_ (https://arxiv.org/abs/2502.17560) but based on more recent GW detector observing scenarios from https://github.com/lpsinger/observing-scenarios-simulations.

These simulations are designed to be run on a [Slurm] computing cluster. They are optimized for [NCSA Delta] and [SDSC Expanse] clusters which are both part of the U.S. National Science Foundation's [ACCESS] ecosystem.

## Instructions to Run

1.  Log in to the cluster.

2.  Install uv.

    I use [uv] to manage the installation of the dependencies. Install uv by
    running the command in the [official uv installation instructions]:

        curl -LsSf https://astral.sh/uv/install.sh | sh

    Then log out, and log back in.

3.  Select and navigate to a directory to work in.

    This should be on a fast, globally mounted filesystem that is not backed
    up. For example, use [`/work/nvme` on NCSA Delta] or
    [`/expanse/lustre` on SDSC Expanse].

4.  Clone this repository:

        git clone https://github.com/m4opt/m4opt-observing-scenarios.git
        cd m4opt-observing-scenarios

5.  Use uv to create a virtual environment and install all of the Python
    dependencies inside it by running the following command:

        uv sync

6.  Install CPLEX inside the project's uv virtual environment by following
    [M4OPT's instructions to install CPLEX].

7.  Run the following command to download the observing scenarios simulations
    (about 30 GB) and unpack and index them. The result will be a `data`
    directory containing sky map FITS files and an [ECSV] summary data table.

        uv run make data/observing-scenarios.ecsv

8.  Edit the file `scripts/schedule-absmag-distribution.py` and adjust the
    lines `#SBATCH --partition=...` and `#SBATCH --account=...` to include the
    correct job accounting information for your cluster and your allocation.

9.  Run the following command to submit the M4OPT jobs to Slurm. This will
    result in observing plans in ECSV format placed alongside the FITS files.

        uv run sbatch scripts/schedule-absmag-distribution.py

    You can monitor the progress of the jobs using standard Slurm command line
    tools like [`squeue --me`].

10. Once the Slurm jobs are done, run the following command to create summary
    tables. The results will be an ECSV file at the path `data/events.ecsv` and
    a LaTeX table of predicted kilonova detection rates at
    `tables/selected-detected.tex`.

        uv run make

[`/expanse/lustre` on SDSC Expanse]: https://www.sdsc.edu/systems/expanse/user_guide.html#narrow-wysiwyg-10
[`/work/nvme` on NCSA Delta]: https://docs.ncsa.illinois.edu/systems/delta/en/latest/user_guide/architecture.html#storage-file-systems
[`squeue --me`]: https://slurm.schedmd.com/squeue.html
[ACCESS]: https://access-ci.org
[ECSV]: https://docs.astropy.org/en/latest/io/ascii/ecsv.html
[M4OPT's instructions to install CPLEX]: https://m4opt.readthedocs.io/en/latest/install/cplex.html
[NCSA Delta]: https://docs.ncsa.illinois.edu/systems/delta/
[official uv installation instructions]: https://docs.astral.sh/uv/getting-started/installation/
[SDSC Expanse]: https://www.sdsc.edu/systems/expanse/user_guide.html
[Slurm]: https://slurm.schedmd.com
[uv]: https://docs.astral.sh/uv/
