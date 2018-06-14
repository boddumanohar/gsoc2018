the way the EFI loader works is that everything that all prestart functions that were added by base and extensions are executed, then the start function (the default one being provided by base) is executed and hypervisor started, then the poststart functions that were added are executed, possibly launching the next target without returning


We use base to add prestart functions. 

First of all what does bareflank driver does?”

BFdriver: Contains the code that loads the bareflank. By maintaining functions to allocate memory for the executable and functions to unload vmm. Once the vmm is removed is unloaded, all the symbols for the vmm are removed from the memory and are no longer accessible. 



platform/efi

Contains 2 repos `efi_main` and `efi_base`. 


My goal is to understand how bootloader loads the bareflank efi and if l learn this, I will be able to understand how to get `gBS-getMemoryMap()`	 function. 

So today, my stab at UEFI is: Some basic difference between UEFI and BIOS

Both UEFI and BIOS are the types of firmwares of a computer. BIOS style is only ever found on IBM PC compatable systems. UEFI is manged by UEFI form and secure boot is not the same thing as uefi. 

BIOS:
All a BIOS can do is run a boot loader from a disk’s MBR. The booting process of UEFI and BIOS is completely different. 

UEFI:
CSM- BIOS compatablity mode. UEFI has the ability to boot the system BIOS style(BIOS Compatibility booting) rather than native UEFI style.

UEFI certainly does understand the concept of `bootloaders` `disk partitions` `operating system`. BIOS understands none of it. 

sudo efibootmgr -v -> to get the details of a boot menu entries.




Tomorrow, I should understand how booting is going to take place. 

