# LinuxFromScratchJourney

# My Linux From Scratch Journey

## Where I started

Three years ago I attempted this very monumental task but it took one month due to not understanding how Linux commands work and I was very new to Linux. I decided to give it a try again since I was more experienced and understood more about Linux and how it works. I will be breaking down my progress based on the chapters of the LFS book you can find on [here](http://www.linuxfromscratch.org/).

### Chapter 1

Since this is the introduction chapter there were no issues here.

### Chapter 2

I used Opensuse Tumbleweed for the LFS install which was using an Intel Celeron G3930 (Kaby Lake) and 8 GB of DDR4 RAM and originally planned to install LFS on a 16GB USB drive. However that was unfeasible due to size constraints and I wanted a drive I can take in and out of my hot swap bays. I used my 320GB hard drive that I had from an old netbook instead. I ran the version-check.sh script and while I met most of the requirements GCC was version 10.2.1 and was newer than what the book recommended. So later on in the process I downgraded to 9.3.1.

### Chapter 3

I had issues with downloading the wget-list due to the fact that it crashed twice but after a third attempt I was able to download all the required tarball files and patch files.

### Chapter 4

Since I was using a 64bit machine I made sure to create a lib64 directory for all the 64 bit libraries.

### Chapter 5

There were no issues here as I was able to install all the compiling tools from this chapter.

### Chapter 6

Because of a text formatting issue in page 57, the Ncurses installation caused issues for me down the line forcing me to restart the LFS process. There was also another text formatting issue in page 57 regarding Coreutils.

### Chapter 7

Because of the issue with Ncurses in chapter 6, the installation of Python, Texinfo and Util-linux failed forcing me to restart the process.

### Chapter 8

GMP gave me an illegal instruction error due to my processor. Even though I had a 64 bit CPU I could not fix the error. I replicated the issue in my Skylake Celeron and Core i3-4160 PCs and they managed to pass with no errors. So I decided to compile LFS on my Core i3-4160 instead of my Kaby Lake Celeron. GCC took three hours to compile, test and install but after that the other packages compiled just fine.

### Chapter 9

I had to copy some of the configuration files from my current install of Opensuse to ensure that I was able to connect to the internet.

### Chapter 10 - Chapter 11

For the fstab file I used the UUID naming scheme to label my drives instead of /dev/sdxx. I did try using UUID with my grub file but it didn't boot. So I did use /dev/sdxx and I was able to boot just fine. Also I did install LFS to another drive and installed grub on that drive. However, I could not install using the x86_64-efi target option and instead had to install using i386-pc. Turns out I my PC used BIOS instead of UEFI. However, even that didn't work so I had to use the --force option and it did install with no errors. If you are to run grub-install on a separate drive make sure you run grub-install /dev/sdx instead of /dev/sda to avoid overwriting your current grub file. Also, if you do have an efi partition make sure you use the --efi-directory option. Even though you can install LFS on another partition it is more sensible to install it on an empty drive to avoid overwriting your grub file and also messing up your fstab file. 


## Conclusion

I was able to successfully install LFS but I did have obstacles in my way as listed above. So here is some advice if to help you with LFS: install it on a separate drive to avoid overwriting your current fstab file and your current grub configuration. Make sure to follow the instructions carefully and if you can just use the online book as the pdf does have text formatting issues as I listed above. Take the time to read any errors and also backup your progress to avoid redoing everything. 
