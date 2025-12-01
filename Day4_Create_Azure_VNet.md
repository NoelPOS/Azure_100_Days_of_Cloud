# 🌐 Day 4: Create Azure Virtual Network (VNet)

> **⚠️ Dynamic Environment Warning**
> Please note that the specific details in this guide (Resource Groups, Usernames, Passwords) are generated dynamically by the lab environment.
> * **Resource Group:** You must use the **existing** Resource Group provided by the lab (check `az group list`).
> * **Credentials:** Use the specific username/password provided below.

## 📋 Scenario
The **Nautilus DevOps team** is laying the foundation for their cloud infrastructure. Before migrating servers or applications, they must establish a secure, isolated network environment. This ensures that future resources (like VMs and Load Balancers) have a defined communication space.

**Task Requirements:**
1.  **Resource:** Virtual Network (VNet).
2.  **Name:** `datacenter-vnet`
3.  **Region:** `East US`
4.  **IP Address Space:** Any valid IPv4 CIDR block (e.g., `10.0.0.0/16`).
5.  **Scope:** Create within the existing Lab Resource Group.

## 💻 Infrastructure Details
| Parameter | Value |
| :--- | :--- |
| **Portal URL** | [https://portal.azure.com](https://portal.azure.com) |
| **Username** | `kk_lab_user_main-3facb0366e894c5c@azurefreekmlprod.onmicrosoft.com` |
| **Password** | `zgxB$ncE` |
| **Region** | `East US` |

---

## 🛠️ Solution A: Azure Portal (GUI)
*Best for visualizing the network topology and address space.*

### Step 1: Navigate to Virtual Networks
1.  Open an Incognito/Private window and log in to **[portal.azure.com](https://portal.azure.com)**.
2.  In the top search bar, type **Virtual Networks**.
3.  Select **Virtual networks** (Services).
4.  Click **+ Create**.

### Step 2: Configure "Basics"
Fill in the form as follows:
* **Subscription:** Default.
* **Resource Group:** Select the **existing** group (e.g., `kodekloud-rg` or `ODL-azure-xxx`).
* **Name:** `datacenter-vnet`
* **Region:** `East US` (This is a strict requirement).



### Step 3: Configure "IP Addresses"
1.  Click **Next: IP Addresses >**.
2.  **IPv4 address space:**
    * The default is usually `10.0.0.0/16`. You can leave this as is (it satisfies "any IPv4 CIDR").
    * If it is empty, type: `10.0.0.0/16`.
3.  **Subnets:**
    * The lab doesn't explicitly ask for a subnet, but a `default` subnet is usually created automatically. You can leave it.

### Step 4: Finalize
1.  Click **Review + create**.
2.  Wait for validation.
3.  Click **Create**.

---

## ⚡ Solution B: Azure CLI
*Best for quick network setup without clicking through tabs.*

### Step 1: Authenticate
Open your terminal (or the lab terminal) and log in.

```bash
az login -u "kk_lab_user_main-3facb0366e894c5c@azurefreekmlprod.onmicrosoft.com" -p "zgxB$ncE"

# Azure VNet Setup via CLI

## ⚡ Solution B: Azure CLI
*Best for quick network setup without clicking through tabs.*


### Step 1: Identify Resource Group

```bash
az group list --output table
```

Make a note of the group name (e.g., `kodekloud-rg`).

### Step 2: Create the VNet

Run the command below.
* Replace `<Resource_Group_Name>` with the name found in Step 2.
* We use `10.0.0.0/16` as the CIDR block.

```bash
az network vnet create \
  --resource-group <Resource_Group_Name> \
  --name datacenter-vnet \
  --location eastus \
  --address-prefixes 10.0.0.0/16
```

## ✅ Verification

Ensure the network was created successfully.

### 1. Via CLI

```bash
az network vnet show \
  --resource-group <Resource_Group_Name> \
  --name datacenter-vnet \
  --output table
```

**Success:** You should see `Succeeded` in the ProvisioningState column.

### 2. Via Portal

1. Go to the Resource Group.
2. Look for the icon representing a Virtual Network.
3. Verify the name is `datacenter-vnet` and location is `East US`.

## 🧠 What We Learned

* **VNet (Virtual Network):** The fundamental building block for your private network in Azure. It is logically isolated from other networks.
* **CIDR (Classless Inter-Domain Routing):** The notation used to define IP ranges (e.g., `/16` means 65,536 IP addresses).
* **Region Affinity:** VNets are scoped to a specific region (in this case, `East US`). You cannot span a single VNet across multiple regions (you would need VNet Peering for that).