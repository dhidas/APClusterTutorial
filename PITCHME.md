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
user@apcpu-master01:~$ mkdir /GPFS/APC/[user]
user@apcpu-master01:~$ cd /GPFS/APC/[user]
```

To setup SLURM and related environment variables:
```bash
user@apcpu-master01:~$ source /opt/slurm/setup.sh
```


