Using CPUID to detect hypervisor presence. 


Hypervisor are mostly used to for virtualization. But they can also be used for Virtual Machine introspection, where we observe the operating system from outside perspective so that the behavior of the malware can be observed more clearly. 



Can a malware modify it behavior if it knows that it is running in a Hypervisor by checking the edx:31 (hypervisor bit)?

