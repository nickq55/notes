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
-  Credentials = Your Azure Credentials (*@dfo-mpo.gc.ca)
  
From inside that sesson, create a new RDP session to connect to nlfin-pdf-computer-prod.
-  Create a new Remote Desktop Connection (mstsc.exe)
-  Destination `10.177.64.11`
-  Credentials = In EFMSI KeePass (NLFINServiceUser OR cfis-admin)

## 4. Accounts & Usage
- **cfis-admin**
  - Top-level administrative account for VM maintenance.
  - Log in/out as needed for patches, configuration, etc.
    
- **NLFINServiceUser**
  - Local service account configured for auto-logon at boot.
  - Session configured to not time-out
  - Upon boot, auto-executes C:\Scripts\MapDrivesHeadless.ps1 to map remote drives. *Failure will send ERROR to APPLICATION log in Event Manager.
  - Runs two key processes:
  1. NLFIN (needs manual login on first launch or after a reboot/failure recovery)
  2. PDF-generation service ([needs manual setup process](https://dev.azure.com/foc-poc/NLFIN/_wiki/wikis/NLFIN.wiki/3785/NLFIN-PDF-Computer))
 
- **ENT\sv-NLFIN-PDF**
  - On-Prem Service Account created by SSC
  - Used by NLFINServiceUser to connect to remote drives
  - Saved using `cmdkey`
    - `cmdkey /add:ent.dfo-mpo.ca /user:ENT\sv-NLFIN-PDF /pass:**PasswordInKeePass**`
  - Confirmed as saved using `cmdkey /list`
  - <img width="371" height="248" alt="image" src="https://github.com/user-attachments/assets/c7553c52-eb64-4893-b3de-688f68bc3cef" />
  - If this account is not saved here, the shared drives will not connect!

## 5. Best Practices

1. Use cfis-admin for any system-level changes.
   
3. NO NOT log off or shut down NLFINServiceUser—always disconnect (RDP “X” or “Disconnect”).
   > - A message is shown when connecting to running session indicating this, to change the message modify the script at `C:\Scripts\ShowProdWarning.ps1`
    
4. After any reboot or crash:
   > - NLFINServiceUser’s drives and session come up automatically.
   > - Manually launch the NLFIN and log in - see [Current Setup Procedure](https://dev.azure.com/foc-poc/NLFIN/_wiki/wikis/NLFIN.wiki/3785/NLFIN-PDF-Computer).
     
4. Monitor the APPLICATION Log in the Event Viewer or in the [Azure Portal under Logs](https://portal.azure.com/#@086gc.onmicrosoft.com/resource/subscriptions/56f512d1-6ae4-4db1-9964-6bf78bcad6cc/resourceGroups/cfis-prodpb-rg-01/providers/Microsoft.Compute/virtualMachines/nlfin-pdf-computer-prod/vmLogAnalytics):
  - When starting, task scheduler runs `C:\Scripts\MapDrivesHeadless.ps1` and that forces mounting of the two prod shared drives (O:\ and P:\)
    > - On success sends info level message to `Application` -> `MapDrivesHeadless` -> `EventID 1000`
    > - On failure sends error level message to `Application` -> `MapDrivesHeadles`s -> `EventID 1001`
      
  - On a 30 minute schedule, task scheduler runs `C:\Scripts\CheckDriveConnectivity.ps1` that confirms the status of the shared drives
    > - On success (drives are confirmed connected) sends info level message to `Application` -> `DriveConnectivityMonitor` -> `EventID 1001`
    > - On failure (drives are NOT connected) sends error level message to `Application` -> `DriveConnectivityMonitor` -> `EventID 2001`
   
  ## 6. Future and TODO

  1. Determine what events we want alerts connected to. Also, where they go.
     > - VM Not running/not responding and shared drive disconnection can be implemented easily
     > - Other less obvious things to monitor? Need more specific NLFIN knowledge.
  2. Backup?
     - Currently the VM's disk is not being backed up.
       > - This does not present a risk for loss of data, as the data is stored on the shared drives themselves.
       > - VM would need to be re-provisioned and software re-installed in case of total loss.

  


  


