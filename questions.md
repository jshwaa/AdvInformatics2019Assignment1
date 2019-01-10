# Questions

## HPC good citizenship

1. On the UCI cluster, the resource request "-pe openmp 64" refers to the number of processors requested.  Does that
   request mean that your commands will use multiple processors?
   
   __No. 64 processors will be allocated, but your command may still only use one core__
   
2. In general, how do you know how many processors, how much RAM, how many files would be required/needed/written by the
   jobs you are running on the cluster?
   
   __You can figure out how many processors are required, RAM needed and files written by the job by prefixing the program command you are running on the cluster with ```/usr/bin/time -v```. To estimate this before running a job to get an idea of the resources you should request, you can perform this same operation on a smaller set of input data than you would run in your actual job, and then scale the resource information it provides up (i.e. use "Percent of CPU this job got", "Maximum resident set size (kbytes)", and "File system outputs" from the run on a smaller set of data to estimate processsor, RAM, and file requirements needed when the real job is run.__
   
3. In order to be a "good citizen", you need to have some idea of much RAM your job requires.  In particular, you need
   to know the "peak" (i.e., maximum) RAM required at any point during execution.  Show an example of the shell command
   that you would use on a Linux machine to measure run time and peak ram usage of an arbitrary command, where the time/peak RAM values    are written to a file.
   
   __An example of a shell command that measures and records run time and peak ram usage to file would be:__
   
   ```{ /usr/bin/time -v command >/dev/null;} 2>&1 | grep -E "Maximum|Elapsed" > memtime.txt``` Where "command" is the arbritrary command being run.
   
   __If we were to do this with a fasta file containing all _D. melanogaster_ chromosome sequences, the command and results would look like this__:
   
    
    ```
    { /usr/bin/time -v grep "A" dmel-all-chromosome-r6.24.fasta >/dev/null;} 2>&1 | grep -E "Maximum|Elapsed" > memtime.txt
    less memtime.txt
    Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.27
    Maximum resident set size (kbytes): 2088
    ```
    
4. What are the units of your answer for number 3?

   __The units for peak RAM usage (i.e. Maximum resident set size) is kilobytes; units for run time are minutes:seconds__

5. What are the bash commands for the following operations:

    * Checking that a file exists
    
    __In the directory of interest:__
    
    ```ls filename```
    
    * Checking that a file exists and is not empty
    
    __In the directory of interest:__
    
    ```ls -l filename```

6. How would you use the commands from your answer to 5 to write a work flow for HPC that only runs a job if the
   expected output file is **not** present.

   __Using ls to generate a list of files in a directory, you could see whether a file exists and, if not, run a job with the following work flow:__

   ```ls | if [ ! -e filename ]; then echo "The file is not present; the job will be run"; fi```
   
   __Where "filename" is the expected output file the echo command may be replaced by any job run on the HPC to generate that output file__

## Trickier questions

7. Would your answer to number 3 work on Apple's OS X operating system?  If no, do you have any idea why not? 

   __No, because the answer to number 3 depends on the -v "verbose" flag for the /usr/bin/time command, which is a GNU implementation that does not exist on OS X. You would need to use homebrew to install the equivalent GNU command.__

8. Most of the HPC nodes have 512Gb (gigabytes) of RAM. Let's say you have a job that will require **no more** than 24Gb
   of RAM.  How would you request resources so that you can run more than one job on a node **and** not cause nodes to
   crash?  Show an example of a skeleton HPC script as part of your answer.  **Knowing how to do this is super important
   and will save you loads of frustration and prevent you from taking out your colleagues' jobs on the cluster,
   preventing you from getting nasty emails from Harry!!!!!!!!!!!**


   ```
   #!/bin/bash
   #$ -N TEST
   #$ -q free*,pub64
   #$ -pe openmp 8-64
   #$ -m beas
   #$ -l mem_size=48G
   
   qsub coolscript1.sh
   qsub coolscript2.sh
   ```
   
   __You would specify the total memory you would use for the job with ```-l mem_size=48G``` if you were running two 24Gb jobs on the node, increasing by 24gb for every additional job you want to run. Then, just ```qsub 2awesomejobs.sh```.__
