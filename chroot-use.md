Absolutely bro — here’s a clean `.md` documentation style write-up for **Issue #2: Terminal Not Opening on Ubuntu 20.04 LTS** where you finally fixed it via TTY using `chroot`, `mount`, etc.

---

````markdown
# Issue #2: Terminal Not Opening on Ubuntu 20.04 LTS (Fixed via TTY + chroot)

## Problem

I was using Ubuntu 20.04 LTS and suddenly noticed that **none of the terminal apps would open**. Clicking the terminal icon just caused a brief flicker — no terminal, no error popup, nothing. But the GUI itself (GNOME desktop) was working fine.

Even more weird: my **username and password worked fine in the GUI**, but when I switched to a TTY session (`Ctrl + Alt + F3`), it **refused to accept my password**.

## What I Tried

- `Ctrl + Alt + F3` → password incorrect even though GUI login worked
- Reboot → no effect
- Used **Ubuntu Live USB** to get in and mount the root partition
- Tried accessing `/home`, password files, etc.

At this point I realized the issue was deeper — maybe something with PAM, login shell, or broken packages. But I couldn't run terminal at all in my main OS.

---

## Solution (Step-by-step recovery)

Here’s how I fixed the whole thing using **Live USB + chroot**. Documenting for future me (and others who get stuck in this weird loop).

### Step 1: Boot into Live USB

I booted into Ubuntu 20.04 Live USB ("Try Ubuntu" option) and opened the **Terminal** from there.

### 🔍 Step 2: Find and mount my main Ubuntu partition

```bash
sudo fdisk -l
````

I noted the correct Linux partition (in my case `/dev/nvme0n1p6`), then mounted it:

```bash
sudo mount /dev/nvme0n1p6 /mnt
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt
```

### 💡 Step 3: From inside chroot, run recovery commands

I was now **inside my original root filesystem** using chroot. From here, I ran:

```bash
# Fix broken packages
apt update
apt upgrade
apt --fix-broken install

# Reinstall the terminal and desktop just in case
apt install --reinstall gnome-terminal ubuntu-desktop

# Regenerate configuration
dpkg-reconfigure gdm3
```

(You could also try `lightdm` if using that instead of `gdm3`.)

### 🧹 Step 4: Clean up

```bash
apt autoremove
```

### Step 5: Exit chroot and reboot

```bash
exit
sudo reboot
```

---

## Result

When I rebooted — boom! 💥
**Terminal worked perfectly again.**
No password issues. No flickering. All packages seem fixed. I was back in control.

---

## Lessons Learned

* `chroot` is a lifesaver when the main OS breaks.
* Always keep a Live USB around — it's my emergency toolkit now.
* If GUI login works but TTY doesn't, it could mean deeper PAM/auth or shell issues.
* Don't panic. Just mount, chroot, fix, reboot.

---

## Tags

`#ubuntu20.04` `#terminal-fix` `#chroot` `#tty` `#recovery` `#password-not-working` `#gnome-terminal` `#mount` `#linux-troubleshooting`

```

---

Let me know if you want a banner image, ASCII header, or link to this issue in GitHub Issues format.
```
