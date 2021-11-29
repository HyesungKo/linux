# CMPE283
By Hyesung Ko

## Assignment 1
### Description

1. Install and login to Ubuntu Machine  
2. Fork the following linux repository  
	[https://github.com/torvalds/linux](https://github.com/torvalds/linux)  
3. Clone the forked repository and Change directory to the cloned linux directory  
	`$ git clone https://github.com/HyesungKo/linux`  
	`$ cd linux`
4. Check the current kernel version using following command  
	`$ uname -a`
5. Copy config file of your kernel to cloned linux directory with ".config" name  
	`cp /boot/{your_linux_config_file} .config`
6. Change the value of the following values in .config file because of certification issue.  
	`CONFIG_SYSTEM_TRUSTED_KEYS=""`  
	`CONFIG_SYSTEM_REVOCATION_KEYS=""`
6. Type the following command to configure new kernel using your kernels config file  
Keep press enter to answer the questions with default answer  
	`$ make oldconfig`
7. Make modules and Kernel by typing the following commands (Could take a couple hours)  
	`$ make -j 4 modules`   
	`$ make -j 4`  
	`$ sudo make INSTALL_MOD_STRIP=1 modules_install`  
	`$ sudo make install`
8. Reboot the machine  
	`$ sudo reboot`
9. Check the kernel version  
	`$ uname -a`
10. Make directory for cmpe283 assignment1  
	`$ mkdir cmpe283`  
	`$ cd cmpe283`  
	`$ mkdir assignment1`  
	`$ cd assignment1`
11. Copy the two given files, cmpe283-1.c and Makefile, in the current directory from Download  
	`$ cp ~/Downloads/cmpe283-1.c ~/Downloads/Makefile .`
12. Add and define the other model specific registers in addition to pinbased in cmpe283-1.c file  

		#define IA32_VMX_PINBASED_CTLS	0x481
		#define IA32_VMX_PROCBASED_CTLS	0x482
		#define IA32_VMX_PROCBASED_CTLS2	0x48B
		#define IA32_VMX_EXIT_CTLS		0x483
		#define IA32_VMX_ENTRY_CTLS		0x484
13. Add the following code to cmpe283-1.c file under `struct capability_info pinbased[5]` to display other capabilities in addition to the given pinbased controls  

		struct capability_info procbased[22] =
		{
			{ 2, "Interrupt-window exiting"},
			{ 3, "Use TSC offsetting"},
			{ 7, "HLT exiting"},
			{ 9, "INVLPG exiting"},
			{ 10, "MWAIT exiting"},
			{ 11, "RDPMC exiting"},
			{ 12, "RDTSC exiting"},
			{ 15, "CR3-load exiting"},
			{ 16, "CR3-store exiting" },
			{ 17, "Activate tertiary controls"},
			{ 19, "CR8-load exiting" },
			{ 20, "CR8-store exiting" },
			{ 21, "Use TPR shadow" },
			{ 22, "NMI-window exiting" },
			{ 23, "MOV-DR exiting" },
			{ 24, "Unconditional I/O" },
			{ 25, "Use I/O bitmaps" },
			{ 27, "Monitor trap flag" },
			{ 28, "Use MSR Bitmaps" },
			{ 29, "MONITOR exiting" },
			{ 30, "PAUSE exiting" },
			{ 31, "Activate secondary controls" }
		};

		struct capability_info procbased2[27] =
		{
			{ 0, "Virtualize APIC accesses" },
			{ 1, "Enable EPT" },
			{ 2, "Descriptor-table exiting" },
			{ 3, "Enable RDTSCP" },
			{ 4, "Virtualize x2APIC mode" },
			{ 5, "Enable VPID" },
			{ 6, "WBINVD exiting" },
			{ 7, "Unrestricted guest" },
			{ 8, "APIC-register virtualization" },
			{ 9, "Virtual-interrupt delivery" },
			{ 10, "PAUSE-loop exiting" },
			{ 11, "RDRAND exiting" },
			{ 12, "Enable INVPCID" },
			{ 13, "Enable VM functions" },
			{ 14, "VMCS shadowing" },
			{ 15, "Enable ENCLS exiting" },
			{ 16, "RDSEED exiting" },
			{ 17, "Enable PML" },
			{ 18, "EPT-violation" },
			{ 19, "Conceal VMX nonroot operation from Intel PT" },
			{ 20, "Enable XSAVES/XRSTORS" },
			{ 22, "Mode-based execute control for EPT" },
			{ 23, "Sub-page write permissions for EPT"},
			{ 24, "Intel PT uses guest physical addresses"},
			{ 25, "Use TSC scaling" },
			{ 26, "Enable user wait and pause" },
			{ 28, "Enable ENCLV exiting" }
		};

		struct capability_info exit_ctl[14] =
		{
			{ 2, "Save debug controls" },
			{ 9, "Host addressspace size" },
			{ 12, "Load IA32_PERF_GLOB AL_CTRL" },
			{ 15, "Acknowledge interrupt on exit" },
			{ 18, "Save IA32_PAT" },
			{ 19, "Load IA32_PAT" },
			{ 20, "Save IA32_EEFR" },
			{ 21, "Load IA32_EFER" },
			{ 22, "Save VMXpreemption timer value" },
			{ 23, "Clear IA32_BNDCFGS" },
			{ 24, "Conceal VM exits from Intel PT" },
			{ 25, "Clear IA32_RTIT_CTL" },
			{ 28, "Load CET state" },
			{ 29, "Load PKRS" }
		};

		struct capability_info entry_ctl[12] = 
		{
			{ 2, "Load debug controls" },
			{ 9, "IA-32e mode guest" },
			{ 10, "Entry to SMM" },
			{ 11, "Deactivate dual-monitor treatment" },
			{ 13, "Load IA32_PERF_GLOBAL_CTRL" },
			{ 14, "Load IA32_PAT" },
			{ 15, "Load IA32_EFER" },
			{ 16, "Load IA32_BNDCFGS" },
			{ 17, "Conceal VM entries from Intel PT" },
			{ 18, "Load IA32_RTIT_CTL" },
			{ 20, "Load CET state" },
			{ 22, "Load PKRS" }
		};
14. Modify `detect_vmx_feature` function as following  

		void
		detect_vmx_features(void)
		{
			uint32_t lo, hi;

			/* Pinbased controls */
			rdmsr(IA32_VMX_PINBASED_CTLS, lo, hi);
			pr_info("Pinbased Controls MSR: 0x%llx\n",
				(uint64_t)(lo | (uint64_t)hi << 32));
			report_capability(pinbased, 5, lo, hi);

			/* Procbased controls */
			rdmsr(IA32_VMX_PROCBASED_CTLS, lo, hi);
			pr_info("Procbased Controls MSR: 0x%llx\n",
				(uint64_t)(lo | (uint64_t)hi << 32));
			report_capability(procbased, 22, lo, hi);

			/* Procbased controls 2 */
			rdmsr(IA32_VMX_PROCBASED_CTLS2, lo, hi);
			pr_info("Procbased2 Controls MSR: 0x%llx\n",
				(uint64_t)(lo | (uint64_t)hi << 32));
			report_capability(procbased2, 27, lo, hi);

			/* VMX Exit controls */
			rdmsr(IA32_VMX_EXIT_CTLS, lo, hi);
			pr_info("Exit Controls MSR: 0x%llx\n",
				(uint64_t)(lo | (uint64_t)hi << 32));
			report_capability(exit_ctl, 14, lo, hi);

			/* VMX Entry controls */
			rdmsr(IA32_VMX_ENTRY_CTLS, lo, hi);
			pr_info("Entry Controls MSR: 0x%llx\n",
				(uint64_t)(lo | (uint64_t)hi << 32));
			report_capability(entry_ctl, 12, lo, hi);
		}	
15. Add License at the end of the cmpe283-1.c file  
	`MODULE_LICENSE("GPL v2");`
16. Type the following command to build and add it to kernel  
	`$ make`  
	`$ sudo insmod cmpe283-1.ko`
17. Display the message  
	`$ dmesg`
18. Remove module from kernel  
	`$ sudo rmmod cmpe283_1`
19. Screenshots of the output
![output1](https://github.com/HyesungKo/linux/blob/master/cmpe283/output/output1.png)  
![output2](https://github.com/HyesungKo/linux/blob/master/cmpe283/output/output2.png)  
![output3](https://github.com/HyesungKo/linux/blob/master/cmpe283/output/output3.png)  

## Assignment 2 & 3 (0x4fffffff, 0x4ffffffe) and 3(0x4ffffffd, 0x4ffffffc)
### Q2. Description
To configure the cpuid, I have have edited 2 files in linux kernel, `arch/x86/kvm/cpuid.c`, `arch/x86/kvm/vmx/vmx.c`. Firstly, I have added two shared arrays to be used to store the number of exit and the time of the exit using `EXPORT_SYMBOL_GPL` and `extern` array.  
Under the file `../cpuid.c`  
		
	u32 exit_categories[75] = {0};
	u64 exit_time[75] ={0};

	EXPORT_SYMBOL_GPL(exit_categories);
	EXPORT_SYMBOL_GPL(exit_time);

Under the file `../vmx.c`  
		
	extern u32 exit_categories[75];
	extern u64 exit_time[75];

When I add the above code in those two file, I have the shared variables, `exit_categories` and `exit_time`. Therefore, When I updated the variables in `vmx.c` file, I am able to access the updated variable from another file and read in `cpuid.c` file.  

Under the file `../vmx.c`, I have added 2 functions which is called in the funcion named `static int __vmx_handle_exit(struct kvm_vcpu *vcpu, fastpath_t exit_fastpath)`. The first function is `inc_exit_category((u16)exit_reason.basic)` which count the each exit based on the exit number. The other function is `add_exit_time((u16)exit_reason.basic)`, which count the cycle during each exit. `add_exit_time` function must be called before the function returns. Those function will update the variable declared above.  

The update functions for the arrays are following:  

	void inc_exit_category(u16 reason_index)
	{
		if (reason_index < 75){
			exit_categories[reason_index]++;
		}
	}

	void add_exit_time(u16 reason_index, u64 start)
	{
		if (reason_index < 75){
			u64 delta = __rdtsc() - start;
			exit_time[reason_index] += delta;
		}
	}

To use rdtsc to count the cycle, I need to add the following in `../vmx.c` file.  
		
	#define _MM_MALLOC_H_INCLUDED
	#include <x86intrin.h>
	 

To read the count and cycle following code are used for each eax value:  

1. For CPUID leaf node %eax=0x4FFFFFFF:  
		
		u32 i;
		eax = 0;
		for (i = 0; i < 75; i++){
			eax += exit_categories[i];
		}
	        
2. For CPUID leaf node %eax=0x4FFFFFFE:  
		
		u64 sum = 0;
		u32 i;
		for (i = 0; i < 75; i++ ) {
			sum += exit_time[i];
		}
		ebx = (u32)(sum >> 32);
		ecx = (u32)sum;  


3. For CPUID leaf node %eax=0x4FFFFFFD:  
		
		if(isValid(&eax, &ebx, &ecx, &edx)) {
			eax = exit_categories[ecx];
			ebx = ecx = edx = 0;
		}  
	        
4. For CPUID leaf node %eax=0x4FFFFFFC:  
		
		if(isValid(&eax, &ebx, &ecx, &edx)) {
			u64 time = exit_time[ecx];
			ebx = (u32)(time >> 32);
			ecx = (u32)time;
		}  
	        

`isValid()` function check the ecx value is correct, if the number is in sdm or not, and the code is following:  
	
	if(isValid(&eax, &ebx, &ecx, &edx)) {
		if(*ecx > 74 || *ecx == 35 || *ecx == 38 || *ecx == 42 || *ecx == 65 || *ecx == 70 || *ecx == 71 || *ecx == 72 || *ecx == 73 ) {
			*eax = *ebx = *ecx = 0;
			*edx = 0xffffffff;
			return 0;
		}
		return 1;
	}

When I modified the codes above I change the directory to linux file and reload the `kvm` and `kvm_intel` modules using the following command.  

	$ make -j 4 modules
	$ sudo make INSTALL_MOD_STRIP=1 modules_install
	$ sudo rmmod kvm_intel
	$ sudo rmmod kvm
	$ sudo modprobe kvm
	$ sudo modprobe kvm_intel

Once I load the updated `kvm` and `kvm_intel` modules, open the virtual machine using `virt-manager` and test the cpuid features. The outputs are following:  

![fe](https://github.com/HyesungKo/linux/blob/master/cmpe283/output/fe.png)
![dc](https://github.com/HyesungKo/linux/blob/master/cmpe283/output/dc.png)

### Q3. Comment on the frequency of exits

Does the number of exits increase at a stable rate? Or are theremore exits performed during certain VM operations?  
-> The total number of exits does not increast at a stable rate. It nomally increases between 300 and 700 exits every second. There are a number of VM exits performed more frequently, such as External Interrupt, HLT, WRMSR, EPT violation. 


The number of total exits after the full boot load (after ubuntu on VM booted) : Approximately 1,350,000 exits  


### Q4. Of the exit types defined in the SDM, which are the most frequent? Least?

The most frequent exit type is EPT violation, whose exit reasom number is 48 (0x30).  
The least performed exit types are the ones in the following outputs. Those includes the one with eax==0x00000000 and edx=0x00000000. If edx=ffffffff, the exit number is not defined in the SDM.  
In the cycle-wise, HLT exit takes the most cycles out of all exit types.  

`$ for i in {0..74}; do cpuid -1 -l 0x4ffffffd -s $i; done` produces the following output.  
		CPU:   0x4ffffffd 0x00: eax=0x00010a67 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x01: eax=0x00088a34 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x02: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x03: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x04: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x05: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x06: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x07: eax=0x00017a73 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x08: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x09: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x0a: eax=0x0008cdb8 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x0b: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x0c: eax=0x0006539f ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x0d: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x0e: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x0f: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x10: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x11: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x12: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x13: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x14: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x15: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x16: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x17: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x18: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x19: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x1a: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x1b: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x1c: eax=0x000206a2 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x1d: eax=0x00000008 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x1e: eax=0x000ca300 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x1f: eax=0x0000260f ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x20: eax=0x0007b170 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x21: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x22: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x23: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffd 0x24: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x25: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x26: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffd 0x27: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x28: eax=0x000035ea ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x29: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x2a: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffd 0x2b: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x2c: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x2d: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x2e: eax=0x0000001c ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x2f: eax=0x0000000c ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x30: eax=0x0037e584 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x31: eax=0x0002c1d2 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x32: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x33: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x34: eax=0x0001ec5e ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x35: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x36: eax=0x0000000e ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x37: eax=0x0000000c ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x38: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x39: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x3a: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x3b: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x3c: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x3d: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x3e: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x3f: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x40: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x41: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffd 0x42: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x43: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x44: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x45: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000
		CPU:   0x4ffffffd 0x46: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffd 0x47: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffd 0x48: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffd 0x49: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffd 0x4a: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0x00000000

`for i in {0..74}; do cpuid -1 -l 0x4ffffffc -s $i; done`
		CPU:   0x4ffffffc 0x00: eax=0x4ffffffc ebx=0x00000000 ecx=0x0024833b edx=0xffffbef5
		CPU:   0x4ffffffc 0x01: eax=0x4ffffffc ebx=0x00000000 ecx=0x0b65b29b edx=0xffffbef5
		CPU:   0x4ffffffc 0x02: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x03: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x04: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x05: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x06: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x07: eax=0x4ffffffc ebx=0x00000000 ecx=0x01e024a1 edx=0xffffbef5
		CPU:   0x4ffffffc 0x08: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x09: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x0a: eax=0x4ffffffc ebx=0x00000000 ecx=0x01303244 edx=0xffffbef5
		CPU:   0x4ffffffc 0x0b: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x0c: eax=0x4ffffffc ebx=0x00000000 ecx=0x15373af8 edx=0xffffbef5
		CPU:   0x4ffffffc 0x0d: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x0e: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x0f: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x10: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x11: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x12: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x13: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x14: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x15: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x16: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x17: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x18: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x19: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x1a: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x1b: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x1c: eax=0x4ffffffc ebx=0x00000000 ecx=0x00401665 edx=0xffffbef5
		CPU:   0x4ffffffc 0x1d: eax=0x4ffffffc ebx=0x00000000 ecx=0x000000cf edx=0xffffbef5
		CPU:   0x4ffffffc 0x1e: eax=0x4ffffffc ebx=0x00000000 ecx=0x02bb7409 edx=0xffffbef5
		CPU:   0x4ffffffc 0x1f: eax=0x4ffffffc ebx=0x00000000 ecx=0x00ad49d0 edx=0xffffbef5
		CPU:   0x4ffffffc 0x20: eax=0x4ffffffc ebx=0x00000000 ecx=0x0a55b38e edx=0xffffbef5
		CPU:   0x4ffffffc 0x21: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x22: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x23: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffc 0x24: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x25: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x26: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffc 0x27: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x28: eax=0x4ffffffc ebx=0x00000000 ecx=0x002b3278 edx=0xffffbef5
		CPU:   0x4ffffffc 0x29: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x2a: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffc 0x2b: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x2c: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x2d: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x2e: eax=0x4ffffffc ebx=0x00000000 ecx=0x000003b9 edx=0xffffbef5
		CPU:   0x4ffffffc 0x2f: eax=0x4ffffffc ebx=0x00000000 ecx=0x0000017e edx=0xffffbef5
		CPU:   0x4ffffffc 0x30: eax=0x4ffffffc ebx=0x00000000 ecx=0x0bfc4d25 edx=0xffffbef5
		CPU:   0x4ffffffc 0x31: eax=0x4ffffffc ebx=0x00000000 ecx=0x0292ec39 edx=0xffffbef5
		CPU:   0x4ffffffc 0x32: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x33: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x34: eax=0x4ffffffc ebx=0x00000000 ecx=0x03214d25 edx=0xffffbef5
		CPU:   0x4ffffffc 0x35: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x36: eax=0x4ffffffc ebx=0x00000000 ecx=0x000001fe edx=0xffffbef5
		CPU:   0x4ffffffc 0x37: eax=0x4ffffffc ebx=0x00000000 ecx=0x0000017d edx=0xffffbef5
		CPU:   0x4ffffffc 0x38: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x39: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x3a: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x3b: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x3c: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x3d: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x3e: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x3f: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x40: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x41: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffc 0x42: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x43: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x44: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x45: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5
		CPU:   0x4ffffffc 0x46: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffc 0x47: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffc 0x48: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffc 0x49: eax=0x00000000 ebx=0x00000000 ecx=0x00000000 edx=0xffffffff
		CPU:   0x4ffffffc 0x4a: eax=0x4ffffffc ebx=0x00000000 ecx=0x00000000 edx=0xffffbef5








