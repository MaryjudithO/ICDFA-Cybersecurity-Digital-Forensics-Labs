# CIP-B101 Lab 1B: PC System Fundamentals, Disk Sectors and Partition Analysis

## Student Information

**Student Name:** Maryjudith Chidinma Ogunaka  
**Registration Number:** C11/26/FCDF/17151  
**Programme:** ICDFA Fellowship in Cybersecurity and Digital Forensics  
**Cohort:** 11  
**Course:** CIP-B101 Basic Computer Skills for Digital Forensics  
**Lab:** Lab 1B  
**Date:** 05 November 2026

---

## Overview

This lab profiled a Kali Linux virtual machine as if it were a system in a forensic investigation. It identified the hardware, operating system, processes and storage, mapped digital evidence to the layers of a computer system, and used sector counts to calculate disk and partition sizes by hand. It also covered the partition table, the MBR boot structure and the file systems in use.

All commands were read-only. Nothing was formatted, deleted, resized or written to the disk.

---

## Repository Contents

| File / Folder | Description |
|---------------|-------------|
| [Lab1B_PC_System_Fundamentals_Report.md](Lab1B_PC_System_Fundamentals_Report.md) | The full lab report with calculations and analysis |
| [Screenshots/](Screenshots) | The 10 evidence screenshots used in the report |

---

## Key Findings

| Item | Result |
|------|--------|
| Operating system | Kali GNU/Linux Rolling, kernel 6.12.13-amd64, VMware virtual machine |
| Disk | /dev/sda, 25 GiB, 52,428,800 sectors of 512 bytes (26,843,545,600 bytes) |
| Partition table | DOS (MBR), boot flag on /dev/sda1 |
| Partitions | sda1 primary ext4 (23.67 GiB), sda2 extended (1.33 GiB), sda5 logical swap (1.33 GiB) |
| Root file system | ext4, mounted at / with read-write access, about 81% used |
| Processes | 233 total: 1 running, 232 sleeping, 0 zombie |

---

## Tools Used

`uname`, `hostnamectl`, `lsblk`, `free`, `lscpu`, `df`, `blkid`, `ps`, `top`, `fdisk`, `parted`, `file`, `mount`

---

## Screenshot Index

| Part | File | What it shows |
|------|------|---------------|
| A | `Lab1B_01_System_Identification_uname_hostnamectl_lsblk.png` | System identification: uname, hostnamectl, lsblk |
| A | `Lab1B_02_Memory_and_CPU_free_lscpu.png` | Memory and processor: free -h, lscpu |
| A | `Lab1B_03_CPU_Virtualization_Caches_Vulnerabilities_lscpu.png` | Virtualisation, caches and vulnerabilities: lscpu |
| A | `Lab1B_04_Disk_Usage_and_UUIDs_df_blkid.png` | Disk usage and UUIDs: df -h, blkid |
| B | `Lab1B_05_Process_List_ps_aux_and_top.png` | Process list: ps aux, top |
| B | `Lab1B_06_Process_Monitoring_top_Extended_View.png` | Process monitoring: top extended view |
| C | `Lab1B_07_Disk_Geometry_fdisk.png` | Disk geometry and partition table: fdisk -l |
| D | `Lab1B_08_Partition_Table_parted_and_lsblk_f.png` | Partition table and file systems: parted, lsblk -f |
| E | `Lab1B_09_MBR_Verification_fdisk_Disklabel_and_file.png` | MBR verification: fdisk Disklabel, file -s |
| F | `Lab1B_10_File_System_Types_df_T_and_mount.png` | File system types and mounts: df -T, mount |


