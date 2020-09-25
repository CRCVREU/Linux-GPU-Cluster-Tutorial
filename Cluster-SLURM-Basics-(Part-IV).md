## SLURM Basics

Author: [@brandons209](https://github.com/brandons209)

[Newton SLURM Wiki](http://newton.i2lab.ucf.edu/wiki/Help:Slurm)

What is SLURM? SLURM stands for: `Simple Linux Utility for Resource Management`. It allows for a computer to distribute its resources to many users efficiently.

Normally, on your local computer, when you run a program your OS will use as much of your system's resources as needed, or whatever is available. However, one a large server cluster as Newton we don't want one user hogging up all the resources whenever they run a program.

**Server cluster?** Yes, Newton isn't just one computer, its multiple compute **nodes** linked together. There is a **user** node which holds your data and user files, and many **compute** nodes that have all the GPU, CPU, and RAM resources. This is why when you log in your bash prompt has the `@evuser1`, meaning you are logged into the `evuser1` user node. Heres a simple diagram below:

![newton](files/newton.png)

Newton has:
* 20 compute nodes
* Each node 32 cores and 192 GB of memory per node
* Each node has two Nvidia Tesla V100 GPUs ($9000-$11,000 dollars each!)
* All total 640 cores and 40 GPUs
* Half the nodes have 16 GB GPU cards, the other half have 32 GB cards

So, SLURM manages all the compute nodes and distributes resources as needed to user's programs. Think of it as a printer queue. Although, it is a bit more complicated to submit a job instead of just clicking print. From now on, when I mention `jobs` I am referencing the programs that are submitted to the SLURM manager to run by users.

Depending on the priority of your user and how much you have used, you may have to wait to run jobs. There is also some resources that you can use that won't count towards your usage, like slower GPUs or only one or two CPU cores.

#### First, lets go over some simple commands to get information about the resources you have used.
*(you need to make sure you are not in an anaconda environment when running these commands!)*
```bash
$ myusage
Usage for Account mshah  on the Newton cluster for   start=2020-05-01T00:00:00   end=2020-06-01T00:00:00
==================================================
  CPU Used:  39,006.3  hours of  70,000.0  (55.7%)
  GPU Used:  3,694.1  hours of  16,000.0  (23.1%)
==================================================
                USER               CPU-Hours   GPU-Hours
                ----               ---------   ---------
              mrizve                37,320.6   3,483.3
             *bsilva                     0.1   0.0
         ptirupattur                 1,685.6   210.7
```

My account is a bit different since it is under Dr. Shah's available resources, but the information is the same. For everyone in the REU, you have a shared max CPU and GPU hour limit to use. You can view everyone's usage of these resources using the `myusage` command. These are reset monthly, on the first.

Next, lets view our hard disk usage:
```bash
$ myquota
Disk quotas for usr bsilva (uid 5890):
     Filesystem    used   quota   limit   grace   files   quota   limit   grace
    /lustre/fs0  394.5G    500G    510G       -  495689  1000000 1010000       -
```

This views your total available disk space and what you have used so far. If you want to see total what is available in terms of storage space to the system, run this:

```bash
$ df -h
```

#### Next, lets go over some SLURM commands for managing your jobs.

Lets get some basic information on the available compute nodes.

```bash
$ sinfo
PARTITION   AVAIL  TIMELIMIT  NODES  STATE NODELIST
normal*        up   infinite      1 drain$ evc15
normal*        up   infinite     19    mix evc[1-14,16-20]
preemptable    up 2-00:00:00      1 drain$ evc15
preemptable    up 2-00:00:00     19    mix evc[1-14,16-20]
display        up   infinite      1  down$ evd1
```

Partitions:
* normal – the default partition
* preemptable – a partition that doesn’t charge against an allocation but on which jobs can be preempted
* display – a special partition set aside for a machine in our workroom for display purposes

States:
* **drain** means offline
* **down** means down
* **mix** means some, but not all cores are allocated
* **alloc** means completely busy
* **idle** means all cores are available

Lets now take a look at what jobs are running.

```bash
$ squeue
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
59233    normal mcha_tss     mcha PD       0:00      1 (Priority)
59228    normal mcha_tss     mcha PD       0:00      1 (Resources)
59229    normal mcha_tss     mcha PD       0:00      1 (Priority)
59230    normal mcha_tss     mcha PD       0:00      1 (Priority)
59231    normal mcha_tss     mcha PD       0:00      1 (Priority)
59232    normal mcha_tss     mcha PD       0:00      1 (Priority)
59238    normal mcha_tss     mcha PD       0:00      1 (Priority)
59234    normal mcha_tss     mcha PD       0:00      1 (Priority)
59235    normal mcha_tss     mcha PD       0:00      1 (Priority)
59236    normal mcha_tss     mcha PD       0:00      1 (Priority)
59237    normal mcha_tss     mcha PD       0:00      1 (Priority)
59244    normal  run1_v9 ptirupat PD       0:00      1 (Priority)
59227    normal mcha_tss     mcha  R    3:19:24      1 evc7
59225    normal mcha_tss     mcha  R    3:21:49      1 evc6
59226    normal mcha_tss     mcha  R    3:21:49      1 evc7
59223    normal mcha_tss     mcha  R    3:21:52      1 evc5
59224    normal mcha_tss     mcha  R    3:21:52      1 evc6
59221    normal mcha_tss     mcha  R    3:21:55      1 evc11
...
```

Here you can view the JOBID, the user who submitted the job, its current state, and the time it has been running.

Possible States:
* **R** means running.
* **PD** means pending; there isn't resources available to run the job so its in queue.
* **CG** means completing; the job is wrapping up.

The other important thing is the JOBID, which you can use to cancel a job. You can only cancel jobs you submitted. To cancel a job:

```bash
$ scancel <jobid>
```

Let's get more information about a job:

```bash
$ scontrol show job <jobid>
```

Let's move on to how to submit jobs to SLURM.

## SRUN and SLURM Scripts
There are two ways to run our jobs, using `srun` or `sbatch` with SLURM scripts. **SRUN is highly discouraged to run your jobs.** It is good to do some testing and make sure your code is working, but not for running jobs for many hours. There a multiple issues, the main one being that once your program is done with srun the resources are still allocated to you until you logout, which makes them unavailable to other people. The IT administration of Newton will cancel SRUN jobs that are running for a long time, so instead we use `sbatch` with SLURM scripts.

#### SLURM scripts
A SLURM script is a shell script that specifies some information about what resources you need to run the program, environment setup and the commands to actually run the program.

Lets just look at an example script and break down each part. For reference, lines marked `#SBATCH` are SLURM arguments for specifying different things like amount of resources needed, job name, etc. Everything else are commands that are ran just like the shell scripts we looked at in the command line tutorial.

This script below contains everything you need to run a job, and also some extra statistics and file organization to keep things from getting hetic.

```bash
#!/bin/bash

## Define resources to allocate

## nodes define how many compute nodes we want to allocate
#SBATCH --nodes=1

## number of CPU cores to allocate per task in the ntask flag
#SBATCH --cpus-per-task=4

## this defines the amount of RAM (in MB) to allocate per CPU specified above.
## so in this example thats 4 * 8192 = 32 GB
#SBATCH --mem-per-cpu=8192

## number of tasks, this is run your program `ntasks` number of times, so usually
## keep it at 1
#SBATCH --ntasks=1

## gres is a generic argument to grab resources define by the SLURM manager
## in this case, we want gpus, and the :1 specifies how many gpus we want.
#SBATCH --gres=gpu:1

## increase gpu to 2 for 2 gpus, increase nodes by 1 if using more than 2 gpus
## there are 2 GPUs per compute node, so if we want to use more than 2 GPUs we need to
## tell SLURM we want to allocate more than one compute node

## by default, jobs are only given 10 minutes to run, so specify the run time below
## I usually just keep it to a long time and let the program exit on it's own
## Nothing should take longer than 5 days
#SBATCH --time=120:00:00

## Put name of job here (shows up in queue)
#SBATCH --job-name=

## Fill in <NAME> here (name of files created for logs)
## it doesn't necessarily have to be the name defined above
## error contains output from your program directed to stderr
## output contains output of your program directed to stdout
#SBATCH --error=<NAME>.%J.err
#SBATCH --output=<NAME>.%J.out

## Folder name for saving log files, should be same as job-name defined for SBATCH
export job_name=""

## Select how logs get stored
## log files will be moved to this directory when your job is COMPLETED
## this helps keeps all your logs organized, although not needed.
## its good to hardcode this path so there isnt issues, replace <user> with your username
export log_dir="/home/<user>/job_logs/$job_name-$SLURM_JOB_ID"
mkdir -p $log_dir
export debug_logs="$log_dir/job_$SLURM_JOB_ID.log"
export benchmark_logs="$log_dir/job_$SLURM_JOB_ID.log"

### Environment Setup ###

## Load Modules used for this
## For your work, this is normally what you will need
module load anaconda/anaconda3
module load cuda/cuda-10.2
module load openmpi/openmpi-4.0.0-ic-2019.1.144

## Enter Working Directory ##
cd $SLURM_SUBMIT_DIR

### Get environment information ###
## the below code just prints a bunch of stats about the environment this job
## is running in
## Create Log File ##
echo "Slurm working directory: $SLURM_SUBMIT_DIR" >> $debug_logs
echo "JobID: $SLURM_JOB_ID" >> $debug_logs
echo "Running on $SLURM_NODELIST" >> $debug_logs
echo "Running on $SLURM_NNODES nodes." >> $debug_logs
echo "Running on $SLURM_NPROCS processors." >> $debug_logs
echo "Current working directory is `pwd`" >> $debug_logs

## Module debugging ##
echo "Modules loaded:" >> $debug_logs
module list >> $debug_logs
echo "mpirun location: $(which mpirun)" >> $debug_logs

echo "Starting time: $(date)" >> $benchmark_logs
echo "ulimit -l: " >> $benchmark_logs
ulimit -l >> $benchmark_logs

### Running the program ###

## Select python File to run
export file=""

## Define any program arguments
export args=""

## Define location to python
## Activating anaconda environments in slurm scripts sometimes fails, so its better to
## just define explictly which python program to use.
## to get the location of the python for your environment, first activate your env
## and then run `which python`
## then copy that path to here.
export python=""

## Run job ##
## the `nvidia-smi` command prints the information about the GPUs that are allocated
## the `time` command will measure how long your program took to ran and output it

## Multi-gpu:
#nvidia-smi && time mpirun -np $SLURM_NTASKS $python $file $args
## Single-gpu:
nvidia-smi && time $python $file $args
sleep 3

echo "Ending time: $(date)" >> $benchmark_logs
echo "ulimit -l: " >> $benchmark_logs
ulimit -l >> $benchmark_logs

## Directory Cleanup ##
## move output files to the log directory to cleanup
mv $job_name.$SLURM_JOB_ID.err $log_dir
mv $job_name.$SLURM_JOB_ID.out $log_dir
```

Save the file as `job.slurm`. Then, make it executable:
```bash
$ chmod +x job.slurm
```

A few extra things on the script above:
* MPI is a program that is used to distrubute the execution of your program across multiple nodes, and is needed when using more than one node. It provides an interface for the code executing on each node to communicate with each other. Since the program isn't running on a single computer the code is ran in parallel on multiple nodes and the output is put together using MPI.
* This specific script runs a python program, but if you want to run something else just replace the python program with whatever else you want to run.

Thats the basics and some extras for SLURM scripts! Lets submit the job now.

***IMPORTANT:*** This SLURM scripts assumes you run the command below **in the same directory where your code is stored**. So, for example if your python code is supposed to be ran in the `~/mycode/` directory, you need to `cd` to that directory, put the job.slurm in that directory, then run the below command.

```bash
$ sbatch job.slurm
```

**Note:** the same options you use in the script file marked with `#SBATCH` you can use as **flags and arguments** for the command line `srun` program, explained below.

#### SRUN
You can use the same arguments above in an interactive program called `srun` to run your jobs. It's usage is simple:
```bash
$ srun <SLURM args> program to run
```
You'll need to activate your anaconda environment and load your modules before running this. You also need to stay logged in while the program runs, as its ran interactively in your current login shell and will quit if you disconnect. There are ways to run this in a virtual shell so you can log out, but I won't go over this here since it isn't really needed.

Lets do a quick example to print out the GPU allocated to us.

```bash
$ srun --nodes=1 --gres=gpu:1 --ntasks=1 nvidia-smi
```

## Managing/Viewing SLURM output files
If you follow the script above, while your program is running the `.err` and `.out` files will contain the output of your program as it runs, to stderr and stdout respectively. It is constantly appened to by the SLURM manager. If you want to view the output of the program as it is running, you can use the `tail` command.

Tail is used to view the "tail" of a file, meaning the last few lines of the file. It has a special flag `-f` that means "follow", which means your terminal will be constantly updated with the new contents to the file as it is being written to.

```bash
$ tail -f job-name.job-id.out
```

## And thats all folks!
That is the basics of running your code on Newton. Feel free to play around with the slurm script, by no means is it exhaustive of what you can do with SLURM.
