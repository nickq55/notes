## 1. Overview

**VM Name:** [nlfin-pdf-computer-prod](https://portal.azure.com/#@086gc.onmicrosoft.com/resource/subscriptions/56f512d1-6ae4-4db1-9964-6bf78bcad6cc/resourceGroups/cfis-prodpb-rg-01/providers/Microsoft.Compute/virtualMachines/nlfin-pdf-computer-prod/overview)

**Environment:** Production  
**Purpose:**  
> This machine provides PDF generation for all production NLFIN documents.

---

## 2. Specifications

| Attribute           | Detail                          |
|---------------------|---------------------------------|
| Subscription        | lz-cfis-prod-pb  |
| Resource Group      | cfis-prodpb-rg-01           |
| Location            | Canada Central                  |
| VM Size             | Standard D2als v6         |
| OS                  | Windows Server 2019    |
| vCPUs               | 2                             |
| Memory              | 4GiB                            |
| OS Disk             | Standard SSD LRS - 127GiB       |
| Private IP    | `10.177.64.11`             |

---

## 3. Accessing the VM

This VM is in the Production RG and therefore cannot be accessed directly.

`YOUR-PC    ---RDP-->     SC2G-Jumpbox     ---RDP-->     nlfin-pdf-computer-prod`

First, you will need to connect to the [SC2G-Jumpbox](https://portal.azure.com/#@086gc.onmicrosoft.com/resource/subscriptions/0d47baca-2ef1-481d-a229-0884393e67cf/resourceGroups/jumpbox-prob-rg/providers/Microsoft.Compute/virtualMachines/SC2G-Jumpbox/overview) using RDP. 
-  Ensure you are on-prem on connected to the VPN
-  Create a new Remote Desktop Connection (mstsc.exe)
-  Destination `10.177.193.9`
-  Crendetials = Your Azure Credentials (*@dfo-mpo.gc.ca)
  
From inside that sesson, create a new RDP session to connect to nlfin-pdf-computer-prod.
-  Create a new Remote Desktop Connection (mstsc.exe)
-  Destination `10.177.64.11`
-  Crendetials = In EFMSI KeePass (NLFINServiceUser OR cfis-admin)


