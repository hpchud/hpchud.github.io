---
layout: page-toc
title: How to use Lustre scratch space on the QGG
permalink: /docs/how-to-use-lustre
---

{% include JB/setup %}

<div class="row">
<div class="col-md-2">
<section id="table-of-contents" class="toc">
<header>
<h3>Contents</h3>
</header>
<div id="drawer" markdown="1">
* generate table of contents
{:toc}
</div>
</section>
</div>
<div class="col-md-10" markdown="1">

# How to use the Lustre scratch space on the QGG

## Introduction

The Lustre scratch space on the QGG provides a filesystem that offers improved parallel performance and an increase of 18TB in storage, for a total of 38TB, over the previous Gluster based solution. It is optimised for storage of large files (> 100MB).

Please take some time to read this document and familiarise yourself with Lustre, so that you can get the best performance out of the system for your jobs.

### Why is it faster?

The previous Gluster solution was a single server that handled every request to read and write files from the cluster nodes.

```

    ------------------                    -----------
    | Gluster server |                    | Eridani |
    ------------------                    -----------
            |                                  |
            ------------------------------------

```

The Lustre provides a `Metadata server`, which directs the client to read and write from one or more `Object Storage Servers`. When you save data to the Lustre filesystem, you can choose how it is distributed among the storage servers. This is why it can achieve much higher parallel performance (when more than 1 process or node reads and writes to the same files).

```

    -------------------              ---------        ---------           ---------
    | Metadata server |              | OSS 1 |        | OSS 2 |   . . .   | OSS n |
    -------------------              ---------        ---------           ---------
             |                           |                |                   |
             ------------------------------------------------------------------
                                               |
                                          -----------
                                          | Eridani |
                                          -----------
```

### What should I use it for?

- Storing large input data files
- Storing large output data files
- As the working directory for jobs that create large temporary files during execution

### What should I not use it for?

- Thousands of small files - Lustre is less efficient when reading and writing to a very large number of small files

## Find your scratch space

The Lustre scratch space is mounted under `/scratch`.

Inside this directory, there are two more directories: `/scratch/sol` and `/scratch/universe`. We will come back to those in a moment.

At the 3rd level directory, you will find your username, e.g. `/scratch/universe/u1056048`. This is your scratch space - it has the same permissions as your home folder.

### sol or universe?

Inside both the `/scratch/sol` and `/scratch/universe` directories, you will find a directory with your username that you can use for scratch space. They are just two different folders that both reside on the same Lustre filesystem. However, the distribution policy for the files you save in each one is different.

The Sol cluster is geographically separated from the rest of the QGG. Therefore, a storage server is also positioned next to Sol. Inside the `/scratch/sol` directory, Lustre will prefer to place your files on the storage server next to Sol. This avoids the long communication distance.

If you plan to use Sol and require the best storage performance, use the `/scratch/sol/<username>` folder. Otherwise, or if you do not care, use the `/scratch/universe/<username>` folder.

#### In summary, unless you are using Sol, the path to your scratch space is `/scratch/universe/<username>/`.


## How to use it

### The easy way

If you have prepared a job in your home folder, you can simply copy the folder to your scratch space and submit the job from there. Remember, the scratch space has no backup, so you should leave a copy of the job submission files in your home folder too.

An example:

```
# pwd
/home/u1056048/myfirstjob
# ls
hello.exe
submit.job
# cp -r /home/u1056048/myfirstjob /scratch/universe/u1056048/
# cd /scratch/universe/u1056048/myfirstjob
# qsub submit.job
```
In this example, we copied the whole `myfirstjob` directory that contains both the submission script, and an executable, to the scratch space. We then changed directory to the scratch space and issued the `qsub submit.job` command.

### The harder way

You can avoid copying files to and from the scratch space by modifying your submission script.

In general, it should be sufficient to replace `cd $PBS_O_WORKDIR` with `cd /scratch/universe/<username>/output` (assuming the folder named output has been created in your scratch space).

Any references to files in the submission directory can be then prefixed with `$PBS_O_WORKDIR/`. Take a look at the example below.

*Original*

```
#!/bin/bash

# set the number of nodes and processes per node
#PBS -l nodes=4:ppn=4

# set name of job
#PBS -N JoshsJob

# use submission environment
#PBS -q paraul

# start job from the directory it was submitted
cd $PBS_O_WORKDIR

# Load the module you need
module load mpi/openmpi

# define MPI host details
nprocs=`wc -l $PBS_NODEFILE | awk '{ print $1 }'`

# run through the mpirun launcher
mpirun -np $nprocs -machinefile $PBS_NODEFILE ./hello
```

*Modified*

```
#!/bin/bash

# set the number of nodes and processes per node
#PBS -l nodes=4:ppn=4

# set name of job
#PBS -N JoshsJob

# use submission environment
#PBS -q paraul

# use scratch space (create the subfolder first!)
cd /scratch/universe/u1056048/test

# Load the module you need
module load mpi/openmpi

# define MPI host details
nprocs=`wc -l $PBS_NODEFILE | awk '{ print $1 }'`

# run through the mpirun launcher
mpirun -np $nprocs -machinefile $PBS_NODEFILE $PBS_O_WORKDIR/hello.exe

```

</div>
</div>