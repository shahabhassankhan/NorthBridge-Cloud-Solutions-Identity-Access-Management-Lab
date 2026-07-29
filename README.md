# NorthBridge Cloud Solutions — Identity & Access Management Lab

An identity and access lab built for a fictional company, covering Microsoft Entra ID, Azure RBAC, Key Vault, and Managed Identity. The point of the project is passwordless access done right, no stored passwords, no hardcoded keys, no connection strings sitting in plain text.

**Author:** Shahab Hassan Khan

---

## Table of Contents

- [Overview](#overview)
- [Business Scenario](#business-scenario)
- [Objectives](#objectives)
- [Azure Services Used](#azure-services-used)
- [Resources Created](#resources-created)
- [Project Steps](#project-steps)
- [Authentication Flow](#authentication-flow)
- [Verification](#verification)
- [Security Concepts Demonstrated](#security-concepts-demonstrated)
- [Skills Demonstrated](#skills-demonstrated)
- [Screenshots](#screenshots)
- [Future Improvements](#future-improvements)

---

## Overview

This project sets up a secure identity and access management environment on Azure for a fictional company, NorthBridge Cloud Solutions. It shows how Microsoft Entra ID, Azure RBAC, Managed Identity, Key Vault, Storage, a Windows Server VM, and Log Analytics fit together.

The core idea being tested: can a virtual machine reach Key Vault and Blob Storage without ever storing a password, key, or connection string anywhere. It can, using Managed Identity, and this lab proves it end to end.

## Business Scenario

NorthBridge Cloud Solutions needs employees organized by department, permissions assigned by job role rather than by individual, secrets stored securely instead of embedded in code, and applications that authenticate without passwords. Everything in this lab follows the principle of least privilege, meaning every identity gets exactly the access it needs and nothing more.

## Objectives

- Organize identity resources under Microsoft Entra ID
- Group users by department and assign roles to groups, not individuals
- Apply Azure RBAC using least privilege
- Store secrets in Key Vault instead of hardcoding them
- Enable Managed Identity on a VM so it authenticates without credentials
- Verify passwordless access to both Key Vault and Blob Storage
- Centralize logging through Log Analytics

## Azure Services Used

- Microsoft Entra ID
- Azure RBAC
- Azure Virtual Machine
- Azure Virtual Network
- Azure Storage Account & Blob Storage
- Azure Key Vault
- Managed Identity
- Azure CLI
- Azure Monitor
- Log Analytics Workspace

## Resources Created

| Resource | Name |
|---|---|
| Resource Group | `nbl3-identity-rg` |
| Virtual Network | `nbl3-identity-vnet` |
| Windows Server VM | `nbl3-vm01` |
| Storage Account | Created inside the resource group |
| Blob Container | `documents` |
| Test Blob | `identity-test.txt` |
| Key Vault | Created inside the resource group |
| Log Analytics Workspace | `nbl3-law01` |

## Project Steps

**Identity setup.** Created a dedicated resource group to keep every identity-related resource in one place. Created Entra ID users representing different roles (IT Administrator, Developer, HR, Auditor), then created matching security groups (IT Admins, Developers, HR, Auditors) and added each user to their group. Assigning permissions to groups instead of individuals keeps the whole thing scalable as more people get added later.

**Access control.** Assigned Azure's built-in roles to each group based on what that role actually needs: Owner for IT Admins, Contributor for Developers, Reader for Auditors. No group got more access than its job requires.

**Secure storage.** Created a Storage Account for application files and a Key Vault for secrets, then added a test secret to the vault and assigned the Key Vault Secrets Officer role to the administrator account so secrets could be managed properly.

**Infrastructure.** Built the virtual network and the Windows Server VM using Azure CLI rather than the portal, since CLI is closer to how this gets done in a real environment.

**Managed Identity.** Enabled a system-assigned Managed Identity on the VM. This gives the VM its own identity in Azure, so instead of logging in with a username and password, Azure recognizes the machine itself.

**Authorization.** Assigned Storage Blob Data Reader and Key Vault Secrets User roles directly to the VM's Managed Identity, letting it read blob data and retrieve secrets without ever touching a password.

**Passwordless authentication.** Connected to the VM over RDP, installed Azure CLI inside it, and authenticated using `az login --identity`. Azure issued an access token to the VM directly, no username or password involved anywhere in the process.

**Monitoring.** Created a Log Analytics Workspace and enabled diagnostic logs for Key Vault, so every access to a secret gets recorded for review.

## Authentication Flow

```
User
  │
Microsoft Entra ID
  │
Security Groups
  │
Azure RBAC
  │
  ▼
Windows VM
  │
System Assigned Managed Identity
  │
Azure Access Token
  │
  ├────────────► Azure Key Vault ──► Read Secret
  │
  └────────────► Azure Storage ────► Read Blob
                       │
                       ▼
              Log Analytics Workspace
                       │
                       ▼
                  Audit Logs
```

## Verification

Once Managed Identity was in place, two things were tested directly from inside the VM using Azure CLI:

- Retrieved the stored secret from Key Vault, confirming passwordless access worked
- Listed containers and blobs in Storage using `--auth-mode login`, confirming the VM could reach Storage without a storage key, SAS token, or connection string anywhere in the picture

Both worked on the first attempt, which confirmed the RBAC assignments on the Managed Identity were correct.

## Security Concepts Demonstrated

Microsoft Entra ID identity management, security groups, Azure RBAC, principle of least privilege, Managed Identity, passwordless authentication, Key Vault secret management, Storage authorization without keys, Azure CLI automation, and centralized monitoring through Log Analytics.

## Skills Demonstrated

Microsoft Entra ID, Azure identity and access management, Azure RBAC, security groups, Managed Identity, passwordless authentication, Key Vault, Azure Storage, blob containers, virtual machines, Azure CLI, virtual networking, Azure Monitor, Log Analytics, and enterprise security practices.

## Screenshots

All screenshots are in `/screenshots`.

| # | Screenshot | Description |
|---|---|---|
| 01 | `01_nbl3_identity_rg.jpg` | Identity resource group |
| 02 | `02_nbl3_create_user_admin.jpg` | Creating the admin user |
| 03 | `03_nbl3_create_user_list.jpg` | Entra ID user list |
| 04 | `04_nbl3_create_group_developer.jpg` | Creating the Developers group |
| 05 | `05_nbl3_create_group_list.jpg` | Security group list |
| 06 | `06_nbl3_assigning_rbac_role_to_admin_group.jpg` | Assigning RBAC role to Admin group |
| 07 | `07_nbl3_iam_role_assignments.jpg` | IAM role assignments |
| 08 | `08_nbl3_add_member_to_group.jpg` | Adding a member to a group |
| 09 | `09_nbl3_create_storage.jpg` | Creating the storage account |
| 10 | `10_nbl3_create_key_vault.jpg` | Creating the Key Vault |
| 11 | `11_nbl3_add_secret_keyvault.jpg` | Adding a secret to Key Vault |
| 12 | `12_nbl3_add_role_secret_officer.jpg` | Assigning Key Vault Secrets Officer role |
| 13 | `13_nbl3_identity_vnet_portal.jpg` | Identity virtual network |
| 14 | `14_nbl3_create_vm01_cli.jpg` | Creating VM01 via Azure CLI |
| 15 | `15_nbl3_vm01_portal.jpg` | VM01 overview in the portal |
| 16 | `16_nbl3_vm01_managed_identity.jpg` | Managed Identity enabled on VM01 |
| 17 | `17_nbl3_storage_rbac_assigned_managed_identity.jpg` | Storage role assigned to Managed Identity |
| 18 | `18_nbl3_keyvault_rbac_assigned_managed_identity.jpg` | Key Vault role assigned to Managed Identity |
| 19 | `19_nbl3_created_container_for_testing.jpg` | Blob container created for testing |
| 20 | `20_nbl3_created_log_analytics_workspace.jpg` | Log Analytics Workspace created |
| 21 | `21_nbl3_enabled_log_analytics_keyvault.jpg` | Diagnostic logs enabled for Key Vault |
| 22 | `22_nbl3_verification.jpeg` | Passwordless access verified via CLI |
| 23 | `23_nbl3_all_resources.jpg` | All resources in the resource group |
| 24 | `24_nbl3_resource_visualizer_identity_rg.jpg` | Resource visualizer for the identity resource group |

## Future Improvements

- Add Conditional Access policies for stronger sign-in control
- Extend Managed Identity to a second workload for a more realistic multi-service setup
- Build a Log Analytics dashboard around the Key Vault audit logs instead of just collecting them
- Automate the entire deployment with Bicep instead of a mix of portal and CLI steps

---

Built and deployed by **Shahab Hassan Khan** as a hands-on Azure identity and access management project.
