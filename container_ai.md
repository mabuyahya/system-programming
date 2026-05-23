this doc is fully ai-generated. 
# linux containers

if you read about containers online, they will tell you that "containers are lightweight virtual machines". this is a lie. it is a complete abstraction that hides what is actually happening in the system.

there is no such thing as a "container" in the linux kernel. if you look at the linux kernel source code, there is no `struct container`. it does not exist. a container is just a normal linux process. it is exactly the same as a c program you run on your machine. 

the only difference is that the kernel is lying to this process.

### the process and the kernel
as we talked about before, a process is just an instance of a program in the ram (text, data, heap, stack) that the cpu is executing. but a process is completely blind and powerless. 

if a process wants to know anything about the world—what files exist, what network it is on, what other processes are running—it cannot look for itself. it has to go to the kernel and ask.

the kernel is the big boss. it is the code running in kernel mode (ring 0) that actually has physical access to the hard drive, the ram, and the hardware. every time a user process wants to do anything, it makes a system call to ask the big boss to do it.

normally, the big boss tells the truth. if you ask to list the running processes, the big boss tells you all of them. but for a "container" process, the big boss lies.

### the illusion: namespaces
how does the big boss lie? it uses a kernel feature called namespaces.

a namespace is essentially a reality filter for a process. when you create a new process in linux using the `clone()` system call, you can pass special flags to tell the big boss to create a new namespace for this process. 

you are basically telling the big boss: "when this specific process asks you a question about the system, filter the answer."

* **what the process thinks:** when the process asks the big boss "who else is running on this computer?", the big boss looks at the namespace filter and says "nobody. you are process ID 1. you are the only one here." the process thinks it is entirely alone, like it just booted up as the init process.
* **what is actually happening:** the process is just running normally alongside hundreds of other processes on your machine. in the kernel's real process table, this process might be PID 5042. but the big boss is translating the process ID and simply hiding the rest of the world from it. the matrix is active.

### the limits: cgroups (control groups)
the big boss uses namespaces to lie about what the process can *see*, but it also needs to restrict what the process can *use*. 

namespaces don't stop a process from eating all the ram and crashing the computer. if the process thinks it's alone, it might try to use 100% of the cpu. to fix this, the kernel uses cgroups.

cgroups do not lie. they are just hard limits enforced by the big boss.

for example, you can tell the kernel to put a process into a cgroup with a 500mb memory limit. the big boss watches it. 
* **what the process thinks:** it tries to allocate memory on the heap normally.
* **what is actually happening:** if that process tries to ask the big boss for 501mb of ram, the big boss just says "no" and refuses the allocation, or the kernel's OOM (out of memory) killer steps in and terminates the process. it is just the kernel putting a physical leash on the program.

### the image (what the process files can see)
when a process starts, it needs files to run (like libraries, configurations, and your code). so what is a docker "image"?

an image is just the big boss lying to a process about what files it can see.

an image is not a virtual hard drive. an image is literally just a bunch of normal files and folders sitting inside a hidden directory on your host machine (if you are on ubuntu, it's usually sitting right there in `/var/lib/docker/`).

when the big boss starts your container process, it does a trick. it redirects the process's root directory `/` to point exactly to that specific folder inside the docker directory.

* **what the process thinks:** it looks at `/` and thinks it has a brand new, clean ubuntu hard drive all to itself. it thinks it can read and write to the root of the system.
* **what is actually happening:** it is just looking at a sub-folder on your host machine. the big boss is keeping it trapped inside that folder. when the process asks to read `/etc/passwd`, the big boss intercepts the system call and redirects it to read the fake `/etc/passwd` inside that hidden host folder.

### docker tools
so if the kernel does all the actual work (the lying, the limiting, the file trapping), what do the docker tools actually do? 

**dockerfile**
since an image is really just a bunch of files sitting in a folder, a dockerfile is simply a way to edit this image. it is just a text script that tells the system how to build that folder. it says "download these base files, copy my python script into the folder, and save it so the kernel can use it later." 

**docker**
docker does not run containers. docker is just a tool that helps me create and manage containers. it is just a program that talks to the big boss. when you type `docker run`, docker just downloads the files, puts them in a folder, and tells the kernel: "hey big boss, start this process, put these namespace filters on it, apply this cgroup limit, and trap its files in this folder." docker just automates the illusions.

**docker compose**
when you want to run a container, the docker commands can get extremely long and complicated because you have to configure all the lies—which ports to fake, which folders to map, what cgroup limits to set. docker compose is just a yaml file where you write all those long commands down. it is just a way to make running docker commands easier so you don't have to type them into the terminal every time.

### docker, OCI, and runc
if docker is just a management tool, who actually gives the final order to the big boss to start the matrix?

when you run `docker run`, docker prepares everything. it unpacks the files into a folder and creates a JSON configuration file that lists all the lies (namespaces) and limits (cgroups) needed. 

but docker itself does not make the system calls to the big boss. it hands that folder and that JSON file to a smaller, lower-level program. this program is called a container runtime, and the standard one is called `runc` (which follows the OCI, or Open Container Initiative, rules).

`runc` is the actual worker. all `runc` does is:
* talk to the big boss (the kernel).
* call `clone()` with the right namespace flags to set up the filters.
* set up the cgroups limits.
* trap the filesystem view into the image folder.
* and finally, `execve()` the target program (like your node app or a database).

once the big boss does this and the process starts running inside its fake reality, `runc` simply exits. its job is done. 

* **what the process thinks:** it just booted up.
* **what is actually happening:** `runc` built the matrix around the process and then disappeared. the process is now running directly on the big boss. docker is just sitting in the background observing, but the kernel is doing all the heavy lifting.