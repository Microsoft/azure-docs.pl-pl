---
title: Szyfrowanie dysków platformy Azure za pomocą usługi Azure AD dla maszyn wirtualnych z systemem Windows (poprzednia wersja)
description: Ten artykuł zawiera instrukcje dotyczące włączania szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych systemu Windows IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8677fa2620c1edc646dcffe120938f03fd13a0e5
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085625"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Szyfrowanie dysków platformy Azure za pomocą usługi Azure AD dla maszyn wirtualnych z systemem Windows (poprzednia wersja)

**Nowa wersja szyfrowania dysków platformy Azure eliminuje konieczność dostarczania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysku maszyny Wirtualnej. W nowej wersji nie są już wymagane do podawania poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje włączania szyfrowania dysku maszyn wirtualnych przy użyciu nowej wersji, zobacz [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych systemu Windows](disk-encryption-windows.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal obsługiwane ze składnią usługi AAD.**


Można włączyć wiele scenariuszy szyfrowania dysku, a kroki mogą się różnić w zależności od scenariusza. W poniższych sekcjach opisano scenariusze bardziej szczegółowo dla maszyn wirtualnych IaaS systemu Windows. Przed użyciem szyfrowania dysku należy wykonać [wymagania wstępne szyfrowania dysków platformy Azure.](disk-encryption-overview-aad.md) 


>[!IMPORTANT]
> - Należy [wykonać migawkę](snapshot-copy-managed-disk.md) i/lub utworzyć kopię zapasową, zanim dyski zostaną zaszyfrowane. Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa, jeśli wystąpi nieoczekiwany błąd podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć [polecenia cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) do szyfrowania dysków zarządzanych, określając parametr -skipVmBackup. Aby uzyskać więcej informacji na temat sposobu 30-100 6000000000000000000000000000000000000000000000000000000000000000000 [Back up and restore encrypted Azure VM](../../backup/backup-azure-vms-encryption.md) 
>
> - Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Włączanie szyfrowania na nowych maszynach wirtualnych IaaS utworzonych w portalu Marketplace
Szyfrowanie dysku można włączyć na nowej maszynie wirtualnej systemu Windows IaaS z portalu Marketplace na platformie Azure przy użyciu szablonu Menedżera zasobów. Szablon tworzy nową zaszyfrowaną maszynę wirtualną systemu Windows przy użyciu obrazu galerii systemu Windows Server 2012.

1. W [szablonie Menedżera zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)kliknij pozycję **Wdrażanie na platformie Azure**.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, warunki prawne i umowę. Kliknij **przycisk Zakup,** aby wdrożyć nową maszynę wirtualną IaaS, w której szyfrowanie jest włączone.

3. Po wdrożeniu szablonu sprawdź stan szyfrowania maszyny Wirtualnej przy użyciu preferowanej metody:
     - Sprawdź za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [az vm szyfrowania show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Sprawdź za pomocą programu Azure PowerShell przy użyciu polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Wybierz maszynę wirtualną, a następnie kliknij **dyski** pod nagłówkiem **Ustawienia,** aby zweryfikować stan szyfrowania w portalu. Na wykresie w obszarze **Szyfrowanie**zobaczysz, czy jest włączona. 
           ![Portal Azure — włączone szyfrowanie dysków](../media/disk-encryption/disk-encryption-fig2.png)

W poniższej tabeli wymieniono parametry szablonu Menedżera zasobów dla nowych maszyn wirtualnych ze scenariusza marketplace przy użyciu identyfikatora klienta usługi Azure AD:

| Parametr | Opis | 
| --- | --- |
| adminUserName | Nazwa użytkownika administratora maszyny wirtualnej. |
| adminPassword | Hasło administratora dla maszyny wirtualnej. |
| nowyName konta administracyjnego | Nazwa konta magazynu do przechowywania os i danych VHD. |
| vmSize | Rozmiar maszyny Wirtualnej. Obecnie obsługiwane są tylko serie Standard A, D i G. |
| virtualNetworkName | Nazwa sieci wirtualnej, do której powinna należeć karta wirtualna. |
| podszecina | Nazwa podsieci w sieci wirtualnej, do której powinna należeć karta wirtualna. |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisywania wpisów tajnych do magazynu kluczy. |
| Protokół AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisywania wpisów tajnych do magazynu kluczy. |
| keyVaultURL | Adres URL magazynu kluczy, do których należy przekazać klucz funkcji BitLocker. Można go uzyskać za pomocą `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` polecenia cmdlet lub interfejsu wiersza polecenia platformy Azure`az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używanego do szyfrowania wygenerowanego klucza funkcji BitLocker (opcjonalnie). </br> </br>KeyEncryptionKeyURL jest parametrem opcjonalnym. Możesz przynieść własne KEK do dalszej ochrony klucza szyfrowania danych (klucz hasło tajne) w magazynie kluczy. |
| keyVaultResourceGroup | Grupa zasobów magazynu kluczy. |
| vmName | Nazwa maszyny Wirtualnej, na których ma być wykonywana operacja szyfrowania. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a>Włączanie szyfrowania na istniejących lub uruchomionych maszynach wirtualnych systemu IaaS systemu Windows
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu, polecenia cmdlet programu PowerShell lub polecenia interfejsu wiersza polecenia. W poniższych sekcjach opisano bardziej szczegółowo, jak włączyć szyfrowanie dysków platformy Azure. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a>Włączanie szyfrowania na istniejących lub uruchomionych maszynach wirtualnych za pomocą programu Azure PowerShell 
Polecenie cmdlet [Set-AzVMMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) umożliwia szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure. Aby uzyskać informacje dotyczące włączania szyfrowania za pomocą szyfrowania dysków platformy Azure przy użyciu poleceń cmdlet programu PowerShell, zobacz wpisy w blogu [Eksploruj szyfrowanie dysków platformy Azure za pomocą programu Azure PowerShell — część 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) i [eksploruj szyfrowanie dysków platformy Azure za pomocą programu Azure PowerShell — część 2](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja AAD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret swoimi wartościami.
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
- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu KEK do zawijania klucza tajnego klienta:** Usługa Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy w celu zawijania kluczy tajnych szyfrowania dysku, które zostały wygenerowane podczas włączania szyfrowania. Po określeniu klucza szyfrowania klucza szyfrowanie platformy Azure Disk Encryption używa tego klucza do zawijania klucza tajne szyfrowania przed zapisaniem do usługi Key Vault. 

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
   > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny Wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysku:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [Disable-AzureRmVMIskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Włączanie szyfrowania na istniejących lub uruchomionych maszynach wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
Użyj polecenia [włączania szyfrowania az vm,](/cli/azure/vm/encryption#az-vm-encryption-enable) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu KEK do zawijania klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny Wirtualnej IaaS, użyj polecenia [az vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [az vm encryption disable.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Korzystanie z szablonu Menedżera zasobów
Szyfrowanie dysku można włączyć na istniejących lub uruchomionych maszynach wirtualnych systemu IaaS z systemem Windows na platformie Azure przy użyciu [szablonu Menedżera zasobów do szyfrowania uruchomionej maszyny Wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. W szablonie szybki start platformy Azure kliknij pozycję **Wdrażanie na platformie Azure**.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, warunki prawne i umowę. Kliknij **przycisk Zakup,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono parametry szablonu Menedżera zasobów dla istniejących lub uruchomionych maszyn wirtualnych, które używają identyfikatora klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, który ma uprawnienia do zapisywania wpisów tajnych do magazynu kluczy. |
| Protokół AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, który ma uprawnienia do zapisywania wpisów tajnych do magazynu kluczy. |
| keyVaultName (nazwa klucza) | Nazwa magazynu kluczy, do których należy przekazać klucz funkcji BitLocker. Można go uzyskać za pomocą `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` polecenia cmdlet lub interfejsu wiersza polecenia platformy Azure`az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używanego do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny, jeśli **wybierzesz nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na liście rozwijanej UseExistingKek, musisz wprowadzić wartość _keyEncryptionKeyURL._ |
| volumeType | Typ woluminu, na który wykonywana jest operacja szyfrowania. Prawidłowe wartości _to OS_, _Dane_i _Wszystkie_. |
| sequenceVersion (wersja sekwencji) | Sekwencja wersji operacji funkcji BitLocker. Przyrost ten numer wersji za każdym razem, gdy operacja szyfrowania dysku jest wykonywana na tej samej maszynie wirtualnej. |
| vmName | Nazwa maszyny Wirtualnej, na których ma być wykonywana operacja szyfrowania. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nowe maszyny wirtualne IaaS utworzone na podstawie zaszyfrowanych przez klienta kluczy VHD i szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Menedżera zasobów, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. W poniższych sekcjach opisano bardziej szczegółowo szablon Menedżera zasobów i polecenia interfejsu wiersza polecenia. 

Użyj instrukcji zawartych w dodatku do przygotowywania wstępnie zaszyfrowanych obrazów, które mogą być używane na platformie Azure. Po utworzeniu obrazu można użyć kroków w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowywanie wstępnie zaszyfrowanego dysku VHD systemu Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> Szyfrowanie maszyn wirtualnych za pomocą wstępnie zaszyfrowanych dysków wirtualnych za pomocą programu Azure PowerShell
Szyfrowanie dysku można włączyć na zaszyfrowanej dysku VHD za pomocą polecenia cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)programu PowerShell . Poniższy przykład zawiera kilka typowych parametrów. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włączanie szyfrowania na nowo dodanym dysku danych
Nowy dysk można dodać do maszyny Wirtualnej systemu Windows za pomocą programu [PowerShell](attach-disk-ps.md)lub [za pośrednictwem portalu Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włączanie szyfrowania na nowo dodanym dysku za pomocą programu Azure PowerShell
 Podczas używania programu Powershell do szyfrowania nowego dysku dla maszyn wirtualnych systemu Windows należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. W niektórych przypadkach nowo dodany dysk danych może być szyfrowany automatycznie przez rozszerzenie szyfrowania dysków azure. Automatyczne szyfrowanie zwykle występuje, gdy maszyna wirtualna uruchamia się ponownie po przejściu nowego dysku do trybu online. Jest to zazwyczaj spowodowane, ponieważ "Wszystkie" został określony dla typu woluminu, gdy szyfrowanie dysku wcześniej uruchomiony na maszynie wirtualnej. Jeśli automatyczne szyfrowanie występuje na nowo dodanym dysku danych, zalecamy ponowne uruchomienie polecenia cmdlet Set-AzVmDiskEncryptionExtension z nową wersją sekwencji. Jeśli nowy dysk danych jest automatycznie szyfrowany i nie chcesz być szyfrowany, najpierw odszyfruj wszystkie dyski, a następnie ponownie zaszyfruj za pomocą nowej wersji sekwencji określającej system operacyjny dla typu woluminu. 
 

-  **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja AAD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret swoimi wartościami. W tym przykładzie użyto "Wszystkie" dla parametru -VolumeType, który zawiera zarówno woluminy systemu operacyjnego, jak i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru -VolumeType. 

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
- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu KEK do zawijania klucza tajnego klienta:** Usługa Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy w celu zawijania kluczy tajnych szyfrowania dysku, które zostały wygenerowane podczas włączania szyfrowania. Po określeniu klucza szyfrowania klucza szyfrowanie platformy Azure Disk Encryption używa tego klucza do zawijania klucza tajne szyfrowania przed zapisaniem do usługi Key Vault. W tym przykładzie użyto "Wszystkie" dla parametru -VolumeType, który zawiera zarówno woluminy systemu operacyjnego, jak i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru -VolumeType. 

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
    > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włączanie szyfrowania na nowo dodanym dysku za pomocą interfejsu wiersza polecenia platformy Azure
  Polecenie interfejsu wiersza polecenia platformy Azure automatycznie zapewni nową wersję sekwencji po uruchomieniu polecenia, aby włączyć szyfrowanie. Dopuszczalne wartości dla parametru volume-yype to Wszystkie, SYSTEM OPERACYJNY i Dane. Może być konieczna zmiana parametru typu woluminu na system operacyjny lub dane, jeśli szyfrujesz tylko jeden typ dysku dla maszyny Wirtualnej. W przykładach użyto "Wszystkie" dla parametru typu woluminu. 

-  **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu KEK do zawijania klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Włącz szyfrowanie przy użyciu uwierzytelniania opartego na certyfikatach klienta usługi Azure AD.
Uwierzytelnianie certyfikatu klienta można używać z kek lub bez. Przed użyciem skryptów programu PowerShell certyfikat powinien być już przekazany do magazynu kluczy i wdrożony na maszynie wirtualnej. Jeśli używasz KEK też, KEK powinien już istnieć. Aby uzyskać więcej informacji, zobacz sekcję [uwierzytelniania opartego na certyfikatach dla usługi Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) w artykule wymagania wstępne.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Włączanie szyfrowania przy użyciu uwierzytelniania opartego na certyfikatach za pomocą programu Azure PowerShell

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
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Włączanie szyfrowania przy użyciu uwierzytelniania opartego na certyfikatach i kek z usługą Azure PowerShell

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
Szyfrowanie można wyłączyć przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Usługi Resource Manager. 

- **Wyłącz szyfrowanie dysku za pomocą programu Azure PowerShell:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [Disable-AzureRmVMIskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj polecenia [az vm encryption disable.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie za pomocą szablonu Menedżera zasobów:** 

    1. Kliknij **pozycję Wdrażanie na platformie Azure** z [przycisku Wyłącz szyfrowanie dysku w uruchomionym szablonie maszyny Wirtualnej systemu Windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, warunki prawne i umowę.
    3.  Kliknij **przycisk Zakup,** aby wyłączyć szyfrowanie dysku na uruchomionej maszynie Wirtualnej systemu Windows. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md)
