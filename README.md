# Euler Config Notes

My notes of using **ETHz Euler cluster**, including some useful commands.


## Useful links

- [Official Tutorial](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters)
  - [Login via SSH](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#SSH)
  - [Setting up the environment](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#Setting_up_the_Environment)
  - [Available modules](https://scicomp.ethz.ch/wiki/Euler_applications_and_libraries)
  - [Python packages](https://scicomp.ethz.ch/wiki/Python_on_Euler)
  - [Personal storage](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#Personal_storage_.28everyone.29)
- [Generating a new SSH key and adding it to the ssh-agent - GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)



## Modules

### Change to the new software stack: 

```bash
env2lmod
```

### Some basic knowledge about modules

- Check [official tutorial - Setting up the Environment](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#Setting_up_the_Environment)

# Jobs

### Submit a job

```bash
# bsub command [arguments]
# bsub /path/to/program [arguments] 
# example: 
bsub gzip big_file.dat
```

### Specify running time

```bash
# bsub -W minutes ...                  
bsub -W 90 gzip big_file.dat
# bsub -W HH:MM ...  
bsub -W 1:30 gzip big_file.dat
```

### Interactive job

```bash
bsub -I command [arguments]
# for a pseudo-terminal, use -Ip:
bsub -W 4:00 -Ip bash
# for a login shell, use -Is
bsub -W 4:00 -Is bash
```

### Specify output file

```bash
# -o
bsub -o output_file command [argument]
# wrong example: 
bsub command -o output_file          ←  WRONG!
```



### Resource requirements

Tutorial for resource requirements [here](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#Resource_requirements).

#### CPU

```bash
# number of cores
bsub -n number_of_procs ...
```

```bash
# memory
bsub -R "rusage[mem=XXX]" ...
```

```bash
# example:
bsub -n 32 -W 4:00 -R "rusage[mem=32768]" ./my_cpu_program
```

#### GPU

``` bash
# number of GPU nodes:
bsub -R "rusage[ngpus_excl_p=1]" ./my_cuda_program
```

```bash
# GPU memory
bsub -n 20 -R "rusage[ngpus_excl_p=1]" -R "select[gpu_mtotal0>=10240]" ./my_cuda_program
```

```bash
# GPU type
bsub -n 20 -R "rusage[mem=4500,ngpus_excl_p=8]" -R "select[gpu_model0==GeForceGTX1080Ti]" ./my_cuda_program

bsub -n 16 -W 4:00 -R "rusage[ngpus_excl_p=1,mem=65536]" -R "select[gpu_mtotal0>=10240]" -R "select[gpu_model0==GeForceRTX2080Ti]" ./my_cuda_program
```

## Monitor jobs

Useful commands: 

```bash
busers	# user limits, number of pending and running jobs
bjobs		# more or less detailed information about pending, running and recently finished jobs
bbjobs	# better bjobs (bjobs with human readable output)
bhist		# information about jobs that finished in the last hours/days
bjob_connect  # login to a node where one of your jobs is running
bkill		# kill a job
bpeek		# display the standard output of a given job
bqueues	# queues status (open/closed; active/inactive)

```

```bash
# Check pending reasons:
bjobs -p

```

*Individual host-based reasons* means that the resources requested by your jobs are not available at this time. Some resources may never become available ( e.g. mem=10000000). Some resource requirements may be mutually exclusive.

For more, please check [here](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#Job_monitoring).

# Python 

## Virtual environment -- use Venv

```bash
# 1. Load python module, e.g.
module purge
module load eth_proxy
module load gcc/9.3.0
module load cuda/10.1.243
module load python/3.6.5

# 2. Create virtual environment
python3 -m venv ~/venv/my_venv_py365

# 3. Activate virtual environment
source ~/venv/my_venv_py365/bin/activate

# 4. Install packages with pip as usual
```

### **Do NOT use Conda!** 

> - Parallel high-performance file systems as Lustre (/cluster/scratch, /cluster/work) are optimized for large files (>4 MB),
>   - A typical Anaconda installation easily contains 100k-200k small files with an average file size of a few KB, Miniconda installations still contain around 60k small files
> - Conda applications that require MPI cannot use the centrally provided MPI installations with are compiled with support for the LSF batch system
> - In your home directory, you have a files/directories quota of 100,000 which often conflicts with conda installations
> - [More reasons](https://scicomp.ethz.ch/wiki/Conda)



## Python versions on Euler

> LMOD allows to define a hierarchy of modules containing 3 layers (Core, Compiler, MPI)

- Not all python versions are available. 
- Not every gcc version contains all available python versions.

Available python versions (slightly different from [wiki](https://scicomp.ethz.ch/wiki/Python_on_Euler)): 

```
gcc/4.8.2
	-
gcc/4.8.5
	python/2.7.14
	python/3.6.4	
	python/3.6.5
	python/3.7.4
gcc/5.4.0
	-
gcc/6.3.0
	fast_python_workshop_cpu/2021.0.0
	fast_python_workshop_gpu/2021.0.0
	python/2.7.14
	python/3.6.4
	python/3.6.5
	python/3.7.4
	python/3.8.5
	python_gpu/3.7.4
	python_gpu/3.8.5
gcc/7.3.0
	-
gcc/8.2.0
	python/2.7.14
	python/3.6.4
  python/3.6.5
	python/3.8.5
	python_gpu/3.8.5
gcc/9.3.0
	python/3.6.5
```

> Q: difference between python and python_gpu
>
> A:  The main difference between the installations is that the `python_gpu` module automatically loads a `cuda`, a `cudnn` and an `nccl` module. (But you can also manually load these modules after loading python)

# Using VSCode

**Do NOT directly connect to login node **via VSCode [Remote SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh), because it will start a large number of threads and stop other users from logging in. 

Then how to connect:

1. Follow [official tutorial to use VSCode](https://scicomp.ethz.ch/wiki/VSCode) to establish ssh tunnel in an interactive job (so you would not use the resource of login node)

   - You can create your own script to submit job and start code-server in one click.

2. (On local machine) copy the command [generated by the script](https://scicomp.ethz.ch/wiki/VSCode#Setting_up_the_SSH_tunnel) and run it in local bash

   ```bash
   # example: 
   ssh -N -f -L localhost:8899:10.205.176.16:56078 sfux@euler.ethz.ch
   ```

3. (On local machine) [Connect via browser](https://scicomp.ethz.ch/wiki/VSCode#Using_the_local_browser_to_display_the_code-server_GUI)

4. (After use) Clean up ssh tunnel: 

   ```bash
   # official tutorial
   ps -u | grep -m1 -- "-L" | grep -- "-N"
   kill PID
   # works for me on Mac
   lsof -i tcp:8899
   kill <PID>
   ```

   

# GitHub / GitLab via ssh

1. Login to cluster
2. [Create and add ssh key ](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) 
3. Pull and push **in login node** (can NOT establish ssh connection in an interactive job)



# Misc

## Check disk quota

```bash
lquota
```

More details [here](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#Quotas).

