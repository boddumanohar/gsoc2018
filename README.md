## GSoC 2018: Summary: 

### Project Introduction:

Bareflank is the first hypervisor written in C++. Being written in C++, we can use inheritance to extend the features of the bareflank. That base hypervisor just provides APIs to interact with and to build custom extensions with it. 

LibVMI is an introspection library, which currently supports, Xen and KVM. The goal of this project is create extensions that can be used to do introspection on the Bareflank VM.

### Bareflank-LibVMI ABI: 

With the new vmcall interface you fill the register with information that you would like to communicate and do call to `vmcall` instruction. Then on the Bareflank receiver side, you
can get that value stored in the register by doing `vmcs->save_state()->rax`.

And if you want to communicate something, more than 64 bits, then you could malloc a buffer and allocate it memory and send the address in the registers. This way, with the register based interface you can communicate data of any size.

Each vmcall has an id: which has to be specified in EAX register. 

Get register data (vmcall-id: 2):

Libvmi sends a malloced buffer to Bareflank to write the register data into this region. Bareflank writes the data in the buffer in the JSON and exits the vmcall. 

Steps:
* LibVMI sends the malloced buffer
* Bareflank creates a new mapping for the physical address associated with the malloced buffer
* So now, we have 2 mapping for the same physical address range, when something is on either of the mapping, the same information can read on both buffers. 

Here we write the register data on the Bareflank’s Mapping so that after vmcall, LibVMI’s buffer can read the same data.

Similarly to set the register data, we write to LibVMI’s buffer and Bareflank’s map can read it. 	 

### EPT Based Memory Mapping: 

When EPT is enabled, there are two sets of pages tables that convert a physical address into a logical address. The first is CR3, which converts a guest virtual, to a guest physical. The second is EPT which converts a guest physical to a host physical. 

We will use EPT to map a physical address(which could belong to other VM or the host VM itself) into LibVMI userspace process. The best way to do that, is to allocate a buffer. That will create a guest virtual to guest physical mapping in the CR3 that is associated with LibVMI userspace process. We then use EPT, and change the guest physical memory address that points to the RAM we just allocated, to instead point to the page we want to map in. 


### What’s left off:

Currently, on the test examples, both the memory mapping techniques work. But I am still seeing kalsr offsets failed. 


### Things learnt: 
* Learnt to  write assembly code
* Got a clear picture on Virtual address to physical Address translation
* Learnt about working of EPT, VPID
* During this GSoC, I spent on task to get the system memory map using UEFI services. Even that I could not complete that task, I was able to understand the working of UEFI boot process. 

### Good Times, Hard times:

Bareflank hypervisor is a best reference hypervisor for someone who wants to learn how a Hypervisor works. The structure of intel SDE manuals and the Bareflank code are coherent with each other.

When I was working on the EPT-based memory mapping hypercall, the logic that I wrote had made perfect sense, but still it the behaviour is as expected. I struggled finding the reason for it, in vain reported to Ryan and he was able to find that MTRR logic that EPT relies on for doing an identity map, has a problem in it. 

# Future work:

I will complete try to use the updated EPT logic and get memory mapping hyerpcall work. 
With the NMI bug fixed, I will start working on events. 


# Current code and test Codes: 



