# Newton and Environments

Author: [@brandons209](https://github.com/brandons209)

This tutorial will cover how to work with the Newton GPU cluster and how to manage your program environments. There is a lot that goes into how Newton works, but ultimately not much is needed to get your code running properly!

But first, lets talk about:

## Transferring Files to and from Newton
You'll need to get your local code up to Newton, and also pull output files from Newton to your local machine. There a few ways to do this.

#### Using a GUI
You can use a program like `MobaXterm` that has a built in file explorer to easily pull and push files to/from Newton. There are many programs to do this, and I'll leave you to find something you like to use.

#### Command Line
With OpenSSH, we have two programs we can use for transferring files: `scp` and `sftp`.

`scp` is a command to transfer files/folders to and from Newton, it is not interactive, you need to know what files you want locally or on Newton when running the command.

`sftp` on the other hand is an interactive program to get and send files to Newton, which is good if you want to explore your directories and pull specific files one at a time.

These actually use different protocols, so you can look up exactly how each of these work.

Basic scp useage (`-r` flag for directories):
* Send: `scp localfile newton:<remote directory on newton>`
  * `scp -r localdirectory newton:~/`
  * This sends the folder localdirectory to your home directory on newton
* Receive: `scp newton:<remote file> localdirectory`
  * `scp -r newton:~/code .`
  * This receives the entire code folder on Newton and moves to the current local directory you are in.

The `scp` command uses the same host names we defined in our config file in the `.ssh` directory. Since we defined the alias newton to log into newton, we use it here to easily send and receive files.

Now, lets try `sftp`:

```bash
$ sftp newton
+===================================================+
| Welcome to the Newton GPU cluster  at the UCF     |
| Advanced Research Computing Center.               |
|---------------------------------------------------|
|  Newton in operational.                           |
|---------------------------------------------------|
| Problems? Email:  arcc-request@ist.ucf.edu        |
+===================================================+
Connected to newton.
sftp>
```

As you see, `sftp` has its own prompt and commands to use. Type `help` to get a list of commands.
You can't run most programs (except commands like cd and rm) in this `sftp` prompt.

Our bread and butter commands are:
* `put localfile remotefile` - put a file on our local computer to newton
* `get remotefile localfile` - get a file on newton and put it on our local computer
* `pwd` - get working directory on the remote computer
* `ls` - check directory on the remote computer
* `cd` - change directory on the remote computer
* `lcd` - cd directory on the local computer
* `!` - this will run a command in a shell prompt, like if you logged in normally into Newton.

To move around your local computer for files use `lcd` and `lls` to check local files. And to browse the remote computer use the standard `cd` and `ls`. Basically, if you want to run a command on the local computer you add a `l` to the beginning of the command.

Try it out now!

## Environments in Newton
There will be two aspects to our environments in Newton, our **modules** and our **anaconda env**.

#### Modules
To manage all the different versions of programs and libraries people need, Newton has "modules" which are specific programs of multiple versions so you always have the right one available for your use without the pain of dealing with PATH variables and malformed environments.

To access these modules, we use the `module` command.

Lets see what modules are available on Newton.
```bash
$ module avail
```

This will bring up a huge list. Some important ones we will always be using is `anaconda/anaconda3` and the different `cuda` and `cudnn` versions.

To load a module, use `module load`.   
Lets load our anaconda3 module
```bash
$ module load anaconda/anaconda3
Please run `conda env list` to see a list of all available environments. Use `source activate <env>` to activate the environment '<env>'.
```

To know what modules you have loaded, use `module list`
```bash
$ module list

Currently Loaded Modules:
  1) anaconda/anaconda3

```

You can unload modules using `module unload` or `module purge` to clear all your currently loaded modules.

#### Anaconda
Using anaconda from the command line is very straight forward, and pretty much required to make sure you have the proper environment. Newton has a much of built in anaconda environments with standard libraries installed, but I find that its better to just maintain your own environments because you can't modify Newton's default environments.

[Anaconda starting tutorial](https://docs.conda.io/projects/conda/en/latest/user-guide/getting-started.html)

Lets create a basic machine learning environment that will be used for most programs. When creating environments, you can specify want packages you want and what version of python you want. Let's use the latest python version.

Use the `-n` flag to specify the environment name.
```bash
$ conda create -n ml python=3.8
Aborted (core dumped)
```

You might get an `Aborted (core dumped)` error when running this. This is because anaconda needs more system resources like CPU to run then we are currently using. This ties into the next section and allocating resources for programs, for now just run this to create the environment.

```bash
$ srun --time=03:00:00 conda create -n ml python=3.8
```

Press enter once all the packages that it will install shows up, and anaconda will download all the libraries it needs and setup your environment.

To activate the environment so we can use it, use `source activate`,

```bash
$ source activate ml
(/home/username/my-envs/ml) $
```

You know it works since you'll see your environment name prefacing your bash prompt.

To install packages, you can use the standard python `pip`, however its recommended to install anaconda versions of the packages when possible. Make sure you run the commands to install packages while your environment is **activated**.

Here are some basic libraries you might need (remember to use srun if the conda command fails):

Graphing:
```bash
$ srun --time=03:00:00 conda install -c conda-forge matplotlib
```

PyTorch:
```bash
$ srun --time=03:00:00 conda install pytorch torchvision cudatoolkit=10.2 -c pytorch
```

Tensorflow/Keras:
```bash
$ srun --time=03:00:00 conda install -c anaconda tensorflow-gpu
```

Now that our environment is setup, lets learn how to run our programs using Newton's CPU and GPU resources.
