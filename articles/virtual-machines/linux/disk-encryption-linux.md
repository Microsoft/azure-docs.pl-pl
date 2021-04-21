---
title: Scenariusze usługi Azure Disk Encryption w przypadku maszyn wirtualnych z systemem Linux
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure Disk Encryption dla maszyn wirtualnych z systemem Linux dla różnych scenariuszy
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f014c07a319cbb07497cba01699b93d092255b93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771524"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Scenariusze usługi Azure Disk Encryption w przypadku maszyn wirtualnych z systemem Linux

Azure Disk Encryption maszyn wirtualnych z systemem Linux funkcja DM-Crypt systemu Linux zapewnia pełne szyfrowanie dysków systemu operacyjnego i dysków danych. Ponadto zapewnia szyfrowanie dysku tymczasowego podczas korzystania z funkcji EncryptFormatAll.

Azure Disk Encryption jest [zintegrowana z Azure Key Vault,](disk-encryption-key-vault.md) aby ułatwić kontrolowanie kluczy szyfrowania dysków i wpisów tajnych oraz zarządzanie nimi. Aby uzyskać omówienie usługi, zobacz Azure Disk Encryption dla maszyn [wirtualnych z systemem Linux.](disk-encryption-overview.md)

Szyfrowanie dysków można zastosować tylko do maszyn wirtualnych o [obsługiwanych rozmiarach maszyn wirtualnych i systemach operacyjnych.](disk-encryption-overview.md#supported-vms-and-operating-systems) Należy również spełnić następujące wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

We wszystkich przypadkach należy [utworzyć](snapshot-copy-managed-disk.md) migawkę i/lub utworzyć kopię zapasową przed zaszyfrowaniem dysków. Kopie zapasowe zapewniają możliwość opcji odzyskiwania w przypadku wystąpienia nieoczekiwanego błędu podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopii zapasowej przed rozpoczęciem szyfrowania. Po kopii zapasowej można użyć polecenia [cmdlet Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) aby zaszyfrować dyski zarządzane, określając parametr -skipVmBackup. Aby uzyskać więcej informacji na temat sposobu kopii zapasowej i przywracania zaszyfrowanych maszyn wirtualnych, zobacz [Azure Backup](../../backup/backup-azure-vms-encryption.md) maszyn wirtualnych. 

>[!WARNING]
> - Jeśli wcześniej zaszyfrowano maszynę wirtualną Azure Disk Encryption usłudze Azure AD, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Zobacz [Azure Disk Encryption z usługą Azure AD (poprzednia wersja),](disk-encryption-overview-aad.md) aby uzyskać szczegółowe informacje. 
>
> - Podczas szyfrowania woluminów systemu operacyjnego Linux maszyna wirtualna powinna być uznawana za niedostępną. Zdecydowanie zalecamy unikanie logowań SSH w trakcie szyfrowania, aby uniknąć problemów z blokowaniem otwartych plików, do których będzie trzeba uzyskać dostęp podczas procesu szyfrowania. Aby sprawdzić postęp, użyj polecenia cmdlet programu PowerShell [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) lub polecenia [vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI. Ten proces może potrwać kilka godzin w przypadku woluminu systemu operacyjnego o pojemności 30 GB oraz dodatkowy czas szyfrowania woluminów danych. Czas szyfrowania woluminu danych będzie proporcjonalny do rozmiaru i ilości woluminów danych, chyba że jest używana cała opcja formatu szyfrowania. 
> - Wyłączanie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwane tylko w przypadku woluminów danych. Nie jest obsługiwana na woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

Azure Disk Encryption można włączyć i zarządzać nimi za pomocą interfejsu wiersza [polecenia platformy Azure](/cli/azure) [Azure PowerShell](/powershell/azure/new-azureps-module-az). Aby to zrobić, musisz zainstalować narzędzia lokalnie i połączyć się z subskrypcją platformy Azure.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs [wiersza polecenia platformy Azure 2.0](/cli/azure) to narzędzie wiersza polecenia do zarządzania zasobami platformy Azure. Interfejs wiersza polecenia został zaprojektowany w celu elastycznego wykonywania zapytań o dane, obsługi długotrwałych operacji jako procesów nieblokowania i ułatwiania wykonywania skryptów. Możesz zainstalować go lokalnie, korzystając z kroków instalowania [interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

 

Aby [zalogować się do konta platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure,](/cli/azure/authenticate-azure-cli)użyj [polecenia az login.](/cli/azure/reference-index#az_login)

```azurecli
az login
```

Jeśli chcesz wybrać dzierżawę, w ramach których chcesz się zalogować, użyj:
    
```azurecli
az login --tenant <tenant>
```

Jeśli masz wiele subskrypcji i chcesz określić określoną subskrypcję, pobierz listę subskrypcji za pomocą az [account list](/cli/azure/account#az_account_list) i określ za pomocą az [account set](/cli/azure/account#az_account_set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do interfejsu wiersza polecenia platformy Azure w wersji 2.0.](/cli/azure/get-started-with-azure-cli) 

### <a name="azure-powershell"></a>Azure PowerShell
Moduł [Azure PowerShell az](/powershell/azure/new-azureps-module-az) zawiera zestaw polecenia cmdlet, które Azure Resource Manager modelu zarządzania zasobami platformy Azure. [](../../azure-resource-manager/management/overview.md) Można go użyć w przeglądarce z programem [Azure Cloud Shell](../../cloud-shell/overview.md)lub zainstalować go na komputerze lokalnym, korzystając z instrukcji w temacie Instalowanie Azure PowerShell [modułu](/powershell/azure/install-az-ps). 

Jeśli masz już zainstalowane lokalnie, upewnij się, że używasz najnowszej wersji zestawu SDK Azure PowerShell, aby skonfigurować Azure Disk Encryption. Pobierz najnowszą wersję [Azure PowerShell wersji](https://github.com/Azure/azure-powershell/releases).

Aby [zalogować się do konta platformy Azure przy użyciu Azure PowerShell](/powershell/azure/authenticate-azureps), użyj polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji i chcesz określić jedną z nich, użyj polecenia cmdlet [Get-AzSubscription,](/powershell/module/Az.Accounts/Get-AzSubscription) aby je wyświetlić, a następnie polecenia cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Uruchomienie polecenia cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) spowoduje sprawdzenie, czy wybrano poprawną subskrypcję.

Aby potwierdzić, Azure Disk Encryption polecenia cmdlet są zainstalowane, użyj polecenia cmdlet [Get-command:](/powershell/module/microsoft.powershell.core/get-command)
     
```powershell
Get-command *diskencryption*
```
Aby uzyskać więcej informacji, zobacz [Wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Resource Manager, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. Jeśli potrzebujesz informacji o schemacie dla rozszerzenia maszyny wirtualnej, zobacz artykuł Azure Disk Encryption [dla rozszerzenia systemu Linux.](../extensions/azure-disk-enc-linux.md)

>[!IMPORTANT]
 >Należy utworzyć migawkę i/lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dysku zarządzanym poza programem i przed włączeniem Azure Disk Encryption. Migawkę dysku zarządzanego można utworzyć z portalu lub za pośrednictwem [Azure Backup](../../backup/backup-azure-vms-encryption.md). Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa w przypadku nieoczekiwanego błędu podczas szyfrowania. Po kopii zapasowej można użyć Set-AzVMDiskEncryptionExtension polecenia cmdlet do szyfrowania dysków zarządzanych, określając parametr -skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension nie powiedzie się względem maszyn wirtualnych opartych na dysku zarządzanym, dopóki nie zostanie wykonana kopia zapasowa i ten parametr nie zostanie określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 

Szyfrowanie dysków na zaszyfrowanym dysku VHD można włączyć, instalując i używając narzędzia wiersza polecenia interfejsu wiersza polecenia platformy [Azure.](/cli/azure/) Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejących lub działających na platformie Azure maszyn wirtualnych z systemem Linux, użyj następujących poleceń interfejsu wiersza polecenia:

Użyj polecenia [az vm encryption enable,](/cli/azure/vm/encryption#az_vm_encryption_show) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure.

- **Szyfrowanie uruchomionej maszyny wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza szyfrowania klucza:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Składnia wartości parametru disk-encryption-keyvault to pełny ciąg identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu-kluczy]</br>
Składnia dla wartości parametru key-encryption-key jest pełnym identyfikatorem URI klucza szyfrowania kluczy, jak w: https://[nazwa magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej, użyj [polecenia az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj [polecenia az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) Wyłączanie szyfrowania jest dozwolone tylko w przypadku woluminów danych dla maszyn wirtualnych z systemem Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure. Tworzenie [migawki i/lub](snapshot-copy-managed-disk.md) tworzenie kopii zapasowej maszyny wirtualnej [za pomocą Azure Backup,](../../backup/backup-azure-vms-encryption.md) zanim dyski zostaną zaszyfrowane. Parametr -skipVmBackup jest już określony w skryptach programu PowerShell w celu zaszyfrowania uruchomionej maszyny wirtualnej z systemem Linux.

-  **Szyfrowanie uruchomionej maszyny wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension cmdlet. Grupa zasobów, maszyna wirtualna i magazyn kluczy zostały utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault swoimi wartościami. Zmodyfikuj parametr -VolumeType, aby określić, które dyski są szyfrowane.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza szyfrowania klucza:** W przypadku szyfrowania dysków danych, a nie dysku systemu operacyjnego, może być konieczne dodanie parametru -VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Składnia wartości parametru disk-encryption-keyvault to pełny ciąg identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu-kluczy]</br> Składnia dla wartości parametru key-encryption-key jest pełnym identyfikatorem URI klucza szyfrowania kluczy, jak w: https://[nazwa magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Wyłączanie szyfrowania jest dozwolone tylko w przypadku woluminów danych dla maszyn wirtualnych z systemem Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu szablonu

Szyfrowanie dysków można włączyć na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux na platformie Azure przy [użyciu Resource Manager szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Kliknij **pozycję Deploy to Azure (Wd wdrażaj** na platformie Azure) w szablonie Szybkiego startu platformy Azure.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowę. Kliknij **pozycję Utwórz,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej.

W poniższej tabeli wymieniono Resource Manager szablonu dla istniejących lub uruchomionych maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny wirtualnej do uruchomienia operacji szyfrowania. |
| keyVaultName | Nazwa magazynu kluczy, do który ma zostać przekazany klucz szyfrowania. Można go pobrać za pomocą polecenia cmdlet lub `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` polecenia interfejsu wiersza polecenia platformy Azure `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` .|
| keyVaultResourceGroup | Nazwa grupy zasobów zawierającej magazyn kluczy. |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używanego do szyfrowania klucza szyfrowania. Ten parametr jest opcjonalny, jeśli wybierzesz **pozycję nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na UseExistingKek listy rozwijanej, należy wprowadzić _wartość keyEncryptionKeyURL._ |
| typ woluminu | Typ woluminu, na który jest wykonywana operacja szyfrowania. Prawidłowe wartości to _OS,_ _Data_ i _All._ 
| forceUpdateTag | Przekaż unikatową wartość, np. identyfikator GUID, za każdym razem, gdy operacja musi zostać wymuś uruchomienie. |
| location | Lokalizacja dla wszystkich zasobów. |

Aby uzyskać więcej informacji na temat konfigurowania szablonu szyfrowania dysków maszyn wirtualnych z systemem Linux, [zobacz Azure Disk Encryption dla systemu Linux.](../extensions/azure-disk-enc-linux.md)

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Używanie funkcji EncryptFormatAll dla dysków danych na maszyn wirtualnych z systemem Linux

Parametr **EncryptFormatAll** skraca czas szyfrowania dysków danych systemu Linux. Partycje, które spełniają określone kryteria, zostaną sformatowane wraz z bieżącymi systemami plików, a następnie ponownie zainstalowane w miejscu, w którym były przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych spełniający kryteria, możesz go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia wszystkie zainstalowane wcześniej dyski zostaną sformatowane, a warstwa szyfrowania zostanie uruchomiona na pustym dysku. Po wybraniu tej opcji dysk tymczasowy dołączony do maszyny wirtualnej również zostanie zaszyfrowany. Jeśli dysk tymczasowy zostanie zresetowany, zostanie ponownie sformatowany i ponownie zaszyfrowany dla maszyny wirtualnej przez Azure Disk Encryption przy następnej okazji. Gdy dysk zasobów zostanie zaszyfrowany, agent systemu [Linux](../extensions/agent-linux.md) Microsoft Azure nie będzie mógł zarządzać dyskiem zasobów i włączyć pliku wymiany, ale można ręcznie skonfigurować plik wymiany.

>[!WARNING]
> EncryptFormatAll nie należy używać, gdy na woluminach danych maszyny wirtualnej są potrzebne dane. Możesz wykluczyć dyski z szyfrowania przez odinstalowanie ich. Najpierw należy wypróbować funkcję EncryptFormatAll na testowej maszynie wirtualnej, poznać parametr funkcji i jego wpływ przed podjęciem próby na produkcyjnej maszynie wirtualnej. Opcja EncryptFormatAll formatuje dysk danych i wszystkie dane na nim zostaną utracone. Przed przystąpieniem sprawdź, czy dyski, które chcesz wykluczyć, są prawidłowo odinstalowane. </br></br>
 >Jeśli ustawiasz ten parametr podczas aktualizowania ustawień szyfrowania, może to prowadzić do ponownego uruchomienia przed rzeczywistym szyfrowaniem. W takim przypadku należy również usunąć dysk, który nie ma być sformatowany z pliku fstab. Podobnie należy dodać partycję, która ma być szyfrowana do pliku fstab przed zainicjowanie operacji szyfrowania. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll criteria (Kryteria EncryptFormatAll)
Parametr przechodzi przez wszystkie partycje i szyfruje je, o ile spełniają **wszystkie** poniższe kryteria:
- Nie jest partycją katalogu głównego/systemu operacyjnego/rozruchu
- Nie jest jeszcze zaszyfrowany
- Nie jest woluminem BEK
- Nie jest woluminem RAID
- Nie jest woluminem LVM
- Jest zainstalowany

Zaszyfruj dyski, które tworzą wolumin RAID lub LVM, a nie wolumin RAID lub LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Używanie parametru EncryptFormatAll z interfejsem wiersza polecenia platformy Azure
Użyj polecenia [az vm encryption enable,](/cli/azure/vm/encryption#az_vm_encryption_enable) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure.

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu funkcji EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Używanie parametru EncryptFormatAll z poleceniem cmdlet programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z parametrem EncryptFormatAll. 

**Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu funkcji EncryptFormatAll:** Na przykład poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension z parametrem EncryptFormatAll. Grupa zasobów, maszyna wirtualna i magazyn kluczy zostały utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault swoimi wartościami.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Używanie parametru EncryptFormatAll z menedżerem woluminów logicznych (LVM) 
Zalecamy konfigurację LVM-on-crypt. W przypadku wszystkich poniższych przykładów zastąp ścieżkę urządzenia i punkty instalacji tym, co pasuje do Twojego przypadku użycia. Tę konfigurację można wykonać w następujący sposób:

1.  Dodaj dyski danych, które będą ować maszynę wirtualną.

1. Sformatuj, zainstaluj i dodaj te dyski do pliku fstab.

1. Wybierz standard partycji, utwórz partycję, która obejmuje cały dysk, a następnie sformatuj partycję. W tym miejscu używamy linków symbolicznych wygenerowanych przez platformę Azure. Użycie linków symbolicznych pozwala uniknąć problemów związanych ze zmianą nazw urządzeń. Aby uzyskać więcej informacji, zobacz artykuł [Rozwiązywanie problemów z nazwami](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems) urządzeń.
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. Zainstaluj dyski:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Dodaj do pliku fstab:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Uruchom polecenie cmdlet Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z poleceniem -EncryptFormatAll, aby zaszyfrować te dyski.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Jeśli chcesz użyć klucza szyfrowania klucza (KEK), przekaż odpowiednio wartości URI klucza szyfrowania kluczy (KEK) i ResourceID magazynu kluczy do parametrów -KeyEncryptionKeyUrl i -KeyEncryptionKeyVaultId:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Skonfiguruj LVM na tych nowych dyskach. Należy pamiętać, że zaszyfrowane dyski są odblokowane po zakończeniu rozruchu maszyny wirtualnej. Dlatego instalowanie LVM również będzie musiało zostać opóźnione.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nowe maszyny wirtualne utworzone na podstawie zaszyfrowanego przez klienta wirtualnego dysku twardego i kluczy szyfrowania
W tym scenariuszu szyfrowanie można włączyć przy użyciu poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. 

Skorzystaj z instrukcji podanych w te same skrypty usługi Azure Disk Encryption, aby przygotować wstępnie zaszyfrowane obrazy, które mogą być używane na platformie Azure. Po utworzeniu obrazu możesz wykonać kroki opisane w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Linux](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Należy utworzyć migawkę i/lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dysku zarządzanym poza programem i przed włączeniem Azure Disk Encryption. Migawkę dysku zarządzanego można uzyskać z portalu lub Azure Backup [użyć.](../../backup/backup-azure-vms-encryption.md) Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa w przypadku nieoczekiwanego błędu podczas szyfrowania. Po kopii zapasowej można użyć Set-AzVMDiskEncryptionExtension polecenia cmdlet do szyfrowania dysków zarządzanych, określając parametr -skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension nie powiedzie się względem maszyn wirtualnych opartych na dysku zarządzanym, dopóki nie zostanie wykonana kopia zapasowa i ten parametr nie zostanie określony. 
>
> Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Używanie Azure PowerShell do szyfrowania maszyn wirtualnych za pomocą wstępnie zaszyfrowanych dysków VHD 
Szyfrowanie dysków na zaszyfrowanym dysku VHD można włączyć za pomocą polecenia cmdlet programu PowerShell [Set-AzVMOSDisk.](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) Poniższy przykład zawiera kilka typowych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włączanie szyfrowania na nowo dodanym dysku danych

Nowy dysk danych można dodać za pomocą narzędzia [az vm disk attach](add-disk.md)lub za [pośrednictwem Azure Portal](attach-disk-portal.md). Przed zaszyfrowaniem należy najpierw zainstalować nowo dołączony dysk danych. Należy zażądać szyfrowania dysku danych, ponieważ dysk będzie bezużytelny, gdy szyfrowanie jest w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włączanie szyfrowania na nowo dodanym dysku przy użyciu interfejsu wiersza polecenia platformy Azure

 Jeśli maszyna wirtualna została wcześniej zaszyfrowana za pomocą parametru "All", parametr --volume-type powinien pozostać "Wszystkie". Wszystkie obejmują zarówno dysk systemu operacyjnego, jak i dysk danych. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "OS", parametr --volume-type powinien zostać zmieniony na "Wszystkie", tak aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych. Jeśli maszyna wirtualna została zaszyfrowana przy użyciu tylko typu woluminu "Dane", może pozostać "Dane", jak pokazano poniżej. Dodawanie i dołączanie nowego dysku danych do maszyny wirtualnej nie jest wystarczającym przygotowaniem do szyfrowania. Nowo dołączony dysk musi być również sformatowany i poprawnie zainstalowany na maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zainstalowany w pliku /etc/fstab z trwałą [nazwą urządzenia blokowego.](../troubleshooting/troubleshoot-device-names-problems.md)  

W przeciwieństwie do składni programu PowerShell interfejs wiersza polecenia nie wymaga od użytkownika podania unikatowej wersji sekwencji podczas włączania szyfrowania. Interfejs wiersza polecenia automatycznie generuje i używa własnej unikatowej wartości wersji sekwencji.

-  **Szyfruj woluminy danych uruchomionej maszyny wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Szyfruj woluminy danych uruchomionej maszyny wirtualnej przy użyciu klucza szyfrowania klucza:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włączanie szyfrowania na nowo dodanym dysku przy użyciu Azure PowerShell
 W przypadku szyfrowania nowego dysku dla systemu Linux przy użyciu programu PowerShell należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. Przed [zaszyfrowanym dyskiem](snapshot-copy-managed-disk.md) należy utworzyć migawkę i/lub utworzyć kopię [zapasową Azure Backup](../../backup/backup-azure-vms-encryption.md) wirtualnej. Parametr -skipVmBackup jest już określony w skryptach programu PowerShell w celu zaszyfrowania nowo dodanego dysku danych.
 

-  **Szyfruj woluminy danych uruchomionej maszyny wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension cmdlet. Grupa zasobów, maszyna wirtualna i magazyn kluczy powinny już zostać utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault swoimi wartościami. Dopuszczalne wartości parametru -VolumeType to All, OS i Data. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "System operacyjny" lub "Wszystkie", parametr -VolumeType powinien zostać zmieniony na "Wszystkie", aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Szyfruj woluminy danych uruchomionej maszyny wirtualnej przy użyciu klucza szyfrowania klucza:** Dopuszczalne wartości parametru -VolumeType to All, OS i Data. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "System operacyjny" lub "Wszystkie", parametr -VolumeType powinien zostać zmieniony na Wszystkie, tak aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Składnia wartości parametru disk-encryption-keyvault jest ciągiem pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa-grupy-kluczy]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu-kluczy]</br> Składnia dla wartości parametru key-encryption-key jest pełnym identyfikatorem URI klucza szyfrowania kluczy, jak w: https://[nazwa magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Wyłączanie szyfrowania dla maszyn wirtualnych z systemem Linux
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Azure Disk Encryption nie działa w przypadku następujących scenariuszy, funkcji i technologii systemu Linux:

- Szyfrowanie podstawowej warstwy maszyny wirtualnej lub maszyn wirtualnych utworzonych za pomocą klasycznej metody tworzenia maszyny wirtualnej.
- Wyłączanie szyfrowania na dysku systemu operacyjnego lub dysku danych maszyny wirtualnej z systemem Linux, gdy dysk systemu operacyjnego jest zaszyfrowany.
- Szyfrowanie dysku systemu operacyjnego dla zestawów skalowania maszyn wirtualnych z systemem Linux.
- Szyfrowanie obrazów niestandardowych na maszynach wirtualnych z systemem Linux.
- Integracja z lokalnym systemem zarządzania kluczami.
- Azure Files (udostępniony system plików).
- Sieciowy system plików (NFS).
- Woluminy dynamiczne.
- Efemeralne dyski systemu operacyjnego.
- Szyfrowanie współdzielonych/rozproszonych systemów plików, takich jak (ale nie tylko): DFS, GFS, DRDB i CephFS.
- Przenoszenie zaszyfrowanej maszyny wirtualnej do innej subskrypcji lub regionu.
- Tworzenie obrazu lub migawki zaszyfrowanej maszyny wirtualnej i używanie jej do wdrażania dodatkowych maszyn wirtualnych.
- Zrzut awaryjne jądra (kdump).
- Oracle ACFS (system plików klastra ASM).
- Dyski NVMe maszyn wirtualnych serii Lsv2 (zobacz: [Seria Lsv2).](../lsv2-series.md)
- Maszyna wirtualna z "zagnieżdżone punkty instalacji"; oznacza to, że wiele punktów instalacji w jednej ścieżce (na przykład "/1stmountpoint/data/2stmountpoint").
- Maszyna wirtualna z napędem danych zainstalowanym na folderze systemu operacyjnego.
- Maszyna wirtualna, na której wolumin logiczny głównego (dysku systemu operacyjnego) został rozszerzony przy użyciu dysku danych.
- Maszyny wirtualne serii M z akcelerator zapisu dyskami.
- Stosowanie usługi ADE do maszyny [](../disk-encryption.md) wirtualnej z dyskami zaszyfrowanymi za pomocą szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta (SSE + CMK). Zastosowanie szyfrowania SSE + CMK do dysku danych na maszynie wirtualnej zaszyfrowanej za pomocą usługi ADE również nie jest obsługiwanym scenariuszem.
- Migrowanie maszyny wirtualnej zaszyfrowanej za  pomocą usługi ADE lub kiedykolwiek zaszyfrowanej za pomocą usługi ADE do szyfrowania po stronie serwera przy użyciu [kluczy zarządzanych przez klienta.](../disk-encryption.md)
- Szyfrowanie maszyn wirtualnych w klastrach trybu failover.
- Szyfrowanie [dysków w warstwie Ultra platformy Azure.](../disks-enable-ultra-ssd.md)

## <a name="next-steps"></a>Następne kroki

- [Azure Disk Encryption omówienie](disk-encryption-overview.md)
- [Przykładowe skrypty usługi Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)