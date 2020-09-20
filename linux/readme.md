sudo apt update && apt upgrade && apt install xorg i3 thunar sacura

startx

[DMs](https://wiki.debian.org/DisplayManager)

sudo apt install lightdm

//VboxGuestAdditions
sudo apt install linux-headers-$(uname -r)
sudo apt install gcc make perl
sudo mount /dev/cdrom /media/cdrom

sudo sh /media/cdrom/VBoxLinuxAdditions.run
