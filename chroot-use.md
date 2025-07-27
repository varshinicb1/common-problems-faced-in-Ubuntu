ssue #2: Terminal Not Opening on Ubuntu 20.04 LTS (Fixed via chroot + TTY recovery)
Problem
I was using Ubuntu 20.04 LTS and suddenly none of the terminal apps would launch. Clicking the terminal icon caused a flicker and then nothing. No error messages, no crashes — just nothing.

Oddly, my username and password worked fine in the GUI login screen. But when I switched to a TTY using Ctrl + Alt + F3, the same username/password were rejected.

This meant I couldn’t open a terminal in GUI, and also couldn’t log in from any TTY. I was stuck.

What I Tried
Switched to TTY via Ctrl + Alt + F3, but couldn’t log in

Rebooted — didn’t help

Tried safe mode / recovery mode — still no terminal access

Finally booted into a Live Ubuntu 20.04 USB to begin actual recovery

Solution: Fix via chroot from Live USB
1. Boot into Ubuntu Live USB
I selected "Try Ubuntu" to get access to the live environment and opened the Terminal.

2. Find and mount my root partition
Checked the disk layout:

bash
Copy
Edit
sudo fdisk -l
Identified the root partition (mine was /dev/nvme0n1p6), then mounted it and set up chroot:

bash
Copy
Edit
sudo mount /dev/nvme0n1p6 /mnt
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt
3. Run recovery commands from inside chroot
Once inside chroot, I ran the following commands:

bash
Copy
Edit
apt update
apt upgrade
apt --fix-broken install
Reinstalled terminal and desktop environment just in case:

bash
Copy
Edit
apt install --reinstall gnome-terminal ubuntu-desktop
Reconfigured the display manager (I use GDM):

bash
Copy
Edit
dpkg-reconfigure gdm3
Removed unused packages:

bash
Copy
Edit
apt autoremove
4. Exit and reboot
bash
Copy
Edit
exit
sudo reboot
Result
After rebooting, the terminal worked perfectly again from the GUI. TTY login was also fixed. No data was lost and the system was fully functional again.

Notes
If you're using lightdm or another display manager, reconfigure that instead of gdm3

A Live USB is essential in cases like this — always keep one around

chroot is a powerful way to repair a broken OS from outside

Tags
ubuntu 20.04 terminal-not-opening tty-login-failure chroot-recovery gnome-terminal desktop-fix linux-troubleshooting

