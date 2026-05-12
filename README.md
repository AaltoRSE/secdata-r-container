# secdata R container

Apptainer (Singularity) image for R workflows used with secdata: geospatial tooling, econometrics-related packages, and **RStudio Server** as the default entrypoint. The image is defined in `sec_r.def` and built with Apptainer’s `build` command.

## What’s in the image

- **Base:** `rocker/geospatial:4.4.3` (R 4.4 with spatial stack).
- **Extra system packages:** XML/SSL/curl development libraries for R packages that need them.
- **R packages:**  listed in `%post` in `sec_r.def`.
- **Locale:** UTF-8 (`LANG` / `LC_ALL`).
- **Default command:** `%runscript` starts **RStudio Server** (`rserver`) bound to `RSTUDIO_WWW_ADDRESS` (default `127.0.0.1`) so you can tunnel or bind a port as needed.

## Prerequisites

[Apptainer](https://apptainer.org/) (or Singularity) on a Linux host or cluster node where builds and runs are allowed.

## How to build

The definition file is `sec_r.def`. The artifact is a single `.sif` file (example name in this repo: `sec_r_may2026.sif`).

**On a machine where you can run builds interactively:**

```bash
apptainer build sec_r_may2026.sif sec_r.def
```

**On Slurm:** `build.sh` is a batch script that runs the same build with the resource requests at the top of the file (job name, time limit, memory, CPUs). Submit it with:

```bash
sbatch build.sh
```

## How to use the container

The container **runscript** starts RStudio Server. You normally:

1. Set an RStudio password in the environment. Apptainer propagates `APPTAINERENV_*` into the container (e.g. `APPTAINERENV_PASSWORD` becomes `PASSWORD` for PAM).
2. Run the `.sif` with appropriate binds and port options.

**Minimal local example** RStudio listens on `127.0.0.1`, port `8787` by default for `rserver`.

```bash
export APPTAINERENV_PASSWORD='your-password'
apptainer run --cleanenv --bind /path/to/your_project:/${HOME}/workspace sec_r_may2026.sif
```

The left side of `--bind` is a host directory; the right side is the path inside the container where it appears.

Then open a browser to `http://127.0.0.1:8787` (or the port you chose) and sign in with your Linux username and the password you set.

RStudio’s file browser shows the bound tree. 
