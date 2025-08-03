```
# 🧱 Chroot Environment Setup (Using debootstrap)

This document explains how to create a minimal Linux environment using `chroot` and `debootstrap`. This is part of **Task 1: Container & Runtime Basics** from the Formcept DevOps Onboarding series.

---

## 🔧 What is `chroot`?

**chroot** means “change root”.  
It’s a Linux/Unix command that creates a *fake environment* for a program by changing its idea of what the `/` (root folder) is.

> **In simple words**:  
It tricks a process into thinking a subfolder is the entire root of the filesystem.  

---

## 🔍 Imagine This

Normally, a process sees:
```

/bin/bash
/etc/passwd
/var/log/syslog

```
But with `chroot`, you tell it:
> *"Hey process, your new / is /home/lavanya/newroot"*  

So if inside chroot the process tries to access `/bin/bash`, it’s actually:
```

/home/lavanya/newroot/bin/bash

````

The process **thinks** it is in a full system, but it is locked in a folder.

---

## 🗓️ Where Did It Come From?

- Introduced in 1982 in BSD UNIX  
- Created by Bill Joy (who also co-created the `vi` editor)  
- Purpose: allow developers to test new software or Linux installs safely

---

## 🧠 Why Is It Useful?

| Use Case         | Meaning in Simple Terms                          |
|------------------|--------------------------------------------------|
| Testing safely   | Install/test software in a fake system           |
| System repair    | Boot rescue mode and chroot into a broken system |
| Building packages| Build inside a clean environment                 |
| FTP/Web Jail     | Limit user access to one directory for safety    |

---

## ⚠️ Is `chroot` Secure?

**Not fully**:
- It does *not* isolate memory, users, or networks
- Hackers with root can easily escape a chroot jail

👉 That’s why modern containers like Docker exist — they fix what chroot cannot.

---

## 🛡️ Summary Table

| Feature       | Description                                           |
|---------------|-------------------------------------------------------|
| What it does  | Changes the root directory (`/`) for a program        |
| Good for      | Sandboxing, testing, repairs                          |
| Not good for  | Real security                                         |
| Invented      | 1982 (BSD UNIX)                                       |
| Used by       | Developers, sysadmins, package builders               |

---

## ⚙️ Objective

Set up a basic isolated shell environment using:
- Core binaries like `bash`, `ls`
- Required shared libraries
- A minimal root filesystem with `debootstrap`

---

## 📦 Prerequisites

- A Linux environment (or WSL on Windows)
- `sudo` privileges
- Tools: `debootstrap`, `chroot`, `ldd`

---

## 🧪 Method 1: Manual chroot (Optional but Good to Know)

> Skip to [Method 2](#-method-2-debootstrap-way-recommended) if you only want debootstrap.

**Steps**:
```bash
mkdir -p ~/jail/{bin,lib,lib64}
sudo cp /bin/bash ~/jail/bin/
ldd /bin/bash
# Copy required .so files to ~/jail/lib and ~/jail/lib64

# Optionally add ls
sudo cp /bin/ls ~/jail/bin/
ldd /bin/ls
# Copy required .so files

sudo chroot ~/jail /bin/bash
````

Inside, fix your path:

```bash
export PATH=/bin
```

✅ Now you are in a minimal fake root.

---

## 🚀 Method 2: debootstrap Way (Recommended-easy)

### ✅ Step 1: Install debootstrap

```bash
sudo apt update
sudo apt install debootstrap
```

### ✅ Step 2: Bootstrap a minimal Debian system

```bash
sudo debootstrap stable ~/mychroot http://deb.debian.org/debian/
```

This creates a working root filesystem in `~/mychroot`.

### ✅ Step 3: Enter the chroot

```bash
sudo chroot ~/mychroot /bin/bash
```

Now you are inside a **minimal Debian OS**!

---

## 🧹 Exiting and Cleaning Up

To exit:

```bash
exit
```

To delete the chroot folder:

```bash
sudo rm -rf ~/mychroot
```

---

## 🔑 What is `sudo`?

**sudo** stands for *“superuser do”*.
It lets a normal user temporarily run commands as the root user (administrator).

**Why?**

* Linux security does not allow normal users to:

  * Install software
  * Change protected system files
  * Create privileged folders like `/fake_root`

**For this chroot task, sudo is needed because**:

* You are creating a new root filesystem (`/fake_root`)
* Downloading & installing system files
* Using `chroot` itself (requires root)

---

### 🛡️ Why not just always be root?

* Being root all the time is risky:

  * One wrong command can break your system
  * Malware can do more damage

**sudo** is safer:

* Forces you to think before running privileged actions
* Grants root only to the commands you choose

> In short:
> **`sudo` is a temporary admin key** — used only when you really need it.

---


## 🎥 Demo Video

[**Click here to see the chroot demo**](https://drive.google.com/file/d/15q35ULMV-M_2eDR8dRb43wWUPnqVZn31/view?usp=sharing)

---

## 🧠 Why is chroot still relevant?

* It shows you how *container-like* isolation was done historically
* Prepares you to understand container runtimes like containerd, runc, and Docker
* Makes you confident with Linux’s lower-level mechanisms

---


— just say **“let’s push”** and I’ll guide you! 🚀
```
