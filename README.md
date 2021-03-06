## GSoC 2018: Summary: 

### Project Introduction:

Bareflank is the first hypervisor written in C++. Being written in C++, we can use inheritance to extend the features of the Bareflank. That base hypervisor just provides APIs to interact with and to build custom extensions with it. 

LibVMI is an introspection library, which currently supports, Xen and KVM. The goal of this project is to create extensions that can be used to do introspection on the Bareflank VM.

#### Tasks acheived
1. Get register information(complete)
2. Set register information(complete)
3. Implement memory mapping hypercalls(90% done)

### Bareflank-LibVMI ABI: 

Using the new vmcall interface, you fill the register with information that you would like to communicate and do call to `vmcall` instruction. Then on the Bareflank receiver side, you
can get that value stored in the register by doing `vmcs->save_state()->rax`.

And if you want to communicate something, more than 64 bits, then you could malloc a buffer and allocate it memory and send the address in the registers. This way, with the register based interface you can communicate data of any size.

Each vmcall has an id: which has to be specified in EAX register. 

### Get register data:

Libvmi sends a malloced buffer to Bareflank to write the register data into this region. Bareflank writes the data in the buffer in the JSON and exits the vmcall. 

##### Steps:
* LibVMI sends the malloced buffer
* Bareflank creates a new mapping for the physical address associated with the malloced buffer
* So now, we have 2 mapping for the same physical address range, when something is on either of the mappings, the same information can read on both buffers. 

Here we write the register data on the Bareflank’s Mapping so that after vmcall, LibVMI’s buffer can read the same data.

![asdf](res/register.png)

Similarly to set the register data, we write to LibVMI’s buffer and Bareflank’s map can read it. 	 

### EPT Based Memory Mapping: 

When EPT is enabled, there are two sets of pages tables that convert a physical address into a logical address. The first is CR3, which converts a guest virtual, to a guest physical. The second is EPT which converts a guest physical to a host physical. 

We will use EPT to map a physical address(which could belong to other VM or the host VM itself) into LibVMI userspace process. The best way to do that, is to allocate a buffer. That will create a guest virtual to guest physical mapping in the CR3 that is associated with LibVMI userspace process. We then use EPT, and change the guest physical memory address that points to the RAM we just allocated, to instead point to the page we want to map in. 

![asdf](res/ept.png)
### What’s left off:

Currently, on the test examples, both the memory mapping techniques work. But I am still seeing kalsr offsets failed. 


### Things learnt: 
* My debugging skill has improved a lot. 
* Since, interaction with bareflank is done using assembly language, I spent first few days of the project leaning to write assembly code
* Got a clear picture on Virtual address to physical Address translation
* While working on memory mapping hypercalls task, I've learnt alot about working of EPT, VPID
* During this GSoC, I spent on task to get the system memory map using UEFI services. Even that I could not complete that task, I was able to understand the working of UEFI boot process. 

### Good Times, Hard times:

Knowledge of Virtual Machine Introspection is not easily found on the internet. Working on this project, and getting my doubts solved from my mentor Tamas K Lengyel, I was able to understand most of the terminology associated with VMI. 

Bareflank hypervisor is the best reference hypervisor I can recommend for someone who wants to learn how a Hypervisor works. The structure of Intel SDE manuals and the Bareflank code are coherent with each other.

When I was working on the EPT-based memory mapping hypercall, the logic that I wrote had made perfect sense, but still it the behaviour is not as expected. I struggled to find reason for it, in vain reported to Ryan and he was able to find that MTRR logic that EPT relies on for doing an identity map, has a problem in it. 

# Future work:

I will try to complete memory mapping hypercall using the updated EPT logic 
With the NMI bug fixed, I will start working on events. 

