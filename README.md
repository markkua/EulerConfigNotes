# Euler Config Notes

My configuration notes of **ETHz Euler cluster.**

#### Useful links

- [Official Tutorial](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#Euler)
  - [Login via SSH](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#SSH)
  - [Available modules](https://scicomp.ethz.ch/wiki/Euler_applications_and_libraries)
  - [Python packages](https://scicomp.ethz.ch/wiki/Python_on_Euler)
- [Generating a new SSH key and adding it to the ssh-agent - GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)



## Modules





## Jobs

#### Interactive job



#### Select resources

GPU and memory



#### Monitor jobs





## Python 

#### Some basic knowledge about modules

- Check [official tutorial - Setting up the Environment](https://scicomp.ethz.ch/wiki/Getting_started_with_clusters#Setting_up_the_Environment)

#### Python versions on Euler

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

> Q: python vs. python_gpu: The main difference between the installations is that the `python_gpu` module automatically loads a `cuda`, a `cudnn` and an `nccl` module. (But you can also manually load these modules after loading python)

#### Virtual environment: Venv

**Do NOT use Conda!** ([why?](https://scicomp.ethz.ch/wiki/Conda))





## GitHub / GitLab via ssh

1. Login to cluster
2. [Create and add ssh key ](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) 
3. Pull and push **in login node** (can NOT establish ssh connection in an interactive job)



## VSCode

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

   

#### 
