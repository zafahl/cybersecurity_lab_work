# 🖥️ Domain Controller Setup

## Server Info
- **OS**: Windows Server 2022 (Eval ISO)
- **Hypervisor**: VirtualBox
- **Hostname**: DC01
- **Domain**: `lab.local`
- **IP**: `192.168.1.10` (static)

## Roles Installed
- Active Directory Domain Services
- DNS Server
- DHCP Server
- WSUS
- Web Server (IIS)
- AD Certificate Services

## Features Enabled
- Group Policy Management
- Remote Server Admin Tools
- Telnet Client
- Windows PowerShell ISE
- Certificate Web Enrollment
- Process Activation Service

## Configuration Summary
- Promoted server to Domain Controller
- Created OUs: `Users`, `Servers`, `TestClients`, `Admins`
- Created test users and groups
- Configured static IP and DNS internally

## Snapshot Saved After
- Initial install
- Roles/features added
- Domain promotion complete
