# 🌐 Day 5: Create Azure VNet with Specific CIDR

> **⚠️ Dynamic Environment Warning**
> Please note that the specific details in this guide (Resource Groups, Usernames, Passwords) are generated dynamically by the lab environment.
> * **Resource Group:** You must use the **existing** Resource Group provided by the lab (check `az group list`).
> * **Credentials:** Use the specific username/password provided below.

## 📋 Scenario
The **Nautilus DevOps team** is proceeding with their incremental migration strategy. Having decided on a segmented network architecture, they now need to provision specific Virtual Networks (VNets) to host different tiers of applications.
For this task, you are required to create a VNet with a **strictly defined IP address space**. This precision is crucial to prevent IP overlapping with on-premise networks during the hybrid cloud phase.

**Task Requirements:**
1.  **Resource:** Virtual Network (VNet).
2.  **Name:** `datacenter-vnet`
3.  **Region:** `East US`
4.  **IP Address Space (CIDR):** `192.168.0.0/24`
    * *Note:* This is a specific requirement. Default values (like `10.0.0.0/16`) will cause the task to fail.
5.  **Scope:** Create within the existing Lab Resource Group.

## 💻 Infrastructure Details
| Parameter | Value |
| :--- | :--- |
| **Portal URL** | [https://portal.azure.com](https://portal.azure.com) |
| **Username** | `kk_lab_user_main-562b722c05b44900@azurefreekmlprod.onmicrosoft.com` |
| **Password** | `cq22@d&5` |
| **Region** | `East US` |
| **Target CIDR** | `192.168.0.0/24` |

---

## 🛠️ Solution A: Azure Portal (GUI)
*Recommended for understanding how to customize IP Address spaces.*

### Step 1: Login and Search
1.  Open an Incognito/Private window.
2.  Navigate to **[portal.azure.com](https://portal.azure.com)** and log in with the credentials above.
3.  In the top search bar, type **Virtual Networks**.
4.  Select **Virtual networks** (under Services).
5.  Click **+ Create**.

### Step 2: Configure "Basics"
* **Subscription:** Leave as default.
* **Resource Group:** Select the **existing** group provided by the lab (e.g., `kodekloud-rg`). *Do not create a new one.*
* **Name:** `datacenter-vnet`
* **Region:** `East US`

### Step 3: Configure "IP Addresses" (Crucial Step)
This is where most errors occur in this specific lab. You must override the Azure default.

1.  Click **Next: IP Addresses >**.
2.  **Modify IPv4 address space:**
    * You will likely see a default range (like `10.0.0.0/16`) pre-filled.
    * Click the **trash bin icon** next to the default range to delete it.
    * **Add the new range:** Type `192.168.0.0/24` in the box.
    * Ensure the green checkmark appears (indicating the range is valid).
3.  **Subnets:**
    * The lab does not explicitly ask for a subnet, but if prompted or if you want to be safe, you can add a `default` subnet (e.g., `192.168.0.0/24`). However, often just defining the VNet space is enough for the requirement.



### Step 4: Finalize
1.  Click **Review + create**.
2.  Wait for the validation to pass.
3.  Click **Create**.

---

## ⚡ Solution B: Azure CLI
*Best for precision. CLI reduces the risk of typos in the IP range.*


### Step 1: Identify Resource Group

```bash
az group list --output table
```

Make a note of the group name (e.g., `kodekloud-rg`).

### Step 2: Create the VNet with Specific CIDR

Run the command below.
* Replace `<Resource_Group_Name>` with the actual name from Step 2.
* The flag `--address-prefixes` sets the specific CIDR required.

```bash
az network vnet create \
  --resource-group <Resource_Group_Name> \
  --name datacenter-vnet \
  --location eastus \
  --address-prefixes 192.168.0.0/24
```

## ✅ Verification

To pass the lab, you must ensure the CIDR is exactly what was requested.

### 1. Check Address Space (CLI)

```bash
az network vnet show \
  --resource-group <Resource_Group_Name> \
  --name datacenter-vnet \
  --query "addressSpace.addressPrefixes"
```

**Expected Output:**
```json
[
  "192.168.0.0/24"
]
```

If you see `10.0.0.0...`, you missed the requirement.

### 2. Check via Portal

1. Go to the Virtual Networks page.
2. Click on `datacenter-vnet`.
3. In the overview or "Address space" settings, verify it says `192.168.0.0/24`.

## 🧠 What We Learned

* **CIDR (Classless Inter-Domain Routing):**
  * `/24` means the first 24 bits are fixed (`192.168.0.x`).
  * This allows for 256 IP addresses (minus 5 reserved by Azure) = 251 usable IPs.
  * This is much smaller than the standard `/16` (65,536 IPs).
* **Why specific CIDRs?** In real-world migrations (like the Nautilus scenario), you cannot just pick random IPs. You must ensure the cloud network does not conflict with your office network (e.g., if your office WiFi is `10.0.0.0/16`, your Azure VNet cannot be `10.0.0.0/16` or VPNs will fail).