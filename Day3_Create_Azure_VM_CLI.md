# ☁️ Day 3: Create Azure VM via CLI

> **⚠️ Dynamic Environment Warning**
> Please note that the specific details in this guide (Resource Groups, Subscriptions) are generated dynamically by the lab environment.
> * **Resource Group:** You must identify the **existing** Resource Group provided by the lab.
> * **Credentials:** Run the `showcreds` command in the terminal to get your username/password if not provided.

## 📋 Scenario
The **Nautilus DevOps team** is migrating workloads to Azure. For security reasons or automation requirements, the team currently **does not have access to the Azure Portal**.
You must provision a new Virtual Machine strictly using the **Azure CLI** on the `azure-client` jump host.

**Task Requirements:**
1. **Tool:** Azure CLI (No Portal access).
2. **VM Name:** `devops-vm`
3. **Image:** `Ubuntu2204`
4. **Size:** `Standard_B2s`
5. **Storage:** `Standard_LRS` (Standard HDD), 30GB size.
6. **Auth:** Admin user `azureuser` with auto-generated SSH keys.
7. **Goal:** VM must be in the **Running** state.

## 💻 Infrastructure Details
| Parameter | Value |
| :--- | :--- |
| **Host** | `azure-client` |
| **CLI Tool** | `az` (Pre-installed) |
| **Resource Group** | (Dynamic - Find via CLI) |

---

## 🛠️ Step-by-Step Implementation

### Step 1: Log in to Azure
Since we have no portal, we start on the command line.

**1. Retrieve Credentials:**
If you don't have them, run:
```bash
showcreds
```

**2. Log in:**

```bash
az login -u "<Username>" -p "<Password>"
```

### Step 2: Identify the Resource Group
We cannot create a VM without a container (Resource Group). Since we must use the existing one:

```bash
az group list --output table
```

**Action:** Copy the name of the group listed (e.g., `kodekloud-rg`, `ODL-azure-1234`).

We will refer to this as `<Resource_Group_Name>` in the next command.

### Step 3: Construct the VM Creation Command
We need to map the requirements to specific CLI flags:

- `--image Ubuntu2204` maps to the Image requirement.
- `--size Standard_B2s` maps to the Size requirement.
- `--storage-sku Standard_LRS` maps to the Storage type.
- `--os-disk-size-gb 30` ensures the disk is exactly 30GB.

**Run the command:** (Replace `<Resource_Group_Name>` with the actual name found in Step 2)

```bash
az vm create \
  --resource-group <Resource_Group_Name> \
  --name devops-vm \
  --image Ubuntu2204 \
  --size Standard_B2s \
  --admin-username azureuser \
  --generate-ssh-keys \
  --storage-sku Standard_LRS \
  --os-disk-size-gb 30 \
  --verbose
```

**What happens now?**

- Azure CLI checks if a Virtual Network (VNet) exists. If not, it creates one.
- It creates a Public IP and Network Security Group (NSG).
- It generates SSH keys in `~/.ssh/`.
- It spins up the VM.

### Step 4: Verify Status
The prompt specifically asks to ensure the VM is in the running state.

**1. Check Power State:**

```bash
az vm show \
  --resource-group <Resource_Group_Name> \
  --name devops-vm \
  --show-details \
  --query "powerState"
```

**Expected Output:** `"VM running"`

**2. (Optional) Check Disk Settings:** Verify we got the Standard HDD and 30GB size.

```bash
az vm show \
  --resource-group <Resource_Group_Name> \
  --name devops-vm \
  --query "storageProfile.osDisk"
```

## ✅ Verification & Troubleshooting

### 1. Connectivity Test
Try to SSH into the new VM to prove it works.

```bash
# Get the IP
az vm list-ip-addresses -g <Resource_Group_Name> -n devops-vm -o table

# SSH (The key is automatically used from ~/.ssh/id_rsa)
ssh azureuser@<Public_IP>
```

### 2. Common Errors

**"SkuNotAvailable":**
- **Cause:** The region (e.g., East US) might be out of Standard_B2s stock.
- **Fix:** Add `--location westus` (or another region) to the create command.

**"ResourceGroupNotFound":**
- **Cause:** You didn't replace `<Resource_Group_Name>` with the actual name from Step 2.

## 🧠 What We Learned

**CLI Efficiency:** We provisioned a complex resource (Compute + Networking + Storage + Security) with a single command (`az vm create`).

**Storage SKUs:**
- `Standard_LRS` = Standard HDD (Cheapest, magnetic spinning disks).
- `StandardSSD_LRS` = Standard SSD.
- `Premium_LRS` = Premium SSD (High performance).

**Size Flags:** `Standard_B2s` offers 2 vCPUs and is double the power of the B1s used in Day 2.