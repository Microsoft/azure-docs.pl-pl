---
title: Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Windows
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Windows w różnych scenariuszach
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: a01d5f48ca3b10f4c49ee621398ae87392dc34a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493462"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Windows

Azure Disk Encryption dla maszyn wirtualnych z systemem Windows używa funkcji BitLocker systemu Windows w celu zapewnienia pełnego szyfrowania dysków dysku systemu operacyjnego i dysku z danymi. Ponadto zapewnia szyfrowanie dysku tymczasowego, gdy parametr Volumetype ma wartość ALL.

Azure Disk Encryption jest [zintegrowana z Azure Key Vault](disk-encryption-key-vault.md) , aby pomóc Ci kontrolować klucze szyfrowania dysków i wpisy tajne oraz zarządzać nimi. Aby zapoznać się z omówieniem usługi, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](disk-encryption-overview.md).

Szyfrowanie dysków można stosować tylko do maszyn wirtualnych [obsługiwanych rozmiarów maszyn wirtualnych i systemów operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems). Musisz również spełniać następujące wymagania wstępne:

- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania zasady grupy](disk-encryption-overview.md#group-policy-requirements)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Jeśli usługa Azure AD została wcześniej Azure Disk Encryption użyta w celu zaszyfrowania maszyny wirtualnej, należy użyć tej opcji, aby zaszyfrować maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [Azure Disk Encryption w usłudze Azure AD (w poprzedniej wersji)](disk-encryption-overview-aad.md) . 
>
> - Przed zaszyfrowaniem dysków należy [wykonać migawkę](snapshot-copy-managed-disk.md) i/lub utworzyć kopię zapasową. Kopie zapasowe zapewniają opcję odzyskiwania, jeśli podczas szyfrowania wystąpi nieoczekiwany błąd. Maszyny wirtualne z dyskami zarządzanymi wymagają utworzenia kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć [polecenia cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania szyfrowanych maszyn wirtualnych, zobacz [wykonywanie kopii zapasowych i przywracanie zaszyfrowanej maszyny wirtualnej platformy Azure](../../backup/backup-azure-vms-encryption.md). 
>
> - Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej.

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Windows
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Menedżer zasobów, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. Jeśli potrzebujesz informacji o schemacie dla rozszerzenia maszyny wirtualnej, zapoznaj się z artykułem [rozszerzenia Azure Disk Encryption dla systemu Windows](../extensions/azure-disk-enc-windows.md) .

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Włącz szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych za pomocą Azure PowerShell 
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby włączyć szyfrowanie na działającej maszynie wirtualnej IaaS na platformie Azure. 

-  **Szyfruj uruchomioną maszynę wirtualną:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet. Grupa zasobów, maszyna wirtualna i Magazyn kluczy powinny być już utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami.

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
- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:** 

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
   > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Wyłączenie szyfrowania dysku danych na maszynie wirtualnej z systemem Windows, gdy zaszyfrowano zarówno dysk systemu operacyjnego, jak i dysk danych, nie działa zgodnie z oczekiwaniami. Zamiast tego należy wyłączyć szyfrowanie na wszystkich dyskach.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Włączanie szyfrowania istniejących lub uruchomionych maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj polecenia [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

- **Szyfruj uruchomioną maszynę wirtualną:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy] </br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Wyłączenie szyfrowania dysku danych na maszynie wirtualnej z systemem Windows, gdy zaszyfrowano zarówno dysk systemu operacyjnego, jak i dysk danych, nie działa zgodnie z oczekiwaniami. Zamiast tego należy wyłączyć szyfrowanie na wszystkich dyskach.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Korzystanie z szablonu Menedżer zasobów

Szyfrowanie dysków można włączyć na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS na platformie Azure przy użyciu [szablonu Menedżer zasobów, aby zaszyfrować działającą maszynę wirtualną](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)z systemem Windows.


1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wdróż na platformie Azure**.

2. Wybierz subskrypcję, grupę zasobów, lokalizację, ustawienia, warunki prawne i umowę. Kliknij przycisk **Kup** , aby włączyć szyfrowanie na istniejącej lub URUCHOMIONEJ maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono Menedżer zasobów parametry szablonu dla istniejących lub uruchomionych maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny wirtualnej do uruchomienia operacji szyfrowania. |
| Nazwakluczamagazynu | Nazwa magazynu kluczy, do którego należy przekazać klucz funkcji BitLocker. Można to zrobić za pomocą polecenia cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` lub interfejsu CLI platformy Azure `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nazwa grupy zasobów zawierającej Magazyn kluczy|
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza w formacie https://nazwa magazynu kluczy. &lt; &gt; Vault.Azure.NET/Key/ &lt; &gt; . Jeśli nie chcesz używać elementu KEK, pozostaw to pole puste. |
| liczba woluminów | Typ woluminu, na którym jest wykonywana operacja szyfrowania. Prawidłowe wartości to _system operacyjny_, _dane_ i _wszystkie_. 
| forceUpdateTag | Przekazuj unikatową wartość, taką jak identyfikator GUID za każdym razem, gdy operacja musi zostać wymuszona. |
| resizeOSDisk | Należy zmienić rozmiar partycji systemu operacyjnego w celu zajmowania całego wirtualnego dysku twardego systemu operacyjnego przed podziałem woluminu systemowego. |
| location | Lokalizacja dla wszystkich zasobów. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Włącz szyfrowanie na dyskach interfejsu NVMe dla maszyn wirtualnych Lsv2

W tym scenariuszu opisano Włączanie Azure Disk Encryption na dyskach interfejsu NVMe dla maszyn wirtualnych z serii Lsv2.  Funkcja serii Lsv2 lokalnego magazynu interfejsu NVMe. Lokalne dyski interfejsu NVMe są tymczasowe i dane zostaną utracone na tych dyskach, jeśli zatrzymasz/zwolnisz maszynę wirtualną (zobacz: [Lsv2-Series](../lsv2-series.md)).

Aby włączyć szyfrowanie na dyskach interfejsu NVMe:

1. Zainicjuj dyski interfejsu NVMe i Utwórz woluminy NTFS.
1. Włącz szyfrowanie na maszynie wirtualnej z parametrem Volumetype ustawionym na wartość ALL. Spowoduje to włączenie szyfrowania dla wszystkich dysków systemu operacyjnego i danych, w tym woluminy obsługiwane przez dyski interfejsu NVMe. Aby uzyskać więcej informacji, zobacz [Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Windows](#enable-encryption-on-an-existing-or-running-windows-vm).

Szyfrowanie będzie przechowywane na dyskach interfejsu NVMe w następujących scenariuszach:
- Ponowne uruchomienie maszyny wirtualnej
- Reobraz zestawu skalowania maszyn wirtualnych
- Wymiana systemu operacyjnego

Dyski interfejsu NVMe zostaną niezainicjowane w następujących scenariuszach:

- Uruchom maszynę wirtualną po cofnięciu alokacji
- Naprawy usługi
- Backup

W tych scenariuszach dyski interfejsu NVMe muszą zostać zainicjowane po uruchomieniu maszyny wirtualnej. Aby włączyć szyfrowanie na dyskach interfejsu NVMe, uruchom polecenie, aby włączyć Azure Disk Encryption ponownie po zainicjowaniu dysków interfejsu NVMe.

Poza scenariuszami wymienionymi w sekcji [nieobsługiwane scenariusze](#unsupported-scenarios) szyfrowanie dysków interfejsu NVMe nie jest obsługiwane dla:

- Maszyny wirtualne zaszyfrowane przy użyciu Azure Disk Encryption z usługą AAD (poprzednia wersja)
- Dyski interfejsu NVMe z miejscami do magazynowania
- Azure Site Recovery jednostek SKU z dyskami interfejsu NVMe (zobacz [Macierz obsługi dla odzyskiwania po awarii maszyny wirtualnej platformy Azure między regionami platformy Azure: zreplikowane maszyny — magazyn](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)).

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nowe maszyny wirtualne IaaS utworzone z poziomu dysków VHD szyfrowanych przez klienta i kluczy szyfrowania

W tym scenariuszu można utworzyć nową maszynę wirtualną na podstawie wstępnie zaszyfrowanego dysku VHD i skojarzonych z nimi kluczy szyfrowania przy użyciu poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. 

Skorzystaj z instrukcji przedstawionych w temacie [przygotowanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Po utworzeniu obrazu możesz wykonać czynności opisane w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Szyfruj maszyny wirtualne z wstępnie zaszyfrowanymi dyskami VHD za pomocą Azure PowerShell
Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym przy użyciu polecenia cmdlet programu PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). W poniższym przykładzie przedstawiono kilka typowych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie na nowo dodanym dysku z danymi
[Nowy dysk można dodać do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](attach-disk-ps.md)lub [Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu Azure PowerShell
 W przypadku szyfrowania nowego dysku dla maszyn wirtualnych z systemem Windows przy użyciu programu PowerShell należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. W niektórych przypadkach nowo dodany dysk danych może być zaszyfrowany automatycznie przez rozszerzenie Azure Disk Encryption. Autoszyfrowanie jest zwykle wykonywane, gdy maszyna wirtualna zostanie ponownie uruchomiona po przejściu nowego dysku w tryb online. Zwykle jest to spowodowane tym, że dla typu woluminu została określona wartość "All", gdy wcześniej uruchomiono szyfrowanie dysku na maszynie wirtualnej. Jeśli funkcja autoszyfrowania występuje na nowo dodanym dysku danych, zalecamy ponowne uruchomienie Set-AzVmDiskEncryptionExtension polecenia cmdlet z nową wersją sekwencji. Jeśli nowy dysk danych jest szyfrowany jako szyfrowany i nie chcesz go zaszyfrować, najpierw Odszyfruj wszystkie dyski, a następnie Zaszyfruj ponownie przy użyciu nowej wersji sekwencji określającej system operacyjny dla typu woluminu. 
  
 

-  **Szyfruj uruchomioną maszynę wirtualną:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet. Grupa zasobów, maszyna wirtualna i Magazyn kluczy powinny być już utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Ten przykład używa "All" dla parametru-Volumetype, który obejmuje woluminy systemu operacyjnego i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru-Volumetype. 

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
- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:** Ten przykład używa "All" dla parametru-Volumetype, który obejmuje woluminy systemu operacyjnego i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru-Volumetype.

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
    > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu interfejsu wiersza polecenia platformy Azure
 Polecenie interfejsu wiersza polecenia platformy Azure automatycznie udostępni nową wersję sekwencji po uruchomieniu polecenia, aby włączyć szyfrowanie. W przykładzie dla parametru typu woluminu jest stosowana wartość "All". W przypadku szyfrowania dysku systemu operacyjnego może być konieczna zmiana parametru typu woluminu na system operacyjny. W przeciwieństwie do składni programu PowerShell interfejs wiersza polecenia nie wymaga od użytkownika podania unikatowej wersji sekwencji podczas włączania szyfrowania. Interfejs wiersza polecenia automatycznie generuje i używa własną unikatową wartość wersji sekwencji.   

-  **Szyfruj uruchomioną maszynę wirtualną:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Wyłącz szyfrowanie
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Azure Disk Encryption nie działa w następujących scenariuszach, funkcjach i technologiach:

- Szyfrowanie maszyny wirtualnej warstwy podstawowej lub maszyn wirtualnych utworzonych za pomocą metody tworzenia klasycznej maszyny wirtualnej.
- Szyfrowanie maszyn wirtualnych skonfigurowanych z oprogramowaniem opartym na oprogramowaniu RAID.
- Szyfrowanie maszyn wirtualnych skonfigurowanych przy użyciu funkcji Bezpośrednie miejsca do magazynowania (S2D) lub Windows Server w wersjach przed 2016 skonfigurowanych przy użyciu funkcji miejsca do magazynowania systemu Windows.
- Integracja z lokalnym systemem zarządzania kluczami.
- Azure Files (udostępniony system plików).
- Sieciowy system plików (NFS).
- Woluminy dynamiczne.
- Kontenery systemu Windows Server, które tworzą woluminy dynamiczne dla każdego kontenera.
- Tymczasowe dyski systemu operacyjnego.
- Szyfrowanie współużytkowanych/rozproszonych systemów plików, takich jak (ale nie ograniczone do) systemu plików DFS, GFS, DRDB i CephFS.
- Przeniesienie zaszyfrowanej maszyny wirtualnej na inną subskrypcję lub region.
- Tworzenie obrazu lub migawki zaszyfrowanej maszyny wirtualnej i używanie jej do wdrażania dodatkowych maszyn wirtualnych.
- Maszyny wirtualne serii M z dyskami akcelerator zapisu.
- Zastosowanie elementu ADE do maszyny wirtualnej z dyskami zaszyfrowanymi za pomocą [szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez klienta](../disk-encryption.md) (SSE + CMK). Stosowanie instrukcji SSE i CMK do dysku danych na maszynie wirtualnej zaszyfrowanej przy użyciu programu ADE jest również nieobsługiwanym scenariuszem.
- Migrowanie maszyny wirtualnej, która jest zaszyfrowana przy użyciu programu ADE lub **kiedykolwiek** została zaszyfrowana za pomocą programu ADE, do [szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez klienta](../disk-encryption.md).
- Szyfrowanie maszyn wirtualnych w klastrach trybu failover.

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
- [Przykładowe skrypty usługi Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)