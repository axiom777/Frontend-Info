sudo apt update && apt upgrade && apt install xorg i3 thunar sakura lightdm

startx

[DMs](https://wiki.debian.org/DisplayManager)

//VboxGuestAdditions
sudo apt install linux-headers-$(uname -r)
sudo apt install gcc make perl
sudo mount /dev/cdrom /media/cdrom

sudo sh /media/cdrom/VBoxLinuxAdditions.run
