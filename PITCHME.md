# Tutorial for APCluster and SLURM

---

# APCluster Login and Setup

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
> mkdir /GPFS/APC/user
> cd /GPFS/APC/user
```

To setup SLURM and related environment variables:
```bash
> source /opt/slurm/setup.sh
```


---

# Test SLURM
Now slurm is setup, test you can run a simple command
```bash
> srun -n 2 hostname
```
should run the 'hostname' command on 2 cores and print something like:
```text
apcpu-002
apcpu-002
```
If this works, SLURM is working correctly for you.


---

# Download Examples & sbatch
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

# The SBATCH script
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

# MPI, Python MPI
You almost never need worry about MPI.  You should NEVER need to use mpirun nor mpiexec.  SLURM knows about MPI.  An example python MPI program:
```bash
> cd ../Python
> srun -n 2 python hello.py
```
will properly engage MPI and should print out something similar to
```text
Hello, World! I am process 1 of 2 on apcpu-002.
Hello, World! I am process 0 of 2 on apcpu-002.
```

---

# Elegant and PElegant
elegant and Pelegant are available on all cluster nodes
```bash
> elegant
```
or
```bash
> Pelegant
```
Move to the elegant directory in SLURMExamples
```bash
> cd ../elegant
```
If you do not already have RPN DEFNS, you need it:
```bash
> cp /GPFS/APC/dhidas/.defns.rpn /GPFS/APC/user/.defns.rpn
```
And you need to add it to your path
```bash
> export RPN_DEFNS=/GPFS/APC/user/.defns.rpn
```

---

# Elegant and Pelegant via SLURM
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
