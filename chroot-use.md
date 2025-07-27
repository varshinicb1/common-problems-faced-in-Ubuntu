# 🛠️ Issue #2: Terminal Not Opening on Ubuntu 20.04 LTS (Fixed via TTY + chroot)

## 🧩 Problem

I was using Ubuntu 20.04 LTS and suddenly noticed that **none of the terminal apps would open**. Clicking the terminal icon just caused a brief flicker — no terminal, no error popup, nothing. But the GUI itself (GNOME desktop) was working fine.

Even more weird: my **username and password worked fine in the GUI**, but when I switched to a TTY session (`Ctrl + Alt + F3`), it **refused to accept my password**.

## 🧪 What I Tried

- `Ctrl + Alt + F3` → password incorrect even though GUI login worked
- Reboot → no effect
- Used **Ubuntu Live USB** to get in and mount the root partition
- Tried accessing `/home`, password files, etc.

At this point I realized the issue was deeper — maybe something with PAM, login shell, or broken packages. But I couldn't run terminal at all in my main OS.

---

## 🧯 Solution (Step-by-step recovery)

Here’s how I fixed the whole thing using **Live USB + chroot**. Documenting for future me (and others who get stuck in this weird loop).

### 🖥️ Step 1: Boot into Live USB

I booted into Ubuntu 20.04 Live USB ("Try Ubuntu" option) and opened the **Terminal** from there.

### 🔍 Step 2: Find and mount my main Ubuntu partition

```bash
sudo fdisk -l
