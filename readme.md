## Running PhyDyn on Imperial HPC
### Register to use HPC
 Follow Imperial [getting started](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/support/getting-started/) instructions to register for the HPC cluster and log in.
 
 On Windows use the [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) shell to log in and interact with the cluster. Follow the instructions [here](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/rds/) to set up your networked home directory for the HPC cluster.

 ### Setting up a BEAST 2.6.2 environment
 HPC staff no longer update most of the installed programs on the cluster. The easiest way to use more recent versions of these programs is to run them in an [anaconda](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/support/applications/conda/) environment.

 To get started with anaconda run the following in the login node:
 ``` module load anaconda3/personal ```
 If it is your first time using anaconda on the cluster you will also need to run
``` anaconda-setup ```
 You can then create your BEAST environment and name it using the -n tag. I have named my environment PhyDyn.
 ``` conda create -n PhyDyn beast2=2.6.2 ```
 You will then need to load (activate) this environment and install packages
 ``` 
 source activate PhyDyn
 conda install beagle
 packagemanager -add PhyDyn 
 ```
You can then deactivate this environment
``` source deactivate PhyDyn ```

To run BEAST on the cluster you will need to load your XML and produce a shell script to interact with the queuing system on HPC. A commented simple shell script is below. An example array script is included [here](https://github.com/JorgensenD/BEAST_CLUSTER/blob/master/qsub_anaconda_array_resub.pbs) to submit BEAST2 array jobs on the cluster.

```
 ## Selecting resorces required 
#PBS -l walltime=55:00:00
#PBS -l select=1:ncpus=2:mem=5gb
 ## Write location for output and errors ~ important for debugging 
#PBS -o beastrun.stdout
#PBS -e beastrun.stderr
 ## These can be merged instead:
#PBS -j oe

 ## load in the PhyDyn environment created earlier
module load anaconda3/personal
source activate PhyDyn

 ## Java occasionally fails trying to write to it's own temporary directory - this forces it to write to the HPC attached storage
export _JAVA_OPTIONS="-Djava.io.tmpdir=$TMP"

 ## copy required files to the temporary directory on the compute node
cp $PBS_O_WORKDIR/<NAME>.* $TMPDIR

 ## Run beast using command line tags
beast  -beagle_SSE <NAME>.xml

 ## copy files back to the submission directory - anything not copied back will be lost
cp * $PBS_O_WORKDIR/
```
Considerations:
1. Replace `<NAME>` with the XML file name
2. Adjust walltime and resources to match your job
3. A list of command line tag options for beast can be seen by running `beast -help` inside your conda environment

Full instructions for submitting and monitoring jobs, choosing resources and a runable template are available on the Imperial RCS [getting started](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/support/getting-started/) pages.

