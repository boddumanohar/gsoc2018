the way the EFI loader works is that everything that all prestart functions that were added by base and extensions are executed, then the start function (the default one being provided by base) is executed and hypervisor started, then the poststart functions that were added are executed, possibly launching the next target without returning


We use base to add prestart functions. 

First of all what does bareflank driver does?”

BFdriver: Contains the code that loads the bareflank. By maintaining functions to allocate memory for the executable and functions to unload vmm. Once the vmm is removed is unloaded, all the symbols for the vmm are removed from the memory and are no longer accessible. 



platform/efi

Contains 2 repos `efi_main` and `efi_base`. 

