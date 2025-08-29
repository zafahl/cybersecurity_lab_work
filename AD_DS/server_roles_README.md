# 🖥️ Windows Server Roles & Features – Cybersecurity Lab Documentation

This document covers all the roles and features we explored in **Windows Server 2022** within a VirtualBox lab environment. It includes explanations, IT relevance, cybersecurity implications, attack vectors, and defenses.

---

## 1. Active Directory Services

### 🔹 Active Directory Domain Services (AD DS) ✅

* **What it does**: Core Domain Controller role. Manages users, computers, groups, authentication, Group Policy.
* **Use case**: Every enterprise with centralized logins and security policies.
* **Cybersecurity**: Prime target — Kerberoasting, Pass-the-Hash, Golden Ticket.
* **Defense**: Strong Kerberos policies, monitoring DC logs, tiered admin model.

### 🔹 Active Directory Lightweight Directory Services (AD LDS) ❌

* **What it does**: Lightweight LDAP directory. No domains, no Kerberos, no Group Policy.
* **Use case**: Applications needing a directory without full AD (DMZ apps, web apps).
* **Cybersecurity**: Can be abused if LDAP traffic not secured.
* **Defense**: LDAPS (encrypted LDAP), limit app integration scope.

### 🔹 Active Directory Certificate Services (AD CS) ✅

* **What it does**: Issues/manages digital certificates. Core of PKI.
* **Use case**: HTTPS, VPN, Wi-Fi auth, code signing.
* **Cybersecurity**: If CA compromised → attackers mint trusted certs.
* **Defense**: Use offline Root CA, restrict access, monitor cert enrollment.

### 🔹 Active Directory Rights Management Services (AD RMS) ❌

* **What it does**: Protects docs/emails with encryption + usage restrictions (prevent printing, forwarding, copying).
* **Use case**: Highly regulated industries (law, defense, gov).
* **Cybersecurity**: RMS keys = jackpot. If stolen, attackers decrypt protected docs.
* **Defense**: Strong key management, integrate with DLP. Mostly replaced by Microsoft Purview Information Protection.

### 🔹 Device Health Attestation ❌

* **What it does**: Uses TPM to verify boot integrity (Secure Boot, BitLocker).
* **Use case**: Conditional Access in Zero Trust.
* **Cybersecurity**: Prevents compromised devices from authenticating.
* **Exploitation**: Attackers may fake attestation tokens.
* **Defense**: Integrate with Intune/Azure AD. Rarely used on-prem today.

---

## 2. Core Infrastructure Roles

### 🔹 DNS Server ✅

* **What it does**: Resolves hostnames to IPs. AD requires DNS (Kerberos, LDAP rely on it).
* **Use case**: Internal name resolution for domain.
* **Cybersecurity**: Target for DNS poisoning, tunneling (C2 over DNS).
* **Defense**: DNSSEC, logging, secure updates.

### 🔹 DHCP Server ✅

* **What it does**: Auto-assigns IPs, gateways, DNS servers.
* **Use case**: Centralized IP management.
* **Cybersecurity**: Rogue DHCP can hijack traffic.
* **Defense**: DHCP snooping, reservations, monitor unauthorized servers.

---

## 3. File & Storage Services

### 🔹 DFS Namespaces / Replication ❌

* **What it does**: Creates a unified namespace (\Company\Files) replicated across multiple servers.
* **Use case**: Multi-site file access.
* **Cybersecurity**: Ransomware can replicate via DFS.
* **Defense**: Monitor replication, test restore plans.

### 🔹 iSCSI Target Server ❌

* **What it does**: Turns Windows into a SAN provider (storage over IP).
* **Use case**: Lab SAN, Hyper-V clusters.
* **Cybersecurity**: Traffic can be sniffed/modified if unencrypted.
* **Defense**: IPsec, isolate storage networks.

### 🔹 Work Folders ❌

* **What it does**: User file sync to personal devices (pre-OneDrive era).
* **Use case**: Remote workers pre-cloud.
* **Cybersecurity**: Data sprawl outside perimeter.
* **Defense**: Use modern OneDrive/Intune instead.

---

## 4. Print & Document Services

### 🔹 Print Server ✅

* **What it does**: Centralized print queue/spooling.
* **Cybersecurity**: PrintNightmare showed exploitation risks.
* **Defense**: Patch immediately, least-privileged print drivers.

### 🔹 Document Services ❌

* Rarely deployed in modern MSP setups. Superseded by cloud print services.

---

## 5. Web Server (IIS)

### 🔹 Common HTTP Features ✅

* **What it does**: Default docs, directory browsing, static content, errors.
* **Relevance**: Core web functionality.

### 🔹 Health & Diagnostics ✅

* **What it does**: Logging, tracing.
* **Cybersecurity**: Helps detect web attacks.

### 🔹 Performance ✅

* **What it does**: Static/dynamic compression.
* **Relevance**: Performance tuning.

### 🔹 Security ✅

* **Features**: Request filtering, Windows Auth, Basic Auth, URL authorization.
* **Cybersecurity**: Critical access controls.

### 🔹 Application Development ✅

* **.NET Extensibility 3.5 / 4.8** → Runs ASP.NET apps.
* **ASP / ASP.NET** → Legacy + modern web app frameworks.
* **CGI** → Legacy, insecure.
* **ISAPI Extensions/Filters** → Custom DLL plugins.
* **SSI (Server Side Includes)** → Legacy templating.
* **WebSockets** → Real-time apps (chat, dashboards).

### 🔹 FTP Server ✅

* **FTP Service**: Legacy file transfer.
* **Cybersecurity**: Insecure unless FTPS used.

### 🔹 IIS Management Tools ✅

* **Management Console**: GUI.
* **IIS 6 Compatibility**: Legacy support.
* **Cybersecurity**: Only enable if needed.

---

## 6. Update Services

### 🔹 Windows Server Update Services (WSUS) ✅

* **What it does**: Centralized patch management.
* **Use case**: Control which updates clients receive.
* **Cybersecurity**: Prevents patch gaps. But if WSUS compromised, can push malicious updates.
* **Defense**: Harden WSUS, SSL, monitor.

---

## 7. Other Roles & Features

### 🔹 Network Policy & Access Services (NPAS)

* Includes NPS (RADIUS server), Routing & Remote Access.
* **Use case**: VPN, 802.1X Wi-Fi auth.
* **Cybersecurity**: Credential theft if misconfigured.

### 🔹 Windows Deployment Services (WDS)

* PXE boot + OS deployment.
* **Use case**: Mass imaging.
* **Cybersecurity**: Rogue images, MITM attacks.

### 🔹 Host Guardian Service

* Protects Hyper-V Shielded VMs.
* Rare outside large enterprises.

---

# ✅ Key Takeaways

* Many roles are **legacy** (AD RMS, Work Folders, IIS CGI).
* Some are **critical to AD** (DNS, DHCP, AD DS, AD CS).
* Modern replacements often live in **Azure AD, Intune, Purview**.
* For labs, enabling these roles teaches you **attack surface vs defense** balance.

---

# 📌 Next Steps for Lab & GitHub

* Add screenshots of setup.
* Demonstrate misconfigs → exploitation.
* Document defenses.
* Compare legacy vs modern (on-prem vs cloud).

---
