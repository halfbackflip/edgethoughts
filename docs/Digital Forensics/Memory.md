# Memory Forensics

## Background
Memory is valuable forensic artifact which can hold running processes, network connections, loaded executables, logged in users, recent commands, injected code, encryption keys, passwords and more. Memory forensics are essential for detecting fileless malware, which is malware which exists only as in memory, operating without an executable and leaving minimal footprint on disk. 

Random Access Memory (RAM) is usually the focus of memory acquisition. RAM is divided into two areas:

* Kernel Space: Operating system and low-level services, i.e. device drivers.
* User Space: Processes launched by the user or applications. 

User space processes are particularly crucial. This memory contains the following areas:

* Stack: Stores emporary data such as function arguments and return addresses. It expands and contracts as functions are called and returned.
* Heap: Used for dynamic memory allocation during runtime, such as objects.
* Executable: Holds code or instructions the CPU executes.
* Data sections: Stores global variables and data the executable requires.

Types of memory dumps generally include:

* Full: Captures all RAM, including user and kernel space.
* Process Dump: Captures the memory of a singular process.
* Pagefile / Swap Analysis: Systems offload some memory content to disk when RAM is full. On Windows, this is stored in pagefile.sys, and on Linux, in a swapfile. These files can be analysed further. 

Windows contains some memory artifacts which can be acquired if the system has been put to sleep or powered off. 

* `%SystemDrive%\hiberfil.sys` Created when a computer has been put to sleep.
* `%SystemRoot%\pagefile.sys` Contains infrequently accessed memory objects from RAM; stored physically in this file.
* `%SystemRoot%\MEMORY` Default location where memory dumps are stored after system failures, i.e. BSOD. 

## Acquisition
Dues to its volatile nature, memory is often the first evidence acquired in a forensics investigation. Tools used to acquire memory include:

| Tool | Description | Link |
|---|---|---|
| Magnet Dumpot | Windows memory acquisition tool | [https://www.magnetforensics.com/resources/magnet-dumpit-for-windows/](https://www.magnetforensics.com/resources/magnet-dumpit-for-windows/) |
| WinPmem | Lightweight windows memory acquisition executable. | [https://winpmem.velocidex.com/](https://winpmem.velocidex.com/) |
| procdump | sysinternals utility for dumping the memory of a given process. | [https://learn.microsoft.com/en-us/sysinternals/downloads/procdump](https://learn.microsoft.com/en-us/sysinternals/downloads/procdump) |
| FTK Imager | Row 1, Col 2 | [https://www.exterro.com/digital-forensics-software/ftk-imager](https://www.exterro.com/digital-forensics-software/ftk-imager) |
| LiME | Classic linux memory acquisition tool. No longer supported. | [https://github.com/504ensicsLabs/LiME](https://github.com/504ensicsLabs/LiME) |
| dd  | Built in linux utility. Requires sudo priveleges to install the fmem kernel module | [https://www.levelblue.com/blogs/levelblue-blog/volatile-data-acquisition-on-linux-systems-using-fmem](https://www.levelblue.com/blogs/levelblue-blog/volatile-data-acquisition-on-linux-systems-using-fmem) |

# Evasion
Attackers will often use anti-forensic techniques to hid their presence in memory and make reverse engineering their tools challenging. These examples include:

| Tool | Description |
|---|---|
| Unlinked & hidden modules | Malware will unlink itself from process lists. This hides it from system tools. |
| DKOM (Direct Kernel Object Manipulation) | This technique alters kernel structures to hide processes, threads, or drivers from system tools. |
| Code injection |	Malicious code is injected into legitimate processes (i.e. notepad.exe) to evade detection. |
| Memory patching | Malware modifies memory or system APIs at runtime for defense evasion. |
| Hooking APIs or system calls | Intercepts and alters the output of critical system functions. |
| Encrypted payloads | Payloads are kept encrypted until execution time, making static analysis difficult.|
| Trigger-based payloads | The in-memory malware only unpacks or runs when specific conditions are met, evading defenses during a normal inspection |

## Memory Analysis

### Volatility

[Volatiltiy](https://volatilityfoundation.org/the-volatility-framework/) is a memory analysis program written in python. This is an industry standard tool for memory analysis. 

Here are some cheat sheet commands below. Note: these commands apply to version 3 only. Version 2 was deprecated in May 2025.

| Command | Description |
|---|---|
| `python3 vol.py -h` | volatility help menu | 
| `python3 vol.py -f "<filepath>" windows.info` | Retrieves memory profile /symbols from a windows memory dump | 
| `python3 vol.py -f "<filepath>" windows.plist` | Retrieves windows processes | 
| `python3 vol.py -f "<filepath>" windows.pstree` | Lists windows processes in tree view | 
| `python3 vol.py -f "<filepath>" windows.pstree` | Scans windows processes | 
| `python3 vol.py -f "<filepath>" windows.cmdline` | Lists windows commandlines | 
| `python3 vol.py -f "<filepath>" windows.handles --pid <PID>` | Lists file handles for a given process | 
