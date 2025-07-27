Got it, here's the `.md` version without any emojis or fluff — clean, technical, and written in your voice:

---

````markdown
# Issue #2: Terminal Not Opening on Ubuntu 20.04 LTS (Fixed via chroot + TTY recovery)

## Problem

I was using Ubuntu 20.04 LTS and suddenly none of the terminal apps would launch. Clicking the terminal icon caused a flicker and then nothing. No error messages, no crashes — just nothing.

Oddly, my username and password worked fine in the GUI login screen. But when I switched to a TTY using `Ctrl + Alt + F3`, the same username/password were rejected.

This meant I couldn’t open a terminal in GUI, and also couldn’t log in from any TTY. I was stuck.

## What I Tried

- Switched to TTY via `Ctrl + Alt + F3`, but couldn’t log in
- Rebooted — didn’t help
- Tried safe mode / recovery mode — didn’t help either
- Finally booted into a Live Ubuntu 20.04 USB to begin actual recovery

## Solution: Fix via chroot from Live USB

### 1. Boot into Ubuntu Live USB

Selected "Try Ubuntu" to get access to the live environment, then opened the Terminal.

### 2. Find and mount my root partition

Checked my disk layout:

```bash
sudo fdisk -l
````

Identified the root partition (mine was `/dev/nvme0n1p6`), then mounted it:

```bash
sudo mount /dev/nvme0n1p6 /mnt
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt
```

### 3. Fix system from inside chroot

Once I was chrooted into my broken OS, I ran the following commands:

```bash
apt update
apt upgrade
apt --fix-broken install
```

Then reinstalled the desktop and terminal packages to be safe:

```bash
apt install --reinstall gnome-terminal ubuntu-desktop
```

Regenerated the display manager config (mine uses GDM):

```bash
dpkg-reconfigure gdm3
```

Finally cleaned up unused packages:

```bash
apt autoremove
```

### 4. Exit and reboot

```bash
exit
sudo reboot
```

## Result

After rebooting, the terminal started working again from the GUI. TTY login also worked normally. The entire system was back to normal with no data lost.

## Notes

* If `gdm3` isn’t your display manager, replace it with `lightdm` or whichever you're using.
* Having a Live USB saved me here. I always keep one around now.
* chroot is a very useful tool for deep system repair when the main OS won’t boot or function properly.

## Tags

`ubuntu` `20.04` `terminal-not-opening` `tty-login-failure` `chroot-recovery` `gnome-terminal` `desktop-fix` `linux-troubleshooting`

```

---

Let me know if you want to add screenshots, link it to a related GitHub Issue, or format it as a GitHub Wiki section.
```
