---
title: Azure Disk Encryption z usługą Azure AD dla maszyn wirtualnych z systemem Windows (poprzednia wersja)
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Windows IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3b7f6f63953ba09e57e4586c698e16b9abb8aa1c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555282"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption z usługą Azure AD dla maszyn wirtualnych z systemem Windows (poprzednia wersja)

**Nowa wersja Azure Disk Encryption eliminuje wymóg udostępniania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje włączania szyfrowania dysków maszyn wirtualnych przy użyciu nowej wersji, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](disk-encryption-windows.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal utrzymywane przy użyciu składni usługi AAD.**


Można włączyć wiele scenariuszy szyfrowania dysku, a kroki mogą się różnić w zależności od scenariusza. W poniższych sekcjach opisano scenariusze bardziej szczegółowo dla maszyn wirtualnych z systemem Windows IaaS. Przed rozpoczęciem korzystania z szyfrowania dysków należy wykonać [Azure Disk Encryption wymagania wstępne](disk-encryption-overview-aad.md) . 


>[!IMPORTANT]
> - Przed zaszyfrowaniem dysków należy [wykonać migawkę](snapshot-copy-managed-disk.md) i/lub utworzyć kopię zapasową. Kopie zapasowe zapewniają opcję odzyskiwania, jeśli podczas szyfrowania wystąpi nieoczekiwany błąd. Maszyny wirtualne z dyskami zarządzanymi wymagają utworzenia kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć [polecenia cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania szyfrowanych maszyn wirtualnych, zobacz [wykonywanie kopii zapasowych i przywracanie zaszyfrowanej maszyny wirtualnej platformy Azure](../../backup/backup-azure-vms-encryption.md). 
>
> - Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Włącz szyfrowanie na nowych maszynach wirtualnych IaaS utworzonych na podstawie portalu Marketplace
Szyfrowanie dysków można włączyć na nowej maszynie wirtualnej z systemem IaaS z witryny Marketplace na platformie Azure przy użyciu szablonu Menedżer zasobów. Szablon tworzy nową zaszyfrowaną maszynę wirtualną z systemem Windows przy użyciu obrazu z galerii systemu Windows Server 2012.

1. Na stronie [szablon Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)kliknij pozycję **Wdróż na platformie Azure**.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, warunki prawne i umowę. Kliknij przycisk **Kup** , aby WDROŻYĆ nową maszynę wirtualną IaaS, w której włączono szyfrowanie.

3. Po wdrożeniu szablonu sprawdź stan szyfrowania maszyny wirtualnej przy użyciu preferowanej metody:
     - Sprawdź przy użyciu interfejsu wiersza polecenia platformy Azure, używając polecenie [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Sprawdź przy użyciu Azure PowerShell za pomocą polecenia cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Wybierz maszynę wirtualną, a następnie kliknij pozycję **dyski** w obszarze **Ustawienia** , aby sprawdzić stan szyfrowania w portalu. Na wykresie w obszarze **szyfrowanie** zobaczysz, czy jest włączony. 
           ![Azure Portal — włączono szyfrowanie dysków](../media/disk-encryption/disk-encryption-fig2.png)

W poniższej tabeli wymieniono parametry szablonu Menedżer zasobów dla nowych maszyn wirtualnych w scenariuszu portalu Marketplace przy użyciu identyfikatora klienta usługi Azure AD:

| Parametr | Opis | 
| --- | --- |
| adminUserName | Nazwa użytkownika administratora dla maszyny wirtualnej. |
| adminPassword | Hasło administratora dla maszyny wirtualnej. |
| newStorageAccountName | Nazwa konta magazynu do przechowywania dysków VHD systemu operacyjnego i danych. |
| vmSize | Rozmiar maszyny wirtualnej. Obecnie obsługiwane są tylko serie w warstwach A, D i G. |
| virtualNetworkName | Nazwa sieci wirtualnej, do której powinna należeć karta sieciowa maszyny wirtualnej. |
| subnetName | Nazwa podsieci w sieci wirtualnej, do której powinna należeć karta sieciowa maszyny wirtualnej. |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, który ma uprawnienia do zapisywania wpisów tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, który ma uprawnienia do zapisywania wpisów tajnych w magazynie kluczy. |
| keyVaultURL | Adres URL magazynu kluczy, do którego powinien zostać przekazany klucz funkcji BitLocker. Można uzyskać za pomocą polecenia cmdlet `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` lub interfejsu wiersza polecenia platformy Azure `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używany do szyfrowania wygenerowanego klucza funkcji BitLocker (opcjonalnie). </br> </br>KeyEncryptionKeyURL jest opcjonalnym parametrem. Możesz wprowadzić własne KEK, aby dodatkowo chronić klucz szyfrowania danych (hasło tajne hasła) w magazynie kluczy. |
| keyVaultResourceGroup | Grupa zasobów magazynu kluczy. |
| vmName | Nazwa maszyny wirtualnej, na której ma zostać wykonana operacja szyfrowania. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Włącz szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS Windows
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. W poniższych sekcjach szczegółowo opisano sposób włączania Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Włącz szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych za pomocą Azure PowerShell 
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby włączyć szyfrowanie na działającej maszynie wirtualnej IaaS na platformie Azure. Aby uzyskać informacje na temat włączania szyfrowania przy użyciu Azure Disk Encryption za pomocą poleceń cmdlet programu PowerShell, zobacz wpisy w blogu [eksplorowanie Azure Disk Encryption z Azure PowerShell-część 1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) i [Eksplorowanie Azure Disk Encryption z Azure PowerShell — część 2](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2).

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet. Grupa zasobów, maszyna wirtualna, Magazyn kluczy, aplikacja usługi AAD i klucz tajny klienta powinny zostać już utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID i my-AAD-Client-Secret wartościami.
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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Szyfruj uruchomioną maszynę wirtualną za pomocą KEK, aby otoczyć klucz tajny klienta:** Azure Disk Encryption pozwala określić istniejący klucz w magazynie kluczy, aby zawijał klucze tajne szyfrowania dysku, które zostały wygenerowane podczas włączania szyfrowania. W przypadku określenia klucza szyfrowania klucza Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisem w Key Vault. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Włączanie szyfrowania istniejących lub uruchomionych maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj polecenia [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

- **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Szyfruj uruchomioną maszynę wirtualną za pomocą KEK, aby otoczyć klucz tajny klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy] </br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Korzystanie z szablonu Menedżer zasobów
Szyfrowanie dysków można włączyć na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS na platformie Azure przy użyciu [szablonu Menedżer zasobów, aby zaszyfrować działającą maszynę wirtualną](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)z systemem Windows.


1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wdróż na platformie Azure**.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, warunki prawne i umowę. Kliknij przycisk **Kup** , aby włączyć szyfrowanie na istniejącej lub URUCHOMIONEJ maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono Menedżer zasobów parametry szablonu dla istniejących lub uruchomionych maszyn wirtualnych, które używają identyfikatora klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, który ma uprawnienia do zapisywania wpisów tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, który ma uprawnienia do zapisywania wpisów tajnych w magazynie kluczy. |
| Nazwakluczamagazynu | Nazwa magazynu kluczy, do którego należy przekazać klucz funkcji BitLocker. Można to zrobić za pomocą polecenia cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` lub interfejsu CLI platformy Azure `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używany do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny w przypadku wybrania opcji **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz pozycję **KEK** na liście rozwijanej UseExistingKek, musisz wprowadzić wartość _keyEncryptionKeyURL_ . |
| liczba woluminów | Typ woluminu, na którym jest wykonywana operacja szyfrowania. Prawidłowe wartości to _system operacyjny_, _dane_ i _wszystkie_. |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Zwiększ ten numer wersji za każdym razem, gdy na tej samej maszynie wirtualnej jest wykonywana operacja szyfrowania dysku. |
| vmName | Nazwa maszyny wirtualnej, na której ma zostać wykonana operacja szyfrowania. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nowe maszyny wirtualne IaaS utworzone z poziomu dysków VHD szyfrowanych przez klienta i kluczy szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Menedżer zasobów, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. W poniższych sekcjach szczegółowo opisano Menedżer zasobów szablonu i poleceń interfejsu wiersza polecenia. 

Aby przygotować wstępnie zaszyfrowane obrazy, które mogą być używane na platformie Azure, należy wykonać instrukcje zawarte w dodatku. Po utworzeniu obrazu możesz wykonać czynności opisane w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"></a> Szyfruj maszyny wirtualne z wstępnie zaszyfrowanymi dyskami VHD za pomocą Azure PowerShell
Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym przy użyciu polecenia cmdlet programu PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). W poniższym przykładzie przedstawiono kilka typowych parametrów. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie na nowo dodanym dysku z danymi
[Nowy dysk można dodać do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](attach-disk-ps.md)lub [Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu Azure PowerShell
 W przypadku szyfrowania nowego dysku dla maszyn wirtualnych z systemem Windows przy użyciu programu PowerShell należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. W niektórych przypadkach nowo dodany dysk danych może być zaszyfrowany automatycznie przez rozszerzenie Azure Disk Encryption. Autoszyfrowanie jest zwykle wykonywane, gdy maszyna wirtualna zostanie ponownie uruchomiona po przejściu nowego dysku w tryb online. Zwykle jest to spowodowane tym, że dla typu woluminu została określona wartość "All", gdy wcześniej uruchomiono szyfrowanie dysku na maszynie wirtualnej. Jeśli funkcja autoszyfrowania występuje na nowo dodanym dysku danych, zalecamy ponowne uruchomienie Set-AzVmDiskEncryptionExtension polecenia cmdlet z nową wersją sekwencji. Jeśli nowy dysk danych jest szyfrowany jako szyfrowany i nie chcesz go zaszyfrować, najpierw Odszyfruj wszystkie dyski, a następnie Zaszyfruj ponownie przy użyciu nowej wersji sekwencji określającej system operacyjny dla typu woluminu. 
 

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension polecenie cmdlet. Grupa zasobów, maszyna wirtualna, Magazyn kluczy, aplikacja usługi AAD i klucz tajny klienta powinny zostać już utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID i my-AAD-Client-Secret wartościami. Ten przykład używa "All" dla parametru-Volumetype, który obejmuje woluminy systemu operacyjnego i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru-Volumetype. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Szyfruj uruchomioną maszynę wirtualną za pomocą KEK, aby otoczyć klucz tajny klienta:** Azure Disk Encryption pozwala określić istniejący klucz w magazynie kluczy, aby zawijał klucze tajne szyfrowania dysku, które zostały wygenerowane podczas włączania szyfrowania. W przypadku określenia klucza szyfrowania klucza Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisem w Key Vault. Ten przykład używa "All" dla parametru-Volumetype, który obejmuje woluminy systemu operacyjnego i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru-Volumetype. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu interfejsu wiersza polecenia platformy Azure
  Polecenie interfejsu wiersza polecenia platformy Azure automatycznie udostępni nową wersję sekwencji po uruchomieniu polecenia, aby włączyć szyfrowanie. Dopuszczalne wartości dla parametru Volume-yype to wszystkie, system operacyjny i dane. Może zajść konieczność zmiany parametru typu woluminu na system operacyjny lub dane, jeśli szyfrowany jest tylko jeden typ dysku dla maszyny wirtualnej. W przykładach użyto "All" dla parametru typu woluminu. 

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Szyfruj uruchomioną maszynę wirtualną za pomocą KEK, aby otoczyć klucz tajny klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Włącz szyfrowanie przy użyciu uwierzytelniania opartego na certyfikatach klienta usługi Azure AD.
Uwierzytelniania certyfikatu klienta można użyć z KEK lub bez niego. Przed użyciem skryptów programu PowerShell należy wcześniej przesłać certyfikat do magazynu kluczy i wdrożyć go na maszynie wirtualnej. Jeśli używasz KEK, KEK powinien już istnieć. Aby uzyskać więcej informacji, zobacz sekcję  [uwierzytelnianie oparte na certyfikatach dla usługi Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) w artykule dotyczącym wymagań wstępnych.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Włącz szyfrowanie przy użyciu uwierzytelniania opartego na certyfikatach z Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Włącz szyfrowanie przy użyciu uwierzytelniania opartego na certyfikatach i KEK z Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Wyłącz szyfrowanie
Szyfrowanie można wyłączyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Menedżer zasobów. 

- **Wyłącz szyfrowanie dysków przy użyciu Azure PowerShell:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie przy użyciu szablonu Menedżer zasobów:** 

    1. Kliknij pozycję **Wdróż na platformie Azure** , korzystając z szablonu [wyłącz szyfrowanie dysków na URUCHOMIONYM szablonie maszyny wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)  .
    2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, warunki prawne i umowę.
    3.  Kliknij przycisk **Kup** , aby wyłączyć szyfrowanie dysków na działającej maszynie wirtualnej z systemem Windows. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
