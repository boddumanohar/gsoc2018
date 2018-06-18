Firmware engineers generally put their work in the form of .efi file. The efi files should be executed by firmware. 

There are many methods to run the efi. The method that I use is:


```
wget https://github.com/tianocore/edk2/raw/master/EdkShellBinPkg/FullShell/X64/Shell_Full.efi
mv Shell_Full.efi shellx64.efi
sudo cp shellx64.efi /boot/efi
sudo cp bfloader.efi /boot/efi
```

add menu entry for efi shell

add these lines to `/etc/grub.d/40_custom`

```
menuentry "EFI shell" {
        insmod part_gpt
        insmod chain
        set root='(hd0,gpt1)'
        chainloader /shellx64.efi
}
```
now after rebooting the system,from the boot menu,  select `EFI shell`

you will be prompted with a shell. 

In the shell, type `fs0:` to change to the first file system.
now, type bfloader.efi and enter. bareflank is expected to start booting. :) 


