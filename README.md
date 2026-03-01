Secure Home NAS Deployment – OpenMediaVault 8 (Synchrony)
Project Overview

This project documents the design, stabilization, hardening, and operational validation of a bare-metal OpenMediaVault 8 NAS system.

The deployment was intentionally built with:

Security-first configuration

Controlled BIOS-level hardware stabilization

Predictable network addressing

Proactive disk health monitoring

Reduced attack surface architecture

Production-readiness validation

This system currently functions as:

Secure home file backup target

Network storage appliance

Homelab infrastructure node

Disk health monitoring platform

Alert-enabled infrastructure service

1. Hardware Platform
Component	Specification
Motherboard	ASUS Maximus VII Formula (Z97)
CPU	Intel i5-4690K
RAM	16GB DDR3 (Corsair Vengeance – matched pair only)
OS Drive	250GB WD SSD
Data Drive	4TB HDD
Network	Ethernet (DHCP reservation → 192.168.0.2)
2. Hardware Stabilization & BIOS Hardening

Multiple POST-level faults were encountered and resolved during deployment.

POST Errors Encountered

A2 – IDE/SATA detection hang

B6 – GPU initialization hang

55 – Memory detection failure

Secure Boot violation after OS install

Root Causes Identified

Mixed DDR3 memory kits causing unstable memory training

Improper RAM seating

Secure Boot auto-re-enabling after CMOS reset

SATA detection timing during hardware changes

Corrective Actions

Removed mixed RAM kits (no cross-brand mixing)

Installed matched Corsair pair only

Reseated GPU and verified PCIe latch engagement

Loaded BIOS Optimized Defaults

Set SATA Mode → AHCI

Set Secure Boot → Other OS

Enabled CSM

Verified UEFI boot mode

Set Boot Option #1 → UEFI OS (SSD)

Result: Stable, repeatable, clean boot with zero POST errors.

3. OpenMediaVault Installation

Version: OpenMediaVault 8.1.1-1 (Synchrony)
Kernel: Linux 6.18.9+deb13-amd64

Installation Actions

Clean install to dedicated SSD

Root password configured (unique)

Domain set to internal

Verified first boot integrity

Web UI validated

4. Network Configuration (Controlled & Predictable)
IP Strategy

DHCP enabled

Router-level DHCP reservation configured:

192.168.0.2 → NAS MAC address

Why DHCP Reservation Instead of Static?

Router retains authority

Prevents subnet misconfiguration

Simplifies device replacement

Centralized IP management

Validation

Ping successful

Web UI reachable

SMB share reachable

File transfer validated

5. Storage Configuration
Drive Preparation

4TB HDD wiped

New filesystem created

Mounted properly

Shared folder created (data)

SMB Configuration

SMB/CIFS enabled

SMB1 explicitly disabled

Single authenticated user created

Windows 11 authentication validated

Access verified via:

\\192.168.0.2

File transfer test completed successfully.

6. Disk Health Monitoring & Alerting

Proactive monitoring was implemented to detect early hardware degradation.

SMART Configuration

SMART enabled on 4TB drive

Monitoring interval: 1800 seconds

Weekly Short Self-Test scheduled

Monthly Extended Self-Test scheduled

Email Alerting (Gmail SMTP)

Secure SMTP notifications configured using:

smtp.gmail.com

Port 587

STARTTLS encryption

Google 2FA-enabled account

Dedicated App Password (revocable)

Alert Categories Enabled

SMART failures

Filesystem errors

System events

Scheduled task failures

Update notifications

This ensures early detection of:

Disk degradation

Filesystem corruption

Service failures

System anomalies

Patch availability

7. Security Hardening Measures

This deployment prioritizes reduced attack surface and controlled exposure.

Authentication

Default admin password changed immediately

Root password unique and separate

SSH currently disabled

No external port forwarding

No WAN exposure

Protocol Security

SMB1 disabled

Secure Boot configured appropriately for Linux

Only required services enabled

Network Exposure

LAN-only accessibility

No UPnP dependencies

No public-facing services

No remote access configured

Intentional minimalism.

8. Validation Checklist
Check	Status
Clean BIOS boot	✅
No POST errors	✅
Secure Boot stable	✅
IP reachable	✅
Web UI login	✅
SMB authentication	✅
File transfer success	✅
SMART tests scheduled	✅
Email alerting verified	✅

System considered stable and operational.

9. Current Architecture State

This system currently operates as:

Dedicated NAS appliance

Local backup target

Alert-enabled infrastructure node

Controlled homelab storage service

No containers.
No exposed services.
No remote access.

Deliberately constrained.

10. Known Limitations

Single 4TB data drive (no redundancy yet)

No UPS installed

No offsite backup

No RAID configuration

No snapshot/versioning strategy yet

11. Planned Improvements (Infrastructure Roadmap)
Short-Term

Add second 4TB drive → RAID1 mirror

Purchase UPS (pure sine wave)

Configure graceful shutdown via NUT

Implement rsync backup jobs

Introduce versioned backups

Secondary offline backup strategy

Long-Term: Cybersecurity Lab Evolution

This homelab will intentionally evolve into a structured cybersecurity lab environment.

Planned capabilities include:

VLAN segmentation

Isolated test network

Vulnerable VM deployment

SIEM integration

Network monitoring stack (Zeek / Suricata)

Centralized log aggregation

Hardened SSH (key-only authentication)

Secure remote access (WireGuard / Tailscale)

Incident simulation exercises

Infrastructure documentation & change tracking

The goal is to transition from:

Storage appliance
to
Controlled security-focused infrastructure platform.

12. Lessons Learned

Do not mix DDR3 kits on older boards

Secure Boot may re-enable after CMOS resets

POST codes must be interpreted sequentially

Hardware instability can mimic storage faults

Always validate boot order after hardware changes

Always revoke exposed credentials immediately

Monitoring is not optional in production systems
