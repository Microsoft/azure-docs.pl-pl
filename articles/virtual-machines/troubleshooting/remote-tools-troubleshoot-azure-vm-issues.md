---
title: Rozwiązywanie problemów z MASZYNami wirtualnymi platformy Azure za pomocą narzędzi zdalnych Microsoft Docs
description: Dowiedz się więcej na temat PsExec, skryptów programu PowerShell i innych zdalnych narzędzi, których można użyć do rozwiązywania problemów z MASZYNami wirtualnymi platformy Azure bez użycia protokołu RDP.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 0681346252f840173d5cd7d4cfe5ef40076f0068
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97912601"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Rozwiązywanie problemów z MASZYNami wirtualnymi platformy Azure za pomocą narzędzi zdalnych

W przypadku rozwiązywania problemów dotyczących maszyn wirtualnych platformy Azure można nawiązać połączenie z MASZYNą wirtualną za pomocą narzędzi zdalnych, które zostały omówione w tym artykule, zamiast korzystać z Remote Desktop Protocol (RDP).

## <a name="serial-console"></a>Konsola szeregowa

Użyj [konsoli szeregowej dla Virtual Machines platformy Azure](serial-console-windows.md) , aby uruchomić polecenia na zdalnej maszynie wirtualnej platformy Azure.

## <a name="remote-cmd"></a>Zdalne polecenie CMD

Pobierz [PsExec](/sysinternals/downloads/psexec). Połącz się z maszyną wirtualną, uruchamiając następujące polecenie:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Polecenie musi być uruchamiane na komputerze znajdującym się w tej samej sieci wirtualnej.
>* Aby zastąpić, można użyć adresu DIP lub nazwy hosta \<computer> .
>* Parametr-s sprawdza, czy polecenie jest wywoływane przy użyciu konta systemowego (uprawnienia administratora).
>* PsExec używa portów TCP 135 i 445. W związku z tym dwa porty muszą być otwarte na zaporze.

## <a name="run-command"></a>Uruchom polecenie

Aby uzyskać więcej informacji na temat używania funkcji run polecenia do uruchamiania skryptów na maszynie wirtualnej, zobacz [Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Uruchom](../windows/run-command.md).

## <a name="custom-script-extension"></a>Rozszerzenie niestandardowego skryptu

Możesz użyć funkcji niestandardowego rozszerzenia skryptu do uruchomienia skryptu niestandardowego na docelowej maszynie wirtualnej. Aby skorzystać z tej funkcji, muszą zostać spełnione następujące warunki:

* Maszyna wirtualna ma łączność.
* Agent maszyny wirtualnej platformy Azure jest zainstalowany i działa zgodnie z oczekiwaniami na maszynie wirtualnej.
* Rozszerzenie nie zostało wcześniej zainstalowane na maszynie wirtualnej.
 
  Rozszerzenie Wstawia skrypt tylko podczas pierwszego użycia. Jeśli użyjesz tej funkcji później, rozszerzenie rozpoznaje, że było już używane i nie przekazuje nowego skryptu.

Przekaż skrypt na konto magazynu i Wygeneruj własny kontener. Następnie uruchom następujący skrypt w Azure PowerShell na komputerze, który ma łączność z maszyną wirtualną.

### <a name="for-classic-deployment-model-vms"></a>Dla maszyn wirtualnych z klasycznym modelem wdrażania

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Dla maszyn wirtualnych Azure Resource Manager

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Zdalne środowisko PowerShell

>[!NOTE]
>Port TCP 5986 (HTTPS) musi być otwarty, aby można było użyć tej opcji.
>
>W przypadku maszyn wirtualnych Azure Resource Manager należy otworzyć port 5986 w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń). Aby uzyskać więcej informacji, zobacz grupy zabezpieczeń. 
>
>W przypadku maszyn wirtualnych frontonu reddog trzeba mieć punkt końcowy z portem prywatnym (5986) i portem publicznym. Następnie trzeba również otworzyć ten port publiczny na sieciowej grupy zabezpieczeń.

### <a name="set-up-the-client-computer"></a>Konfigurowanie komputera klienckiego

Aby użyć programu PowerShell do zdalnego nawiązywania połączenia z maszyną wirtualną, należy najpierw skonfigurować komputer kliencki w taki sposób, aby zezwalał na połączenie. W tym celu należy dodać maszynę wirtualną do listy zaufanych hostów programu PowerShell, uruchamiając następujące polecenie zgodnie z potrzebami.

Aby dodać jedną maszynę wirtualną do listy zaufanych hostów:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Aby dodać wiele maszyn wirtualnych do listy zaufanych hostów:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Aby dodać wszystkie komputery do listy zaufanych hostów:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Włącz RemotePS na maszynie wirtualnej

W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania Użyj niestandardowego rozszerzenia skryptu, aby uruchomić następujący skrypt:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

W przypadku maszyn wirtualnych Azure Resource Manager Użyj poleceń Uruchom z portalu, aby uruchomić skrypt EnableRemotePS:

![Uruchom polecenie](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Uruchom następujące polecenie w oparciu o lokalizację komputera klienckiego:

* Poza siecią wirtualną lub wdrożeniem

  * W przypadku maszyny wirtualnej utworzonej przy użyciu klasycznego modelu wdrażania Uruchom następujące polecenie:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * W przypadku maszyny wirtualnej Azure Resource Manager należy najpierw dodać nazwę DNS do publicznego adresu IP. Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie w pełni kwalifikowanej nazwy domeny w Azure Portal dla maszyny wirtualnej z systemem Windows](../create-fqdn.md). Następnie uruchom następujące polecenie:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* W obrębie sieci wirtualnej lub wdrożenia Uruchom następujące polecenie:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Ustawienie flagi SkipCaCheck pomija wymagania dotyczące importowania certyfikatu do maszyny wirtualnej podczas uruchamiania sesji.

Możesz również użyć polecenia cmdlet Invoke-Command, aby zdalnie uruchomić skrypt na maszynie wirtualnej.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Rejestr zdalny

>[!NOTE]
>Aby można było użyć tej opcji, port TCP 135 lub 445 musi być otwarty.
>
>W przypadku maszyn wirtualnych Azure Resource Manager należy otworzyć port 5986 w sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz grupy zabezpieczeń. 
>
>W przypadku maszyn wirtualnych frontonu reddog trzeba mieć punkt końcowy z portem prywatnym 5986 i portem publicznym. Należy również otworzyć ten port publiczny na sieciowej grupy zabezpieczeń.

1. Z poziomu innej maszyny wirtualnej w tej samej sieci wirtualnej, Otwórz Edytor rejestru (regedit.exe).

2. Wybierz pozycję **plik**  >  **Połącz rejestr sieciowy**.

   ![Edytor rejestru](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Znajdź docelową maszynę wirtualną według **nazwy hosta** lub **dynamicznego adresu IP** (preferowany), wprowadzając ją w polu **Wprowadź nazwę obiektu do wybrania** .

   ![Wprowadź nazwę obiektu, aby zaznaczyć pole](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Wprowadź poświadczenia dla docelowej maszyny wirtualnej.

5. Wprowadź wszelkie niezbędne zmiany w rejestrze.

## <a name="remote-services-console"></a>Konsola usług zdalnych

>[!NOTE]
>Aby można było użyć tej opcji, porty TCP 135 lub 445 muszą być otwarte.
>
>W przypadku maszyn wirtualnych Azure Resource Manager należy otworzyć port 5986 w sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz grupy zabezpieczeń. 
>
>W przypadku maszyn wirtualnych frontonu reddog trzeba mieć punkt końcowy z portem prywatnym 5986 i portem publicznym. Należy również otworzyć ten port publiczny na sieciowej grupy zabezpieczeń.

1. Z poziomu innej maszyny wirtualnej w tej samej sieci wirtualnej, Otwórz wystąpienie programu **Services. msc**.

2. Kliknij prawym przyciskiem myszy pozycję **usługi (lokalne)**.

3. Wybierz pozycję **Połącz z innym komputerem**.

   ![Usługa zdalna](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Wprowadź dynamiczny adres IP docelowej maszyny wirtualnej.

   ![Wejściowy dynamiczny adres IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Wprowadź wszelkie niezbędne zmiany w usługach.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat polecenia cmdlet Enter-PSSession, zobacz [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession?view=powershell-5.1&preserve-view=true).
- Aby uzyskać więcej informacji o rozszerzeniu niestandardowego skryptu dla systemu Windows przy użyciu klasycznego modelu wdrażania, zobacz [rozszerzenie niestandardowego skryptu dla systemu Windows](../extensions/custom-script-windows.md).
- PsExec jest częścią [pakietu program PsTools](https://download.sysinternals.com/files/PSTools.zip).
- Aby uzyskać więcej informacji na temat pakietu program PsTools, zobacz [program PsTools](/sysinternals/downloads/pstools).
