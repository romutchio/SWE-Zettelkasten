# What is the container? 
Container is a linux process with additional configurations. Use: docker top <container-name> to get PID

# /proc
In Linux, the /proc is a directory on Virtual File System which contains the files and configurations that belongs to each process.  

# Namespaces
The Namespace is a feature of the Linux Kernel, which limits what processes can access certain resources of the system.  
The purpose of each namespace is to wrap a particular global system resource (process IDs, hostnames, user IDs, file names, and some names associated with network access, and interprocess communication) in an abstraction that makes it appear to the processes within the namespace that they have their own isolated instance of the global resource.  
Currently, Linux kernel 8 different types of namespaces: [More Info](https://en.wikipedia.org/wiki/Linux_namespaces)  
* Mount (mnt) - Mount namespaces control mount points. 
* Process ID (pid) - The PID namespace provides processes with an independent set of process IDs (PIDs) from other namespaces. PID namespaces are nested, meaning when a new process is created it will have a PID for each namespace from its current namespace up to the initial PID namespace. Hence the initial PID namespace is able to see all processes, albeit with different PIDs than other namespaces will see processes with.
* Network (net) - virtualize the network stack. On creation a network namespace contains only a loopback interface.
* Interprocess Communication (ipc)
* UTS (hostnames) - allow a single system to appear to have different host and domain names to different processes.
* User ID (user) - provide both privilege isolation and user identification segregation across multiple sets of processes available 
* Control group (cgroup) - hides the identity of the control group of which process is a member. With cgroups, the Linux operating system can easily manage and monitor resource allocation for a given process and set resource limits, like CPU, memory, and network limits.
* Time

Docker containers also have network isolation (via libnetwork), allowing for separate virtual interfaces and IP addressing between containers.

# Union File System
Docker uses the union file system to create and layer Docker images.  
This means all images are built on top of a base image, actions are then added to that base image.  
For example, RUN apt install curl creates a new image. 
Under the hood, it allows files and directories of separate file systems, known as branches, to be transparently overlaid, 
forming a single coherent file system. When branch contents have the same directory, the contents are merged and seen together the directory.  
  
When building an image, Docker will save these branches in its cache. 
A benefit is, if Docker detects the command to create a branch or layer already existing in the cache, it will re-use the branch, not the command, which is a cache hit. This is known as docker layer caching.

# libcontainer / runC
The C library that provides the underlying Docker functionality is called libcontainer, or now known as runC.  
For each execution, each container gets its own root file system, /tmp/docker/[uuid]/, and the underlying host does not let the process leave that directory, also called a chroot jail.  
One of the process variables that happens when starting the operating system is the current working directory. A chroot jail is where the operating system prevents that started process from accessing a parent directory, such as ../).
  
