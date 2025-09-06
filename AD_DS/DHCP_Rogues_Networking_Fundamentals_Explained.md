# Lab Documentation: DHCP, Rogue DHCP, Subnetting, and IP Addressing

## 1. DHCP Setup on DC01

### Scope Configuration

* **Scope Name:** LabScope
* **Range:** 10.0.0.50 – 10.0.0.200

  * Reserved .1 – .49 for static infrastructure (e.g., DC, DNS, printers, firewall).
* **Subnet Mask:** 255.255.255.0 (/24)
* **Lease Duration:** Default 8 days.

### Exclusions

* **Excluded Range:** 10.0.0.50 – 10.0.0.60
* **Reason:** Reserved for future static assignments or lab testing purposes so DHCP will not hand these out accidentally.

### Options Configured

* **003 Router (Gateway):** 10.0.0.1
  (Confirmed as default gateway of the VM’s network adapter in VirtualBox/Hyper-V NAT/Host-Only config.)
* **006 DNS Server:** 10.0.0.10 (our Domain Controller).
* **015 DNS Domain Name:** lab.local

### Status

* Scope authorized in AD.
* Scope shows as **Active**.
* Ready to hand out addresses to test VMs and client machines later.

---

## 2. Rogue DHCP Overview

### What It Is

A rogue DHCP server is any unauthorized device handing out IP configurations.

### How It Works

1. Attacker configures a DHCP service with crafted settings:

   * Pool in same subnet as victim network.
   * Default Gateway → attacker’s machine.
   * DNS Server → attacker’s DNS server.
2. Attacker connects to the LAN.
3. Victims broadcast **DHCPDISCOVER**.
4. If attacker’s DHCP **responds first**, victims accept attacker’s config.

### What Attackers Can Do

* **IP/Subnet:** Break connectivity by forcing clients into the wrong subnet.
* **Gateway:** Make attacker’s machine the default gateway → full MITM.
* **DNS:** Give out malicious DNS → redirect users to fake banking or update sites.
* **WINS/Other:** Legacy tricks for NetBIOS hijacking.

### Defense

* **DHCP Snooping** (switch feature): Trust only known ports (firewall, DC). Drop rogue offers.
* **Authorization in AD:** Only authorized DHCP servers run.
* **Monitoring:** SIEM/Huntress/N-central logs.

---

## 3. Subnetting Lessons Learned

### CIDR Recap

* **/24** → 255.255.255.0 → 254 usable IPs.
* **/23** → 255.255.254.0 → \~510 usable IPs.
* **/22** → 255.255.252.0 → \~1022 usable IPs.

### Flat vs Scoped

* **Flat /22 (10.0.0.0 – 10.0.2.255):** One giant broadcast domain. 1022 devices but noisy and less secure.
* **Three /24 scopes (10.0.0.x, 10.0.1.x, 10.0.2.x):** \~762 usable total (each loses 2 per subnet), but segmented for performance/security.

### Why Exclusions Matter

We excluded 10.0.0.50–10.0.0.60 to:

* Keep addresses free for testing static configs.
* Avoid DHCP giving out IPs we might later want for lab servers.

---

## 4. Public vs Private IPs

### RFC1918 Private Ranges

* 10.0.0.0/8 (large)
* 172.16.0.0/12 (medium)
* 192.168.0.0/16 (small)

### Why Only These Ranges?

IANA reserved them so internal networks don’t conflict with public internet assignments.

### Why Not Use 192.0.0.0/24 or Others?

* Some ranges (e.g., 192.0.0.0/24) are reserved for **special use** (e.g., testing, protocols).
* If you use them internally, you risk conflicts if the internet later allocates them.

### Key Insight

* Public servers (like Yahoo/Google) usually have **only public IPs**.
* They may still run private ranges **internally** in their data centers, but you never see those. You only hit their **NAT’d public IP**.
* That’s why using proper RFC1918 avoids routing conflicts.

---

## 5. Issues Encountered Today

* **PTR Records:** Initially didn’t auto-update when IP was changed. Solved by checking the “Update associated PTR record” box.
* **IPv6 Confusion:** nslookup showed `::1` (IPv6 localhost) instead of IPv4. Fixed by disabling IPv6 temporarily.
* **Gateway Confirmation:** Confirmed via VM NAT adapter; typically .1 in the subnet.
* **Scope Exclusion:** Learned why exclusions matter (to prevent overlaps with statics).

---

✅ At this point, DHCP is active and properly configured. Next steps:

1. Spin up test VMs to validate leases.
2. Then configure **ADCS (Certificate Services)** for PKI in the lab.
