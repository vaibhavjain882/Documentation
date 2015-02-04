### Docker
```
Docker is “an open source project to pack, ship and run any application as a lightweight container.” It builds on LinuX Containers, which uses the cgroups functionality to enable creation and running of multiple isolated Linux virtual environments (VE) on a single control host. So unlike a VM, a VE like Docker doesn’t create its own virtual computer with a distinct OS and processors and hardware emulation. A VE is VM-lite; it rides on the already existing kernel’s image of the underlying hardware, and only creates a ‘container’ in which to run your apps, and even recreate the OS if you want since the OS is also just another app running on the kernel. It places only a little extra load on the system, so unlike the traditional VM there is very little overhead when using Docker. 
```
```
Note: Few people compares Docker with Vagrant while both are different things. Docker is really an extension of LXC’s capabilities. It is an Virtual Environmentwhile Vagrant is a VM manager.It allows you to script and package the VM config and the provisioning setup. It is designed to run on top of almost any VM tool – VirtualBox, VMWare, AWS, etc. You can integrate Vagrant with CM tools such as Puppet and Chef to provision your own VM setups and configs.
```
### Reasons To Use Docker :

#### 1. Faster Development Life Cycle :
```
Docker is perfect for helping you with the development lifecycle. Docker allows your developers to develop on local containers that contain your applications and services. It can then integrate into a continuous integration and deployment workflow.

For example, your developers write code locally and share their development stack via Docker with their colleagues. When they are ready, they push their code and the stack they are developing onto a test environment and execute any required tests. From the testing environment, you can then push the Docker images into production and deploy your code.
```
#### 2. Easy Scaling And Deployment: 
```
Docker containers can run on a developer's local host, on physical or virtual machines in a data center, or in the Cloud. Docker's portability and lightweight nature also make dynamically managing workloads easy. You can use Docker to quickly scale up or tear down applications and services.
```

### Few Basic Definitions:

#### Docker Images:
```
A Docker image is a read-only template. For example, an image could contain an Ubuntu operating system with Apache and your web application installed.Docker images are the build component of Docker.
```
#### Docker Registries :
```
Docker registries hold images. These are public or private stores from which you upload or download images.
```
#### Docker containers :
```
Docker containers are similar to a directory. A Docker container holds everything that is needed for an application to run. Each container is created from a Docker image. Docker containers can be run, started, stopped, moved, and deleted. Each container is an isolated and secure application platform. Docker containers are the run component of Docker.
```
### Demonstration Of Docker: Continued...
