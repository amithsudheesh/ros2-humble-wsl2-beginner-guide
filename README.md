# ROS 2 Humble on Windows 11 (WSL2): A Beginner's Setup Guide

> A click-by-click guide to installing WSL2, Ubuntu 22.04 and ROS 2 Humble on a Windows PC, and proving it works with a first test. Written for complete beginners, tested on real hardware.

![ROS 2](https://img.shields.io/badge/ROS_2-Humble-blue)
![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04_LTS-orange)
![Platform](https://img.shields.io/badge/Windows_11-WSL2-0078D6)
![Level](https://img.shields.io/badge/Level-Beginner-brightgreen)

## Table of Contents
1. [Overview](#1-overview)
2. [Prerequisites](#2-prerequisites)
3. [Installation](#3-installation)
   - [Part A: WSL2 and Ubuntu](#part-a-enable-wsl2-and-install-ubuntu-2204)
   - [Part B: ROS 2 Humble](#part-b-install-ros-2-humble)
   - [Part C: Trial test](#part-c-run-the-trial-test-talker-and-listener)
   - [Part D: Explore](#part-d-look-at-the-system-from-outside)
4. [Verify Your Setup](#4-verify-your-setup)
5. [Troubleshooting](#5-troubleshooting-common-beginner-pitfalls)
6. [Reset or Uninstall](#6-reset-or-uninstall)
7. [Check Your Understanding](#7-check-your-understanding)
8. [Where to Go Next](#8-where-to-go-next)

---

## 1. Overview

### What is this?
This repository shows, step by step, how to set up a **ROS 2** development environment on a Windows computer without wiping Windows or restarting into another operating system.

### Why does it exist?
ROS 2 is the most widely used toolkit in robotics, but it works best on Linux. Many beginners use Windows and get stuck on setup. **WSL2** solves this by running Linux inside Windows. This guide records every step, including the mistakes and confusing moments, so you don't have to guess.

### Plain-English glossary
| Term | What it means | Everyday analogy |
|------|---------------|------------------|
| **Terminal** | A window where you type commands instead of clicking | Texting instructions to your computer |
| **WSL2** (Windows Subsystem for Linux) | A Microsoft feature that runs Linux inside Windows | A Linux room built inside your Windows house |
| **Ubuntu** | A popular version of Linux | The furniture style in that room |
| **ROS 2** (Robot Operating System 2) | Free software tools that let robot programs talk to each other. Despite the name, it's not an operating system | A group chat for programs |
| **Node** | One program in ROS 2 | One member of the group chat |
| **Topic** | A named channel that nodes post to or listen on | One channel in the group chat |
| **Publish / Subscribe** | Posting to a topic / listening to a topic | Posting a message / reading the channel |
| **apt** | Ubuntu's app store, used from the terminal | A grocery store you order from by typing |

### What you will have at the end
- Windows 11 with WSL2 enabled
- Ubuntu 22.04 LTS installed
- ROS 2 Humble Hawksbill (desktop edition) installed and loading automatically
- A working test where two programs exchange messages

> **Why Ubuntu 22.04?** Each ROS 2 release is built for one specific Ubuntu version, like a phone app built for one phone model. **Humble pairs with Ubuntu 22.04.** (Jazzy pairs with 24.04.) Humble is supported until about May 2027, and everything you learn carries over to newer releases.

---

## 2. Prerequisites

No programming experience is needed. You need:

- **Windows 11**, or Windows 10 version 2004 or newer (build 19041+)
- At least **8 GB RAM** (16 GB recommended) and **30 GB free disk space**
- A stable internet connection (downloads are 1 to 2 GB; on slow connections the install can take 30 to 60+ minutes)
- Administrator rights on your PC
- Virtualization enabled in BIOS (usually on by default; see Troubleshooting)
- About **90 minutes**

**Check your Windows version:** press `Windows key + R`, type `winver`, press Enter.

### Tested on
| Item | Value |
|------|-------|
| Laptop | HP Victus (AMD Ryzen 5, 16 GB RAM, RTX 3050, 512 GB SSD) |
| Windows | Windows 11 |
| Linux | Ubuntu 22.04.5 LTS on WSL2 |
| ROS 2 | Humble Hawksbill |

---

## 3. Installation

**How to run a command:** copy it, paste it into the terminal, press Enter. In an Ubuntu terminal, paste with `Ctrl + Shift + V` or right-click. **In Linux, a command that prints nothing usually succeeded.**

### Part A: Enable WSL2 and install Ubuntu 22.04

**What:** install the "Linux room" inside Windows. **Where:** Windows PowerShell. **Why:** ROS 2 Humble needs Ubuntu 22.04.

**A1. Open PowerShell as administrator**
1. Click **Start** and type `PowerShell`.
2. Right-click **Windows PowerShell** and choose **Run as administrator**.
3. Click **Yes**.

**A2. Check whether WSL is already installed**
```powershell
wsl --status
```
If it says *"The Windows Subsystem for Linux is not installed"*, that's fine. The next step installs it.

**A3. Install WSL2 with Ubuntu 22.04**
```powershell
wsl --install -d Ubuntu-22.04
```
Wait for the downloads. Restart your computer if asked.

**A4. Create your Linux account**
1. An Ubuntu window opens (or open **Ubuntu 22.04** from the Start menu).
2. Type a **username** (lowercase, no spaces) and press Enter.
3. Type a **password** and press Enter. *Nothing appears while you type. This is normal.* Type it again to confirm.

**A5. Go to your Linux home folder and check the version**

If your prompt shows `/mnt/c/Windows/System32`, you are standing in a *Windows* folder seen from Linux. Do your work in the Linux home area instead, which is faster and avoids permission problems.
```bash
cd ~
lsb_release -a
```
Look for `Release: 22.04`.

---

### Part B: Install ROS 2 Humble

**What:** install ROS 2 itself. **Where:** the Ubuntu terminal. **Why:** this is the main toolkit. Steps B1 to B7 are preparation, B8 is the big install.

**B1. Update Ubuntu's software list**
```bash
sudo apt update && sudo apt upgrade -y
```
`sudo` means "run as administrator." Type your Linux password if asked.

**B2. (Optional) Check the language setting**
```bash
locale
```
ROS 2 needs a UTF-8 setting. If you see `LANG=C.UTF-8` or `en_US.UTF-8`, skip to B3. If not, run:
```bash
sudo apt install locales -y
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
```

**B3. Switch on the "universe" software shelf**
```bash
sudo apt install software-properties-common -y
sudo add-apt-repository universe
```
Press Enter if asked to continue. Messages like "already the newest version" are fine.

**B4. Install curl (a download tool)**
```bash
sudo apt install curl -y
```

**B5. Add the ROS 2 security key** (proves the software is genuine, like an official seal)
```bash
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
```
It prints nothing. Check that it saved (the size should not be 0):
```bash
ls -l /usr/share/keyrings/ros-archive-keyring.gpg
```

**B6. Add the ROS 2 software source** (copy as one single line)
```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```
Check it worked:
```bash
cat /etc/apt/sources.list.d/ros2.list
```
You should see one line mentioning `packages.ros.org` and `jammy`.

**B7. Refresh and upgrade again**
```bash
sudo apt update && sudo apt upgrade -y
```

**B8. Install ROS 2 Humble Desktop** (large download)
```bash
sudo apt install ros-humble-desktop -y
```
Leave the window open and keep your laptop awake until the `$` prompt returns. If it stalls or fails, run the same command again and it continues where it stopped.

> **Why "desktop"?** `ros-humble-ros-base` is just the core. `ros-humble-desktop` adds RViz (a 3D viewer), demos and tutorials.

**B9. Load ROS 2 in the current window and confirm**
```bash
source /opt/ros/humble/setup.bash
printenv ROS_DISTRO
```
The second command should print `humble`.

**B10. Make ROS 2 load automatically in every new window**
```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
tail -n 3 ~/.bashrc
```
> **Use `>>` (two arrows), not `>` (one arrow).** One arrow would erase the whole file.

The last line shown should be `source /opt/ros/humble/setup.bash`.

---

### Part C: Run the trial test (talker and listener)

**What:** start two small programs, a **talker** that posts messages and a **listener** that reads them. **Why:** if the listener hears the talker, ROS 2's messaging works end to end.

**C1. Start the talker** (first Ubuntu window)
```bash
ros2 run demo_nodes_cpp talker
```
Expected, one new line per second:
```
[INFO] [talker]: Publishing: 'Hello World: 1'
```

**C2. Open a second Ubuntu window.** Start menu, type `Ubuntu 22.04`, press Enter. Leave the talker running.

**C3. Start the listener** (second window)
```bash
ros2 run demo_nodes_cpp listener
```
Expected:
```
[INFO] [listener]: I heard: [Hello World: 7]
```
The number depends on when you started the listener. If it counts upward along with the talker, **your setup works.** You don't need to type `source` again, because step B10 did that automatically.

**C4. Stop the programs:** click each window and press `Ctrl + C`.

---

### Part D: Look at the system from outside

Start the talker again in window 1. In window 2, run these inspection commands:

```bash
ros2 node list                          # who is in the group chat
ros2 topic list                         # which channels exist
ros2 topic info /chatter                # message type and publisher/subscriber counts
ros2 interface show std_msgs/msg/String
ros2 interface show geometry_msgs/msg/Point
```

| Command | Expected result |
|---------|-----------------|
| `ros2 node list` | `/talker` |
| `ros2 topic list` | `/chatter`, `/parameter_events`, `/rosout` |
| `ros2 topic info /chatter` | Type `std_msgs/msg/String`, Publisher count 1 |
| `ros2 interface show std_msgs/msg/String` | One field: `string data` |
| `ros2 interface show geometry_msgs/msg/Point` | Three fields: `float64 x`, `y`, `z` |

**Key ideas**
- A **message type** is the shape of the envelope. A topic carries exactly one type.
- A talker keeps posting even when nobody is listening (Subscription count 0 is normal).
- ROS uses one standard for directions: **x = forward, y = left, z = up.**

Finish by stopping the talker (`Ctrl + C`) and running `ros2 node list`. An empty result means everything shut down cleanly.

---

## 4. Verify Your Setup

Tick every box. If one fails, jump to Troubleshooting.

- [ ] `wsl -l -v` (in PowerShell) shows `Ubuntu-22.04` with VERSION **2**
- [ ] `lsb_release -a` shows Release **22.04**
- [ ] `printenv ROS_DISTRO` prints **humble**
- [ ] A **new** Ubuntu window can run `ros2 --help` without typing `source`
- [ ] The listener prints messages from the talker
- [ ] `ros2 node list` is empty after both programs are stopped

---

## 5. Troubleshooting Common Beginner Pitfalls

| Problem | Likely cause | Fix |
|---------|--------------|-----|
| "The Windows Subsystem for Linux is not installed" | WSL not installed yet | Normal. Run `wsl --install -d Ubuntu-22.04` |
| `wsl` is "not recognized" | Old Windows, or PowerShell not as administrator | Update Windows; reopen PowerShell with **Run as administrator** |
| Error mentioning virtualization (`0x80070003`, `0x80370102`) | Virtualization is off in BIOS | Restart, enter BIOS (`F2`, `F10`, `Del` or `Esc`), enable **SVM Mode** (AMD) or **Intel VT-x**, save and exit |
| Prompt shows `/mnt/c/Windows/System32` | You're in a Windows folder | Run `cd ~` |
| Nothing appears when typing the password | Normal Linux behavior | Keep typing and press Enter |
| "already the newest version" | Tool was already installed | Harmless; move on |
| Welcome banner with "100% of 96MB" | Harmless WSL notice | Ignore it |
| Download is very slow | Large files, slow connection | Be patient; keep the laptop awake and the window open |
| Install stops partway | Network drop | Re-run the same `apt install` command; it resumes |
| `ros2: command not found` | ROS 2 not loaded in this window | Run `source /opt/ros/humble/setup.bash`; check step B10 |
| `printenv ROS_DISTRO` prints nothing | Same as above | Re-run B9 |
| `Unable to locate package ros-humble-desktop` | ROS source missing, or `apt update` not run | Redo B6 and B7 |
| `E: Could not get lock ... apt` | Another install is running | Wait a few minutes, retry |
| Listener shows nothing | Talker closed, or window didn't load ROS 2 | Keep the talker running; check B10 |
| `^C` and `signal_handler(SIGINT/SIGTERM)` after Ctrl+C | ROS 2 acknowledging a polite shutdown | Normal |
| Wrong Ubuntu version | A different release was installed | Check with `lsb_release -a`. Humble needs 22.04 |
| Ubuntu window closes immediately | Corrupted install | See "Reset or Uninstall" below |

**Still stuck?** Copy the exact error message, search for it, or open an **Issue** in this repository.

---

## 6. Reset or Uninstall

Because Linux lives in its own "room," you can delete it without touching Windows.

**Start over completely** (deletes everything inside that Ubuntu). In PowerShell:
```powershell
wsl --unregister Ubuntu-22.04
```
Then repeat Part A.

**Stop WSL without deleting anything:**
```powershell
wsl --shutdown
```

---

## 7. Check Your Understanding

Try answering in your own words. Answers are in `LEARNING_LOG.md` once you write them.

1. What is the difference between a **node** and a **topic**?
2. Why do we install Ubuntu **22.04** and not the newest version?
3. What does the `source` command do, and why did we add it to `~/.bashrc`?
4. Why is it fine that the talker keeps running with zero listeners?

---

## 8. Where to Go Next

- Work through the official [ROS 2 Humble beginner tutorials](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools.html)
- Try the 3D viewer: run `rviz2` (WSL2 on Windows 11 can display it)
- Learn to write your own node in Python or C++

---

## Repository Structure

```
.
├── README.md          <- this guide
├── LEARNING_LOG.md    <- notes on what I learned and the problems I solved
└── LICENSE            <- MIT license
```

## Acknowledgements
- [ROS 2 Humble documentation](https://docs.ros.org/en/humble/)
- [Microsoft WSL documentation](https://learn.microsoft.com/windows/wsl/)

## License
Released under the [MIT License](LICENSE).
