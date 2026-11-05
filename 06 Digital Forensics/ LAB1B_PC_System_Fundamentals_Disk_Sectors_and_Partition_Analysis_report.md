 Lab 1B: PC System Fundamentals, Disk Sectors and Partition Analysis

| Item | Details |
|------|---------|
| Student Name | Maryjudith Chidinma Ogunaka |
| Registration Number | C11/26/FCDF/17151 |
| Programme | ICDFA Fellowship in Cybersecurity and Digital Forensics (Cohort 11) |
| Course Code | CIP-B101 |
| Course Title | Basic Computer Skills for Digital Forensics |
| Lab | Lab 1B: PC System Fundamentals, Disk Sectors and Partition Analysis |
| Platform | Kali GNU/Linux Rolling virtual machine (VMware) |
| Date | 05 November 2026 |


## 1. Introduction

This laboratory introduced the basic computer system knowledge needed for beginner digital forensic work. A Kali Linux virtual machine was treated as a system that might later be used in an investigation. Its hardware, operating system, storage, memory and running processes were identified, and the layers of a computer system were mapped to the evidence each one can produce.

Disk sector information was then used to calculate disk and partition sizes by hand, the partition table was inspected, and the boot process, the MBR partitioning scheme and the file systems in use were explained. All work was observation only: no command in this lab formatted, deleted, resized or wrote to the disk.

## 2. Objectives

- Identify the hardware, operating system, storage, memory and processor of the system.
- Map digital evidence sources to the layers of a computer system.
- Observe running processes and explain program, process and thread.
- Describe disk sectors and calculate disk and partition sizes from sector counts.
- Inspect the partition table using safe, read-only commands.
- Explain the PC boot process and compare MBR and GPT.
- Identify the file systems in use and explain why they matter in forensics.


## 3. Lab Environment and Tools

### 3.1 System under examination

| Item | Value |
|------|-------|
| Hostname | icdfa |
| Operating system | Kali GNU/Linux Rolling |
| Kernel | Linux 6.12.13-amd64 |
| Architecture | x86-64 |
| Virtualisation | VMware (full virtualisation), VMware Virtual Platform |
| Processor | Intel Core i7-10610U @ 1.80 GHz, 1 vCPU |
| Memory | 1.9 GiB RAM, 1.3 GiB swap |
| Virtual disk | /dev/sda, 25 GiB |


### 3.2 Commands used

| Command | Purpose |
|---------|---------|
| `uname -a`, `hostnamectl` | Identify the kernel, operating system and platform |
| `lsblk`, `lsblk -f` | List disks, partitions and file systems |
| `free -h`, `lscpu` | Show memory and processor information |
| `df -h`, `df -T` | Show file system size, usage and type |
| `blkid` | Show file system UUIDs and types |
| `ps aux`, `top` | Observe running processes |
| `sudo fdisk -l` | Show disk geometry and the partition table |
| `sudo parted -l` | Show the partition table in decimal units |
| `file -s /dev/sda` | Identify the type of the first sector |
| `mount` | Show how file systems are mounted |


### 3.3 Safety and evidence handling

Every command above only reads information. No partition was created, formatted, resized or deleted, and no command wrote to the disk.

## 4. Part A: System Identification and Evidence Layer Mapping

### 4.1 System specification

<img width="1273" height="735" alt="Lab1B_01_System_Identification_uname_hostnamectl_lsblk" src="https://github.com/user-attachments/assets/478deb4e-00cb-404e-827b-eb461d770d50" />


*Figure 1: Kernel, platform and disk layout: uname -a, hostnamectl and lsblk.*

**Observation:** The system runs Kali GNU/Linux Rolling with kernel 6.12.13-amd64 on x86-64. hostnamectl reports a VMware virtual machine (chassis: vm). lsblk shows a 25 GB disk (sda) with sda1 (23.7G, mounted at /), sda2 (1K, extended) and sda5 (1.3G, swap).

<img width="1272" height="805" alt="Lab1B_02_Memory_and_CPU_free_lscpu" src="https://github.com/user-attachments/assets/c8fb775d-99a9-4eb8-8a22-63c6313078b8" />


*Figure 2: Memory and processor information: free -h and lscpu.*

**Observation:** The system has 1.9 GiB of RAM (779 MiB used, 1.2 GiB available) and 1.3 GiB of swap, with no swap in use. The processor is a single Intel Core i7-10610U at 1.80 GHz, with 1 core and 1 thread.

<img width="1287" height="757" alt="Lab1B_03_CPU_Virtualization_Caches_Vulnerabilities_lscpu" src="https://github.com/user-attachments/assets/ed0c8216-fab0-4a40-95c9-a771d107343c" />

*Figure 3: Virtualisation, cache and vulnerability information from lscpu.*

**Observation:** The hypervisor vendor is VMware with full virtualisation. The caches are L1d 32 KiB, L1i 32 KiB, L2 256 KiB and L3 8 MiB. The vulnerability list shows which CPU weaknesses are mitigated, not affected, or dependent on the hypervisor.

<img width="1269" height="760" alt="Lab1B_04_Disk_Usage_and_UUIDs_df_blkid" src="https://github.com/user-attachments/assets/7098eb7e-e76f-4874-8f92-9b224a890ee5" />

*Figure 4: Disk usage and identifiers: df -h and blkid (run with sudo).*

**Observation:** The root partition /dev/sda1 is 24G in size with 18G used (81%). Plain blkid returned nothing, and sudo blkid was needed to read the UUIDs: sda1 is ext4 and sda5 is swap.

### 4.2 Summary

The examination system was a VMware virtual machine running Kali GNU/Linux Rolling on x86-64. It used a 25 GiB virtual disk divided into a 23.7 GiB root partition and a 1.3 GiB swap partition inside an extended partition. The virtual machine had about 2 GB of RAM. The root partition was mounted at / with about 81% utilisation.

### 4.3 Evidence layer mapping

| Layer / Source | Example Evidence | Where It May Be Found | Why It Matters |
|----------------|------------------|-----------------------|----------------|
| User layer | Opened files, downloads, searched terms | Recent files, browser history, shell history | Associates activity with a specific user |
| Application layer | Browser cache, logs, chat records | Application folders and databases | Reveals application usage and activity |
| Operating system layer | Event logs, user accounts, installed software | Linux /var/log, Windows logs and registry | Establishes a system activity timeline |
| Disk layer | Partitions, deleted files, unallocated space | File system and unallocated space | Preserves persistent evidence |
| Memory / process layer | Running processes, network sessions | RAM capture, process list | Captures the live state of the system |


Evidence can exist in more than one layer. For example, a downloaded file leaves a record in the user layer (the Downloads folder), the application layer (browser history), the operating system layer (logs) and the disk layer (file system metadata), so each layer can confirm the others.

## 5. Part B: Process Investigation

<img width="1282" height="729" alt="Lab1B_05_Process_List_ps_aux_and_top" src="https://github.com/user-attachments/assets/a8bf0aa5-166f-4fcb-8e84-4080a18996c8" />

*Figure 5: Process list: ps aux | head and top -b -n 1 | head -20.*

**Observation:** The first processes are root-owned kernel and system tasks, starting with PID 1 (/sbin/init splash, which runs systemd).

<img width="1282" height="729" alt="Lab1B_05_Process_List_ps_aux_and_top" src="https://github.com/user-attachments/assets/0e7de89f-fa05-46db-bded-6e70263c206b" />

*Figure 6: Process monitoring: top output with more processes visible.*

**Observation:** top reports 233 total tasks: 1 running, 232 sleeping, 0 stopped and 0 zombie. The system had been up for about 11 minutes with 2 users logged in, a load average of 0.00, 0.14, 0.18, and a CPU that was 64.3% idle.

### 5.1 Selected processes

| Process | PID | Likely purpose |
|---------|-----|----------------|
| systemd | 1 | First process; starts and manages system services |
| kthreadd | 2 | Creates the other kernel threads |
| ksoftirqd | 16 | Handles software interrupts on the CPU |


### 5.2 Program, process and thread

- A **program** is a file on disk containing instructions, such as /usr/bin/top. It does nothing until it is run.
- A **process** is a program that is running. It has its own PID, memory space and resources.
- A **thread** is a unit of execution inside a process. Threads of the same process share its memory.

### 5.3 Process investigation write-up

The top utility was used to examine active processes. The system had 233 processes, with one running and 232 sleeping, and no zombie processes. It had been running for about 11 minutes with minimal processor use. The list showed standard kernel and system management processes: systemd, kthreadd, kworker, rcu_tasks and ksoftirqd. These handle task scheduling, interrupt handling and resource management.

### 5.4 Why process monitoring matters in digital forensics

Process analysis shows what was happening on a system at the time of examination. Investigators can find unauthorised applications, malware, suspicious scripts, persistence mechanisms and active network connections. Memory-resident threats that are not yet written to disk can also be found this way.


## 6. Part C: Disk Sector and Capacity Calculations

<img width="1297" height="793" alt="Lab1B_07_Disk_Geometry_fdisk" src="https://github.com/user-attachments/assets/da6d26a5-c11b-4c62-9ec3-99d6db2b1e6b" />

*Figure 7: Disk geometry and partition table: sudo fdisk -l.*

**Observation:** /dev/sda is 25 GiB (26,843,545,600 bytes) with 52,428,800 sectors of 512 bytes. The disklabel type is dos and /dev/sda1 carries the boot flag (*).

### 6.1 Questions

| Question | Answer |
|----------|--------|
| Sector size | 512 bytes (logical and physical) |
| Number of sectors on the disk | 52,428,800 |
| Partition table type | dos, which is MBR (Master Boot Record) |
| Bootable partition | /dev/sda1 |

### 6.2 Verify the disk capacity

Disk size = total sectors x bytes per sector

52,428,800 x 512 = 26,843,545,600 bytes

26,843,545,600 / 1024 / 1024 / 1024 = 25 GiB

### 6.3 Slide calculation (reference example)

Sectors per track = 400, heads = 12, cylinders = 17000, bytes per sector = 512.

| Item | Formula | Answer |
|------|---------|--------|
| Total sectors | 400 x 12 x 17000 | 81,600,000 sectors |
| Total bytes | 81,600,000 x 512 | 41,779,200,000 bytes |
| Kilobytes | 41,779,200,000 / 1024 | 40,800,000 KB |
| Megabytes | 40,800,000 / 1024 | 39,843.75 MB |
| Gigabytes | 39,843.75 / 1024 | 38.91 GB |

### 6.4 Partition sizes from the partition table

Partition size in GiB = sector count x 512 / 1024 / 1024 / 1024. The sector counts come from the fdisk output in Figure 7.

| Partition | Start sector | End sector | Sector count | Calculation | Size |
|-----------|--------------|------------|--------------|-------------|------|
| /dev/sda1 (Linux, boot) | 2048 | 49641471 | 49,639,424 | 49,639,424 x 512 = 25,415,385,088 bytes | 23.67 GiB |
| /dev/sda2 (extended) | 49643518 | 52426751 | 2,783,234 | 2,783,234 x 512 = 1,425,015,808 bytes | 1.33 GiB |
| /dev/sda5 (Linux swap, logical) | 49643520 | 52426751 | 2,783,232 | 2,783,232 x 512 = 1,425,014,784 bytes | 1.33 GiB |

The sector count is checked as end sector - start sector + 1. For /dev/sda1: 49,641,471 - 2,048 + 1 = 49,639,424.

### 6.5 Disk sector and capacity write-up

The fdisk -l utility was used to inspect disk geometry and partition information. The virtual disk contained 52,428,800 sectors of 512 bytes. Multiplying the sector count by the sector size gave 26,843,545,600 bytes (25 GiB).

The DOS disk label shows the MBR partitioning scheme. The bootable partition is /dev/sda1, which holds the Linux installation. The other partitions are an extended partition and a Linux swap partition.


## 7. Part D: Partition Analysis

<img width="1266" height="753" alt="Lab1B_08_Partition_Table_parted_and_lsblk_f" src="https://github.com/user-attachments/assets/f5818a6b-de54-49c7-9424-abb0ffa28609" />


*Figure 8: Partition table and file systems: sudo parted -l and lsblk -f.*

**Observation:** parted shows the msdos partition table with a primary ext4 partition (boot flag), an extended partition, and a logical linux-swap(v1) partition. lsblk -f shows the UUIDs and that sda1 is mounted at / with 4.3G available.

### 7.1 Partition structure

| Partition | Type | File system | Size (parted) | Purpose |
|-----------|------|-------------|---------------|---------|
| /dev/sda1 | Primary | ext4 | 25.4 GB | Operating system partition |
| /dev/sda2 | Extended | None | 1.4 GB | Container for the logical partition |
| /dev/sda5 | Logical | linux-swap(v1) | 1.4 GB | Virtual memory (swap space) |

### 7.2 Key findings

- Partition table type: msdos (MBR).
- Main file system: ext4. Swap file system: linux-swap(v1).
- The boot flag is set on /dev/sda1.
- The root file system is mounted at /.
- No EFI System Partition appears in the partition list.

### 7.3 Why the sizes look different

parted uses decimal units (GB, 1000-based) while fdisk and lsblk use binary units (GiB, 1024-based). The same partition therefore shows different numbers: /dev/sda1 is 23.67 GiB, which is 25.4 GB (23.67 x 1.0737). The swap partition is 1.33 GiB, which is 1.4 GB.

The usage percentage also differs. lsblk -f shows 76% and df shows 81%. df calculates Used / (Used + Available) = 18,524,008 / (18,524,008 + 4,472,152) = 80.6%, while lsblk calculates Used / Size = 18,524,008 / 24,253,528 = 76.4%. The difference is the space reserved by the file system.

### 7.4 Partition analysis write-up

The parted and lsblk utilities were used to examine the partition layout and file systems. The system uses an MBR (msdos) scheme with one primary, one extended and one logical partition. The primary partition (/dev/sda1) is formatted with ext4 and holds the operating system. The extended partition (/dev/sda2) contains the logical swap partition (/dev/sda5) used for virtual memory. The boot flag on the primary partition shows it contains the files needed to start the operating system.

### 7.5 Forensic importance

Partition analysis helps investigators identify storage locations, understand how a disk is organised, locate operating systems and decide where evidence may be. Knowing the file system types helps in choosing the right acquisition and analysis methods.


## 8. Part E: Boot Process, MBR and GPT Concepts

<img width="1288" height="760" alt="Lab1B_09_MBR_Verification_fdisk_Disklabel_and_file" src="https://github.com/user-attachments/assets/277761cd-3d13-4eb2-b916-2c7acbc03144" />


*Figure 9: MBR verification: sudo fdisk -l | grep "Disklabel" and sudo file -s /dev/sda.*

**Observation:** fdisk reports Disklabel type: dos. The file utility identifies /dev/sda as a DOS/MBR boot sector, which confirms boot code in the first sector of the disk.

### 8.1 The PC boot process

1. **Power-on and POST:** the hardware is initialised and checked.
2. **BIOS/UEFI firmware:** the boot device order and firmware settings are applied.
3. **MBR or GPT / EFI boot record:** the firmware locates the bootable partition or EFI boot entry.
4. **Boot loader:** the boot loader or boot manager selects the operating system and loads the kernel.
5. **Kernel and operating system:** the kernel starts, and the operating system takes control.

This system uses an MBR (dos) partition table and has no EFI System Partition (Figures 7 and 8), which is consistent with BIOS/Legacy boot.

### 8.2 What is MBR?

The Master Boot Record occupies the first sector of a storage device (sector 0) and contains:

1. Bootstrap code used during system startup.
2. The partition table.
3. The boot signature.

When a computer starts, the BIOS loads the MBR into memory and runs the boot code, which then locates the active operating system partition.

### 8.3 MBR and GPT compared

| Feature | MBR | GPT |
|---------|-----|-----|
| Age and design | Older partitioning system from the 1980s | Modern system used with UEFI |
| Size limit | Commonly about 2 TB | Supports very large disks |
| Partition information | Stored in the first sector with the boot code | Primary and backup GPT headers and partition entries |
| Forensic issue | Critical information is in sector 0, and damage can affect partition interpretation | Backup structures can help recovery and validation |

### 8.4 Boot process write-up

The fdisk and file utilities were used to examine the boot structure. The disk label type is DOS, which corresponds to the MBR scheme, and the file utility identified /dev/sda as a DOS/MBR boot sector.

### 8.5 Forensic significance

The MBR is a critical forensic artifact because malware can modify boot records to gain persistence. Examiners validate the MBR during investigations to find unauthorised changes or bootkits.


## 9. Part F: File System Analysis

<img width="1294" height="760" alt="Lab1B_10_File_System_Types_df_T_and_mount" src="https://github.com/user-attachments/assets/0e934db0-1f73-40d4-b7fa-e27a85948f3d" />


*Figure 10: File system types and mounts: df -T and mount | grep "^/dev".*

**Observation:** /dev/sda1 is ext4 and is mounted at / as (rw,relatime,errors=remount-ro), so it is mounted read-write. Disk usage is 81%. The other entries are tmpfs and devtmpfs file systems that live in memory.

### 9.1 File systems found

| Partition | File system | Mounted at |
|-----------|-------------|------------|
| /dev/sda1 | ext4 | / |
| /dev/sda5 | linux-swap(v1) | [SWAP] |
| udev, tmpfs | devtmpfs, tmpfs | /dev, /run, /tmp and others (memory-based) |

### 9.2 File system analysis write-up

The df -T and mount commands were used to identify the file system types and mounted devices. The primary partition (/dev/sda1) uses ext4, the default file system of many Linux distributions, and is mounted as the root file system (/) with read-write access. The ext4 journal improves reliability and helps keep the file system consistent after an unexpected shutdown.

### 9.3 Forensic significance

The file system decides how files, timestamps, permissions and metadata are stored. Investigators analyse file system structures to recover deleted files, identify user activity and rebuild timelines. ext4 stores metadata that shows when files were created, modified and accessed.

| File system | Common use | Forensic importance |
|-------------|------------|---------------------|
| FAT/FAT32 | Removable media, older systems | Simple structure, often used on USB drives |
| NTFS | Modern Windows | MFT entries, timestamps, permissions and journals |
| ext family | Linux | Inode-based metadata that supports timeline analysis |
| XFS, JFS, btrfs | Linux and Unix-like systems | Advanced features can affect recovery and metadata interpretation |
| HFS+ | Older macOS | Important in Mac investigations |


## 10. Final Practical Assignment: Partition Size Verification

| Task | Answer |
|------|--------|
| Partition 1 | /dev/sda1, primary, ext4, boot flag (Figure 7) |
| Partition 1 sector count | Start 2048, end 49641471, 49,639,424 sectors |
| Partition 1 size | 49,639,424 x 512 / 1024 / 1024 / 1024 = 23.67 GiB |
| Partition 2 | /dev/sda2, extended partition (Figure 7) |
| Partition 2 sector count | Start 49643518, end 52426751, 2,783,234 sectors |
| Partition 2 size | 2,783,234 x 512 / 1024 / 1024 / 1024 = 1.33 GiB |
| OS-reported sizes | lsblk: sda1 23.7G and sda2 1K (an extended container). parted: sda1 25.4GB and sda2 1425MB. df: file system 24G |
| Difference | The calculated values match lsblk (GiB). parted uses decimal GB, so 23.67 GiB shows as 25.4 GB. df shows the file system size (about 23.13 GiB), which is smaller than the partition because ext4 uses part of the space for its own metadata |


## 11. Conclusion

The objectives of the laboratory were achieved. The Kali virtual machine was profiled safely, using read-only commands only, and its evidence layers, running processes, disk geometry, partition table, boot structure and file systems were documented. The disk has 52,428,800 sectors of 512 bytes (25 GiB) and uses the MBR scheme with an ext4 root partition, an extended partition and a swap partition. Calculating the partition sizes by hand from the sector counts matched the sizes reported by the operating system once GiB and GB units were taken into account. This work matters in digital forensics because a correct system profile, partition map and file system identification are needed before evidence is acquired and analysed.
