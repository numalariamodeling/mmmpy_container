# How to build the container

## Docker
```
docker build -f Dockerfile.rocky8 -t emod:rocky8 .
```

## Singularity

### Step 1
Log into Quest and load the singularity modules

```
module load singularity
```

### Step 2
Authenticate against the remote builder. Will request that you create a token and copy the token in order to authenticate.

```
singularity remote login
```

### Step 3

Build the container using the remote builder

```
singularity build --remote emod-with-om-46-R-4.4.1.sif Singularity.recipe
```

# Understanding where things are in the container

## Open Malaria

The container currently only has v46.0 and it lives in the container here:

```
/openmalaria-schema-46.0/build
```

Any future Open Malaria XX.X would live in the container here:

```
/openmalaria-schema-XX.X/build
```

## The `emodpy_om` virtual environment

The `emodpy_om` virtual environment lives here:

```
/opt/envs/emodpy_om
```

## R
The R executable can be found here

```
/opt/R/4.4.1/bin/R
```

# How to invoke the container

## Set things in manifest.py

```
    OPENMALARIA_venv = f'\n\nexport PATH=/openmalaria-schema-46.0/build/:$PATH\n'
    EMOD_venv = f'source /opt/envs/emodpy_om/bin/activate'
```

## Run `launch_sim_test.py` with the container.

```
module purge
module load singularity
singularity exec -B /etc/passwd -B /etc/slurm -B `which sbatch ` -B `which srun ` -B `which sacct ` -B `which scontrol ` -B `which salloc ` -B `which scancel ` -B `which squeue ` -B /usr/lib64/slurm/ -B /usr/lib64/liblua-5.1.so -B /usr/lib64/liblua-5.1.so:/usr/lib64/liblua.so -B /usr/lib64/libjson-c.so.2 -B /usr/lib64/libjson-c.so.2.0.1 -B /usr/lib64/libmunge.so.2 -B /usr/lib64/libmunge.so.2.0.0 -B /usr/lib64/libpmi2.so -B /usr/lib64/libpmi2.so.0 -B /usr/lib64/libpmi2.so.0.0.0 -B /usr/lib64/libpmi.so -B /usr/lib64/libpmi.so.0 -B /usr/lib64/libpmi.so.0.0.0 -B /usr/local/pmix -B /usr/local/hwloc -B /usr/local/ucx-1.13.0 -B /usr/local/ucx-1.10.0 -B /usr/local/ucx-1.8.1 -B /usr/local/spack -B /usr/local/spack_v20d1 -B /usr/local/libevent/ -B /run -B /projects -B /scratch --writable-tmpfs /home/mrm9534/rcs-support/emod-scotty/emod-with-om-46-R-4.4.1.sif /opt/envs/emodpy_om/bin/python3 launch_sim_test.py --models EMOD -r test
```
