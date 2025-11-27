# 🔑 Day 42: Create Azure SSH Key Pair (GUI Method)

> **⚠️ Dynamic Environment Warning**
> * **Resource Group:** You must select the **existing** Resource Group provided by the lab (usually named `kodekloud-rg` or similar).
> * **Credentials:** Use the specific username/password provided in your lab ticket.

## 📋 Scenario
The **Nautilus DevOps team** is migrating infrastructure incrementally.
For this task, you need to generate an **SSH Key Pair** resource (`datacenter-kp`) using the Azure Portal. This key will be used to secure Linux VMs later in the migration.

**Task Requirements:**
1.  **Interface:** Azure Portal (GUI).
2.  **Resource Name:** `datacenter-kp`.
3.  **Type:** `rsa` (Standard SSH).

## 💻 Infrastructure Details
| Parameter | Value |
| :--- | :--- |
| **Portal URL** | [https://portal.azure.com](https://portal.azure.com) |
| **Username** | `kk_lab_user...` |
| **Password** | `S7dg%-xV` |

---

## 🛠️ Step-by-Step Implementation

### Step 1: Log in to the Azure Portal
1.  Open **[portal.azure.com](https://portal.azure.com)**.
2.  Login with the provided Lab Credentials.

### Step 2: Navigate to SSH Keys
1.  In the top search bar, type: **SSH Keys**.
2.  Select **SSH Keys** from the services list.
3.  Click **+ Create**.

### Step 3: Configure the Resource
Fill in the details exactly as requested:

* **Subscription:** Default.
* **Resource Group:** Select the **existing** lab group (e.g., `kodekloud-rg`).
* **Region:** Default.
* **Key pair name:** `datacenter-kp`.
* **SSH public key source:** Generate new key pair.
* **Key type:** RSA.

### Step 4: Finalize
1.  Click **Review + create**.
2.  Click **Create**.
3.  **Important:** When the popup appears, click **Download private key and create resource**.

## ✅ Verification
1.  Go to the resource list or search **SSH Keys** again.
2.  Verify that `datacenter-kp` is listed and the status is valid.

## 🧠 What We Learned
* **Azure Portal Navigation:** How to use the global search to find specific resources.
* **Key Lifecycle:** Azure stores the public key object, but the private key (`.pem`) is provided to the user only once at the moment of creation.