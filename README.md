# CMPE283 : Virtualization<br />

## Assignment 1: Discovering VMX Features<br />

• Read the VMX configuration MSRs to ascertain support capabilities/features
Entry / Exit / Procbased / Secondary Procbased / Pinbased controls<br />

• For each group of controls above, interpret and output the values read from the MSR to the system
via printk(..), including if the value can be set or cleared.<br />


### Answer 1

#### Team Cahill

##### Team Members:<br />

• Mohith Girigowdara Girish (017090736)<br />
	- Analyzing hardware requirements to choose host (Intel machine vs GCP).<br />
	- Setting up VMWare Workstation and cloning Linux kernel.<br />
	- Building the new Kernel source code.<br />
	- Code changes to print Pinbased Controls MSR.<br />
	- Added missing configuration in .config file.<br />

• Miyar Karthik Kamath (017449133)<br />
	- Code changes to print Primary, Secondary Procbased, Entry, Exit.<br />
	- Implemented new kernel module with assignment functionality.<br />
	- Loading new module and verifying the output, saving the documentation.<br />


### Answer 2

#### Specifications:<br />

#####System Configurations<br />
Host OS: Windows 11 Home x64 based processor<br />
RAM: 12GB <br />
Processor: Intel(R) Core(TM) i7-8550U CPU @1.80GHz 1.99 GHz<br />
Virtual Machine: VMWare Workstation 17 Player<br />
VM Disc Image: ubuntu-22.04.3-desktop-amd64<br />

#### Steps to create a Linux kernel module that will query various MSRs to determine virtualization features available in your CPU<br />

1) Fork the official ![Linux Kernal Source](https://github.com/torvalds/linux) code into your github repository:

2) Install VMware Workstation 17 Player on host OS. (Install VM fusion if you are using Mac as Host OS)<br />

3) Install Ubuntu OS as Guest OS (Ubuntu 64-bit 22.04.3) <br />

4) After installation enable hardware assisted virtaulization in VMware as follows <br />
> Go to VM Machine settings -> Processors -> Virtualization engine -> Enable the 'Virtualize Intel VT-x/EPT or AMD-V/RVI' checkbox.<br />

5) Install  git, build-essential, libssl-dev, flex, bison, and others<br />

```bash
$ sudo apt-get install git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc flex libelf-dev bison
```

6) Clone the repository having kernel fork:

```bash
$ git clone https://github.com/mkarthikkamath/linux.git
```

7) Check the system version by using:<br />

```bash
$ uname -a
```

8) Create a folder cmpe283 inside linux folder and copy Makefile and cmpe283-1.c provided.<br />

9) Update the cmpe283-1.c file to get all the VMX functionalities and build it. Add Modules license code too. <br/>

10) Go inside the linux folder<br />

```bash
$ cd linux
```

10) Update the .config file to get the configurations required from the latested cloned version of linux.<br />

```bash
$ cp /boot/config-[Choose the version based on above uname] .config

$ make oldconfig
```
> Keep pressing "Enter" key to choose default values.

11) Install all the modules and kernel code.(This step will take time depending on the your system configuration)<br />

```bash
$ make prepare

$ make -j {x} modules 

$ make -j {x}

$ sudo make INSTALL_MOD_STRIP=1 modules_install

$ sudo make install
```

> Replace x with number of cores in your local machine


12) Reboot the VM check if the latest recently installed linux version is selected.<br />

```bash
$ sudo reboot
```

13) Run the command make and see if build is successful.<br />

```bash
$ make
```

14) Run command 'sudo insmod cmpe283-1.ko' to load the file to kernel.<br />

```bash
$ sudo insmod cmpe283-1.ko
```

15) Run command 'dmesg' to get the output. Observe the if the values can be set or cleared for the 5 controls.<br />

```bash
$ dmesg
```

16) Run command 'sudo rmmod cmpe283-1' to load the file out of the kernel.<br />

```bash
$ sudo rmmod cmpe283-1
```

17) Commit and push the Makefile and cmpe283-1.c to git repository, after giving username and token.<br />


#### Screen prints of the output

Pinbased and Primary Proc Based controls
![alt text](https://github.com/mkarthikkamath/linux/blob/master/cmpe283/Assignment%201/Pinbased%20and%20Primary%20Procbased%20Controls.png?raw=true)

Secondary Proc Based controls
![alt text](https://github.com/mkarthikkamath/linux/blob/master/cmpe283/Assignment%201/Procbased%20Controls.png?raw=true)

Entry and Exit Based controls
![alt text](https://github.com/mkarthikkamath/linux/blob/master/cmpe283/Assignment%201/Entry%20and%20Exit%20Controls.png?raw=true)

