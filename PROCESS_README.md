# Compilation and Installation of Linux From Scratch

## Getting Started

To get things started I used a separate drive in order to avoid overwriting the grub configuration files and also avoid causing issues with my fstab file. Originally I had planned to use a 16GB USB drive but that was not sufficient and considering my plans to implement BLFS I went with an old 320 GB Hard Drive from an old netbook. For the operating system I used Opensuse Tumbleweed. For the PC I used my custom built PC that has an Intel Celeron G3930 with 8GB of DDR4 RAM and a 120GB SSD. However, I ran into an issue with a compilation issue so I decided to move the SSD to another computer that has an Intel Core i3-4160 and 8GB of DDR3 RAM. The following sections explain how I installed LFS and also some tips since some might want to install it in different ways.


### Chapter 2	

To begin with, before anything could be done it's important to check if your PC has the right requirements. In page 14-15 there is a script called version_check.sh. I have provided the script on the repository to make things easier. However, it must be executable so make sure to run the `chmod +x version_check.sh` to make the script executable. If you have `command not found` errors then you must install that package. Also, in the listed packages the book recommends that gcc not be newer than 10.2.0. Sadly the gcc installed in Opensuse was 10.2.1 and halfway through the build I decided to downgrade it to 9.3.1. Next, decide on a partitioning scheme. I used GPT since Opensuse uses GPT for partitioning. However you can use MBR if you want. The reason is because when you make it bootable, grub works better with MBR. And if you use GPT you want to use grub2 instead. However, if you use grub with GPT you will need to create a boot partition. Originally I created an ext2 formatted partition but before the beginning of Chapter 10 I decided to change that partition to vfat and made it 500MB. Also, adding a swap is advisable although not necessary. A root partition is needed however to store the main build. You can add any partitions you want. In my case I only added a boot partition, the root and swap. You can use either cfdisk or if you prefer gparted to set up your partitions. I used gparted as it was easier.  Once you decide on a partitioning scheme use the `mkfs` command to create a partition. For example if you want to create a boot partition for the first partition using vfat then you would type `mkfs -v -t vfat /dev/sdxx` where v means verbose, t means filesystem type. You can do the same with `mkfs.vfat -v /dev/sdxx`. Then make sure to set up the lfs variable with `export LFS=/mnt/lfs`. Make sure to type `echo $LFS` in case you run into any issues. Then create a directory or several using the variable. For example, I created the main and boot directory with the following commands:

* `mkdir -pv $LFS`
* `mkdir -pv $LFS/boot`

Make sure to run these commands as sudo on your own machine. Next, make sure to mount your partitions. In my case I mounted my partitions as follows:

* `mount -v -t ext4 /dev/sdxx $LFS`
* `mount -v -t vfat /dev/sdxx $LFS/boot`

Again make sure to run these commands as root. Then if you have a swap partition, enable it with this command. Again run it as sudo:

* `/sbin/swapon -v /dev/sdxx`


### Chapter 3

In this I created the sources directory and then used `wget` to download the needed packages that will be installed. It took me three tries due to my internet connection. If you are to do this on your own make sure that your connection isn't interrupted.


### Chapter 4

In this section, the usr, bin, etc, lib, sbin and var directories are created. If you have a 64 bit machine create a lib64 directory. Then create a tools directory. Also create a group/user named lfs. Give the user a password with the `passwd` command and then use the `chown` command as described in the lfs user section to give lfs access to the resources. Set up a `~/.bash_profile` and `~/.bashrc` file for the user. I have provided the files for you to use rather than having to copy and paste. Remember to source `~/.bash_profile` after you're done. 

### Chapter 5

In this section, this is the first stage of the cross compilation. Here, you will install the following packages:

* `Binutils-2.35`: This is contains a linker and assembler for object files. This is used for Glib and GCC.
* `GCC-10.2.0 Pass 1`: This is the first pass of GCC and this is the C and C++ compiler.
* `Linux-5.8.3`: This is the Linux Kernel that will be used for compilation.
* `Glib-2.32`: This contains the main C library.
* `Libstdcc++`: This is the standard C++ library.

A few notes here, GCC might take a lot of time to make so you can add the flags to the make command. If you have four cores in your CPU you can use the `make -j4` command instead to speed up the process. Also make sure that you configure the commands correctly or you will get errors later on.

### Chapter 6

This is more about cross compilation and all you need to do is to compile and build each package. Note there are text errors. I had issues with `Ncurses` and `Coreutils` which forced me to rebuild LFS. So use the online guide if you can.

### Chapter 7

In the final stage, the directories must now be owned by the root user. So make sure to do that and create the dev, proc, sys and run kernel directories for the file systems. Create the device nodes, mount all the kernel filesystems and then `chroot` into the environment. Again, I provided that in my text file so you can follow along. But please make sure to run the commands as sudo. As the book details, if you ever log out again you must make sure to remount the file systems and then `chroot` back into the drive. Create the root level directories as listed in the book, make sure to create the `/etc/hosts`, `/etc/passwd`, and the tester user to test some of the packages in chapter 8. Then create the log files and you can start the rest of the compilation. I ran into errors with `Util-Linux`, `Texinfo` and `Python-3.8.5` because I compiled `Ncurses`incorrectly. Be careful with the text formatting. 


### Chapter 8 

In this section you will be doing lots of compiling and building. There are 73 packages to install and some you will have to test such as `GCC`. If the page tells you not to skip the test do not skip it. In most cases you can skip it just fine. There will be errors but they are harmless because these packages aren't complete. In this part I ran into an error with `GMP` as it gave me the `illegal instruction` error due to an issue with my processor architecture. I had to move from my Celeron G3930 to my Core-i3-4160. After that I had no issue. Expect some to take a long time to install. For me `GCC` took three hours to install. After that you can remove some files to save space and log out to log back in using the new `chroot` options. Again I provided the options in my text file so you can avoid having to constantly reference that page again if you ever log out. And don't forget to delete the tester user.

### Chapter 9

First install the bootscript files provided by the LFS people. Create the `/etc/sysconfig`, `/etc/hostname`, `/etc/resolv.conf` and `/etc/hosts` to set up networking. The `/etc/inittab` is used to change runlevels. The `/etc/sysconfig/clock` file is used to resolve time. The `/etc/sysconfig/console` file is used to set up your console only if you aren't using a US keyboard layout. The `/etc/profile` file creates the language file of your specific region. The `/etc/inputrc` file is used for the readline library. The `/etc/shells` library is used for the login shells.

### Chapter 10

Here I made my system bootable but I had to follow different guidelines that I hope will help you. First create your `/etc/fstab` file as shown in the chapter. However, I recommend using the `UUID` of the specific filesystem rather than the `/dev/sdxx` name to ensure that the drive will always recognize those partitions. Then begin kernel compilation by running `make mrproper` and use the `make menuconfig` to ensure the options you want are selected. If you have an old kernel make sure to run `make oldconfig` to update it. If you have a separate boot partition in your host system be sure to bind that to your drive in order to copy the files after compilation. In the `grub install` step, make sure to use the `--target=i386-pc` due to the error explained in that section. Also do not use `/dev/sda` since that will overwrite your grub file. Use the drive that will have LFS instead such as `/dev/sdb` or `/dev/sdf`. If it doesn't install you'll have to force it with the `--force` option. In the `/boot/grub/grub.cfg` file, if you used MBR for your drive you can use msdos2 instead of just 2. If you have GPT use gpt2. By the way in my case I had a separate boot drive so I used gpt1 in the set root line. Also, make sure to remove the `/boot` section if you have a separate boot partition. So instead of `/boot/vmlinuz` it will be `/vmlinuz`.

### Chapter 11
  
After that I created the release files and then registered my name to the website provided on that section. You don't have to do that but you can. Logout of your drive and then `umount` all the directories. Then reboot and you should be introduced to a login prompt. In my case I screwed up the grub file and had to reedit it but after that it booted just fine. Now you can log in and of course bask in the glory of all your hard work paying off. 