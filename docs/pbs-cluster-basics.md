---
layout: page-toc
title: PBS cluster basics
permalink: /docs/pbs-cluster-basics
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

# PBS cluster basics and FAQs

## New user warning

- If you have never used a cluster before, or are not familiar with clusters or Linux in general, please take time to read the documentation pages. 
- Do not try and run jobs without understanding how to submit them. Carelessness will impact hundreds of jobs and other users running on the cluster.
- If any actions compromise the HPC service, your account will be disabled.

## PBS Scheduler

Our Linux clusters run the [Torque Resource Manager](http://www.adaptivecomputing.com/products/open-source/torque/). When scheduling a job/simulation/render task on the Linux cluster, a PBS script needs to be written. The job scheduler parses this for instructions (such as how many cores to use) and then passes the remaining instructions to the command line for execution once the Job starts. 

This behaviour is one of the major reasons PBS is the preferred job scheduler on the QGG. PBS scripts can be written in any terminal scripting language. As 'BASH' is the default shell environment in the QGG this tutorial will use bash syntax.

### Queue Definitions

Each job must be assigned to a queue that determines how long the job can run for and thus it's priority.

| Queue name | Maximum time | Maximum nodes |
|------------|--------------|---------------|
| serialsrt  | 6 hours      | 1 node        |
| serialstd  | 48 hours     | 1 node        |
| parasrt    | 12 hours     | 4 nodes       |
| parastd    | 48 hours     | 4 nodes       |
| serualul   | 1000 hours   | 1 nodes       |
| parabig    | 48 hours     | 16 nodes      |
| paraul     | 1000 hours   | 80% nodes     |

The queues are listed in top down priority order. This means that the scheduler will prefer to complete jobs waiting in the short queues before the long queues.

### Good file management practice

It is good practice to keep all the files related to a job in their own folder. An example file structure is shown below

```
/home/username/myfirstjob
                         /submit.job
                         /executable_name
```

## Writing a submission script

A PBS job submission script is just a simple shell script. At the top a number of headers must be specified to choose the correct queue and number of cores.

### Recommended headers

```
#/bin/bash

#PBS -l nodes=X:ppn=Y
#PBS -N jobname
#PBS -q queuename
```

- Replace X with the number of nodes and Y with number of cores in each node (processors per node)
- Replace `jobname` with an identifier for this job
- Replace `queuename` with one of the queues from above

### Optional headers

```
#PBS -e stderr-filename
#PBS -o stdout-filename
#PBS -j oe
```

The previous 3 options allow a user to specify a special file name for the standard error (-e) and for the standard output (-o). These can be set to appear as a more useful name but it is not recommended as on each run it will overwrite the previous logs unless the file name is changed. By default it uses the job number and appends an extension 'err' for the standard error and 'out' for the standard output. The (-j) option allows the user to combine both files into one - which helps reduce the number of files.

```
#PBS -m abe
#PBS -M john.smith@email.com
```

Instructs PBS to email the user on (a)bort (b)egin (e)nd. The three initials can be used in any combination or order.

### Rest of the script

After the headers are fed in, users are free to enter bash commands to invoke their program, handle file management etc. If you have requested more than 1 node, these commands will be run by the first node assigned by the job scheduler when the job begins to run.

### Putting it all together

#### Example serial job (running on a single node)

```
#!/bin/bash

# set the number of nodes and processes per node
#PBS -l nodes=1:ppn=4

# set name of job
#PBS -N SerialJob

# use submission environment
#PBS -q serialstd

# start job from the directory it was submitted
cd $PBS_O_WORKDIR

# run the executable
./executable_name
```

#### Example parallel job (running on multiple nodes)

```
#!/bin/bash

# set the number of nodes and processes per node
#PBS -l nodes=4:ppn=4

# set name of job
#PBS -N ParallelJob

# use submission environment
#PBS -q paraul

# start job from the directory it was submitted
cd $PBS_O_WORKDIR

# Load the module you need
module load mpi/openmpi

# define MPI host details
nprocs=`wc -l $PBS_NODEFILE | awk '{ print $1 }'`

# run through the mpirun launcher
mpirun -np $nprocs -machinefile $PBS_NODEFILE ./executable_name

```

## CLI tools

### qsub

The `qsub` command submits a PBS script file to the system and takes as an argument the name of the PBS script. In return, the system will print out a job ID.

### qstat

The `qstat` command shows the status of all your jobs running in the queues on the system. 

### qdel

To delete a job from the Batch system the `qdel` command can be used. Supply the job ID as the argument. Only jobs submitted by you can be deleted using this command.

### showq

The `showq` command will show the scheduler status. This allows you to see other jobs that are running on the system and the total resources in use and available.

</div>
</div>
