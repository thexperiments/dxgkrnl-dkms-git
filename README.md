# dxgkrnl driver/module
This is a small aur which builds the dxgkrnl dkms module from the wsl2 kernel. It allows using a partitioned GPU from the host windows system within the VM running arch linux.

## Additional Setup
In addition to installing the driver you need to do quite some legwork to get this running.

Original source for all the info: https://gist.github.com/krzys-h/e2def49966aa42bbd3316dfb794f4d6a

* Set up GPU Partitioning on the host and assign a GPU to the VM
  * See original source above
  * See [Microsoft documentation](https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/partition-assign-vm-gpu?tabs=powershell&pivots=windows-server)
* Copy over quite some files/drivers from windows to the VM
  * See original source above