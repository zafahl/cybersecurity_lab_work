# 🐢 How to Remove the Turtle Icon in VirtualBox (Hyper-V Interference)

If you’ve seen the 🐢 turtle icon at the bottom-right of your VirtualBox VM window, it means your system is using **Hyper-V**, which slows down VirtualBox performance. This happens because:

- **Hyper-V** is a **Type-1 hypervisor** (low-level hardware access)
- **VirtualBox** is a **Type-2 hypervisor** (runs on top of the host OS)
- Hyper-V often takes priority and blocks optimal virtualization performance

---

## 🧯 Fix: Turn Off Hyper-V + Memory Isolation

### Step 1: Disable Memory Integrity (Windows 10/11)

1. Open **Settings**
2. Go to **Privacy & Security → Windows Security → Device Security**
3. Click on **Core Isolation Details**
4. Toggle **Memory Integrity** to **Off**

---

### Step 2: Registry Edits

> ⚠️ Run `regedit` as Administrator

#### 🔧 Disable Hypervisor Launch Type

- Go to:
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Hypervisor

- Find: `HypervisorLaunchType`
- Modify `Value Data` to: 0

  
#### 🔒 Disable Credential Guard

- Go to:
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\CredentialGuard

- Modify `Enabled` to: 0

---

### 🔁 Reboot

After reboot, VirtualBox should now show a **microchip icon with a “V”**, meaning virtualization is working properly.

---

### ✅ Result

- 🐢 Turtle icon = Hyper-V active = slow VM
- 🧠 Microchip icon = Correct VT-x or AMD-V use = full speed VMs

---
Authored by: `Zackary Fahl`



