# Demeter

## Software requirements:

 - A whole slurm cluster (with or without slurmdbd)
 - An ElasticSearch database
 - Cgroup plugin (optional) to gather memory and cpu usage data
 - Slurm source code
 - Slurm lib
 - Ibmad lib
 - git
 - Make

##  How to install Demeter:

First things first, you will want to compile the plugins by yourself, preferably on a machine that has the same configuration as the one that will run the plugin.

### lib_demeter:

First, you need to clone the repository that contain all of lib_demeter sources
```
git clone https://github.com/sOmEoNe2lOvEgIt/lib_demeter.git
```
In the makefile provided, you can see some global variables (like "SLURM_INC_DIR" or "LIBIBMAD_DIR"), theses need to be set to the directories that match the variables names.

SLURM_INC_DIR should be set to the lowest directry of the slurm sources, the "slurm.build" directory.

IBMAD_INC_DIR should be set to infiniband include path (generaly located at "*/usr/include/infiniband/*").

LIB_SLURM should be se to slurm's subdirectory in your PATH's lib directory (like /usr/lib64/slurm).

Good! Now you can try to compile the lib whith the make command in the lib directory. You may need to resolve some more dependencies.

If everything went according to plan, you can now move the lib file "libdemeter.so" that was created wherever you want as long as it is readable by the slurm user.

### prep_demeter:


