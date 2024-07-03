# Google Compute Engine (GCE)

- In corporate data centers, applications are deployed to physical servers
- Where do you deploy applications in the cloud?
	- Rent virtual servers
	- Virtual Machines - Virtual servers in GCP
	- Google Compute Engine (GCE) - Provision & Manage Virtual Machines

![[GCE Icon 1.png]]
# Compute Engine - Features

![[GCE Features 1.png]]

- Create and manage lifecycle of Virtual Machine (VM) instances
- **Load balancing** and **auto scaling** for multiple VM instances
- **Attach storage** (& network storage) to your VM instances
- Manage **network connectivity and configuration** for your VM instances
- **Our Goal:**
	- Setup VM instances as HTTP (Web) Server
	- Distribute load with Load Balancers
# Compute Engine Machine Family

- What type of hardware do you want to run your workloads on?
- Different Machine Families for Different Workloads:
	- **General Purpose (E2, N2, N2D, N1)** : Best price-performance ratio
		- Web and application servers, Small-medium databases, Dev environments
	- **Memory Optimized (M2, M1)**: Ultra high memory workloads
		- Large in-memory databases and In-memory analytics
	- **Compute Optimized (C2)**: Compute intensive workloads
		- Gaming applications

![[GCE Machine Family 1.png]]

- How much CPU, memory or disk do you want?
	- Variety of machine types are available for each machine family
	- Let's take an example : **e2-standard-2**:
		- **e2** - Machine Type Family
		- **standard** - Type of workload
		- **2** - Number of CPUs
- Memory, disk and networking capabilities increase along with vCPUs
# Images

![[GCE Images.png]]

- What operating system and what software do you want on the instance?
- Use operating system (OS) images to create boot disks for your virtual machine (VM) instances.
- Type of Images:
	- Public Images: Provided & maintained by Google or Open source communities or third party vendors
	- Custom Images: Created by you for your projects

[[GCE Hands on - Setting up a HTTP server]]

# Internal and External IP Addresses

- **External** (Public) IP addresses are **Internet addressable.**
- **Internal** (Private) IP addresses are **internal** to a corporate network
- You CANNOT have two resources with same public (External) IP address.
	- HOWEVER, two different corporate networks CAN have resources with same Internal (private) IP address
- All **VM instances** are assigned at least one Internal IP address
- Creation of External IP addresses can be enabled for VM instances
	- (Remember) When you stop an VM instance, External IP address is lost
## Static IP Address

Scenario : How do you get a constant External IP address for a VM
instance?
	Quick and dirty way is to assign an Static IP Address to the VM!

- Static IP **can be switched** to another VM instance in same project
- Static IP **remains attached** even if you stop the instance. You have to manually detach it.
- Remember : You are **billed** for an Static IP when **you are NOT using it**!
	- Make sure that you explicitly release an Static IP when you are not using it.
# Simplify VM HTTP server setup

- How do we reduce the number of steps in creating an VM instance and setting up a HTTP Server?
- Let's explore a few options:
	- Startup script
	- Instance Template
	- Custom Image
## Bootstrapping with Startup script

```bash
#!/bin/bash
apt update
apt -y install apache2
echo "Hello world from $(hostname) $(hostname -I)" > /var/www/html/index.html
```

- Bootstrapping: Install OS patches or software when an VM instance is launched.
- In VM, you can configure Startup script to bootstrap

## Instance templates

- Why do you need to specify all the VM instance details (Image, instance type etc) every time you launch an instance?
	- How about creating a Instance template?
	- Define machine type, image, labels, startup script and other properties
- Used to create VM instances and managed instance groups
	- Provides a convenient way to create similar instances
- CANNOT be updated
	- To make a change, copy an existing template and modify it
- (Optional) Image family can be specified (example - debian-9):
	- Latest non-deprecated version of the family is used

![[GCP Instance Template.png]]

## Reducing Launch Time with Custom Image

- Installing OS patches and software at launch of VM instances **increases boot up time**
- How about creating a custom image with OS patches and software **pre-installed?**
	- Can be created from an instance, a persistent disk, a snapshot, another image, or a file in Cloud Storage
	- Can be shared across projects
	- (Recommendation) Deprecate old images (& specify replacement image)
	- (Recommendation) **Hardening an Image** - Customize images to your corporate security standards
- **Prefer** using **Custom Image** to **Startup script**

![[GCE Custom Image.png]]

# Billings

## Sustained use discounts

![[GCE sustained use discounts.png]]

- Automatic discounts for running VM instances for significant portion of the billing month
	- Example: If you use N1, N2 machine types for more than 25% of a month, you get a 20% to 50% discount on every incremental minute.
	- Discount increases with usage (graph)
	- No action required on your part!
- Applicable for instances created by Google Kubernetes Engine and Compute Engine
- RESTRICTION: Does NOT apply on certain machine types (example: E2 and A2)
- RESTRICTION: Does NOT apply to VMs created by App Engine flexible and Dataflow

