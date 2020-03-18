## Running PhyDyn on Imperial HPC
### Register to use HPC
 Follow Imperial [getting started](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/support/getting-started/) instructions to register for the HPC cluster and log in.

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

To run BEAST on the cluster you will need to load your XML and produce a shell script to interact with the queuing system on HPC. An example shell script is below.

```
#PBS -l walltime=55:00:00
#PBS -l select=1:ncpus=2:mem=5gb
#PBS -o beastrun.stdout
#PBS -e beastrun.stderr

# beast and java required, beagle optional to optimise the performance
module load anaconda3/personal
source activate PhyDyn

export _JAVA_OPTIONS="-Djava.io.tmpdir=$TMP"

# copy required xml file to the TMPDIR on the computer being used
cp $PBS_O_WORKDIR/<NAME>.* $TMPDIR

beast -overwrite  -beagle_SSE <NAME>.xml

# copy files back to the home dir


cp <NAME>.* $PBS_O_WORKDIR/
```
Considerations:
1. Replace `<NAME>` with the XML file name
2. Exporting Java options seems to be required to allow BEAST to write files
3. Adjust walltime and resources to match your job

Full instructions for submitting and monitoring jobs, choosing resources and a runnable template are available on the Imperial RCS [getting started](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/support/getting-started/) pages.
