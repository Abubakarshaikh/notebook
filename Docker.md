# Quick Docker Notes:
1. **VM:** Using VM software, for example, Ubuntu can be installed inside a Windows. And they would both run at the same time. It is like building a PC, with its core components like CPU, RAM, Disks, Network Cards etc, within an operating system and assemble them to work as if it was a real PC. This way, the virtual PC becomes a "guest" inside an actual PC which with its operating system, which is called a host.
2. **Container:** instead of using an entire operating system, Container cut down the "unnecessary" components of the virtual OS to create a minimal version of it. This lead to the creation of LXC (Linux Containers).It's faster than VM Machines. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and deploy it as one package.

3. **Docker:** A docker container, unlike a virtual machine and container, does not require or include a separate operating system. Instead, it relies on the Linux kernel's functionality and uses resource isolation.

4. **Purpose of Docker:** Its primary focus is to automate the deployment of applications inside software containers and the automation of operating system level virtualization on Linux. It's more lightweight than standard Containers and boots up in seconds.

### Features of Docker ###
Here are the essential features of Docker:

* Isolated environments for managing your applications
* Easy Modeling
* Version control
* Placement/Affinity
* Application Agility
* Developer Productivity
* Operational Efficiencies

### How do I run more than one process in a Docker container?
This approach is discouraged for most use cases. For maximum efficiency and isolation, each container should address one specific area of concern.
