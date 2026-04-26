Day 1 – Lab Setup (Ubuntu VM)

Objective

Set up the foundation of a home lab using virtualization and Linux.

---

Environment

- Host Machine:
  - CPU: AMD Ryzen 5 3600
  - RAM: 16GB
  - GPU: RX 6600 XT
- Virtualization: Oracle VM VirtualBox
- OS Installed: Ubuntu Desktop 24.04 LTS

---

Tasks Completed

1. Virtual Machine Creation

- Created Ubuntu VM in VirtualBox
- Allocated:
  - RAM: 4GB
  - CPU: 2 cores
  - Storage: ~30–50GB (on available drive)

---

2. Ubuntu Installation

- Installed Ubuntu Desktop from ISO
- Completed initial setup (user account + password)

---

3. Network Verification

Checked network configuration:

ip a

Result:

- Assigned IP address: 10.0.2.15 (NAT network)

Tested internet connectivity:

ping 8.8.8.8

Result:

- Successful response → Internet working

---

4. System Update

sudo apt update && sudo apt upgrade -y

---

5. Installed Basic Tools

sudo apt install net-tools curl wget git htop -y

Tools installed:

- net-tools (network commands)
- curl / wget (downloads)
- git (version control)
- htop (system monitoring)

---

6. SSH Setup

sudo apt install openssh-server -y

Checked service:

sudo systemctl status ssh

Result:

- SSH running successfully

---

7. Documentation Practice

- Created lab notes file:

nano ~/home_lab/lab_day1.txt

---

Key Skills Practiced

- Virtualization setup
- Linux installation
- Basic networking (IP + connectivity testing)
- Package management (APT)
- Remote access setup (SSH)

---

Challenges

- Understanding VirtualBox networking (NAT vs Host-only)
- Learning basic Linux commands

---

Outcome

A fully working Ubuntu virtual machine with:

- Internet connectivity
- Essential tools installed
- SSH enabled

---

Next Steps

- Set up Windows Server VM
- Configure Active Directory
- Build multi-machine network lab
