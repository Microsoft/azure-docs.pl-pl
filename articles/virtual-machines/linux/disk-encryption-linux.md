---
title: Scenariusze usługi Azure Disk Encryption w przypadku maszyn wirtualnych z systemem Linux
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Linux w różnych scenariuszach
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: eb7db3c95fb56ebbd62d6cf882a75ce03baeb75d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736058"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Scenariusze usługi Azure Disk Encryption w przypadku maszyn wirtualnych z systemem Linux


Azure Disk Encryption dla maszyn wirtualnych z systemem Linux używa DM-Crypt funkcji systemu Linux w celu zapewnienia pełnego szyfrowania dysków systemu operacyjnego i dysków z danymi. Ponadto zapewnia szyfrowanie dysku tymczasowego przy użyciu funkcji EncryptFormatAll.

Azure Disk Encryption jest [zintegrowana z Azure Key Vault](disk-encryption-key-vault.md) , aby pomóc Ci kontrolować klucze szyfrowania dysków i wpisy tajne oraz zarządzać nimi. Aby zapoznać się z omówieniem usługi, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md).

Szyfrowanie dysków można stosować tylko do maszyn wirtualnych [obsługiwanych rozmiarów maszyn wirtualnych i systemów operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems). Musisz również spełniać następujące wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

We wszystkich przypadkach należy [wykonać migawkę](snapshot-copy-managed-disk.md) i/lub utworzyć kopię zapasową przed zaszyfrowaniem dysków. Kopie zapasowe zapewniają opcję odzyskiwania, jeśli podczas szyfrowania wystąpi nieoczekiwany błąd. Maszyny wirtualne z dyskami zarządzanymi wymagają utworzenia kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć [polecenia cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania szyfrowanych maszyn wirtualnych, zobacz artykuł [Azure Backup](../../backup/backup-azure-vms-encryption.md) . 

>[!WARNING]
> - Jeśli usługa Azure AD została wcześniej Azure Disk Encryption użyta w celu zaszyfrowania maszyny wirtualnej, należy użyć tej opcji, aby zaszyfrować maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [Azure Disk Encryption w usłudze Azure AD (w poprzedniej wersji)](disk-encryption-overview-aad.md) . 
>
> - W przypadku szyfrowania woluminów systemu operacyjnego Linux maszyna wirtualna powinna być uważana za niedostępną. Zdecydowanie zalecamy uniknięcie logowania przy użyciu protokołu SSH, gdy szyfrowanie jest w toku, aby uniknąć problemów z blokowaniem otwartych plików, do których konieczne będzie uzyskanie dostępu podczas procesu szyfrowania. Aby sprawdzić postęp, użyj polecenia cmdlet programu PowerShell [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) lub [szyfrowania maszyny wirtualnej](/cli/azure/vm/encryption#az-vm-encryption-show) . Ten proces może potrwać kilka godzin w przypadku woluminu systemu operacyjnego 30 GB, a także dodatkowy czas na szyfrowanie woluminów danych. Czas szyfrowania woluminu danych będzie proporcjonalny do rozmiaru i ilości woluminów danych, chyba że zostanie użyta opcja Szyfruj cały format. 
> - Wyłączanie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwane tylko dla woluminów danych. Nie jest obsługiwany na woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

Azure Disk Encryption można włączać i zarządzać nimi za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure) i [Azure PowerShell](/powershell/azure/new-azureps-module-az). Aby to zrobić, należy zainstalować narzędzia lokalnie i połączyć się z subskrypcją platformy Azure.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia [platformy azure 2,0](/cli/azure) to narzędzie z wierszem poleceń do zarządzania zasobami platformy Azure. Interfejs wiersza polecenia jest przeznaczony do elastycznego wykonywania zapytań dotyczących danych, obsługuje długotrwałe operacje jako procesy nieblokujące i ułatwiają wykonywanie skryptów. Możesz zainstalować ją lokalnie, wykonując kroki opisane w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

 

Aby [zalogować się do konta platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli), użyj polecenia [AZ login](/cli/azure/reference-index#az_login) .

```azurecli
az login
```

Jeśli chcesz wybrać dzierżawcę do logowania, użyj:
    
```azurecli
az login --tenant <tenant>
```

Jeśli masz wiele subskrypcji i chcesz określić konkretną, Pobierz listę subskrypcji za pomocą opcji [AZ Account List](/cli/azure/account#az-account-list) i określ polecenie [AZ Account Set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z interfejsem wiersza polecenia platformy Azure 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az) udostępnia zestaw poleceń cmdlet, które używają modelu [Azure Resource Manager](../../azure-resource-manager/management/overview.md) do zarządzania zasobami platformy Azure. Możesz użyć jej w przeglądarce z [Azure Cloud Shell](../../cloud-shell/overview.md)lub zainstalować ją na komputerze lokalnym, korzystając z instrukcji w temacie [Install the Azure PowerShell module](/powershell/azure/install-az-ps). 

Jeśli masz już zainstalowany lokalnie, upewnij się, że korzystasz z najnowszej wersji zestawu SDK Azure PowerShell, aby skonfigurować Azure Disk Encryption. Pobierz najnowszą wersję [Azure PowerShell wydania](https://github.com/Azure/azure-powershell/releases).

Aby [zalogować się do konta platformy Azure przy użyciu Azure PowerShell](/powershell/azure/authenticate-azureps), użyj polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji i chcesz określić ją, użyj polecenia cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) , aby je wyświetlić, a następnie polecenie cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Uruchomienie polecenia cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) sprawdzi, czy wybrano poprawną subskrypcję.

Aby potwierdzić, że Azure Disk Encryption polecenia cmdlet są zainstalowane, użyj polecenia cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command) :
     
```powershell
Get-command *diskencryption*
```
Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Menedżer zasobów, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. Jeśli potrzebujesz informacji o schemacie dla rozszerzenia maszyny wirtualnej, zapoznaj się z artykułem [rozszerzenia Azure Disk Encryption dla systemu Linux](../extensions/azure-disk-enc-linux.md) .

>[!IMPORTANT]
 >Należy wykonać migawkę i/lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dyskach zarządzanych poza programem i przed włączeniem Azure Disk Encryption. Migawkę dysku zarządzanego można pobrać z portalu lub za pośrednictwem [Azure Backup](../../backup/backup-azure-vms-encryption.md). Kopie zapasowe zapewniają opcję odzyskiwania w przypadku wystąpienia dowolnego nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension, aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem na maszynach wirtualnych opartych na dyskach zarządzanych, dopóki nie zostanie utworzona kopia zapasowa i ten parametr nie został określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 

Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym, instalując narzędzie wiersza polecenia [platformy Azure](/cli/azure/) i korzystając z niego. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych z systemem Linux na platformie Azure, użyj następujących poleceń interfejsu wiersza polecenia:

Użyj polecenia [AZ VM Encryption Enable](/cli/azure/vm/encryption#az_vm_encryption_show) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure.

- **Szyfruj uruchomioną maszynę wirtualną:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br>
Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej, użyj polecenia [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Wyłączenie szyfrowania jest dozwolone tylko dla woluminów danych dla maszyn wirtualnych z systemem Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure. Utwórz [migawkę](snapshot-copy-managed-disk.md) i/lub wykonaj kopię zapasową maszyny wirtualnej przy użyciu [Azure Backup](../../backup/backup-azure-vms-encryption.md) przed zaszyfrowaniem dysków. Parametr-skipVmBackup jest już określony w skryptach programu PowerShell, aby można było zaszyfrować działającą maszynę wirtualną z systemem Linux.

-  **Szyfruj uruchomioną maszynę wirtualną:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet. Grupa zasobów, maszyna wirtualna i Magazyn kluczy zostały utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Zmodyfikuj parametr-Volumetype, aby określić, które dyski są szyfrowane.

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
- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:** Może być konieczne dodanie parametru-Volumetype w przypadku szyfrowania dysków danych, a nie dysku systemu operacyjnego. 

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
    > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 
    
- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Wyłączenie szyfrowania jest dozwolone tylko dla woluminów danych dla maszyn wirtualnych z systemem Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu szablonu

Szyfrowanie dysków można włączyć na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Kliknij pozycję **Wdróż na platformie Azure** w szablonie szybkiego startu platformy Azure.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, warunki prawne i umowę. Kliknij przycisk **Utwórz** , aby włączyć szyfrowanie na istniejącej lub URUCHOMIONEJ maszynie wirtualnej.

W poniższej tabeli wymieniono Menedżer zasobów parametry szablonu dla istniejących lub uruchomionych maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny wirtualnej do uruchomienia operacji szyfrowania. |
| Nazwakluczamagazynu | Nazwa magazynu kluczy, do którego ma zostać przekazany klucz szyfrowania. Możesz uzyskać ten element za pomocą polecenia cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` lub interfejsu CLI platformy Azure `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` .|
| keyVaultResourceGroup | Nazwa grupy zasobów zawierającej Magazyn kluczy. |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używany do szyfrowania klucza szyfrowania. Ten parametr jest opcjonalny w przypadku wybrania opcji **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz pozycję **KEK** na liście rozwijanej UseExistingKek, musisz wprowadzić wartość _keyEncryptionKeyURL_ . |
| liczba woluminów | Typ woluminu, na którym jest wykonywana operacja szyfrowania. Prawidłowe wartości to _system operacyjny_, _dane_ i _wszystkie_. 
| forceUpdateTag | Przekazuj unikatową wartość, taką jak identyfikator GUID za każdym razem, gdy operacja musi zostać wymuszona. |
| location | Lokalizacja dla wszystkich zasobów. |

Aby uzyskać więcej informacji o konfigurowaniu szablonu szyfrowania dysku maszyny wirtualnej z systemem Linux, zobacz [Azure Disk Encryption dla systemu Linux](../extensions/azure-disk-enc-linux.md).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Korzystanie z funkcji EncryptFormatAll w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux

**EncryptFormatAll** parametr skraca czas szyfrowania dysków danych systemu Linux. Partycje spełniające określone kryteria zostaną sformatowane wraz z ich bieżącymi systemami plików, a następnie ponownie zainstalowane w miejscu, w którym były przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych spełniający kryteria, możesz go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia wszystkie zainstalowane wcześniej dyski zostaną sformatowane, a warstwa szyfrowania zostanie uruchomiona na pustym dysku. Po wybraniu tej opcji dysk tymczasowy dołączony do maszyny wirtualnej również będzie szyfrowany. Jeśli dysk tymczasowy jest resetowany, zostanie on zmieniony w formacie i ponownie zaszyfrowany dla maszyny wirtualnej przez rozwiązanie Azure Disk Encryption w następnej okazji. Po zaszyfrowaniu dysku zasobów [Agent Microsoft Azure systemu Linux](../extensions/agent-linux.md) nie będzie w stanie zarządzać dyskiem zasobów i włączać pliku wymiany, ale można ręcznie skonfigurować plik wymiany.

>[!WARNING]
> EncryptFormatAll nie należy używać w przypadku danych na woluminach danych maszyny wirtualnej. Dyski można wykluczać z szyfrowania, odinstalując je. Najpierw należy wypróbować EncryptFormatAll najpierw na testowej maszynie wirtualnej, zrozumieć parametr funkcji i jej implikacje przed podjęciem próby na produkcyjną maszynę wirtualną. Opcja EncryptFormatAll formatuje dysk danych i wszystkie jego dane zostaną utracone. Przed kontynuowaniem upewnij się, że dyski, które chcesz wykluczyć, zostały prawidłowo odinstalowane. </br></br>
 >Jeśli ustawiasz ten parametr podczas aktualizowania ustawień szyfrowania, może to doprowadzić do ponownego uruchomienia przed rzeczywistym szyfrowaniem. W takim przypadku należy również usunąć dysk, który nie ma być sformatowany z pliku fstab. Podobnie należy dodać partycję, która ma być szyfrowana w pliku fstab przed zainicjowaniem operacji szyfrowania. 

### <a name="encryptformatall-criteria"></a>Kryteria EncryptFormatAll
Parametr jest określany przez wszystkie partycje i szyfruje je, o ile spełniają **wszystkie** poniższe kryteria:
- Nie jest partycją główną/OS/rozruchową
- Nie jest jeszcze zaszyfrowany
- Nie jest woluminem klucz szyfrowania bloków
- Nie jest woluminem RAID
- Nie jest woluminem LVM
- Jest zainstalowany

Zaszyfruj dyski tworzące wolumin RAID lub LVM, a nie wolumin RAID lub LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Używanie parametru EncryptFormatAll z interfejsem wiersza polecenia platformy Azure
Użyj polecenia [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure.

-  **Szyfruj uruchomioną maszynę wirtualną przy użyciu EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Użyj parametru EncryptFormatAll z poleceniem cmdlet programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z parametrem EncryptFormatAll. 

**Szyfruj uruchomioną maszynę wirtualną przy użyciu EncryptFormatAll:** Na przykład poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet z parametrem EncryptFormatAll. Grupa zasobów, maszyna wirtualna i Magazyn kluczy zostały utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami.
  
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
Zalecamy instalację LVM-on-Crypt. We wszystkich poniższych przykładach Zastąp ścieżkę urządzenia i mountpoints w dowolny sposób pasujący do przypadków użycia. Tę konfigurację można wykonać w następujący sposób:

1.  Dodaj dyski danych, które będą tworzyć maszyny wirtualne.

1. Sformatuj, zainstaluj i Dodaj te dyski do pliku fstab.

1. Wybierz pozycję partycja standardowa, Utwórz partycję obejmującą cały dysk, a następnie sformatuj partycję. W tym miejscu używamy linków symbolicznych wygenerowanego przez platformę Azure. Korzystanie z programu linków symbolicznych pozwala uniknąć problemów związanych z zmianami nazw urządzeń. Aby uzyskać więcej informacji, zobacz artykuł [Rozwiązywanie problemów z nazwami urządzeń](../troubleshooting/troubleshoot-device-names-problems.md) .
    
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
    
1. Uruchom polecenie cmdlet Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z parametrem-EncryptFormatAll, aby zaszyfrować te dyski.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Jeśli chcesz użyć klucza szyfrowania klucza (KEK), Przekaż identyfikator URI KEK i ResourceID magazynu kluczy do parametrów-KeyEncryptionKeyUrl i-KeyEncryptionKeyVaultId odpowiednio:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Skonfiguruj LVM na nowych dyskach. Pamiętaj, że zaszyfrowane dyski są odblokowane po zakończeniu rozruchu maszyny wirtualnej. W związku z tym należy również opóźnić Instalowanie LVM.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nowe maszyny wirtualne utworzone z dysku VHD i kluczy szyfrowania szyfrowanych przez klienta
W tym scenariuszu można włączyć szyfrowanie przy użyciu poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. 

Aby przygotować wstępnie zaszyfrowane obrazy, które mogą być używane na platformie Azure, należy wykonać instrukcje zawarte w tym samym skrypcie usługi Azure Disk Encryption. Po utworzeniu obrazu możesz wykonać czynności opisane w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Linux](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Należy wykonać migawkę i/lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dyskach zarządzanych poza programem i przed włączeniem Azure Disk Encryption. Migawkę dysku zarządzanego można pobrać z portalu lub można użyć [Azure Backup](../../backup/backup-azure-vms-encryption.md) . Kopie zapasowe zapewniają opcję odzyskiwania w przypadku wystąpienia dowolnego nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension, aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem na maszynach wirtualnych opartych na dyskach zarządzanych, dopóki nie zostanie utworzona kopia zapasowa i ten parametr nie został określony. 
>
> Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Używanie Azure PowerShell do szyfrowania maszyn wirtualnych z wstępnie szyfrowanymi dyskami VHD 
Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym przy użyciu polecenia cmdlet programu PowerShell [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). W poniższym przykładzie przedstawiono kilka typowych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie na nowo dodanym dysku z danymi

Nowy dysk danych można dodać przy użyciu polecenia [AZ VM Disk Attach](add-disk.md)lub [za pośrednictwem Azure Portal](attach-disk-portal.md). Przed zaszyfrowaniem należy najpierw zainstalować nowo dołączony dysk danych. Musisz zażądać szyfrowania dysku danych, ponieważ dysk będzie bezużyteczny, gdy szyfrowanie jest w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu interfejsu wiersza polecenia platformy Azure

 Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu "All", parametr--type Wszystkie obejmują zarówno system operacyjny, jak i dyski danych. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "OS", należy zmienić parametr---type na "All", aby uwzględnić zarówno system operacyjny, jak i nowy dysk danych. Jeśli maszyna wirtualna została zaszyfrowana przy użyciu tylko typu woluminu "dane", może to pozostawać "dane", jak pokazano poniżej. Dodawanie i dołączanie nowego dysku z danymi do maszyny wirtualnej nie jest wystarczającym przygotowaniem do szyfrowania. Nowo dołączony dysk musi być również sformatowany i poprawnie zainstalowany na maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zainstalowany w/etc/fstab z [trwałą nazwą urządzenia blokowego](../troubleshooting/troubleshoot-device-names-problems.md).  

W przeciwieństwie do składni programu PowerShell interfejs wiersza polecenia nie wymaga od użytkownika podania unikatowej wersji sekwencji podczas włączania szyfrowania. Interfejs wiersza polecenia automatycznie generuje i używa własną unikatową wartość wersji sekwencji.

-  **Szyfruj woluminy danych uruchomionej maszyny wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Szyfruj woluminy danych uruchomionej maszyny wirtualnej przy użyciu KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu Azure PowerShell
 W przypadku szyfrowania nowego dysku dla systemu Linux przy użyciu programu PowerShell należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. Utwórz [migawkę](snapshot-copy-managed-disk.md) i/lub wykonaj kopię zapasową maszyny wirtualnej przy użyciu [Azure Backup](../../backup/backup-azure-vms-encryption.md) przed zaszyfrowaniem dysków. Parametr-skipVmBackup jest już określony w skryptach programu PowerShell w celu zaszyfrowania nowo dodanego dysku z danymi.
 

-  **Szyfruj woluminy danych uruchomionej maszyny wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet. Grupa zasobów, maszyna wirtualna i Magazyn kluczy powinny być już utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Dopuszczalne wartości dla parametru-Volumetype to wszystkie, system operacyjny i dane. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "OS" lub "All", należy zmienić parametr-Volumetype na "All", aby uwzględnić zarówno system operacyjny, jak i nowy dysk danych.

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
- **Szyfruj woluminy danych uruchomionej maszyny wirtualnej przy użyciu KEK:** Dopuszczalne wartości dla parametru-Volumetype to wszystkie, system operacyjny i dane. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "OS" lub "All", należy zmienić parametr-Volumetype na wartość All, aby uwzględnić zarówno system operacyjny, jak i nowy dysk danych.

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
    > Składnia wartości parametru dysk-szyfrowanie-kluczy jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[KVresource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 


## <a name="disable-encryption-for-linux-vms"></a>Wyłącz szyfrowanie dla maszyn wirtualnych z systemem Linux
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Azure Disk Encryption nie działa w następujących scenariuszach, funkcjach i technologiach systemu Linux:

- Szyfrowanie maszyny wirtualnej warstwy podstawowej lub maszyn wirtualnych utworzonych za pomocą metody tworzenia klasycznej maszyny wirtualnej.
- Wyłączanie szyfrowania na dysku systemu operacyjnego lub dysku danych maszyny wirtualnej z systemem Linux podczas szyfrowania dysku systemu operacyjnego.
- Szyfrowanie dysku systemu operacyjnego dla zestawów skalowania maszyn wirtualnych z systemem Linux.
- Szyfrowanie obrazów niestandardowych na maszynach wirtualnych z systemem Linux.
- Integracja z lokalnym systemem zarządzania kluczami.
- Azure Files (udostępniony system plików).
- Sieciowy system plików (NFS).
- Woluminy dynamiczne.
- Tymczasowe dyski systemu operacyjnego.
- Szyfrowanie współużytkowanych/rozproszonych systemów plików, takich jak (ale nie ograniczone do): DFS, GFS, DRDB i CephFS.
- Przeniesienie zaszyfrowanej maszyny wirtualnej na inną subskrypcję lub region.
- Tworzenie obrazu lub migawki zaszyfrowanej maszyny wirtualnej i używanie jej do wdrażania dodatkowych maszyn wirtualnych.
- Zrzut awaryjny jądra (kdump).
- Oracle ACFS (system plików klastra ASM).
- Maszyny wirtualne Gen2 (zobacz: [Obsługa maszyn wirtualnych 2. generacji na platformie Azure](../generation-2.md#generation-1-vs-generation-2-capabilities)).
- Dyski interfejsu NVMe maszyn wirtualnych z serii Lsv2 (zobacz: [Lsv2-Series](../lsv2-series.md)).
- Maszyna wirtualna z "zagnieżdżonych punktów instalacji"; oznacza to, że wiele punktów instalacji w jednej ścieżce (na przykład "/1stmountpoint/Data/2stmountpoint").
- Maszyna wirtualna z dyskiem danych zainstalowanym na górze folderu systemu operacyjnego.
- Maszyny wirtualne serii M z dyskami akcelerator zapisu.
- Zastosowanie elementu ADE do maszyny wirtualnej z dyskami zaszyfrowanymi za pomocą [szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez klienta](../disk-encryption.md) (SSE + CMK). Stosowanie instrukcji SSE i CMK do dysku danych na maszynie wirtualnej zaszyfrowanej przy użyciu programu ADE jest również nieobsługiwanym scenariuszem.
- Migrowanie maszyny wirtualnej, która jest zaszyfrowana przy użyciu programu ADE lub **kiedykolwiek** została zaszyfrowana za pomocą programu ADE, do [szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez klienta](../disk-encryption.md).
- [Rozmiary maszyn wirtualnych platformy Azure bez lokalnego dysku tymczasowego](../azure-vms-no-temp-disk.md); w Dv4, Dsv4, Ev4 i Esv4.
- Szyfrowanie maszyn wirtualnych w klastrach trybu failover.

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
- [Przykładowe skrypty usługi Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
