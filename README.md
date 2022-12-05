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
In the makefile.inc provided, you can see some global variables (like "SLURM_INC_DIR" or "LIBIBMAD_DIR"), theses need to be set to the directories that match the variables names:

 - SLURM_INC_DIR should be set to the lowest directry of the slurm sources, the "slurm.build" directory.
 - IBMAD_INC_DIR should be set to infiniband include path (generaly located at "*/usr/include/infiniband/*").
 - LIB_SLURM should be se to slurm's subdirectory in your PATH's lib directory (like /usr/lib64/slurm).

Good! Now you can try to compile the lib whith the make command in the lib's source directory. You may need to resolve some more dependencies.
```
make
```
If everything went according to plan, you can now move the lib file "libdemeter.so" that was created wherever you want as long as it is readable by the slurm user. (/!\ The lib has to be on all of the slurm components that use demeter's plugins or ask_demeter, on each slurmd, each slurm controller, slurmdbd, you could create a shared folder for easyer setup /!\)

### prep_demeter:

First, clone the repository that contains all of prep_demeter sources
```
git clone https://github.com/sOmEoNe2lOvEgIt/prep_demeter.git
```
In the makefile.inc:

 - SLURM_INC_DIR should be set to the root directry of the slurm sources, the "slurm.build" directory.
 - DEMETER_LIB_DIR should be set to lib_demeter's sources directory
 - DEMETER_RUN_PATH should be set to lib_demeter's binary parent folder.

Good! Now you can try to compile the plugin whith the make command in the plugin's sources directory. You may need to resolve some more dependencies.
```
make
```
If everything went according to plan, you can now move the plugin's binary "prep_demeter.so" that was created in slurm's plugin directory. Also add the plugin to slurm's configuration file by adding ```PrepPlugins=prep/demeter```.

/!\ The plugin has to be on all of the slurm components, on each slurmd, each slurm controller, slurmdbd, you could create a shared folder for easyer setup /!\

### task_demeter:

First, clone the repository that contains all of task_demeter sources
```
git clone https://github.com/sOmEoNe2lOvEgIt/task_demeter.git
```
In the makefile.inc:

 - SLURM_INC_DIR should be set to the root directry of the slurm sources, the "slurm.build" directory.
 - DEMETER_LIB_DIR should be set to lib_demeter's sources directory
 - DEMETER_RUN_PATH should be set to lib_demeter's binary parent folder.

Good! Now you can try to compile the plugin whith the make command in the plugin's sources directory. You may need to resolve some more dependencies.
```
make
```
If everything went according to plan, you can now move the plugin's binary "task_demeter.so" that was created in slurm's plugin directory. Also add the plugin to slurm's configuration file by adding ```TaskPlugin=task/demeter```. Make sure that the demeter plugin is specified first because in recent SLURM version, the cgroup plugin can prevent demeter from functioning propperly in the case of an OOM event.

(/!\ The plugin has to be on all of the slurm components, on each slurmd, each slurm controller, slurmdbd, you could create a shared folder for easyer setup /!\)

### ask_demeter:

First, clone the repository that contains all of ask_demeter sources
```
git clone https://github.com/sOmEoNe2lOvEgIt/ask_demeter.git
```
In the makefile.inc:

 - SLURM_INC_DIR should be set to the root directry of the slurm sources, the "slurm.build" directory.
 - DEMETER_LIB_DIR should be set to lib_demeter's sources directory
 - DEMETER_RUN_PATH should be set to lib_demeter's binary parent folder.

Good! Now you can try to compile the binary whith the make command in the binary's sources directory. You may need to resolve some more dependencies.
```
make
```
If everything went according to plan, you can now move the binary that was created somewhere accessible in your PATH (for ease of use).
