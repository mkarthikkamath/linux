# CMPE283 : Virtualization<br />

## Assignment #1 Discovering VMX Features<br />

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

1) Fork the official [Linux Kernal Source](https://github.com/torvalds/linux) code into your github repository:

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



## Assignment #2 (0x4FFFFFFF) & (0x4FFFFFFE)<br />

### Answer 1

#### Team Cahill

##### Team Members:<br />

• Mohith Girigowdara Girish (017090736)<br />
	- Setting up Virtual machine<br />
	- Code changes in cpuid.c and vmx.c.<br />
	- Building the code changes. <br />
	- Debugging the build issues.<br />

• Miyar Karthik Kamath (017449133)<br />
	- Understanding the usage of int, atomic variables and u32, u64 etc<br />
	- Installing nested machine and implemented test cases.<br />
	- Debugging issues while installing nested virtual machine.<br />


### Answer 2

#### Part 1:<br />

For CPUID leaf node %eax=0x4FFFFFFF:
Return the total number of exits (all types) in %eax

#### Part 2:<br />

For CPUID leaf node %eax=0x4FFFFFFE:
Return the high 32 bits of the total time spent processing all exits in %ebx
Return the low 32 bits of the total time spent processing all exits in %ecx
%ebx and %ecx return values are measured in processor cycles, across all VCPUs

> Configuration done in Assignment 1 is required to proceed with assignment 2.

Do the code changes in cpuid.c and vmx.c 

Path :

```bash
$ /linux/arch/x86/kvm/cpuid.c 
$ /linux/arch/x86/kvm/vmx/vmx.c
```

Run the below commands in order

```bash
$ sudo make modules
$ sudo make INSTALL_MOD_STRIP=1 modules_install 
$ sudo make install
```

To check for KVM (Kernel-based Virtual Machine), run the below command

```bash
$ lsmod | grep kvm
```
If you find "kvm_intel" and "kvm", remove them using the below commands

```bash
$ sudo rmmod kvm_intel 
$ sudo rmmod kvm
```

Verify the removal

```bash
$ lsmod | grep kvm
```

To explicitly load the KVM modules, run the below commands

```bash
$ sudo modprobe kvm
$ sudo modprobe kvm_intel
```

Reboot the VM

```bash
$ sudo reboot
```

#### Steps to test 

-Set up an VM inside the host machine to test the changes.

-Install the following virtinst, libvirt-clients, virt-top, qemu-kvm, libvirt-daemon, libvirt-daemon-systems,  virt-manager, bridge-utils

```bash
$ sudo apt-get install virtinst libvirt-clients virt-top qemu-kvm libvirt-daemon libvirt-daemon-systems virt-manager bridge-utils
```

-Run the following command 

```bash
$ sudo virt-manager
```

- Download ubuntu-22.04.3-desktop-amd64 
- Start Virtual Machine Manager and install Ubuntu. 
- Once the inner VM is started run the below commands in the inner VM

```bash
$ sudo apt install cpuid
```

-Run below to get output of part 1:<br />

```bash
$ cpuid -l 0x4FFFFFFF
```

-Run below to get output of part 2: <br />

```bash
$ cpuid -l 0x4FFFFFFE
```



## Assignment #3 (0x4FFFFFFC) & (0x4FFFFFFD)<br />

### Answer 1: 

#### Team Cahill

1. ##### Team Members:<br />

```
Same as Assignment #2
```

### Answer 2

2. Steps to be followed

```
Repeat the steps from assignment 2 to build the changes.
```

#### Part 1: <br />
For CPUID leaf node %eax=0x4FFFFFFD:
Return the number of exits for the exit number provided (on input) in %ecx
This value should be returned in %eax 

#### Part 2: <br />
For CPUID leaf node %eax=0x4FFFFFFC:
Return the time spent processing the exit number provided (on input) in %ecx
Return the high 32 bits of the total time spent for that exit in %ebx

### Answer 3

3. Comment on the frequency of exits – does the number of exits increase at a stable rate? Or are there 
more exits performed during certain VM operations? Approximately how many exits does a full VM 
boot entail?

```
Frequency of some exits have increased, while some remain almost same.

Exit 0 (Exception or non-maskable interrupt) does not show any notable difference (Changed from 8919 to 8945) but Exit 1(External interrupt) almost doubles (From 27140 to 56295). I opened firefox and searched for some terms in the inner VM to note the difference in number of exits. 46 (Access to GDTR or IDTR), 48 (EPT violation) 49 (EPT misconfiguration) have increased a lot, where as reason code 28 to 32 does not show any much difference. Most of the other exit code shows 0, that is exit did not occur at all.

The total number of exits on inner VM reboot,  as follows 

Before count
001f17e3 = 2037731 decimal 

After count
0030b376 = 3191670 decimal

Difference is 11,53,939
``` 

### Answer 4

4. Of the exit types defined in the SDM, which are the most frequent? Least?

``` 
Most frequent 
Exit : 0   Exception or non-maskable interrupt (NMI)<br />
Exit : 1   External interrupt<br />
Exit : 7   Interrupt window<br />
Exit : 10  CPUID <br />
Exit : 12  HLT<br />
Exit : 28  Control-register accesses<br />
Exit : 30  I/O instruction<br />
Exit : 31  RDMSR<br />
Exit : 32  WRMSR<br />
Exit : 48  EPT violation<br />
Exit : 49  EPT misconfiguration<br />

Least frequent<br />
Exit : 29   MOV DR<br />
Exit : 46   Access to GDTR or IDTR. <br />
Exit : 54   WBINVD or WBNOINVD<br />
Exit : 55   XSETBV<br />

Rest of the exits did not occur.<br />
```
