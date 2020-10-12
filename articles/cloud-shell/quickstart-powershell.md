---
title: Azure Cloud Shell — szybki start — PowerShell
description: Dowiedz się, jak używać programu PowerShell w przeglądarce z Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: d4a7f1453ec686cfa16d260101ba81f429ce1da0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469460"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Przewodnik Szybki Start dla programu PowerShell w programie Azure Cloud Shell

W tym dokumencie szczegółowo przedstawiono sposób użycia programu PowerShell w Cloud Shell w [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Dostępna jest również funkcja [bash w Azure Cloud Shell](quickstart.md) Szybki Start.

## <a name="start-cloud-shell"></a>Uruchamianie usługi Cloud Shell

1. Kliknij przycisk **Cloud Shell** na górnym pasku nawigacyjnym Azure Portal

   ![Zrzut ekranu przedstawiający sposób uruchamiania Azure Cloud Shell z Azure Portal.](media/quickstart-powershell/shell-icon.png)

2. Wybierz środowisko PowerShell z listy rozwijanej, a ty będziesz na dysku platformy Azure `(Azure:)`

   ![Zrzut ekranu przedstawiający sposób wybierania środowiska programu PowerShell dla Azure Cloud Shell.](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Uruchom polecenia programu PowerShell

Uruchamianie zwykłych poleceń programu PowerShell w Cloud Shell, takich jak:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Nawigowanie po zasobach platformy Azure

 1. Wyświetl listę wszystkich subskrypcji z `Azure` dysku

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd` na preferowaną subskrypcję

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Wyświetlanie wszystkich zasobów platformy Azure w ramach bieżącej subskrypcji

    Wpisz `dir` , aby wyświetlić listę wielu widoków zasobów platformy Azure.

    ```azurepowershell-interactive
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
    ```

### <a name="allresources-view"></a>Widok AllResources

Wpisz `dir` w `AllResources` katalogu, aby wyświetlić zasoby platformy Azure.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Eksplorowanie grup zasobów

 Możesz przejść do `ResourceGroups` katalogu i wewnątrz określonej grupy zasobów, aby znaleźć maszyny wirtualne.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Można zauważyć, że podczas drugiego czasu podczas wpisywania `dir` Cloud Shell będzie można znacznie szybciej wyświetlić elementy.
> Wynika to z faktu, że elementy podrzędne są buforowane w pamięci w celu lepszego środowiska użytkownika.
Można jednak zawsze użyć, `dir -Force` Aby pobrać dane świeże.

### <a name="navigate-storage-resources"></a>Nawigowanie po zasobach magazynu

Wprowadzając do `StorageAccounts` katalogu, można łatwo nawigować po wszystkich zasobach magazynu

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Korzystając z parametrów połączenia, można zainstalować udział Azure Files za pomocą następującego polecenia.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Aby uzyskać szczegółowe informacje, zobacz [Instalowanie udziału Azure Files i dostęp do udziału w systemie Windows][azmount].

Możesz również przejść do katalogów w udziale Azure Files w następujący sposób:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Praca z maszynami wirtualnymi

Wszystkie maszyny wirtualne można znaleźć w ramach bieżącej subskrypcji za pośrednictwem `VirtualMachines` katalogu.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Wywoływanie skryptu programu PowerShell na zdalnych maszynach wirtualnych

 > [!WARNING]
 > Zapoznaj się z [tematem Rozwiązywanie problemów z zarządzaniem zdalnym maszyn wirtualnych platformy Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Przy założeniu, że masz maszynę wirtualną MyVM1, użyjmy `Invoke-AzVMCommand` do wywołania bloku skryptu programu PowerShell na maszynie zdalnej.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Możesz również najpierw przejść do katalogu VirtualMachines i uruchomić go `Invoke-AzVMCommand` w następujący sposób.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktywna logowanie do zdalnej maszyny wirtualnej

Możesz użyć `Enter-AzVM` , aby interakcyjnie zalogować się do maszyny wirtualnej działającej na platformie Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Możesz również przejść do `VirtualMachines` katalogu jako pierwszy i uruchomić go `Enter-AzVM` w następujący sposób

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Odkryj aplikacje webapps

Wprowadzając do `WebApps` katalogu, można łatwo nawigować po zasobach aplikacji sieci Web

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>Protokół SSH

Aby przeprowadzić uwierzytelnianie na serwerach lub maszynach wirtualnych przy użyciu protokołu SSH, należy wygenerować parę kluczy publiczny-prywatny w Cloud Shell i opublikować klucz publiczny na `authorized_keys` komputerze zdalnym, na przykład `/home/user/.ssh/authorized_keys` .

> [!NOTE]
> Można utworzyć klucze prywatne SSH `ssh-keygen` i opublikować je `$env:USERPROFILE\.ssh` w programie w Cloud Shell.

### <a name="using-ssh"></a>Korzystanie z protokołu SSH

Postępuj zgodnie z instrukcjami [tutaj](../virtual-machines/linux/quick-create-powershell.md) , aby utworzyć nową konfigurację maszyny wirtualnej za pomocą poleceń cmdlet Azure PowerShell.
Przed wywołaniem programu w `New-AzVM` celu rozpoczęcia wdrażania należy dodać klucz publiczny SSH do konfiguracji maszyny wirtualnej.
Nowo utworzona maszyna wirtualna będzie zawierać klucz publiczny w `~\.ssh\authorized_keys` lokalizacji, umożliwiając w ten sposób włączenie sesji SSH bez poświadczeń do maszyny wirtualnej.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Wyświetl dostępne polecenia

W obszarze `Azure` dysk wpisz polecenie, `Get-AzCommand` Aby pobrać specyficzne dla kontekstu polecenia platformy Azure.

Możesz również użyć, `Get-Command *az* -Module Az.*` Aby znaleźć dostępne polecenia platformy Azure.

## <a name="install-custom-modules"></a>Instalowanie modułów niestandardowych

Można uruchomić program, `Install-Module` Aby zainstalować moduły z [Galeria programu PowerShell][gallery].

## <a name="get-help"></a>Get-Help

Wpisz `Get-Help` , aby uzyskać informacje o programie PowerShell w Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

W przypadku określonego polecenia można nadal wykonać polecenie `Get-Help` cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Przechowywanie danych przy użyciu Azure Files

Możesz utworzyć skrypt, powiedzieć `helloworld.ps1` i zapisać go w `clouddrive` usłudze, aby używać go w sesjach powłoki.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Następnym razem, gdy używasz programu PowerShell w Cloud Shell, `helloworld.ps1` plik będzie istniał w `$HOME\clouddrive` katalogu, który instaluje udział Azure Files.

## <a name="use-custom-profile"></a>Użyj profilu niestandardowego

Możesz dostosować środowisko programu PowerShell, tworząc profile programu PowerShell — `profile.ps1` (lub `Microsoft.PowerShell_profile.ps1` ).
Zapisz ją w obszarze `$profile.CurrentUserAllHosts` (lub `$profile.CurrentUserAllHosts` ), aby można było załadować ją w każdym programie PowerShell w sesji Cloud Shell.

Aby dowiedzieć się, jak utworzyć profil, zapoznaj się z [tematem profile][profile].

## <a name="use-git"></a>Korzystanie z narzędzia Git

Aby sklonować repozytorium Git w Cloud Shell, musisz utworzyć [osobisty token dostępu][githubtoken] i użyć go jako nazwy użytkownika. Gdy masz token, Sklonuj repozytorium w następujący sposób:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Wyjdź z powłoki:

Wpisz, `exit` Aby przerwać sesję.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/