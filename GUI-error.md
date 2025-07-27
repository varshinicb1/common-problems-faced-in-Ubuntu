# Issue #2: Ubuntu 20.04 LTS Not Booting (Stuck on Black Screen with Dots) – Fixed via TTY and chroot

## Problem

My Ubuntu 20.04 system suddenly stopped booting properly. After selecting Ubuntu from the GRUB menu, it only showed a black screen with blinking dots in the top-left corner. The GUI never loaded, and none of the recovery options worked either.

## Assumptions

- I had dual boot with Windows and Ubuntu installed on an SSD.
- The Ubuntu root partition was intact (confirmed using a live USB).
- GRUB was showing correctly, but booting into Ubuntu got stuck.
- I had internet access and a working USB stick to boot live Ubuntu.

## Solution

I fixed it by mounting the root partition, chrooting into the broken system from a live USB, and reinstalling the Ubuntu desktop environment.

---

## Steps

### 1. Boot from Live USB

I inserted the Ubuntu 20.04 live USB and chose "Try Ubuntu" from the menu.

### 2. Find My Root Partition

In the live environment terminal:

```bash
lsblk
```

I located my Linux root partition. Mine was `/dev/nvme0n1p5`.

### 3. Mount the Root Partition

```bash
sudo mount /dev/nvme0n1p5 /mnt
```

Then I mounted other essential directories:

```bash
for dir in /dev /proc /sys /run; do
    sudo mount --bind $dir /mnt$dir
done
```

### 4. Chroot into the System

```bash
sudo chroot /mnt
```

Now I was inside my actual Ubuntu installation, as if I had booted normally.

### 5. Check and Fix Broken Packages

```bash
apt update
apt upgrade
apt --fix-broken install
```

### 6. Reinstall Ubuntu Desktop

```bash
apt install --reinstall ubuntu-desktop
```

### 7. Optional: Reinstall NVIDIA Drivers

If I had removed NVIDIA drivers earlier or had issues related to them:

```bash
ubuntu-drivers devices
ubuntu-drivers autoinstall
```

### 8. Exit and Unmount Everything

```bash
exit
for dir in /run /sys /proc /dev; do
    sudo umount /mnt$dir
done
sudo umount /mnt
```

### 9. Reboot

```bash
reboot
```

---

## Result

After rebooting, Ubuntu booted correctly into the GUI. I was able to log in and access my system as before.

## Notes

- If TTY wasn't working, I would have used `Ctrl + Alt + F3` to get to a virtual terminal.
- The issue likely came from a broken desktop session or missing display manager package.
- This method let me fix the system without losing any data or reinstalling Ubuntu.

