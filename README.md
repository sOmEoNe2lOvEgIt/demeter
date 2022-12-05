![hoe](https://user-images.githubusercontent.com/87118859/185426997-149c94e8-e33e-4621-b193-81594645686a.png)
# Demeter

Demeter gathers info by reading sys files just after a job ran as well as reading output logs to parse them into more digestible outputs.

## Software requirements:

 - A whole slurm cluster (with or without slurmdbd)
 - An ElasticSearch database
 - Cgroup plugin (optional) to gather memory and cpu usage data
 - Slurm source code
 - Slurm lib
 - Ibmad lib
 - git
 - Make
 - gcc

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

## Conf:
A conf file can be provided to the plugins if placed at /etc/slurm/demeter.conf.

This file has to be readable by the slurm user and has to be present on every slurmd's (slurm nodes) as well as on the slurm controller.s.

This is strictly optional but highly recommended as you will probably need to setup some file paths or your elastic database adress and index and such.

### This file can contain multiple parameters that you can define:

- Verbose: *"Verbose=(uint)"*. Sets the verbose level for the demeter logs.

- LogLevel: *"LogLevel=DEBUG||INFO||WARNING||ERROR||FATAL"*. Sets the logging level for demeter logs.

- LogStyle: *"LogStyle=FANCY||SIMPLE||SYSTEM"*. Sets the logging style for demeter logs.

- SlurmLogLevel: *"SlurmLogLevel=DEBUG||INFO||WARNING||ERROR||FATAL"*. Sets the minimum log level at which the plugin will parse slurm logs.

- SysLogLevel: *"SlurmLogLevel=DEBUG||INFO||WARNING||ERROR||FATAL"*. Sets the minimum log level at which the plugin will parse sys logs.

- LogFilePath: *"LogFilePath=path/to/an/existing/or/non/existing/file"*. Sets a custom path for the log file that demeter outputs in.

- SlurmLogPath: *"SlurmLogPath=path/to/an/existing/folder"*. Sets a custom path for the folder containing the slurm log file that demeter will parse.

- SysLogPath: *"SysLogPath=path/to/an/existing/folder"*. Sets a custom path for the folder containing the system log file that demeter will parse.

- DemeterCompLoc: *"DemeterCompLoc= protocol://link:port/to/the/elastic/database/and/index"*. Sets the link and port that demeter needs to talk with the Elasticsearch database and index the data that it gathered.

- DemeterCompProxy: *"DemeterCompProxy=proxyhost:port"*. Sets a proxy used by demeter to talk with the database if needed to access it. Can be ommited if not needed.

- DemeterCompUsr: *"DemeterCompUsr=username"*. Sets a username for the authentification with the Elasticsearch database.

- DemeterCompPsswd: *"DemeterCompPsswd=password"*. Sets a password for the authentification with the Elasticsearch database.

- TaskPlugin: *"TaskPlugin=true||false"*. Tells demeter if the task plugin is used in pair with the prep plugin.

!!!NEW OPTIONS TO BE ADDED!!!

Note: In slurm conf, specifying the task plugin, it is better to put the task plugin first (in name order), as in cetain vertions of slurm, in the case of an OOM, the Cgroup task plugin stops the execution of any more plugins and prevents demeter gathering memory data (wich can be pretty important in the case of an OOM...). Specifying the demeter plugin first prevents this behaviour.

/!\ Task plugin doesn't work propperly with "AllowedRAMSpace" enabled in cgroup.conf in older slurm. /!\

/!\ The demete.conf file has to be owned by the slurm usr and has to have 600 (or rw*-***-*** ) as it access rights as it holds sensitive data like a password to a user on the elk database. /!\
