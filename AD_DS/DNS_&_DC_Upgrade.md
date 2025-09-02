# Active Directory Domain Controller & DNS Lab Setup (with Troubleshooting)

This document summarizes the setup process we went through for upgrading our server into a Domain Controller (DC), configuring DNS (forward + reverse zones), and fixing all the issues encountered along the way.

---

## 1. Promoting Server to a Domain Controller

* **Deployment Wizard Options:**

  * Created a **new forest** called `lab.local`.
  * Selected **Windows Server 2016 functional level** for both forest and domain.
  * Enabled **DNS server** and **Global Catalog** roles.
  * Skipped **RODC (Read-Only Domain Controller)**.
  * Set a **DSRM (Directory Services Restore Mode) password**.

### Issues Encountered

* **Prerequisite Check Failed:**

  * Initially failed because **Certificate Services** was already installed.
  * Solution: Remove ADCS before promoting to DC.

* **Prerequisite Warnings:**

  * Windows Server 2022 uses stricter cryptography by default → warning about NT 4.0 compatibility.
  * No static IP detected → set a static IP (10.0.0.10).
  * DNS delegation warning → safe to ignore in lab since no parent zone.

✅ After addressing these, DC promotion completed successfully.

---

## 2. DNS Setup

* DNS was installed automatically with AD DS promotion.
* Created **Forward Lookup Zone** for `lab.local` and `_msdcs.lab.local` (auto-created).
* Verified records:

  * SOA (Start of Authority)
  * NS (Name Server)
  * A record for DC01

### Reverse Lookup Zone

* Created reverse zone for **10.0.0.x network** → `0.0.10.in-addr.arpa`.
* Added a **PTR record** mapping `10.0.0.10` → `dc01.lab.local`.

### Records Summary

* **A Record (Host):** Name → IP (ex: `dc01.lab.local` → `10.0.0.10`).
* **PTR Record:** IP → Name (ex: `10.0.0.10` → `dc01.lab.local`).
* **CNAME Record:** Alias → Real Host (not yet configured but important to know).
* **AAAA Record:** Same as A record but for IPv6.

---

## 3. Issues & Fixes with DNS

1. **PTR Record Not Updating Automatically:**

   * Root cause: Auto PTR creation wasn’t enabled.
   * Fix: Checked **Update associated pointer (PTR) record** box when editing A records.

2. **Nslookup Returned `Unknown` Server:**

   * Root cause: IPv6 (::1) was taking precedence, no PTR set for it.
   * Fix: Disabled IPv6 temporarily → forced IPv4 resolution.

3. **Nslookup Timeout:**

   * Root cause: DNS server wasn’t being queried properly.
   * Fixes:

     * Set static IP on DC: `10.0.0.10`.
     * Set **Preferred DNS Server** to DC’s own IP (10.0.0.10).
     * Flushed DNS cache → `ipconfig /flushdns`.
     * Re-registered records → `ipconfig /registerdns`.

✅ Final Test: `nslookup 10.0.0.10` successfully resolved → `dc01.lab.local`.

---

## 4. Key Takeaways

* **Always assign a static IP** to a Domain Controller before promotion.
* **Forward & Reverse Zones** must both be configured for proper name resolution.
* **PTR records** are crucial for reverse lookups (IP → Hostname).
* **IPv6 can interfere** in labs if not configured → disable if troubleshooting.
* Fixing DNS issues requires checking:

  * Forward Zone (A records)
  * Reverse Zone (PTR records)
  * NIC IP & DNS settings
  * Cache/registration (`ipconfig` commands)

---

## 5. Next Steps

* Configure **DHCP** so clients automatically receive:

  * IPs in correct subnet.
  * DC01 as their DNS server.
* Reinstall **ADCS (Certificate Services)** now that DC + DNS are stable.
* Expand lab with more roles (e.g., WSUS, File Server, etc.).

---

📌 This doc covers **everything from DC promotion → DNS setup → troubleshooting forward/reverse lookups** with all problems we hit and how we solved them.
