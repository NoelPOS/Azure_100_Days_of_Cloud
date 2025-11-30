# ☁️ Day 2: Create Azure Virtual Machine

> **⚠️ Dynamic Environment Warning**
> Please note that the specific details in this guide (Usernames, Passwords, Resource Groups) are generated dynamically by the lab environment.
> * **Resource Group:** You must use the **existing** group provided by the lab (check `az group list` or the Portal).
> * **Region:** The scenario asks for `West US`, but if the lab policy blocks it, check if you are forced to use `East US`.
> * **Credentials:** Use the specific username/password provided below.

## 📋 Scenario
The **Nautilus DevOps team** is continuing their incremental migration. Now that security keys were handled in Day 1, the next step is to provision the compute resources.
You are tasked with deploying the first Linux server, which will serve as a general-purpose DevOps instance.

**Task Requirements:**
1.  **VM Name:** `devops-vm`
2.  **Region:** `West US`
3.  **Image:** `Ubuntu 22.04 LTS`
4.  **Size:** `Standard_B1s` (Low cost instance)
5.  **Storage:** OS Disk must be **Standard HDD** (30 GB)
6.  **Network:** Allow SSH (Port 22)
7.  **Access:** Ensure you can SSH into it.

## 💻 Infrastructure Details
| Parameter | Value |
| :--- | :--- |
| **Portal URL** | [https://portal.azure.com](https://portal.azure.com) |
| **Username** | `kk_lab_user_main-c3bbe044d68d4431@azurefreekmlprod.onmicrosoft.com` |
| **Password** | `m2Z8LrYj` |
| **Resource Group** | (Use Existing / Check Portal) |

---

## 🛠️ Solution A: Azure Portal (GUI)
*Recommended for managing disk types and size selection visually.*

### Step 1: Navigate to Virtual Machines
1.  Open an Incognito/Private window and log in to **[portal.azure.com](https://portal.azure.com)** using the credentials above.
2.  In the top search bar, type **Virtual machines**.
3.  Select **Virtual machines** (under Services).
4.  Click **+ Create** -> **Azure virtual machine**.

### Step 2: Configure "Basics" Tab
Fill in the form carefully. If a field isn't mentioned, leave it default.

* **Subscription:** Default.
* **Resource Group:** Select the **existing** group (e.g., `kodekloud-rg`). **Do not create new.**
* **Virtual machine name:** `devops-vm`
* **Region:** `West US`
* **Availability options:** No infrastructure redundancy required.
* **Security type:** Standard.
* **Image:** Select `Ubuntu Server 22.04 LTS - x64 Gen2` (or Gen1, usually Gen2 is default).
* **Size:**
    * The default might be large. Click **See all sizes**.
    * Search for `B1s`.
    * Select `Standard_B1s` (1 vcpu, 1 GiB memory) and click **Select**.
* **Authentication type:** SSH public key.
* **Username:** `azureuser` (default).
* **SSH public key source:** Generate new key pair (or use existing if you have one from Day 1, but generating new is safer for this specific lab).
* **Key pair name:** `devops-vm_key` (or similar).
* **Public inbound ports:** Allow selected ports.
* **Select inbound ports:** Ensure **SSH (22)** is checked.



### Step 3: Configure "Disks" Tab (Crucial)
1.  Click **Next: Disks >**.
2.  **OS disk type:** Change this from "Premium SSD" to **Standard HDD** (Locally-redundant storage).
    * *Note:* The default size for Ubuntu images is usually 30GB, which meets the requirement.
3.  Leave Key management as Platform-managed key.



### Step 4: Networking & Finalize
1.  Click **Next: Networking >**.
    * Ensure a generic vnet and public IP are being created.
    * NIC network security group: **Basic**.
    * Public inbound ports: **Allow selected ports (SSH 22)**.
2.  Click **Review + create**.
3.  Wait for validation (Green checkmark).
4.  Click **Create**.
5.  **Download Key:** A popup will appear. Click **Download private key and create resource**.
    * *Save `devops-vm_key.pem` to a known location (e.g., your Downloads folder or `~/.ssh/`).*


## ⚡ Solution B: Azure CLI
*Fastest method if you are comfortable with command flags.*

### Step 1: Login and Find Group

Login to Azure:

```bash
az login -u "kk_lab_user_main-c3bbe044d68d4431@azurefreekmlprod.onmicrosoft.com" -p "m2Z8LrYj"
```

Find the Resource Group name:

```bash
az group list --output table
```

Example result: `kodekloud-rg`

### Step 2: Create the VM

Run the following command. Replace `<Resource_Group_Name>` with the actual name.

Note the `--storage-sku Standard_LRS` flag which sets the disk to Standard HDD.

```bash
az vm create \
  --resource-group <Resource_Group_Name> \
  --name devops-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --location westus \
  --admin-username azureuser \
  --generate-ssh-keys \
  --storage-sku Standard_LRS \
  --verbose
```

## ✅ Verification

We need to prove the VM is running and accessible.

### 1. Get Public IP

Go to the Portal overview of the VM, or run:

```bash
az vm list-ip-addresses --resource-group <Resource_Group_Name> --name devops-vm --output table
```

### 2. SSH into the VM

#### If you used GUI:

Locate the `.pem` file you downloaded. Open your terminal in that folder.

```bash
chmod 400 devops-vm_key.pem
```

```bash
ssh -i devops-vm_key.pem azureuser@<Public_IP_Address>
```

#### If you used CLI:

The keys were generated in `~/.ssh/`.

```bash
ssh azureuser@<Public_IP_Address>
```

If you see the prompt `azureuser@devops-vm:~$`, you have succeeded.

## 🧠 What We Learned

### VM Sizes (B-Series)
The **Standard_B1s** is a "Burstable" VM. It builds up credits when idle and burns them when CPU is used. It is the cheapest option for small experiments.

### Storage Types
- **Premium SSD**: High IOPS, expensive (Default)
- **Standard HDD**: Spinning platters, lower cost, used for backups or non-critical workloads (Required by this lab)

### NSG (Network Security Group)
The firewall that wraps the VM. We explicitly allowed Port 22 so we could connect via SSH.