---
title: Connect to a Linux VM
description: Learn how to connect to a Linux VM in Azure.
author: mattmcinnes
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/25/2022
ms.author: mattmcinnes
ms.reviewer: jamesser

---
# Connect to a Linux VM

In Azure there are multiple ways to connect to a Linux virtual machine. The most common practice for connecting to a Linux VM is using the Secure Shell Protocol (SSH). This is done via any standard SSH client commonly found in Linux and Windows. You can also use [Azure Cloud Shell](../cloud-shell/overview.md) from any browser.
 
This document describes how to connect, via SSH, to a VM that has a public IP. If you need to connect to a VM without a public IP, see [Azure Bastion Service](../bastion/bastion-overview.md).

## Prerequisites

- You need an SSH key pair. If you don't already have one, Azure will create a key pair during the deployment process. If you need help with creating one manually, see [Create and use an SSH public-private key pair for Linux VMs in Azure](./linux/mac-create-ssh-keys.md).
- You need an existing Network Security Group (NSG). Most VMs will have an NSG by default, but if you don't already have one you can create one and attach it manually. For more information, see [Create, change, or delete a network security group](../virtual-network/manage-network-security-group.md).
- To connect to a Linux VM, you need the appropriate port open. Typically this will be port 22. The following instructions assume port 22 but the process is the same for other port numbers. You can validate an appropriate port is open for SSH using the troubleshooter or by checking manually in your VM settings. To check if port 22 is open: 

    1. On the page for the VM, select **Networking** from the left menu.
    1. On the **Networking** page, check to see if there is a rule which allows TCP on port 22 from the IP address of the computer you are using to connect to the VM. If the rule exists, you can move to the next section.
    
       :::image type="content" source="media/linux-vm-connect/check-rule.png" alt-text="Screenshot showing how to check to see if there is already a rule allowing S S H connections.":::

    1. If there isn't a rule, add one by selecting **Add inbound port rule**.
    1. For **Service**, select **SSH** from the dropdown.
    
       :::image type="content" source="media/linux-vm-connect/create-rule.png" alt-text="Screenshot showing where to choose S S H when creating a new N S G rule.":::

    1. Edit **Priority** and **Source** if necessary
    1. For **Name**, type *SSH*.
    1. When you're done, select **Add**.
    1. You should now have an SSH rule in the table of inbound port rules.

- Your VM must have a public IP address. To check if your VM has a public IP address, select **Overview** from the left menu and look at the **Networking** section. If you see an IP address next to **Public IP address**, then your VM has a public IP
 
    If your VM does not have a public IP Address, it will look like this:

    :::image type="content" source="media/linux-vm-connect/no-public-ip.png" alt-text="Screenshot of how the networking section looks when you do not have a public I P.":::

 
    To learn more about adding a public IP address to an existing VM, see  [Associate a public IP address to a virtual machine](../virtual-network/ip-services/associate-public-ip-address-vm.md)

- Verify your VM is running. On the Overview tab, in the **Essentials** section, verify the status of the VM is **Running**. To start the VM, select **Start** at the top of the page.

    :::image type="content" source="media/linux-vm-connect/running.png" alt-text="Screenshot showing how to check to make sure your virtual machine is in the running state.":::
   
## Connect to the VM

Once the above prerequisites are met, you are ready to connect to your VM. Open your SSH client of choice. The SSH client command is typically included in Linux, macOS, and Windows. If you are using Windows 7 or older, where Win32 OpenSSH is not included by default, consider installing [WSL](/windows/wsl/about) or using [Azure Cloud Shell](../cloud-shell/overview.md) from the browser.

> [!NOTE]
> The following examples assume the SSH key is in the key.pem format. If you used CLI or Azure PowerShell to download your keys, they may be in the id_rsa format.

## [WSL, macOS, or native Linux client](#tab/Linux)

### SSH with a new key pair
1. Ensure your public and private keys are in the correct directory. The directory is usually `~/.ssh`.

    If you generated keys manually or generated them with the CLI, then the keys are probably already there. However, if you downloaded them in pem format from the Azure portal, you may need to move them to the right location. This can be done with the following syntax: `mv PRIVATE_KEY_SOURCE  PRIVATE_KEY_DESTINATION`
   
    For example, if the key is in the `Downloads` folder, and `myKey.pem` is the name of your SSH key, type:
    ```bash
    mv /Downloads/myKey.pem ~/.ssh
    ```  
    > [!NOTE]
    > If you're using WSL, local files are found in the `mnt/c/` directory. Accordingly, the path to the downloads folder and SSH key would be `/mnt/c/Users/{USERNAME}/Downloads/myKey.pem`
    
2. Ensure you have read-only access to the private key by running 
    ```bash
    chmod 400 ~/.ssh/myKey.pem
    ``` 
3. Run the SSH command with the following syntax: `ssh -i PATH_TO_PRIVATE_KEY USERNAME@EXTERNAL_IP`
    
    For example, if your `azureuser` is the username you created and `20.51.230.13` is the public IP address of your VM, type:
    ```bash
    ssh -i ~/.ssh/myKey.pem azureuser@20.51.230.13
    ```
4. Validate the returned fingerprint.

    If you have never connected to this VM before, you'll be asked to verify the hosts fingerprint. It's tempting to simply accept the fingerprint presented, but that exposes you to a potential person in the middle attack. You should always validate the hosts fingerprint. You only need to do this the first time you connect from a client. To get the host fingerprint via the portal, use the Run Command feature to execute the command:
    
     ```bash
     ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub | awk '{print $2}'
     ```
5. Success! You should now be connected to your VM. If you're unable to connect, see [Troubleshoot SSH connections](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).

### SSH With existing public key
1. Run the following command in your SSH client. In this example, *20.51.230.13* is the public IP Address of your VM and *azureuser* is the username you created when you created the VM.
    ```bash
    ssh azureuser@20.51.230.13
    ```
2. Validate the returned fingerprint.
    
    If you have never connected to this VM before you will be asked to verify the hosts fingerprint. It is tempting to simply accept the fingerprint presented, however, this exposes you to a possible person in the middle attack. You should always validate the hosts fingerprint. You only need to do this on the first time you connect from a client. To obtain the host fingerprint via the portal, use the Run Command feature to execute the command:
    
    ```bash
    ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub | awk '{print $2}'
    ```

3. Success! You should now be connected to your VM. If you're unable to connect, see our troubleshooting guide [Troubleshoot SSH connections](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).

### Password authentication
 
> [!WARNING]
> This type of authentication method is not as secure and is not recommended.

1. Run the following command in your SSH client. In this example, *20.51.230.13* is the public IP Address of your VM and *azureuser* is the username you created when you created the VM.

    ```bash
    ssh azureuser@20.51.230.13
    ```

    If you forgot your password or username see [Reset Access to an Azure VM](./extensions/vmaccess.md)

2. Validate the returned fingerprint.

    If you have never connected to this VM before you will be asked to verify the hosts fingerprint. It is tempting to simply accept the fingerprint presented, however, this exposes you to a possible person in the middle attack. You should always validate the hosts fingerprint. You only need to do this on the first time you connect from a client. To obtain the host fingerprint via the portal, use the Run Command feature to execute the command:
    ```bash
    ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub | awk '{print $2}'
    ```

3. Success! You should now be connected to your VM. If you're unable to connect using the correct method above, see [Troubleshoot SSH connections](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).


## [Windows command line (cmd.exe, PowerShell etc.)](#tab/Windows)

### SSH with a new key pair

1. Locate your private SSH Key
2. Run the SSH command with the following syntax: `ssh -i PATH_TO_PRIVATE_KEY USERNAME@EXTERNAL_IP`

    For example, if your `azureuser` is the username you created and `20.51.230.13` is the public IP address of your VM, type:
    ```powershell
    ssh -i  .\Downloads\myKey.pem azureuser@20.51.230.13 
    ```
3. Validate the returned fingerprint.

    If you have never connected to this VM before you will be asked to verify the hosts fingerprint. It is tempting to simply accept the fingerprint presented, however, this exposes you to a possible person in the middle attack. You should always validate the hosts fingerprint. You only need to do this on the first time you connect from a client. To obtain the host fingerprint via the portal, use the Run Command feature to execute the command: 
    
    ```azurepowershell-interactive
    Invoke-AzVMRunCommand -ResourceGroupName 'myResourceGroup' -VMName 'myVM' -CommandId 'RunPowerShellScript' -ScriptString 
    'ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub | awk '{print $2}''
    ```

4. Success! You should now be connected to your VM. If you are unable to connect, see [Troubleshoot SSH connections](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).

### Password authentication
 
> [!WARNING]
> This type of authentication method is not as secure and is not our recommended way to connect.

1. Run the following command in your SSH client, where `20.51.230.13` is the public IP Address of your VM and `azureuser` is the username you created when you created the VM. 

    ```bash
    ssh azureuser@20.51.230.13
    ```

   If you forgot your password or username see [Reset Access to an Azure VM](./extensions/vmaccess.md)

2. Validate the returned fingerprint.
    
    If you have never connected to this VM before you will be asked to verify the hosts fingerprint. It is tempting to simply accept the fingerprint presented, however, this exposes you to a potential person in the middle attack. You should always validate the hosts fingerprint. You only need to do this on the first time you connect from a client. To obtain the host fingerprint via the portal, use the Run Command feature to execute the command:
    
    ```bash
    ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub | awk '{print $2}'
    ```

3. Success! You should now be connected to your VM. If you're unable to connect using the methods above, see [Troubleshoot SSH connections](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).

---

## Next steps

Learn how to transfer files to an existing Linux VM, see [Use SCP to move files to and from a Linux VM](./linux/copy-files-to-linux-vm-using-scp.md).
