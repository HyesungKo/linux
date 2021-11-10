# CMPE283
By Hyesung Ko
***

## Assignment 1
### Description

1. Install and login to Ubuntu Machine  
2. Clone the following linux repository  
	[https://github.com/torvalds/linux](https://github.com/torvalds/linux)  
3. Change directory to the cloned linux directory  
	`$ cd linux`
4. Check the current kernel version using following command  
	`$ uname -a`
5. Copy config file of your kernel to cloned linux directory with ".config" name  
	`cp /boot/{your_linux_config_file} .config`
6. Change the value of the following values in .config file because of certification issue.  
	`CONFIG_SYSTEM_TRUSTED_KEYS=""`  
	`CONFIG_SYSTEM_REVOCATION_KEYS=""`
6. Type the following command to configure new kernel using your kernels config file  
		`$ make oldconfig`
7. Keep press enter to answer the questions with default answer Make modules and Kernel by typing the following commands (Could take a couple hours)  
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

12. Add the following code to cmpe283-1.c file under `struct capability_info pinbased[5]` to display other capabilities in addition to the given pinbased controls  

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
13. Modify `detect_vmx_feature` function as following  

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
14. Add License at the end of the cmpe283-1.c file  
	`MODULE_LICENSE("GPL v2");`
15. Type the following command to build and add it to kernel  
	`$ make`  
	`$ sudo insmod cmpe283-1.ko`
16. Display the message  
	`$ dmesg`
17. Remove module from kernel  
	`$ sudo rmmod cmpe283-1`
18. Screenshots of the output
![output1](https://github.com/HyesungKo/linux/blob/master/cmpe283/output/output1.png)  
![output2](https://github.com/HyesungKo/linux/blob/master/cmpe283/output/output2.png)  
![output3](https://github.com/HyesungKo/linux/blob/master/cmpe283/output/output3.png)

