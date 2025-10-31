# 🐧 Day 1: Linux and AWS Learning

## 📋 Table of Contents
- [Initial Configuration](#initial-configuration)
- [System Information](#system-information)
- [Resource Monitoring](#resource-monitoring)
- [Network Commands](#network-commands)
- [Diagnostic Tools](#diagnostic-tools)
- [Navigation and Utilities](#navigation-and-utilities)

---

## Initial Configuration

### Customize Terminal Color

To have a terminal with more vibrant colors (green), modify the `/etc/profile` file:

```bash
# Edit the global configuration file
sudo nano /etc/profile

# Add at the end of the file:
export TERM="xterm-256color"
```

**What does this do?**
- `TERM` is an environment variable that defines your terminal's capabilities
- `xterm-256color` enables 256 colors instead of the basic 16
- This improves the display of editors, prompts, and colored outputs

### System Update

```bash
sudo apt update    # Updates the list of available packages
sudo apt upgrade   # Installs updates for installed packages
```

---

## System Information

### Check Ubuntu Version

```bash
cat /etc/os-release
```

**Expected output:**
```
PRETTY_NAME="Ubuntu 24.04.3 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.3 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
ID_LIKE=debian
```

**Alternative commands:**
```bash
lsb_release -a      # Shows distribution information
uname -a            # Kernel and architecture information
```

**Example output from `uname -a`:**
```
Linux ip-172-31-17-34 6.14.0-1016-aws #16~24.04.1-Ubuntu SMP x86_64 x86_64 x86_64 GNU/Linux
```

This tells us:
- **Operating system:** Linux
- **Hostname:** ip-172-31-17-34
- **Kernel version:** 6.14.0-1016-aws
- **Architecture:** x86_64 (64-bit)

### Basic Identification Commands

```bash
whoami              # Shows your current user
who                 # Shows who is connected to the system
uptime              # System uptime + average load
```

**Example of `uptime`:**
```
 15:32:01 up 2:15,  1 user,  load average: 0.08, 0.05, 0.01
```
- System up for 2 hours and 15 minutes
- 1 connected user
- System load: 0.08 (last minute), 0.05 (last 5 min), 0.01 (last 15 min)

---

## Resource Monitoring

### CPU Information

```bash
lscpu               # Fastest way to see CPU information
cat /proc/cpuinfo   # Detailed information for each core
```

**Important data from `lscpu`:**
- **CPU(s):** Total number of cores
- **Thread(s) per core:** Threads per core (hyperthreading)
- **Model name:** Processor model

### RAM Memory

```bash
free -h             # Shows free RAM in readable format (MB, GB)
```

**Example output:**
```
              total        used        free      shared  buff/cache   available
Mem:          7.7Gi       1.2Gi       5.8Gi        12Mi       715Mi       6.2Gi
Swap:            0B          0B          0B
```

- **total:** Total installed RAM
- **used:** RAM in use by applications
- **free:** Completely free RAM
- **available:** RAM available for new applications (includes recoverable cache)

### Complete Hardware

```bash
sudo lshw           # Lists ALL detected hardware
sudo lshw -short    # Summarized version
sudo lshw -c memory # Only memory information
```

---

## Resource Monitoring

### 📊 `vmstat` - Virtual Memory Statistics

```bash
vmstat              # Single snapshot
vmstat 2 5          # Updates every 2 seconds, 5 times
```

**Important columns:**
- **r:** Processes waiting for CPU
- **b:** Blocked processes
- **swpd:** Swap memory used
- **free:** Free memory
- **si/so:** Swap in/out
- **bi/bo:** Blocks in/out (disk I/O)
- **us:** % CPU in user mode
- **sy:** % CPU in system mode
- **id:** % CPU idle
- **wa:** % CPU waiting for I/O

### 📈 `top` and `htop` - Real-Time Monitoring

```bash
top                 # Basic process monitor
htop                # Advanced monitor (requires installation)
```

**Useful shortcuts in `top`:**
- `M` - Sort by memory usage
- `P` - Sort by CPU usage
- `k` - Kill a process
- `q` - Quit
- `1` - Show all cores individually

**Install htop:**
```bash
sudo apt install htop
```

`htop` is more visual and intuitive than `top`, with colors and progress bars.

### 🔍 Processes with Highest CPU Consumption

```bash
ps -eo pcpu,pid,user,args | sort -k 1 -r | head -10
```

**Command explanation:**
- `ps -eo` - Shows processes with custom format
- `pcpu` - % of CPU used
- `pid` - Process ID
- `user` - Process owner
- `args` - Complete command
- `sort -k 1 -r` - Sort by first column (CPU), reverse (highest first)
- `head -10` - Show only the first 10

**Alternative with `less` for navigation:**
```bash
ps -eo pcpu,pid,user,args | sort -r -k1 | less
```

### 📊 `mpstat` - Multi-Processor Statistics

```bash
sudo apt install sysstat    # Install statistics tools
mpstat                      # Stats for all CPUs
mpstat -P ALL               # Stats for each individual core
```

### 📈 `sar` - System Activity Reporter

```bash
sar -u 2 5          # CPU usage every 2 seconds, 5 times
sudo sar            # System activity history
```

### 💾 `iostat` - Input/Output Statistics

```bash
iostat              # Disk I/O and CPU statistics
iostat -x           # Extended version with more details
iostat 2            # Updates every 2 seconds
```

### 🎯 `nmon` - Nigel's Monitor

```bash
sudo apt install nmon
nmon
```

**Navigation in `nmon`:**
- `c` - Show CPU
- `m` - Show memory
- `d` - Show disks
- `n` - Show network
- `q` - Quit

It's an all-in-one, very visual monitoring tool.

---

## Network Commands

### Network Interface Information

```bash
ip a                # Shows all interfaces and their IPs
ip addr show        # Same as above
```

**Common interfaces on AWS:**
- `ens5` - Main network interface on EC2 instances
- `lo` - Loopback (127.0.0.1)

### `netstat` - Network Statistics

```bash
sudo apt install net-tools    # Install network tools

netstat -i                    # Interface statistics
netstat -a                    # All connections
netstat -l                    # Only listening ports
netstat -tulpne              # Most useful combination
```

**Breakdown of `netstat -tulpne`:**
- `-t` - TCP
- `-u` - UDP
- `-l` - Listening
- `-p` - Shows PID and program name
- `-n` - Shows numbers instead of names
- `-e` - Extended information

**Example: Search for SSH connections**
```bash
netstat -a | grep --color ssh
```

**View routing table:**
```bash
netstat -rn         # -r = routing, -n = numeric
```

### `iftop` - Real-Time Bandwidth Monitoring

```bash
sudo apt install iftop
sudo iftop -i ens5      # Monitor ens5 interface
```

Shows in real-time which connections are using the most bandwidth.

### `ifstat` - Interface Statistics

```bash
sudo apt install ifstat
ifstat              # Shows incoming/outgoing traffic per interface
```

---

## Diagnostic Tools

### Command and Documentation Search

```bash
man command         # Complete manual for a command
man lscpu           # Example: lscpu manual

whatis command      # Brief description of a command
whatis inode        # Example: what is an inode

apropos word        # Search commands related to a word
apropos systemctl   # Search everything related to systemctl
apropos "mass scan" # Search for phrases in quotes
```

### `tldr` - Simplified Version of Manuals

```bash
sudo apt install tealdeer
tldr --update       # Update the database
tldr vmstat         # Practical examples of vmstat
tldr netstat        # Examples of netstat
```

`tldr` (too long; didn't read) shows practical examples instead of extensive manuals.

### Check Environment Variables

```bash
echo $TERM          # Shows the value of the TERM variable
echo $PATH          # Shows command search paths
echo $HOME          # User's home directory
```

### Search Available Packages

```bash
apt-cache search name       # Search packages containing "name"
apt-cache search ifstat     # Example: search for ifstat
apt-cache search iftop      # Example: search for iftop
```

### File System Hierarchy

```bash
man hier            # Manual explaining the Linux directory structure
```

Documents what each directory contains:
- `/bin` - Essential binaries
- `/etc` - Configuration files
- `/var` - Variable data (logs, caches)
- `/home` - User directories
- `/dev` - Device files

---

## Navigation and Utilities

### `pushd` and `popd` - Directory Stack

```bash
pushd /var/log      # Change to /var/log and save current directory
pushd /dev          # Change to /dev and save previous one
pushd /etc          # Change to /etc and save previous one
popd                # Return to previous directory in the stack
popd                # Return to the next previous one
```

**Advantages:**
- Quickly navigate between multiple directories
- Don't need to remember full paths
- Useful in scripts that change directories frequently

**View the stack:**
```bash
pushd               # Without arguments, shows directories in the stack
```

### `pwd` - Print Working Directory

```bash
pwd                 # Shows current directory
pwd -P              # Shows physical path (resolves symlinks)
```

### Command History

```bash
history             # Shows all executed commands
history | grep ssh  # Search commands containing "ssh"
!number             # Execute command number X from history
!!                  # Execute last command
!$                  # Last argument of previous command
```

---

## 📝 Essential Commands Summary

| Category | Command | Description |
|----------|---------|-------------|
| **System** | `uname -a` | Kernel and architecture info |
| | `lsb_release -a` | Distribution info |
| | `cat /etc/os-release` | System version |
| **CPU** | `lscpu` | CPU info (fast way) |
| | `mpstat -P ALL` | Stats per core |
| **Memory** | `free -h` | Available RAM |
| | `vmstat` | Virtual memory statistics |
| **Processes** | `top` / `htop` | Process monitor |
| | `ps -eo pcpu,pid,user,args` | Processes by CPU |
| **Network** | `ip a` | Network interfaces |
| | `netstat -tulpne` | Ports and connections |
| | `sudo iftop -i ens5` | Bandwidth monitoring |
| **I/O** | `iostat` | Disk statistics |
| | `lshw` | List all hardware |
| **Help** | `man command` | Complete manual |
| | `tldr command` | Practical examples |
| | `whatis command` | Brief description |

---

## 🎯 Suggested Next Steps

1. **Master `top` and `htop`:**
   - Practice identifying problematic processes
   - Learn to kill processes from `top` (press `k`)
   - Configure `htop` to your liking (F2 for setup)

2. **Explore system logs:**
   ```bash
   cd /var/log
   less syslog          # General system log
   less auth.log        # Authentication attempts
   ```

3. **Learn about services:**
   ```bash
   systemctl status ssh         # Service status
   systemctl list-units --type=service  # All services
   ```

4. **Continuous monitoring:**
   - Configure `sar` to collect historical data
   - Learn to read `nmon` graphs
   - Practice with `watch` to automatically update commands:
     ```bash
     watch -n 2 free -h    # Update free every 2 seconds
     ```

---

## 📚 Additional Resources

- **Linux Manual:** `man intro`
- **System Hierarchy:** `man hier`
- **AWS EC2 Guide:** [docs.aws.amazon.com/ec2](https://docs.aws.amazon.com/ec2)

---

**Date:** Thu Oct 30 11:08:54 PM -05 2025  
**System:** Ubuntu 24.04.3 LTS (Noble Numbat)  
**Instance:** AWS EC2 (x86_64)
