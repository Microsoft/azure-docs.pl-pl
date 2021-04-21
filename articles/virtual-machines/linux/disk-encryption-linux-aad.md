---
title: Azure Disk Encryption z maszynami aplikacja usługi Azure AD IaaS z systemem Linux (poprzednia wersja)
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure disk encryption dla maszyn wirtualnych IaaS z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 94440b71eee1ff9dcc4a86733582e3e5f57f6a00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764625"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Włączanie Azure Disk Encryption z usługą Azure AD na komputerach wirtualnych z systemem Linux (poprzednia wersja)

Nowa wersja usługi Azure Disk Encryption eliminuje wymaganie podania parametru aplikacji Azure Active Directory (Azure AD) w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby uzyskać instrukcje dotyczące włączania szyfrowania dysków maszyny wirtualnej przy użyciu nowej wersji, zobacz Azure Disk Encryption [maszyny wirtualnej z systemem Linux.](disk-encryption-linux.md) Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal obsługiwane przy użyciu składni usługi AAD.

Można włączyć wiele scenariuszy szyfrowania dysków, a kroki mogą się różnić w zależności od scenariusza. W poniższych sekcjach bardziej szczegółowo opisano scenariusze dotyczące maszyn wirtualnych infrastruktury jako usługi (IaaS) z systemem Linux. Szyfrowanie dysków można zastosować tylko do maszyn wirtualnych o [obsługiwanych rozmiarach maszyn wirtualnych i systemach operacyjnych.](disk-encryption-overview.md#supported-vms-and-operating-systems) Należy również spełnić następujące wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Sieć i zasady grupy](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Przed [zaszyfrowaniem](snapshot-copy-managed-disk.md)dysków należy utworzyć kopię zapasową lub utworzyć kopię zapasową. Kopie zapasowe zapewniają możliwość opcji odzyskiwania w przypadku wystąpienia nieoczekiwanego błędu podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopii zapasowej przed rozpoczęciem szyfrowania. Po kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension do szyfrowania dysków zarządzanych, określając parametr -skipVmBackup. Aby uzyskać więcej informacji na temat sposobu kopii zapasowej i przywracania zaszyfrowanych maszyn wirtualnych, [zobacz Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Jeśli ta maszyna wirtualna Azure Disk Encryption wcześniej używana do szyfrowania tej maszyny wirtualnej za pomocą aplikacji [usługi Azure AD,](disk-encryption-overview-aad.md) należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Na tej zaszyfrowanej maszynie wirtualnej nie można używać funkcji Azure Disk Encryption, ponieważ nie jest [to](disk-encryption-overview.md) obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi Azure AD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane.
 > - Aby mieć pewność, że wpisy tajne szyfrowania nie przekroczy granic regionalnych, Azure Disk Encryption magazyn kluczy i maszyny wirtualne muszą być zlokalizowane w tym samym regionie. Utwórz magazyn kluczy, który jest w tym samym regionie co maszyna wirtualna, i użyj go do zaszyfrowania.
 > - Szyfrowanie woluminów systemu operacyjnego Linux może potrwać kilka godzin. Zwykle szyfrowanie woluminów systemu operacyjnego Linux trwa dłużej niż woluminy danych.
> - W przypadku szyfrowania woluminów systemu operacyjnego Linux maszyna wirtualna powinna być uznawana za niedostępną. Zdecydowanie zalecamy unikanie logowań SSH w trakcie szyfrowania, aby uniknąć blokowania otwartych plików, do których należy uzyskać dostęp podczas procesu szyfrowania. Aby sprawdzić postęp, użyj poleceń [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) lub [vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) Możesz oczekiwać, że ten proces potrwa kilka godzin w przypadku woluminu systemu operacyjnego o pojemności 30 GB oraz dodatkowego czasu na szyfrowanie woluminów danych. Czas szyfrowania woluminu danych jest proporcjonalny do rozmiaru i ilości woluminów danych, chyba że jest używana **cała** opcja formatu szyfrowania. 
 > - Wyłączanie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwane tylko w przypadku woluminów danych. Jeśli wolumin systemu operacyjnego został zaszyfrowany, nie jest on obsługiwany na woluminach danych ani na woluminach systemu operacyjnego. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"></a> Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej IaaS z systemem Linux

W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Azure Resource Manager, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia platformy Azure. 

>[!IMPORTANT]
 >Należy utworzyć migawkę lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dysku zarządzanym poza i przed włączeniem Azure Disk Encryption. Możesz utworzyć migawkę dysku zarządzanego z Azure Portal lub użyć Azure Backup [.](../../backup/backup-azure-vms-encryption.md) Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa w przypadku nieoczekiwanego błędu podczas szyfrowania. Po kopii zapasowej użyj polecenia cmdlet Set-AzVMDiskEncryptionExtension, aby zaszyfrować dyski zarządzane, określając parametr -skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension kończy się niepowodzeniem względem maszyn wirtualnych opartych na dysku zarządzanym, dopóki nie zostanie wykonana kopia zapasowa i ten parametr zostanie określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 
Szyfrowanie dysków na zaszyfrowanym dysku VHD można włączyć, instalując narzędzie wiersza polecenia interfejsu wiersza polecenia platformy [Azure 2.0](/cli/azure) i korzystając z tego narzędzia. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejących lub działających na platformie Azure maszyn wirtualnych IaaS z systemem Linux, użyj następujących poleceń interfejsu wiersza polecenia:

Użyj polecenia [az vm encryption enable,](/cli/azure/vm/encryption#az_vm_encryption_enable) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Zaszyfruj uruchamianą maszynę wirtualną przy użyciu klucza KEK w celu opakwania klucza tajnego klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > Składnia wartości parametru disk-encryption-keyvault jest ciągiem pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa-magazynu-kluczy].</br> </br> Składnia wartości parametru key-encryption-key to pełny identyfikator URI klucza szyfrowania klucza, jak w: https://[nazwa_magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj [polecenia az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj [polecenia az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) Wyłączanie szyfrowania jest dozwolone tylko na woluminach danych dla maszyn wirtualnych z systemem Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"></a> Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure. Tworzenie [migawki](snapshot-copy-managed-disk.md) lub tworzenie kopii zapasowej maszyny wirtualnej za [pomocą Azure Backup,](../../backup/backup-azure-vms-encryption.md) zanim dyski zostaną zaszyfrowane. Parametr -skipVmBackup jest już określony w skryptach programu PowerShell w celu zaszyfrowania uruchomionej maszyny wirtualnej z systemem Linux.

- **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension cmdlet. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja usługi Azure AD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret swoimi wartościami. Zmodyfikuj parametr -VolumeType, aby określić, które dyski są szyfrowane.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Zaszyfruj uruchamianą maszynę wirtualną przy użyciu klucza szyfrowania klucza szyfrowania w celu opakwania klucza tajnego klienta:** Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy w celu opakwania wpisów tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Po podano klucz szyfrowania klucza, Azure Disk Encryption używa tego klucza do opakowywania wpisów tajnych szyfrowania przed zapisem w magazynie kluczy. Zmodyfikuj parametr -VolumeType, aby określić, które dyski są szyfrowane. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > Składnia wartości parametru disk-encryption-keyvault to pełny ciąg identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa-grupy-kluczy]/providers/Microsoft.KeyVault/vaults/[nazwa-magazynu-kluczy].</br> </br>
  Składnia wartości parametru key-encryption-key to pełny identyfikator URI klucza szyfrowania klucza, jak w: https://[nazwa_magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Wyłączanie szyfrowania jest dozwolone tylko na woluminach danych dla maszyn wirtualnych z systemem Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"></a> Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej IaaS z systemem Linux przy użyciu szablonu

Szyfrowanie dysków można włączyć na istniejącej lub uruchomionej maszynie wirtualnej IaaS z systemem Linux na [platformie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)Azure przy użyciu Resource Manager szablonu .

1. Wybierz **pozycję Deploy to Azure (Wd wdrażaj** na platformie Azure) w szablonie Szybkiego startu platformy Azure.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowę. Wybierz **pozycję Utwórz,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono Resource Manager szablonu dla istniejących lub uruchomionych maszyn wirtualnych, które używają identyfikatora klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu wpisów tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu wpisów tajnych w magazynie kluczy. |
| keyVaultName | Nazwa magazynu kluczy, do który ma zostać przekazany klucz. Możesz go pobrać za pomocą polecenia interfejsu wiersza polecenia platformy Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup` . |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używanego do szyfrowania wygenerowanego klucza. Ten parametr jest opcjonalny, jeśli wybierzesz **pozycję nokek** na liście rozwijanej **UseExistingKek.** Jeśli wybierzesz **kek** na **UseExistingKek** listy rozwijanej, należy wprowadzić _wartość keyEncryptionKeyURL._ |
| typ woluminu | Typ woluminu, na który jest wykonywana operacja szyfrowania. Prawidłowe obsługiwane wartości to _System operacyjny_ lub _Wszystkie._ (Zobacz obsługiwane dystrybucje systemu Linux i ich wersje dla dysków systemu operacyjnego i danych w sekcji wymagań wstępnych wcześniej). |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Zwiększaj ten numer wersji za każdym razem, gdy operacja szyfrowania dysku jest wykonywana na tej samej maszynie wirtualnej. |
| vmName | Nazwa maszyny wirtualnej, na podstawie których ma zostać wykonana operacja szyfrowania. |
| hasło | Wpisz silne hasło jako klucz szyfrowania danych. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Używanie funkcji EncryptFormatAll dla dysków danych na maszynach wirtualnych IaaS z systemem Linux
Parametr EncryptFormatAll skraca czas szyfrowania dysków danych systemu Linux. Partycje spełniające określone kryteria są formatowane (z bieżącym systemem plików). Następnie są one ponownie instalowane w miejscu, w którym były przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych spełniający kryteria, możesz go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia wszystkie zainstalowane wcześniej dyski zostaną sformatowane. Następnie warstwa szyfrowania jest uruchamiana na pustym dysku. Po wybraniu tej opcji dysk tymczasowy dołączony do maszyny wirtualnej również jest szyfrowany. Jeśli dysk efemeralny zostanie zresetowany, zostanie ponownie sformatowany i ponownie zaszyfrowany dla maszyny wirtualnej przez rozwiązanie Azure Disk Encryption przy następnej okazji.

>[!WARNING]
> EncryptFormatAll nie należy używać, gdy na woluminach danych maszyny wirtualnej są potrzebne dane. Dyski można wykluczyć z szyfrowania, odinstalując je. Najpierw wypróbuj parametr EncryptFormatAll na testowej maszynie wirtualnej, aby zrozumieć parametr funkcji i jego wpływ przed wypróbowaniem go na produkcyjnej maszynie wirtualnej. Opcja EncryptFormatAll formatuje dysk danych, więc wszystkie dane na nim zostaną utracone. Przed kontynuowaniem sprawdź, czy wszystkie dyski, które chcesz wykluczyć, są prawidłowo odinstalowane. </br></br>
 >Jeśli ten parametr zostanie ustawiony podczas aktualizowania ustawień szyfrowania, może to prowadzić do ponownego uruchomienia przed rzeczywistym szyfrowaniem. W takim przypadku należy również usunąć dysk, który nie ma być sformatowany z pliku fstab. Podobnie należy dodać partycję, która ma być szyfrowana do pliku fstab przed zainicjowanie operacji szyfrowania. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"></a> EncryptFormatAll criteria (Kryteria EncryptFormatAll)
Parametr przechodzi przez wszystkie partycje i szyfruje je, o ile *spełniają* wszystkie następujące kryteria: 
- Nie jest partycją katalogu głównego/systemu operacyjnego/rozruchu
- Nie jest jeszcze zaszyfrowany
- Nie jest woluminem BEK
- Nie jest woluminem RAID
- Nie jest woluminem LVM
- Jest zainstalowany

Zaszyfruj dyski, które tworzą wolumin RAID lub LVM, a nie wolumin RAID lub LVM.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"></a> Używanie parametru EncryptFormatAll z szablonem
Aby użyć opcji EncryptFormatAll, użyj dowolnego szablonu usługi Azure Resource Manager, który szyfruje maszynę wirtualną z systemem Linux i zmienia pole **EncryptionOperation** dla zasobu AzureDiskEncryption.

1. Na przykład użyj szablonu usługi Resource Manager do zaszyfrowania uruchomionej maszyny wirtualnej [IaaS](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)z systemem Linux. 
2. Wybierz **pozycję Deploy to Azure (Wd** wdrażaj na platformie Azure) w szablonie Szybkiego startu platformy Azure.
3. Zmień pole **EncryptionOperation z** **EnableEncryption na** **EnableEncryptionFormatAl.**
4. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, inne parametry, postanowienia prawne i umowę. Wybierz **pozycję Utwórz,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej IaaS.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"></a> Używanie parametru EncryptFormatAll z poleceniem cmdlet programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z parametrem EncryptFormatAll.

**Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta i funkcji EncryptFormatAll:** Na przykład poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension z parametrem EncryptFormatAll. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja usługi Azure AD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret swoimi wartościami.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"></a> Używanie parametru EncryptFormatAll z Menedżerem woluminów logicznych (LVM) 
Zalecamy konfigurację LVM-on-crypt. W przypadku wszystkich poniższych przykładów zastąp ścieżkę urządzenia i punkty instalacji tym, co pasuje do Twojego przypadku użycia. Tę konfigurację można wykonać w następujący sposób:

- Dodaj dyski danych, które będą ować maszynę wirtualną.
- Sformatuj, zainstaluj i dodaj te dyski do pliku fstab.

    1. Sformatuj nowo dodany dysk. W tym miejscu używamy linków symbolicznych wygenerowanych przez platformę Azure. Użycie linków symbolicznych pozwala uniknąć problemów związanych ze zmianą nazw urządzeń. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z nazwami urządzeń.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)
    
        ```console
        mkfs -t ext4 /dev/disk/azure/scsi1/lun0
        ```

    2. Zainstaluj dyski.

        ```console
        mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint
        ```

    3. Dodaj do fstab.

        ```console
        echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab
        ```

    4. Uruchom polecenie Set-AzVMDiskEncryptionExtension cmdlet programu PowerShell za pomocą polecenia -EncryptFormatAll, aby zaszyfrować te dyski.

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. Skonfiguruj LVM na tych nowych dyskach. Należy pamiętać, że zaszyfrowane dyski są odblokowane po zakończeniu rozruchu maszyny wirtualnej. Dlatego instalowanie LVM również będzie musiało zostać opóźnione.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"></a> Nowe maszyny wirtualne IaaS utworzone na podstawie zaszyfrowanego przez klienta wirtualnego dysku twardego i kluczy szyfrowania
W tym scenariuszu szyfrowanie można włączyć przy użyciu szablonu Resource Manager, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. W poniższych sekcjach bardziej szczegółowo opisano Resource Manager szablonu i interfejsu wiersza polecenia. 

Skorzystaj z instrukcji w dodatku dotyczącym przygotowywania wstępnie zaszyfrowanych obrazów, które mogą być używane na platformie Azure. Po utworzeniu obrazu możesz wykonać kroki opisane w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Linux](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Należy utworzyć migawkę lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dysku zarządzanym poza i przed włączeniem Azure Disk Encryption. Możesz utworzyć migawkę dysku zarządzanego z portalu lub użyć Azure Backup [.](../../backup/backup-azure-vms-encryption.md) Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa w przypadku nieoczekiwanego błędu podczas szyfrowania. Po kopii zapasowej użyj polecenia cmdlet Set-AzVMDiskEncryptionExtension, aby zaszyfrować dyski zarządzane, określając parametr -skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension kończy się niepowodzeniem względem maszyn wirtualnych opartych na dysku zarządzanym, dopóki nie zostanie wykonana kopia zapasowa i ten parametr zostanie określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"></a> Używanie Azure PowerShell do szyfrowania maszyn wirtualnych IaaS za pomocą wstępnie zaszyfrowanych dysków VHD 
Szyfrowanie dysków na zaszyfrowanym dysku VHD można włączyć za pomocą polecenia cmdlet programu PowerShell [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) Poniższy przykład zawiera kilka typowych parametrów. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włączanie szyfrowania na nowo dodanym dysku danych
Nowy dysk danych można dodać za pomocą narzędzia [az vm disk attach](add-disk.md) lub za [pośrednictwem Azure Portal](attach-disk-portal.md). Przed zaszyfrowaniem należy najpierw zainstalować nowo dołączony dysk danych. Należy zażądać szyfrowania dysku danych, ponieważ dysk będzie bezużytelny, gdy szyfrowanie jest w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Włączanie szyfrowania na nowo dodanym dysku za pomocą interfejsu wiersza polecenia platformy Azure
 Jeśli maszyna wirtualna została wcześniej zaszyfrowana za pomocą wartości "Wszystkie", parametr --volume-type powinien pozostać wartością Wszystkie. Wszystkie obejmują zarówno dysk systemu operacyjnego, jak i dysk danych. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "System operacyjny", parametr --volume-type powinien zostać zmieniony na Wszystkie, aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych. Jeśli maszyna wirtualna została zaszyfrowana tylko przy użyciu typu woluminu "Dane", może pozostać w nim data, jak pokazano tutaj. Dodanie i dołączenie nowego dysku danych do maszyny wirtualnej nie jest wystarczającym przygotowaniem do szyfrowania. Nowo dołączony dysk musi być również sformatowany i poprawnie zainstalowany na maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zainstalowany w pliku /etc/fstab z trwałą [nazwą urządzenia blokowego.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems) 

W przeciwieństwie do składni programu PowerShell interfejs wiersza polecenia nie wymaga podania unikatowej wersji sekwencji po włączeniu szyfrowania. Interfejs wiersza polecenia automatycznie generuje i używa własnej unikatowej wartości wersji sekwencji.

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Zaszyfruj uruchamianą maszynę wirtualną przy użyciu klucza KEK w celu opakwania klucza tajnego klienta:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włączanie szyfrowania na nowo dodanym dysku przy użyciu Azure PowerShell
 W przypadku szyfrowania nowego dysku dla systemu Linux przy użyciu programu PowerShell należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. 
 

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension cmdlet. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja usługi Azure AD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret. Parametr -VolumeType jest ustawiony na dyski danych, a nie dysk systemu operacyjnego. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "System operacyjny" lub "Wszystkie", parametr -VolumeType powinien zostać zmieniony na Wszystkie, aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Zaszyfruj uruchamianą maszynę wirtualną przy użyciu klucza KEK w celu opakwania klucza tajnego klienta:** Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy w celu opakwania wpisów tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Po podano klucz szyfrowania klucza, Azure Disk Encryption używa tego klucza do opakowywania wpisów tajnych szyfrowania przed zapisem w magazynie kluczy. Parametr -VolumeType jest ustawiony na dyski danych, a nie dysk systemu operacyjnego. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "System operacyjny" lub "Wszystkie", parametr -VolumeType powinien zostać zmieniony na Wszystkie, aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> Składnia wartości parametru disk-encryption-keyvault jest ciągiem pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa-magazynu-kluczy]. </br> </br>
Składnia wartości parametru key-encryption-key to pełny identyfikator URI klucza szyfrowania klucza, jak w: https://[nazwa_magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Wyłączanie szyfrowania dla maszyn wirtualnych z systemem Linux
Szyfrowanie można wyłączyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub Resource Manager szablonu. 

>[!IMPORTANT]
>Wyłączanie szyfrowania za pomocą Azure Disk Encryption na maszynach wirtualnych z systemem Linux jest obsługiwane tylko w przypadku woluminów danych. Jeśli wolumin systemu operacyjnego został zaszyfrowany, nie jest on obsługiwany na woluminach danych ani na woluminach systemu operacyjnego. 

- **Wyłącz szyfrowanie dysków za pomocą Azure PowerShell:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj [polecenia az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie za pomocą Resource Manager szablonu:** Aby wyłączyć szyfrowanie, użyj szablonu [Wyłącz szyfrowanie na uruchomionej maszynie wirtualnej z systemem Linux.](https://aka.ms/decrypt-linuxvm)
     1. Wybierz pozycję **Deploy to Azure (Wd wdrażaj na platformie Azure).**
     2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, postanowienia prawne i umowę.
     3. Wybierz **pozycję Kup,** aby wyłączyć szyfrowanie dysków na uruchomionej maszynie wirtualnej z systemem Windows. 


## <a name="next-steps"></a>Następne kroki

- [omówienie Azure Disk Encryption dla systemu Linux](disk-encryption-overview-aad.md)
- [Tworzenie i konfigurowanie magazynu kluczy na Azure Disk Encryption z usługą Azure AD (poprzednia wersja)](disk-encryption-key-vault-aad.md)