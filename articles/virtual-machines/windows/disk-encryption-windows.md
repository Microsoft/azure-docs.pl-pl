---
title: Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Windows
description: Ten artykuł zawiera instrukcje dotyczące włączania szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych z systemem Windows dla różnych scenariuszy
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: deb2860c8d027a0a258c4a962fe33d6f516e10dc
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085647"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Windows

Szyfrowanie dysków platformy Azure dla maszyn wirtualnych systemu Windows (VMs) używa funkcji funkcji bitlocker systemu Windows, aby zapewnić pełne szyfrowanie dysku systemu operacyjnego i dysku danych. Ponadto zapewnia szyfrowanie dysku zasobów efemeryczny, gdy parametr VolumeType jest wszystko.

Usługa Azure Disk Encryption jest [zintegrowana z usługą Azure Key Vault,](disk-encryption-key-vault.md) która ułatwia kontrolowanie kluczy szyfrowania dysku i zarządzanie nimi. Aby zapoznać się z omówieniem usługi, zobacz [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Windows](disk-encryption-overview.md).

Szyfrowanie dysku można stosować tylko do maszyn wirtualnych [obsługiwanych rozmiarów maszyn wirtualnych i systemów operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems). Należy również spełnić następujące wymagania wstępne:

- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące zasad grupy](disk-encryption-overview.md#group-policy-requirements)
- [Wymagania dotyczące przechowywania kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Jeśli wcześniej używano szyfrowania dysków platformy Azure z usługą Azure AD do szyfrowania maszyny wirtualnej, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Szczegółowe informacje można znaleźć [w witrynie Azure Disk Encryption with Azure AD (previous release).](disk-encryption-overview-aad.md) 
>
> - Należy [wykonać migawkę](snapshot-copy-managed-disk.md) i/lub utworzyć kopię zapasową, zanim dyski zostaną zaszyfrowane. Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa, jeśli wystąpi nieoczekiwany błąd podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć [polecenia cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) do szyfrowania dysków zarządzanych, określając parametr -skipVmBackup. Aby uzyskać więcej informacji na temat sposobu 30-100 6000000000000000000000000000000000000000000000000000000000000000000 [Back up and restore encrypted Azure VM](../../backup/backup-azure-vms-encryption.md) 
>
> - Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej.

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i łączenie się z platformą Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie Wirtualnej systemu Windows
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Menedżera zasobów, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. Jeśli potrzebujesz informacji o schemacie rozszerzenia maszyny wirtualnej, zobacz artykuł [rozszerzenia Azure Disk Encryption for Windows.](../extensions/azure-disk-enc-windows.md)

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Włączanie szyfrowania na istniejących lub uruchomionych maszynach wirtualnych za pomocą programu Azure PowerShell 
Polecenie cmdlet [Set-AzVMMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) umożliwia szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure. 

-  **Szyfrowanie uruchomionej maszyny Wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna i magazyn kluczy powinny być już utworzone jako wymagania wstępne. Zastąp MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami.

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
- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu funkcji KEK:** 

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
   > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny Wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysku:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Wyłączenie szyfrowania dysku danych na maszynie wirtualnej z systemem Windows, gdy zaszyfrowano zarówno dysk systemu operacyjnego, jak i dysk danych, nie działa zgodnie z oczekiwaniami. Zamiast tego wyłącz szyfrowanie na wszystkich dyskach.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Włączanie szyfrowania na istniejących lub uruchomionych maszynach wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
Użyj polecenia [włączania szyfrowania az vm,](/cli/azure/vm/encryption#az-vm-encryption-enable) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

- **Szyfrowanie uruchomionej maszyny Wirtualnej:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu funkcji KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny Wirtualnej IaaS, użyj polecenia [az vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [az vm encryption disable.](/cli/azure/vm/encryption#az-vm-encryption-disable) Wyłączenie szyfrowania dysku danych na maszynie wirtualnej z systemem Windows, gdy zaszyfrowano zarówno dysk systemu operacyjnego, jak i dysk danych, nie działa zgodnie z oczekiwaniami. Zamiast tego wyłącz szyfrowanie na wszystkich dyskach.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Korzystanie z szablonu Menedżera zasobów

Szyfrowanie dysku można włączyć na istniejących lub uruchomionych maszynach wirtualnych systemu IaaS z systemem Windows na platformie Azure przy użyciu [szablonu Menedżera zasobów do szyfrowania uruchomionej maszyny Wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. W szablonie szybki start platformy Azure kliknij pozycję **Wdrażanie na platformie Azure**.

2. Wybierz subskrypcję, grupę zasobów, lokalizację, ustawienia, warunki prawne i umowę. Kliknij **przycisk Zakup,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono parametry szablonu Menedżera zasobów dla istniejących lub uruchomionych maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny Wirtualnej do uruchomienia operacji szyfrowania. |
| keyVaultName (nazwa klucza) | Nazwa magazynu kluczy, do których należy przekazać klucz funkcji BitLocker. Można go uzyskać za pomocą `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` polecenia cmdlet lub interfejsu wiersza polecenia platformy Azure`az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nazwa grupy zasobów zawierającej magazyn kluczy|
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza w&lt;formacie https://&gt;nazwa keyvault&lt;.vault.azure.net/key/&gt;nazwa klucza . Jeśli nie chcesz używać KEK, pozostaw to pole puste. |
| volumeType | Typ woluminu, na który wykonywana jest operacja szyfrowania. Prawidłowe wartości _to OS_, _Dane_i _Wszystkie_. 
| forceUpdateTag (ForceUpdateTag) | Przekaż w unikatowej wartości, takich jak identyfikator GUID za każdym razem, gdy operacja musi być wymuszać. |
| resizeOSDisk | Jeśli partycja systemu operacyjnego zostanie przesunięta w celu zajęcia pełnego dysku OS VHD przed podziałem woluminu systemu. |
| location | Lokalizacja dla wszystkich zasobów. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nowe maszyny wirtualne IaaS utworzone na podstawie zaszyfrowanych przez klienta kluczy VHD i szyfrowania

W tym scenariuszu można utworzyć nową maszynę wirtualną ze wstępnie zaszyfrowanej dysku twardego wirtualnego i skojarzonych kluczy szyfrowania przy użyciu poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. 

Użyj instrukcji w przygotowanie wstępnie zaszyfrowanego dysku [VHD systemu Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Po utworzeniu obrazu można użyć kroków w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Szyfrowanie maszyn wirtualnych za pomocą wstępnie zaszyfrowanych dysków wirtualnych za pomocą programu Azure PowerShell
Szyfrowanie dysku można włączyć na zaszyfrowanej dysku VHD za pomocą polecenia cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)programu PowerShell . Poniższy przykład zawiera kilka typowych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włączanie szyfrowania na nowo dodanym dysku danych
Nowy dysk można dodać do maszyny Wirtualnej systemu Windows za pomocą programu [PowerShell](attach-disk-ps.md)lub [za pośrednictwem portalu Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włączanie szyfrowania na nowo dodanym dysku za pomocą programu Azure PowerShell
 Podczas używania programu Powershell do szyfrowania nowego dysku dla maszyn wirtualnych systemu Windows należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. W niektórych przypadkach nowo dodany dysk danych może być szyfrowany automatycznie przez rozszerzenie szyfrowania dysków azure. Automatyczne szyfrowanie zwykle występuje, gdy maszyna wirtualna uruchamia się ponownie po przejściu nowego dysku do trybu online. Jest to zazwyczaj spowodowane, ponieważ "Wszystkie" został określony dla typu woluminu, gdy szyfrowanie dysku wcześniej uruchomiony na maszynie wirtualnej. Jeśli automatyczne szyfrowanie występuje na nowo dodanym dysku danych, zalecamy ponowne uruchomienie polecenia cmdlet Set-AzVmDiskEncryptionExtension z nową wersją sekwencji. Jeśli nowy dysk danych jest automatycznie szyfrowany i nie chcesz być szyfrowany, najpierw odszyfruj wszystkie dyski, a następnie ponownie zaszyfruj za pomocą nowej wersji sekwencji określającej system operacyjny dla typu woluminu. 
  
 

-  **Szyfrowanie uruchomionej maszyny Wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna i magazyn kluczy powinny być już utworzone jako wymagania wstępne. Zastąp MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. W tym przykładzie użyto "Wszystkie" dla parametru -VolumeType, który zawiera zarówno woluminy systemu operacyjnego, jak i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru -VolumeType. 

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
- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu funkcji KEK:** W tym przykładzie użyto "Wszystkie" dla parametru -VolumeType, który zawiera zarówno woluminy systemu operacyjnego, jak i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru -VolumeType.

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
    > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włączanie szyfrowania na nowo dodanym dysku za pomocą interfejsu wiersza polecenia platformy Azure
 Polecenie interfejsu wiersza polecenia platformy Azure automatycznie zapewni nową wersję sekwencji po uruchomieniu polecenia, aby włączyć szyfrowanie. W przykładzie użyto "Wszystkie" dla parametru typu woluminu. Może być konieczna zmiana parametru typu woluminu na system operacyjny, jeśli szyfrujesz tylko dysk systemu operacyjnego. W przeciwieństwie do składni programu Powershell interfejs wiersza polecenia nie wymaga od użytkownika podania unikatowej wersji sekwencji podczas włączania szyfrowania. Wierszka polecenia automatycznie generuje i używa własnej unikatowej wartości wersji sekwencji.   

-  **Szyfrowanie uruchomionej maszyny Wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu funkcji KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Wyłącz szyfrowanie
Szyfrowanie można wyłączyć przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Usługi Resource Manager. Wyłączenie szyfrowania dysku danych na maszynie wirtualnej z systemem Windows, gdy zaszyfrowano zarówno dysk systemu operacyjnego, jak i dysk danych, nie działa zgodnie z oczekiwaniami. Zamiast tego wyłącz szyfrowanie na wszystkich dyskach.

- **Wyłącz szyfrowanie dysku za pomocą programu Azure PowerShell:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj polecenia [az vm encryption disable.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Wyłącz szyfrowanie za pomocą szablonu Menedżera zasobów:** 

    1. Kliknij **pozycję Wdrażanie na platformie Azure** z [przycisku Wyłącz szyfrowanie dysku w uruchomionym szablonie maszyny Wirtualnej systemu Windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, typ woluminu, warunki prawne i umowę.
    3.  Kliknij **przycisk Zakup,** aby wyłączyć szyfrowanie dysku na uruchomionej maszynie Wirtualnej systemu Windows. 

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Szyfrowanie dysków platformy Azure nie działa w następujących scenariuszach, funkcjach i technologii:

- Szyfrowanie maszyn wirtualnych warstwy podstawowej lub maszyn wirtualnych utworzonych za pomocą klasycznej metody tworzenia maszyn wirtualnych.
- Szyfrowanie maszyn wirtualnych skonfigurowanych za pomocą oprogramowania opartych na systemach RAID.
- Szyfrowanie maszyn wirtualnych skonfigurowanych przy pomocą bezpośrednich miejsc do magazynowania (S2D) lub wersji systemu Windows Server przed 2016 r. skonfigurowanych przy pracy z miejscami do magazynowania systemu Windows.
- Integracja z lokalnym systemem zarządzania kluczami.
- Usługi Azure Files (udostępniony system plików).
- sieciowego systemu plików (NFS).
- Woluminy dynamiczne.
- Kontenery systemu Windows Server, które tworzą woluminy dynamiczne dla każdego kontenera.
- Efemeryczne dyski systemu operacyjnego.
- Szyfrowanie systemów plików udostępnionych/rozproszonych, takich jak (ale nie ograniczając się do) DFS, GFS, DRDB i CephFS.
- Przenoszenie zaszyfrowanych maszyn wirtualnych do innej subskrypcji.
- Maszyny wirtualne gen2 (zobacz: [Obsługa maszyn wirtualnych generacji 2 na platformie Azure)](generation-2.md#generation-1-vs-generation-2-capabilities)
- Maszyny wirtualne serii Lsv2 (patrz: [seria Lsv2)](../lsv2-series.md)

## <a name="next-steps"></a>Następne kroki

- [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md)
- [Przykładowe skrypty usługi Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
