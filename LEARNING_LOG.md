# Learning Log

My notes from setting up WSL2 and ROS 2 Humble as a complete beginner: what I decided, what I learned, and what went wrong.

---

## Decisions and why

| Decision | Choice | Reason |
|----------|--------|--------|
| How to run Linux on Windows | WSL2 | Safe, fast, supports 3D windows (RViz) via WSLg, and can be deleted and rebuilt without touching Windows |
| Rejected: dual boot | n/a | Risky for beginners; a mistake can affect Windows |
| Rejected: virtual machine | n/a | Slow for 3D viewers |
| ROS 2 release | Humble Hawksbill | Most tutorials and community answers online |
| Ubuntu version | 22.04 | The release Humble is built for |
| ROS 2 package | `ros-humble-desktop` | Includes RViz, demos and tutorials |

---

## Concepts I learned

| Concept | In my own words |
|---------|-----------------|
| Terminal | Typing instructions to the computer instead of clicking |
| `/mnt/c/...` | My Windows C: drive seen from inside Linux; do work in the Linux home (`cd ~`) instead |
| `sudo` | Run a command with administrator rights |
| `apt` | Ubuntu's app store for the terminal |
| "universe" | An extra shelf of community software in Ubuntu's catalog that ROS 2 needs |
| GPG key | An official seal proving downloaded software is genuine |
| `source` | Loads ROS 2 into the current terminal |
| `~/.bashrc` | A startup checklist that runs in every new terminal |
| `>>` vs `>` | Append to a file vs overwrite it |
| Node | One running program in ROS 2 |
| Topic | A named channel that nodes publish to or subscribe to |
| Message type | The shape of the data a topic carries (one type per topic) |
| x, y, z in ROS | x forward, y left, z up |

---

## Commands I now know

| Command | Purpose |
|---------|---------|
| `wsl --status` | Check whether WSL is installed |
| `wsl --install -d Ubuntu-22.04` | Install WSL2 and Ubuntu 22.04 |
| `wsl -l -v` | List installed Linux systems and their WSL version |
| `cd ~` | Go to my Linux home folder |
| `lsb_release -a` | Show the Ubuntu version |
| `sudo apt update && sudo apt upgrade -y` | Refresh and upgrade installed software |
| `printenv ROS_DISTRO` | Confirm which ROS 2 release is loaded |
| `ros2 run <package> <node>` | Start a node |
| `ros2 node list` | List running nodes |
| `ros2 topic list` | List topics |
| `ros2 topic info <topic>` | Show a topic's type and publisher/subscriber counts |
| `ros2 interface show <type>` | Show the fields in a message type |

---

## Problems I ran into

| Problem | What it turned out to be | Lesson |
|---------|--------------------------|--------|
| "WSL is not installed" on first check | Expected before installing | An error message isn't always a failure |
| Prompt showed `/mnt/c/Windows/System32` | I was in a Windows folder from inside Linux | Always `cd ~` first |
| Many "already the newest version" messages | Tools were already present | Read the message before worrying |
| Very slow ROS 2 download (~300 to 400 kB/s) | Large package on a slow connection | Be patient; the command resumes if re-run |
| Commands printing nothing | Silence usually means success in Linux | Verify with a follow-up command (`ls`, `cat`, `tail`) |

---

## My answers to the self-check questions

1. **Node vs topic:** *(write it in your own words)*
2. **Why Ubuntu 22.04:** *(write it in your own words)*
3. **What `source` does and why it's in `~/.bashrc`:** *(write it in your own words)*
4. **Why the talker keeps running with no listeners:** *(write it in your own words)*

---

## Time log

| Part | Approx. time |
|------|--------------|
| A: WSL2 and Ubuntu | *(fill in)* |
| B: ROS 2 install | *(fill in)* |
| C and D: Trial test and exploring | *(fill in)* |
