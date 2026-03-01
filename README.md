# Secure Home NAS Deployment – OpenMediaVault 8 (Synchrony)

## Project Overview

This project documents the design, deployment, hardening, and validation of a bare-metal OpenMediaVault 8 NAS system.

The system was intentionally built with:

- A security-first configuration
- Controlled BIOS and hardware stabilization
- Network segmentation awareness
- Backup-oriented architecture
- Production-readiness validation

This NAS functions as:

- Secure home file backup target
- Network storage appliance
- Homelab infrastructure node
- System monitoring and disk health platform

---

# 1. Hardware Platform

| Component | Specification |
|-----------|---------------|
| Motherboard | ASUS Maximus VII Formula (Z97) |
| CPU | Intel i5-4690K |
| RAM | 16GB DDR3 (Corsair Vengeance – matched pair only) |
| OS Drive | 250GB WD SSD |
| Data Drive | 4TB HDD |
| Network | Ethernet (DHCP reservation → 192.168.0.2) |

---

# 2. Hardware Stabilization & BIOS Hardening

During deployment, multiple POST-level hardware faults were resolved:

### POST Errors Encountered

- A2 – IDE/SATA detection hang
- B6 – GPU initialization hang
- 55 – Memory detection failure
- Secure Boot violation after install

### Root Causes Identified

- Mixed DDR3 memory kits causing unstable training
- Improper RAM seating
- Secure Boot auto-re-enabling after CMOS reset
- SATA detection delays during hardware changes

### Corrective Actions

- Removed mixed RAM kits (no cross-brand mixing)
- Installed matched Corsair pair only
- Reseated GPU and verified PCIe latch engagement
- Loaded BIOS Optimized Defaults
- Set SATA Mode → AHCI
- Set Secure Boot → Other OS
- Enabled CSM
- Verified UEFI boot mode
- Set Boot Option #1 → UEFI OS (SSD)

Result:
System now boots consistently without POST errors.

---

# 3. OpenMediaVault Installation

Version Installed:
OpenMediaVault 8.1.1-1 (Synchrony)

Kernel:
Linux 6.18.9+deb13-amd64

Installation Actions:

- Clean install to dedicated SSD
- Root password configured
- Domain set to `internal`
- Verified successful first boot
- Confirmed system reachable via web UI

---

# 4. Network Configuration (Controlled & Predictable)

### IP Strategy

- DHCP enabled
- Router-level DHCP reservation configured:
  - 192.168.0.2 → NAS MAC address

This approach provides:
- Predictable addressing
- Router-level authority
- Simplified management
- Reduced static misconfiguration risk

### Validation Steps

- Ping test successful
- Web UI reachable
- SMB share reachable
- File transfer tested

---

# 5. Storage Configuration

### Drive Preparation

- 4TB HDD wiped
- New filesystem created
- Drive mounted properly
- Shared folder created (`data`)

### SMB Configuration

- SMB/CIFS enabled
- SMB1 explicitly disabled
- Single authenticated user created
- Windows 11 authentication validated
- Transfer test completed successfully

Access confirmed via:
```
\\192.168.0.2
```

---

# 6. Disk Health Monitoring

SMART Monitoring enabled on 4TB drive.

Configured:

- Weekly Short Self-Test
- Monthly Extended Self-Test
- Monitoring interval: 1800 seconds

Purpose:
Early detection of drive degradation or failure indicators.

---

# 7. Security Hardening Measures

This deployment intentionally prioritizes reduced attack surface.

### Authentication

- Default admin password changed immediately
- Root password unique and separate
- SSH currently disabled
- No external port forwarding configured

### Protocol Security

- SMB1 disabled
- Secure Boot set appropriately for Linux
- No unnecessary services enabled

### Network Exposure

- LAN-only accessibility
- No WAN exposure
- No UPnP dependencies
- No public services running

---

# 8. Validation Checklist

| Check | Status |
|-------|--------|
| Clean BIOS boot | ✅ |
| No POST errors | ✅ |
| Secure Boot stable | ✅ |
| IP reachable | ✅ |
| Web UI login | ✅ |
| SMB authentication | ✅ |
| File transfer success | ✅ |
| SMART tests scheduled | ✅ |

System considered stable and operational.

---

# 9. Current Architecture State

This system currently operates as:

- Dedicated NAS appliance
- Local backup target
- Controlled homelab storage node

No containers.
No exposed services.
No remote access configured.

Intentional minimalism.

---

# 10. Known Limitations

- Single 4TB data drive (no redundancy yet)
- No UPS installed
- No offsite backup
- No RAID configuration
- No snapshot strategy yet

---

# 11. Planned Improvements

- Add second 4TB drive → RAID1 mirror
- Purchase UPS (pure sine wave recommended)
- Configure alert notifications
- Implement rsync backup job
- Introduce versioned backups
- Add secondary offline backup strategy

---

# 12. Lessons Learned

- Do not mix DDR3 kits on older boards
- Secure Boot will re-enable after CMOS resets
- POST codes must be interpreted sequentially
- Hardware instability can mimic storage faults
- Always validate boot order after hardware changes

---

# Conclusion

This deployment demonstrates:

- Hardware-level troubleshooting
- BIOS-level configuration control
- Secure service configuration
- Network validation testing
- Storage lifecycle management
- Operational hardening mindset

The system is stable, validated, and running in a controlled LAN environment.
