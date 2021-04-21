---
title: Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Windows
description: Ten artykuł zawiera instrukcje dotyczące włączania szyfrowania Microsoft Azure dla maszyn wirtualnych z systemem Windows dla różnych scenariuszy
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2d8173e8b79e8696c2a3e4a7ea722b2947b1aee6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776815"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Windows

Azure Disk Encryption maszyn wirtualnych z systemem Windows używa funkcji BitLocker systemu Windows w celu zapewnienia pełnego szyfrowania dysku systemu operacyjnego i dysku danych. Ponadto zapewnia szyfrowanie dysku tymczasowego, gdy parametr VolumeType ma wartość Wszystkie.

Azure Disk Encryption jest [zintegrowana z Azure Key Vault,](disk-encryption-key-vault.md) aby ułatwić kontrolowanie kluczy szyfrowania dysków i wpisów tajnych oraz zarządzanie nimi. Aby uzyskać omówienie usługi, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows.](disk-encryption-overview.md)

Szyfrowanie dysków można zastosować tylko do maszyn wirtualnych o [obsługiwanych rozmiarach maszyn wirtualnych i systemach operacyjnych.](disk-encryption-overview.md#supported-vms-and-operating-systems) Należy również spełnić następujące wymagania wstępne:

- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [zasady grupy wymagań](disk-encryption-overview.md#group-policy-requirements)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Jeśli wcześniej zaszyfrowano maszynę wirtualną Azure Disk Encryption z usługą Azure AD, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Aby [uzyskać Azure Disk Encryption, zobacz temat Azure Disk Encryption z usługą Azure AD (poprzednia](disk-encryption-overview-aad.md) wersja). 
>
> - Przed [zaszyfrowaniem dysków](snapshot-copy-managed-disk.md) należy utworzyć migawkę i/lub utworzyć kopię zapasową. Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa w przypadku wystąpienia nieoczekiwanego błędu podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopii zapasowej przed rozpoczęciem szyfrowania. Po kopii zapasowej można użyć polecenia [cmdlet Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) aby zaszyfrować dyski zarządzane, określając parametr -skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn wirtualnych, zobacz Temat Tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn [wirtualnych platformy Azure.](../../backup/backup-azure-vms-encryption.md) 
>
> - Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej.

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Windows
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Resource Manager, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. Jeśli potrzebujesz informacji o schemacie rozszerzenia maszyny wirtualnej, zobacz [Azure Disk Encryption dla rozszerzenia systemu Windows.](../extensions/azure-disk-enc-windows.md)

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Włączanie szyfrowania istniejących lub uruchomionych maszyn wirtualnych przy użyciu Azure PowerShell 
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure. 

-  **Szyfrowanie uruchomionej maszyny wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension cmdlet. Grupa zasobów, maszyna wirtualna i magazyn kluczy powinny już zostać utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault swoimi wartościami.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza szyfrowania klucza:** 

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Składnia wartości parametru disk-encryption-keyvault to pełny ciąg identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu-kluczy]</br> Składnia dla wartości parametru key-encryption-key jest pełnym identyfikatorem URI klucza szyfrowania kluczy, jak w: https://[nazwa magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Wyłączenie szyfrowania dysku danych na maszynie wirtualnej z systemem Windows, gdy zaszyfrowano zarówno dysk systemu operacyjnego, jak i dysk danych, nie działa zgodnie z oczekiwaniami. Zamiast tego wyłącz szyfrowanie na wszystkich dyskach.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Włączanie szyfrowania na istniejących lub uruchomionych maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj polecenia [az vm encryption enable,](/cli/azure/vm/encryption#az_vm_encryption_enable) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

- **Szyfrowanie uruchomionej maszyny wirtualnej:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza szyfrowania klucza:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Składnia wartości parametru disk-encryption-keyvault to pełny ciąg identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu-kluczy] </br> Składnia dla wartości parametru key-encryption-key jest pełnym identyfikatorem URI klucza szyfrowania kluczy, jak w: https://[nazwa magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj [polecenia az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj [polecenia az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) Wyłączenie szyfrowania dysku danych na maszynie wirtualnej z systemem Windows, gdy zaszyfrowano zarówno dysk systemu operacyjnego, jak i dysk danych, nie działa zgodnie z oczekiwaniami. Zamiast tego wyłącz szyfrowanie na wszystkich dyskach.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Korzystanie z Resource Manager szablonu

Szyfrowanie dysków można włączyć na istniejących lub działających maszynach wirtualnych IaaS z systemem Windows na platformie Azure przy użyciu szablonu Resource Manager do szyfrowania uruchomionej [maszyny wirtualnej z systemem Windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)


1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wd wdrażaj na platformie Azure.**

2. Wybierz subskrypcję, grupę zasobów, lokalizację, ustawienia, postanowienia prawne i umowę. Kliknij **pozycję Kup,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono Resource Manager szablonu dla istniejących lub uruchomionych maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny wirtualnej do uruchomienia operacji szyfrowania. |
| keyVaultName | Nazwa magazynu kluczy, do który ma zostać przekazany klucz funkcji BitLocker. Możesz go uzyskać za pomocą polecenia cmdlet lub `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` polecenia interfejsu wiersza polecenia platformy Azure `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nazwa grupy zasobów zawierającej magazyn kluczy|
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza w formacie https:// &lt; keyvault-name &gt; vault.azure.net/key/ &lt; key-name. &gt; Jeśli nie chcesz używać KEK, pozostaw to pole puste. |
| volumeType | Typ woluminu, na który jest wykonywana operacja szyfrowania. Prawidłowe wartości to _OS,_ _Data_ i _All._ 
| forceUpdateTag | Za każdym razem, gdy trzeba wymusić uruchomienie operacji, przekaż unikatową wartość, na przykład identyfikator GUID. |
| resizeOSDisk | Należy zmienić rozmiar partycji systemu operacyjnego tak, aby zajmowała pełny wirtualny dysk twardy systemu operacyjnego przed podziałem woluminu systemowego. |
| location | Lokalizacja dla wszystkich zasobów. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Włączanie szyfrowania dysków NVMe dla maszyn wirtualnych Lsv2

W tym scenariuszu opisano włączanie Azure Disk Encryption dyskach NVMe dla maszyn wirtualnych serii Lsv2.  Seria Lsv2 zawiera lokalny magazyn NVMe. Lokalne dyski NVMe są tymczasowe, a dane zostaną utracone na tych dyskach w przypadku zatrzymania maszyny wirtualnej/cof jego alokacji (zobacz: [seria Lsv2).](../lsv2-series.md)

Aby włączyć szyfrowanie dysków NVMe:

1. Zaimicjuj dyski NVMe i utwórz woluminy NTFS.
1. Włącz szyfrowanie na maszynie wirtualnej z parametrem VolumeType ustawionym na Wszystkie. Umożliwi to szyfrowanie wszystkich dysków systemu operacyjnego i danych, w tym woluminów z dyskami NVMe. Aby uzyskać więcej informacji, zobacz Włączanie szyfrowania na istniejącej lub [uruchomionej maszynie wirtualnej z systemem Windows.](#enable-encryption-on-an-existing-or-running-windows-vm)

Szyfrowanie zostanie utrwalone na dyskach NVMe w następujących scenariuszach:
- Ponowne uruchamianie maszyny wirtualnej
- Odtwarzanie zestawu skalowania maszyn wirtualnych z obrazu
- Zamień system operacyjny

Dyski NVMe zostaną niezainicjowane w następujących scenariuszach:

- Uruchamianie maszyny wirtualnej po coklokacji
- Service healing (Koryguje usługę
- Backup

W tych scenariuszach dyski NVMe muszą zostać zainicjowane po uruchamianiu maszyny wirtualnej. Aby włączyć szyfrowanie na dyskach NVMe, uruchom polecenie , aby Azure Disk Encryption ponownie po zainicjowaniu dysków NVMe.

Oprócz scenariuszy wymienionych w sekcji [Nieobsługiwane](#unsupported-scenarios) scenariusze szyfrowanie dysków NVMe nie jest obsługiwane w przypadku:

- Maszyny wirtualne zaszyfrowane za pomocą Azure Disk Encryption za pomocą aAD (poprzednia wersja)
- Dyski NVMe z miejscami do magazynowania
- Azure Site Recovery jednostki SKU z dyskami NVMe (zobacz Tabela obsługi odzyskiwania po awarii maszyny wirtualnej platformy Azure między regionami platformy [Azure: Replikowane maszyny — magazyn).](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nowe maszyny wirtualne IaaS utworzone na podstawie zaszyfrowanego przez klienta wirtualnego dysku twardego i kluczy szyfrowania

W tym scenariuszu można utworzyć nową maszynę wirtualną ze wstępnie zaszyfrowanego wirtualnego dysku twardego i skojarzonych kluczy szyfrowania przy użyciu poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. 

Skorzystaj z instrukcji z [tematu Prepare a pre-encrypted Windows VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)(Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Windows). Po utworzeniu obrazu możesz wykonać kroki opisane w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Szyfrowanie maszyn wirtualnych za pomocą wstępnie zaszyfrowanych dysków VHD za pomocą Azure PowerShell
Szyfrowanie dysków na zaszyfrowanym dysku VHD można włączyć za pomocą polecenia cmdlet programu PowerShell [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) Poniższy przykład zawiera kilka typowych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włączanie szyfrowania na nowo dodanym dysku danych
Nowy dysk [można dodać do maszyny wirtualnej z](attach-disk-ps.md)systemem Windows przy użyciu programu PowerShell lub za [pośrednictwem Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włączanie szyfrowania na nowo dodanym dysku przy użyciu Azure PowerShell
 W przypadku szyfrowania nowego dysku dla maszyn wirtualnych z systemem Windows przy użyciu programu PowerShell należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. W niektórych przypadkach nowo dodany dysk danych może zostać automatycznie zaszyfrowany przez rozszerzenie Azure Disk Encryption danych. Automatyczne szyfrowanie zwykle występuje, gdy maszyna wirtualna jest uruchamiana ponownie po przywłoceniu nowego dysku w trybie online. Jest to zwykle spowodowane tym, że dla typu woluminu określono wartość "Wszystkie", gdy wcześniej na maszynie wirtualnej było stosowane szyfrowanie dysków. Jeśli automatyczne szyfrowanie odbywa się na nowo dodanym dysku danych, zalecamy uruchomienie polecenia cmdlet Set-AzVmDiskEncryptionExtension ponownie z nową wersją sekwencji. Jeśli nowy dysk danych jest automatycznie szyfrowany i nie chcesz go szyfrować, najpierw odszyfruj wszystkie dyski, a następnie ponownie zaszyfruj za pomocą nowej wersji sekwencji, określając system operacyjny dla typu woluminu. 
  
 

-  **Szyfrowanie uruchomionej maszyny wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension cmdlet. Grupa zasobów, maszyna wirtualna i magazyn kluczy powinny już zostać utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault swoimi wartościami. W tym przykładzie użyto wartości "All" dla parametru -VolumeType, który zawiera woluminy systemu operacyjnego i danych. Jeśli chcesz zaszyfrować tylko wolumin systemu operacyjnego, użyj wartości "OS" dla parametru -VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza szyfrowania klucza:** W tym przykładzie użyto wartości "All" dla parametru -VolumeType, który zawiera woluminy systemu operacyjnego i danych. Jeśli chcesz zaszyfrować tylko wolumin systemu operacyjnego, użyj wartości "OS" dla parametru -VolumeType.

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Składnia wartości parametru disk-encryption-keyvault to pełny ciąg identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu-kluczy]</br> Składnia dla wartości parametru key-encryption-key jest pełnym identyfikatorem URI klucza szyfrowania kluczy, jak w: https://[nazwa magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włączanie szyfrowania na nowo dodanym dysku przy użyciu interfejsu wiersza polecenia platformy Azure
 Polecenie interfejsu wiersza polecenia platformy Azure automatycznie udostępni nową wersję sekwencji po uruchomieniu polecenia w celu włączenia szyfrowania. W przykładzie użyto wartości "All" dla parametru typu woluminu. W przypadku szyfrowania dysku systemu operacyjnego może być konieczne zmiana parametru typu woluminu na system operacyjny. W przeciwieństwie do składni programu PowerShell interfejs wiersza polecenia nie wymaga od użytkownika podania unikatowej wersji sekwencji podczas włączania szyfrowania. Interfejs wiersza polecenia automatycznie generuje i używa własnej unikatowej wartości wersji sekwencji.   

-  **Szyfrowanie uruchomionej maszyny wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza szyfrowania klucza:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Wyłączanie szyfrowania
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Azure Disk Encryption nie działa w przypadku następujących scenariuszy, funkcji i technologii:

- Szyfrowanie podstawowej warstwy maszyny wirtualnej lub maszyn wirtualnych utworzonych za pomocą klasycznej metody tworzenia maszyny wirtualnej.
- Szyfrowanie maszyn wirtualnych skonfigurowanych przy użyciu oprogramowania opartego na systemach RAID.
- Szyfrowanie maszyn wirtualnych skonfigurowanych przy użyciu Bezpośrednie miejsca do magazynowania (S2D) lub systemu Windows Server w wersjach starszych niż 2016 skonfigurowanych przy użyciu funkcji Miejsca do magazynowania systemu Windows.
- Integracja z lokalnym systemem zarządzania kluczami.
- Azure Files (udostępniony system plików).
- Sieciowy system plików (NFS).
- Woluminy dynamiczne.
- Kontenery systemu Windows Server, które tworzą woluminy dynamiczne dla każdego kontenera.
- Efemeralne dyski systemu operacyjnego.
- Szyfrowanie udostępnionych/rozproszonych systemów plików, takich jak (ale nie tylko) DFS, GFS, DRDB i CephFS.
- Przenoszenie zaszyfrowanej maszyny wirtualnej do innej subskrypcji lub regionu.
- Tworzenie obrazu lub migawki zaszyfrowanej maszyny wirtualnej i używanie jej do wdrażania dodatkowych maszyn wirtualnych.
- Maszyny wirtualne serii M z akcelerator zapisu dyskami.
- Stosowanie usługi ADE do maszyny [](../disk-encryption.md) wirtualnej z dyskami zaszyfrowanymi za pomocą szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta (SSE + CMK). Zastosowanie szyfrowania SSE + CMK do dysku danych na maszynie wirtualnej zaszyfrowanej za pomocą usługi ADE również nie jest obsługiwanym scenariuszem.
- Migrowanie maszyny wirtualnej zaszyfrowanej za  pomocą usługi ADE lub kiedykolwiek zaszyfrowanej za pomocą usługi ADE do szyfrowania po stronie serwera przy użyciu [kluczy zarządzanych przez klienta.](../disk-encryption.md)
- Szyfrowanie maszyn wirtualnych w klastrach trybu failover.
- Szyfrowanie [dysków w warstwie Ultra platformy Azure.](../disks-enable-ultra-ssd.md)

## <a name="next-steps"></a>Następne kroki

- [Azure Disk Encryption omówienie](disk-encryption-overview.md)
- [Przykładowe skrypty usługi Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)