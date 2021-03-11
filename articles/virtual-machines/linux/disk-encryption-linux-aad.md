---
title: Azure Disk Encryption z maszynami wirtualnymi aplikacja usługi Azure AD Linux IaaS (poprzednia wersja)
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Linux IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: d1607ef4ff277f9c9cdb55db3e58da1052a00756
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558405"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Włączanie Azure Disk Encryption z usługą Azure AD na maszynach wirtualnych z systemem Linux (poprzednia wersja)

Nowa wersja Azure Disk Encryption eliminuje konieczność podawania parametru aplikacji Azure Active Directory (Azure AD) w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby uzyskać instrukcje dotyczące włączania szyfrowania dysków maszyn wirtualnych przy użyciu nowej wersji, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-linux.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal utrzymywane przy użyciu składni usługi AAD.

Można włączyć wiele scenariuszy szyfrowania dysku, a kroki mogą się różnić w zależności od scenariusza. W poniższych sekcjach opisano scenariusze bardziej szczegółowo dotyczące maszyn wirtualnych z systemem Linux Infrastructure as a Service (IaaS). Szyfrowanie dysków można stosować tylko do maszyn wirtualnych [obsługiwanych rozmiarów maszyn wirtualnych i systemów operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems). Musisz również spełniać następujące wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Sieci i zasady grupy](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Przed zaszyfrowaniem dysków wykonaj [migawkę](snapshot-copy-managed-disk.md), Utwórz kopię zapasową lub obie te funkcje. Kopie zapasowe zapewniają opcję odzyskiwania, jeśli podczas szyfrowania wystąpi nieoczekiwany błąd. Maszyny wirtualne z dyskami zarządzanymi wymagają utworzenia kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension, aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania szyfrowanych maszyn wirtualnych, zobacz [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Jeśli wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](disk-encryption-overview-aad.md) w celu zaszyfrowania tej maszyny wirtualnej, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Nie można użyć [Azure Disk Encryption](disk-encryption-overview.md) dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączenie z aplikacji usługi Azure AD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane.
 > - Aby upewnić się, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, Azure Disk Encryption wymaga, aby Magazyn kluczy i maszyny wirtualne znajdowały się w tym samym regionie. Utwórz i Użyj magazynu kluczy znajdującego się w tym samym regionie co maszyna wirtualna, która ma być szyfrowana.
 > - W przypadku szyfrowania woluminów systemu operacyjnego Linux proces może potrwać kilka godzin. Jest to normalne dla woluminów systemu operacyjnego Linux, których szyfrowanie ma trwać dłużej niż woluminy danych.
> - W przypadku szyfrowania woluminów systemu operacyjnego Linux maszyna wirtualna powinna być uważana za niedostępną. Zdecydowanie zalecamy uniknięcie logowania przy użyciu protokołu SSH, gdy szyfrowanie jest w toku, aby uniknąć zablokowania otwartych plików, do których należy uzyskać dostęp podczas procesu szyfrowania. Aby sprawdzić postęp, użyj polecenia [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) lub [szyfrowania maszyny wirtualnej show](/cli/azure/vm/encryption#az-vm-encryption-show) . Proces ten może potrwać kilka godzin w przypadku woluminu systemu operacyjnego 30 GB i dodatkowego czasu na szyfrowanie woluminów danych. Czas szyfrowania woluminu danych jest proporcjonalny do rozmiaru i ilości woluminów danych, chyba że jest używana opcja **Szyfruj cały format** . 
 > - Wyłączanie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwane tylko dla woluminów danych. Nie jest on obsługiwany w woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"></a> Włączanie szyfrowania na istniejącej lub URUCHOMIONEJ maszynie wirtualnej z systemem IaaS Linux

W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Azure Resource Manager, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia platformy Azure. 

>[!IMPORTANT]
 >Należy wykonać migawkę lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dyskach zarządzanych poza programem i przed włączeniem Azure Disk Encryption. Możesz wykonać migawkę dysku zarządzanego z Azure Portal lub użyć [Azure Backup](../../backup/backup-azure-vms-encryption.md). Kopie zapasowe zapewniają opcję odzyskiwania w przypadku wystąpienia dowolnego nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej Użyj polecenia cmdlet Set-AzVMDiskEncryptionExtension, aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Set-AzVMDiskEncryptionExtension polecenie kończy się niepowodzeniem przed maszynami wirtualnymi opartymi na dyskach, dopóki nie zostanie utworzona kopia zapasowa i ten parametr jest określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Włączanie szyfrowania na istniejącej lub URUCHOMIONEJ maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 
Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym, instalując narzędzie wiersza polecenia [platformy Azure 2,0](/cli/azure) i korzystając z niego. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS Linux na platformie Azure, użyj następujących poleceń interfejsu wiersza polecenia:

Użyj polecenia [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

-  **Szyfruj uruchomioną maszynę wirtualną przy użyciu klucza tajnego klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Zaszyfruj działającą maszynę wirtualną za pomocą KEK, aby otoczyć klucz tajny klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > Składnia wartości parametru dysk-szyfrowanie-kluczy jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[nazwa grupy-klasy]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy].</br> </br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID].

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Wyłączenie szyfrowania jest dozwolone tylko dla woluminów danych dla maszyn wirtualnych z systemem Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"></a> Włączanie szyfrowania na istniejącej lub URUCHOMIONEJ maszynie wirtualnej z systemem Linux przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby włączyć szyfrowanie na działającej maszynie wirtualnej IaaS na platformie Azure. Zrób [migawkę](snapshot-copy-managed-disk.md) lub Utwórz kopię zapasową maszyny wirtualnej z [Azure Backup](../../backup/backup-azure-vms-encryption.md) przed zaszyfrowaniem dysków. Parametr-skipVmBackup jest już określony w skryptach programu PowerShell, aby można było zaszyfrować działającą maszynę wirtualną z systemem Linux.

- **Szyfruj uruchomioną maszynę wirtualną przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet. Grupa zasobów, maszyna wirtualna, Magazyn kluczy, aplikacja usługi Azure AD i klucz tajny klienta powinny zostać już utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID i my-AAD-Client-Secret wartościami. Zmodyfikuj parametr-Volumetype, aby określić, które dyski są szyfrowane.

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
- **Zaszyfruj działającą maszynę wirtualną za pomocą KEK, aby otoczyć klucz tajny klienta:** Azure Disk Encryption pozwala określić istniejący klucz w magazynie kluczy, aby zawijał klucze tajne szyfrowania dysku, które zostały wygenerowane podczas włączania szyfrowania. Jeśli klucz szyfrowania klucza jest określony, Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisaniem w magazynie kluczy. Zmodyfikuj parametr-Volumetype, aby określić, które dyski są szyfrowane. 

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
  > Składnia wartości parametru dysk-szyfrowanie-kluczy jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[KVresource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy].</br> </br>
  Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID]. 
    
- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Wyłączenie szyfrowania jest dozwolone tylko dla woluminów danych dla maszyn wirtualnych z systemem Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"></a> Włączanie szyfrowania na istniejącej lub URUCHOMIONEJ maszynie wirtualnej z systemem IaaS Linux przy użyciu szablonu

Szyfrowanie dysków można włączyć na istniejącej lub uruchomionej maszynie wirtualnej IaaS z systemem Linux na platformie Azure przy użyciu [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Wybierz pozycję **Wdróż na platformie Azure** w szablonie szybkiego startu platformy Azure.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, warunki prawne i umowę. Wybierz pozycję **Utwórz** , aby włączyć szyfrowanie na istniejącej lub URUCHOMIONEJ maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono Menedżer zasobów parametry szablonu dla istniejących lub uruchomionych maszyn wirtualnych, które używają identyfikatora klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, który ma uprawnienia do zapisywania wpisów tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, który ma uprawnienia do zapisywania wpisów tajnych w magazynie kluczy. |
| Nazwakluczamagazynu | Nazwa magazynu kluczy, do którego ma zostać przekazany klucz. Możesz uzyskać go za pomocą polecenia interfejsu CLI platformy Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup` . |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używany do szyfrowania wygenerowanego klucza. Ten parametr jest opcjonalny w przypadku wybrania opcji **nokek** na liście rozwijanej **UseExistingKek** . Jeśli wybierzesz pozycję **KEK** na liście rozwijanej **UseExistingKek** , musisz wprowadzić wartość _keyEncryptionKeyURL_ . |
| liczba woluminów | Typ woluminu, na którym jest wykonywana operacja szyfrowania. Prawidłowe obsługiwane wartości to _system operacyjny_ lub _wszystkie_. (Zobacz obsługiwane dystrybucje systemu Linux i ich wersje dla systemów operacyjnych i danych w sekcji wymagania wstępne wcześniej). |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Zwiększ ten numer wersji za każdym razem, gdy na tej samej maszynie wirtualnej jest wykonywana operacja szyfrowania dysku. |
| vmName | Nazwa maszyny wirtualnej, na której ma zostać wykonana operacja szyfrowania. |
| hasło | Wpisz silne hasło jako klucz szyfrowania danych. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Korzystanie z funkcji EncryptFormatAll w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux IaaS
EncryptFormatAll parametr skraca czas szyfrowania dysków danych systemu Linux. Partycje spełniające określone kryteria są formatowane (wraz z ich bieżącym systemem plików). Następnie są one ponownie instalowane w miejscu, w którym były przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych spełniający kryteria, możesz go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia wszystkie zainstalowane wcześniej dyski są sformatowane. Następnie warstwa szyfrowania zaczyna się u góry pustego dysku. Po wybraniu tej opcji dysk tymczasowy dołączony do maszyny wirtualnej jest również szyfrowany. Jeśli dysk tymczasowych jest resetowany, zostanie on ponownie sformatowany i zaszyfrowany na potrzeby maszyny wirtualnej za pomocą rozwiązania Azure Disk Encryption w następnej okazji.

>[!WARNING]
> EncryptFormatAll nie należy używać w przypadku danych na woluminach danych maszyny wirtualnej. Dyski można wykluczać z szyfrowania, odinstalując je. Wypróbuj najpierw parametr EncryptFormatAll na testowej maszynie wirtualnej, aby zrozumieć parametr funkcji i jej implikacje przed podjęciem próby na produkcyjną maszynę wirtualną. Opcja EncryptFormatAll powoduje sformatowanie dysku z danymi, dzięki czemu wszystkie dane na nim zostaną utracone. Przed kontynuowaniem sprawdź, czy wszystkie dyski, które chcesz wykluczyć, są prawidłowo odinstalowywane. </br></br>
 >Jeśli ustawisz ten parametr podczas aktualizacji ustawień szyfrowania, może to doprowadzić do ponownego uruchomienia komputera przed rzeczywistym szyfrowaniem. W takim przypadku należy również usunąć dysk, który nie ma być sformatowany z pliku fstab. Podobnie należy dodać partycję, która ma być szyfrowana w pliku fstab przed zainicjowaniem operacji szyfrowania. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"></a> Kryteria EncryptFormatAll
Parametr przechodzi przez wszystkie partycje i szyfruje je, o ile spełniają *wszystkie* następujące kryteria: 
- Nie jest partycją główną/OS/rozruchową
- Nie jest jeszcze zaszyfrowany
- Nie jest woluminem klucz szyfrowania bloków
- Nie jest woluminem RAID
- Nie jest woluminem LVM
- Jest zainstalowany

Zaszyfruj dyski tworzące wolumin RAID lub LVM, a nie wolumin RAID lub LVM.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"></a> Używanie parametru EncryptFormatAll z szablonem
Aby użyć opcji EncryptFormatAll, użyj istniejącego szablonu Azure Resource Manager, który szyfruje maszynę wirtualną z systemem Linux, i Zmień pole **EncryptionOperation** dla zasobu AzureDiskEncryption.

1. Przykładowo Użyj [szablonu Menedżer zasobów, aby zaszyfrować działającą maszynę wirtualną IaaS systemu Linux](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Wybierz pozycję **Wdróż na platformie Azure** w szablonie szybkiego startu platformy Azure.
3. Zmień pole **EncryptionOperation** z **EnableEncryption** na **EnableEncryptionFormatAl**.
4. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, inne parametry, warunki prawne i umowę. Wybierz pozycję **Utwórz** , aby włączyć szyfrowanie na istniejącej lub URUCHOMIONEJ maszynie wirtualnej IaaS.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"></a> Użyj parametru EncryptFormatAll z poleceniem cmdlet programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z parametrem EncryptFormatAll.

**Szyfruj uruchomioną maszynę wirtualną przy użyciu klucza tajnego klienta i EncryptFormatAll:** Na przykład poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet z parametrem EncryptFormatAll. Grupa zasobów, maszyna wirtualna, Magazyn kluczy, aplikacja usługi Azure AD i klucz tajny klienta powinny zostać już utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID i my-AAD-Client-Secret wartościami.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"></a> Używanie parametru EncryptFormatAll z menedżerem woluminów logicznych (LVM) 
Zalecamy instalację LVM-on-Crypt. We wszystkich poniższych przykładach Zastąp ścieżkę urządzenia i mountpoints w dowolny sposób pasujący do Twojego przypadku użycia. Tę konfigurację można wykonać w następujący sposób:

- Dodaj dyski danych, które będą tworzyć maszyny wirtualne.
- Sformatuj, zainstaluj i Dodaj te dyski do pliku fstab.

    1. Sformatuj nowo dodany dysk. W tym miejscu używamy linków symbolicznych wygenerowanego przez platformę Azure. Korzystanie z programu linków symbolicznych pozwala uniknąć problemów związanych z zmianami nazw urządzeń. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z nazwami urządzeń](../troubleshooting/troubleshoot-device-names-problems.md).
    
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

    4. Uruchom polecenie cmdlet programu Set-AzVMDiskEncryptionExtension PowerShell z parametrem-EncryptFormatAll, aby zaszyfrować te dyski.

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. Skonfiguruj LVM na nowych dyskach. Pamiętaj, że zaszyfrowane dyski są odblokowane po zakończeniu rozruchu maszyny wirtualnej. W związku z tym należy również opóźnić Instalowanie LVM.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"></a> Nowe maszyny wirtualne IaaS utworzone z poziomu dysków VHD szyfrowanych przez klienta i kluczy szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Menedżer zasobów, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. W poniższych sekcjach szczegółowo opisano Menedżer zasobów szablonu i poleceń interfejsu wiersza polecenia. 

Aby przygotować wstępnie zaszyfrowane obrazy, które mogą być używane na platformie Azure, należy wykonać instrukcje zawarte w dodatku. Po utworzeniu obrazu możesz wykonać czynności opisane w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Linux](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Należy wykonać migawkę lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dyskach zarządzanych poza programem i przed włączeniem Azure Disk Encryption. Możesz wykonać migawkę dysku zarządzanego z portalu lub użyć [Azure Backup](../../backup/backup-azure-vms-encryption.md). Kopie zapasowe zapewniają opcję odzyskiwania w przypadku wystąpienia dowolnego nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej Użyj polecenia cmdlet Set-AzVMDiskEncryptionExtension, aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Set-AzVMDiskEncryptionExtension polecenie kończy się niepowodzeniem przed maszynami wirtualnymi opartymi na dyskach, dopóki nie zostanie utworzona kopia zapasowa i ten parametr jest określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"></a> Używanie Azure PowerShell do szyfrowania maszyn wirtualnych IaaS z wstępnie szyfrowanymi dyskami VHD 
Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym przy użyciu polecenia cmdlet programu PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). W poniższym przykładzie przedstawiono kilka typowych parametrów. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie na nowo dodanym dysku z danymi
Nowy dysk danych można dodać za pomocą polecenia [AZ VM Disk Attach](add-disk.md) lub [przez Azure Portal](attach-disk-portal.md). Przed zaszyfrowaniem należy najpierw zainstalować nowo dołączony dysk danych. Musisz zażądać szyfrowania dysku danych, ponieważ dysk będzie bezużyteczny, gdy szyfrowanie jest w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu interfejsu wiersza polecenia platformy Azure
 Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu "wszystkie", parametr--type Wszystkie obejmują zarówno system operacyjny, jak i dyski danych. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "system operacyjny", należy zmienić parametr--type-to all, aby uwzględnić zarówno system operacyjny, jak i nowy dysk danych. Jeśli maszyna wirtualna została zaszyfrowana przy użyciu tylko typu woluminu "dane", dane te mogą pozostać w tym miejscu. Dodawanie i dołączanie nowego dysku z danymi do maszyny wirtualnej nie jest wystarczającym przygotowaniem do szyfrowania. Nowo dołączony dysk musi być również sformatowany i poprawnie zainstalowany na maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zainstalowany w/etc/fstab z [trwałą nazwą urządzenia blokowego](../troubleshooting/troubleshoot-device-names-problems.md). 

W przeciwieństwie do składni programu PowerShell interfejs wiersza polecenia nie wymaga podania unikatowej wersji sekwencji po włączeniu szyfrowania. Interfejs wiersza polecenia automatycznie generuje i używa własną unikatową wartość wersji sekwencji.

-  **Szyfruj uruchomioną maszynę wirtualną przy użyciu klucza tajnego klienta:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Zaszyfruj działającą maszynę wirtualną za pomocą KEK, aby otoczyć klucz tajny klienta:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu Azure PowerShell
 W przypadku szyfrowania nowego dysku dla systemu Linux przy użyciu programu PowerShell należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. 
 

-  **Szyfruj uruchomioną maszynę wirtualną przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet. Grupa zasobów, maszyna wirtualna, Magazyn kluczy, aplikacja usługi Azure AD i klucz tajny klienta powinny zostać już utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID i my-AAD-Client-Secret wartościami. Parametr-Volumetype jest ustawiony na dyski danych, a nie dysk systemu operacyjnego. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "OS" lub "All", należy zmienić parametr-Volumetype na wartość All, aby uwzględnić zarówno system operacyjny, jak i nowy dysk danych.

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
- **Zaszyfruj działającą maszynę wirtualną za pomocą KEK, aby otoczyć klucz tajny klienta:** Azure Disk Encryption pozwala określić istniejący klucz w magazynie kluczy, aby zawijał klucze tajne szyfrowania dysku, które zostały wygenerowane podczas włączania szyfrowania. Jeśli klucz szyfrowania klucza jest określony, Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisaniem w magazynie kluczy. Parametr-Volumetype jest ustawiony na dyski danych, a nie dysk systemu operacyjnego. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "OS" lub "All", należy zmienić parametr-Volumetype na wartość All, aby uwzględnić zarówno system operacyjny, jak i nowy dysk danych.

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
> Składnia wartości parametru dysk-szyfrowanie-kluczy jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[nazwa grupy-klasy]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]. </br> </br>
Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID].

## <a name="disable-encryption-for-linux-vms"></a>Wyłącz szyfrowanie dla maszyn wirtualnych z systemem Linux
Szyfrowanie można wyłączyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Menedżer zasobów. 

>[!IMPORTANT]
>Wyłączanie szyfrowania przy użyciu Azure Disk Encryption na maszynach wirtualnych z systemem Linux jest obsługiwane tylko dla woluminów danych. Nie jest on obsługiwany w woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany. 

- **Wyłącz szyfrowanie dysków przy użyciu Azure PowerShell:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie przy użyciu szablonu Menedżer zasobów:** Aby wyłączyć szyfrowanie, należy użyć szablonu [wyłącz szyfrowanie w uruchomionym szablonie maszyny wirtualnej z systemem Linux](https://aka.ms/decrypt-linuxvm) .
     1. Wybierz pozycję **Wdróż na platformie Azure**.
     2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, warunki prawne i umowę.
     3. Wybierz pozycję **Kup** , aby wyłączyć szyfrowanie dysków na działającej maszynie wirtualnej z systemem Windows. 


## <a name="next-steps"></a>Następne kroki

- [Azure Disk Encryption dla systemu Linux — Omówienie](disk-encryption-overview-aad.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednia wersja)](disk-encryption-key-vault-aad.md)
