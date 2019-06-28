# Tutorial for APCluster and SLURM

---

## APCluster Login and Setup

To access apcluster you first need to be on the internal network, for instance get to box64-1:
```
user@localhost:~$ ssh -X user@box64-1.nsls2.bnl.gov
```
You may then login to the head-node, currently apcpu-master01
```bash
user@box64-1:~$ ssh -X user@apcpu-master01
```

Never use your home directory, instead use GPFS
```bash
> cd /GPFS/APC/user
```

The slurm environment is setup for you.  You can test running a job with:
```bash
> srun -n 2 hostname
```
should run the 'hostname' command on 2 cores and print something like:
```text
apcpu-002
apcpu-002
```


---

## Environment Modules
You can load and unload software modules you wish to use:
```bash
> module load python/3.7.3
> module load elegant-latest
> module unload python
```
To see a list of all available software:
```bash
> module spider
```
To see a list of available software for the compiler/MPI combination currently loaded:
```bash
> module avail
```
To see what modules are currently loaded:
```bash
> module list
```


---

## Environment Modules - MPI
To use most MPI, you need a compiler+MPI combination.  A few recommendations are:
```bash
> module load gcc openmpi       # Trust the defaults
> module load gcc mpich         # Trust the defaults
> module load intel             # Has its own mpiicc, mpiifort
> module load intel openmpi     # Use openmpi with intel compiler
```
To see a list of all available software:
```bash
> module spider
```
To see a list of available software for the compiler/MPI combination currently loaded:
```bash
> module avail
```



---

## Modules of interest
Some currently installed modules of interest:
```text
   gcc/4.9    gcc/6.5    gcc/7.4 (D)    gcc/8.3    gcc/9.1
   python/2.7.16    python/3.5.7    python/3.7.3 (D)
   python/3.4.10    python/3.6.8
   elegant-latest
   fluka-latest
   MAD-X/5.04.02
   julia/1.1.1
```




---

## Download Examples & sbatch
Setup gcc and mpi version of choice
```bash
module load gcc/7.4 openmpi
```
Download a set of basic examples, compile, and submit sbatch
```bash
> git clone https://gitlab.nsls2.bnl.gov/dhidas/SLURMExamples
> cd SLURMExamples/CPP
```
Compile the hello executable (note this is not the system default mpi.  Look to /opt for correct software)
```bash
> mpic++ hello.cc -o hello
```
You can use srun as follows
```bash
> srun -n 2 ./hello
```
or submit a batch job using the submit.sh script and sbatch
```bash
> sbatch submit.sh
```

---

## The SBATCH script
Many options can be specified with the #SBATCH directives.  The basic example script is
```text
#!/bin/bash
#SBATCH --job-name=hello
#SBATCH --error=job.%J.err
#SBATCH --output=job.%J.out
#SBATCH --ntasks=2
#SBATCH --time=10:00

srun ./hello
```
This will start 2 hello jobs and log output and errors.

For more information see:
- https://slurm.schedmd.com/sbatch.html

---

## MPI, Python MPI
You almost never need worry about MPI.  You should NEVER need to use mpirun nor mpiexec.  SLURM knows about MPI.  An example python MPI program:
```bash
> cd ../Python
> module load python
> srun -n 2 python hello.py
```
will properly engage MPI and should print out something similar to
```text
Hello, World! I am process 1 of 2 on apcpu-002.
Hello, World! I am process 0 of 2 on apcpu-002.
```

---

## Elegant and Pelegant
Recommended setup:
```bash
module load elegant-latest
```
Move to the elegant directory in SLURMExamples
```bash
> cd ../elegant
```
Run elegant:
```bash
srun elegant fma1p.ele
```
Run Pelegant with 88 cores (here you need to specify the correct mpi interface library: pmi2)
```bash
> srun -n 88 --mpi=pmi2 Pelegant fma1p.ele
```
You can use sbatch to submit a Pelegant job
```bash
sbatch submit.sh
```
and you can plot something with
```
sddsplot -columnNames=s,etax -columnNames=s,betax fma1p.twi
```


---


