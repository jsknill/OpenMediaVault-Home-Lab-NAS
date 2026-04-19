# OpenMediaVault Home Lab NAS

## 📌 Project Overview
This project documents the design and implementation of a **home lab Network Attached Storage (NAS)** using OpenMediaVault 8 Synchrony (OMV).

The goal of this build was to:
- Create a centralized storage solution
- Implement **redundancy with RAID**
- Deploy **automated workstation backups**
- Build an **offline backup strategy**
- Gain hands-on experience with Linux-based storage systems

---

## 🖥️ Hardware Used

### NAS System
- **Motherboard:** ASUS Maximus VII Formula (Z97)
- **CPU:** Intel i5-4690K
- **RAM:** 16GB DDR3 Corsair Vengeance
- **Boot Drive:** 250GB WD SSD (OMV OS)
- **Storage Array:** 4x 1TB HDDs (RAID 10)
- **External Backup Drive:** 4TB USB SSD

### Client System
- Windows 11 Workstation

---

## 🧱 Architecture Overview


[ Windows PC ]
│
│ (Veeam Backup)
▼
[ NAS (RAID 10 Array) ]
│
│ (rsync)
▼
[ External 4TB Drive (Offline Backup) ]


---

## 💾 Storage Configuration

### RAID Configuration
- **RAID Type:** RAID 10
- **Purpose:** Balance of redundancy and performance
- **Usable Capacity:** ~2TB
- **Fault Tolerance:** Up to 1 drive per mirrored pair

### File Systems
- EXT4 used across all volumes

---

## 📂 Shared Folders

| Share Name | Purpose |
|-----------|--------|
| `NAS` | General storage |
| `backups` | Veeam backup repository |
| `offline-backup` | Rsync replication target |

---

## 🔐 Permissions Model

This project required understanding the difference between:

### 1. SMB Permissions (Network Access)
- Controls user authentication over the network
- Configured via OMV Shared Folder permissions

### 2. Filesystem ACL (Critical Fix)
- Controls actual read/write access on disk
- Required for rsync and system-level operations

### Key Lesson:
> SMB permissions alone are NOT sufficient — ACLs must be configured for services like rsync.

---

## 🔄 Backup Strategy (3-2-1 Model)

### Primary Backup
- Tool: **Veeam Agent for Microsoft Windows (Free)**
- Target: `\\NAS\backups`
- Schedule: Daily @ 2:00 AM
- Type: Full system image backup

### Secondary Backup (Offline)
- Tool: **OMV rsync**
- Source: `backups`
- Destination: `offline-backup` (4TB external drive)

### Design Goal:
- Protect against:
  - Disk failure (RAID)
  - OS corruption (Veeam image)
  - Ransomware / NAS compromise (offline backup)

---

## ⚙️ Rsync Configuration

- Type: Local
- Mode: Archive
- Recursive: Enabled
- Permissions Preserved
- Scheduled execution

### Issue Encountered:
- Rsync failed with:
  - `500 Internal Server Error`
  - `Connection lost`

### Root Cause:
- Missing filesystem ACL permissions
- System required reboot after package updates

### Resolution:
- Applied ACL recursively to both source and destination
- Rebooted NAS to apply system updates
- Rsync executed successfully afterward

---

## 💻 Windows Integration

- NAS mapped as network drives:
  - `Z:` → Main NAS share
  - `Y:` → Backups share
  - `X:` → Offline backup drive

- Persistent mapping achieved using:
  - Saved credentials
  - Standard drive letters (not A:)

---

## 🧪 Testing & Validation

✔ Verified Veeam backup creation  
✔ Verified restore points  
✔ Verified file-level restore  
✔ Verified rsync replication  
✔ Verified offline backup accessibility  

---

## 🧠 Skills Demonstrated

- RAID configuration (RAID 10)
- Linux filesystem and permissions (ACL vs SMB)
- Network file sharing (SMB/CIFS)
- Backup architecture design (3-2-1 strategy)
- Troubleshooting Linux services (rsync failures)
- Windows ↔ Linux integration
- Disaster recovery planning

---

## 🚧 Future Improvements

- Deploy Docker on OMV
- Implement Nextcloud (self-hosted cloud storage)
- Add snapshot-based backups
- Network segmentation for lab security
- Monitoring and alerting

---

## 📚 Key Takeaways

- RAID is NOT a backup — it is redundancy
- Backup systems require layered design
- Linux permissions are critical for service functionality
- Testing restores is just as important as backups

---

## 👤 Author

**Jordan Knill**  
Aspiring IT / Cybersecurity Professional  
