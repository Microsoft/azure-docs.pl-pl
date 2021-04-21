---
title: Azure Disk Encryption z usługą Azure AD dla maszyn wirtualnych z systemem Windows (poprzednia wersja)
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych IaaS z systemem Windows.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 081ead434dbdc3b9c348e3fa35068a638bab6e62
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776865"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption z usługą Azure AD dla maszyn wirtualnych z systemem Windows (poprzednia wersja)

**Nowa wersja usługi Azure Disk Encryption eliminuje wymaganie podania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje dotyczące włączania szyfrowania dysków maszyny wirtualnej przy użyciu nowej wersji, zobacz Azure Disk Encryption dla usługi [VMS z systemem Windows.](disk-encryption-windows.md) Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal obsługiwane przy użyciu składni usługi AAD.**


Można włączyć wiele scenariuszy szyfrowania dysków, a kroki mogą się różnić w zależności od scenariusza. W poniższych sekcjach bardziej szczegółowo opisano scenariusze dotyczące maszyn wirtualnych IaaS z systemem Windows. Aby można było korzystać z szyfrowania dysków, [Azure Disk Encryption wymagania](disk-encryption-overview-aad.md) wstępne muszą zostać spełnione. 


>[!IMPORTANT]
> - Przed [zaszyfrowaniem dysków](snapshot-copy-managed-disk.md) należy utworzyć migawkę i/lub utworzyć kopię zapasową. Kopie zapasowe zapewniają możliwość opcji odzyskiwania w przypadku wystąpienia nieoczekiwanego błędu podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopii zapasowej przed rozpoczęciem szyfrowania. Po kopii zapasowej można użyć polecenia [cmdlet Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) aby zaszyfrować dyski zarządzane, określając parametr -skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn wirtualnych, zobacz Temat Back up and restore encrypted Azure VM (Back up and restore encrypted VM ) (Jak tworzyć i przywracać zaszyfrowane maszyny wirtualne [platformy Azure).](../../backup/backup-azure-vms-encryption.md) 
>
> - Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Włączanie szyfrowania na nowych maszyn wirtualnych IaaS utworzonych z poziomu witryny Marketplace
Szyfrowanie dysków na nowej maszynie wirtualnej IaaS z systemem Windows można włączyć z witryny Marketplace na platformie Azure przy użyciu Resource Manager wirtualnej. Szablon tworzy nową zaszyfrowaną maszynę wirtualną z systemem Windows przy użyciu obrazu z galerii systemu Windows Server 2012.

1. Na [szablonie Resource Manager kliknij](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)pozycję **Deploy to Azure (Wd wdrażaj na platformie Azure).**

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowę. Kliknij **pozycję Kup,** aby wdrożyć nową maszynę wirtualną IaaS, na której włączono szyfrowanie.

3. Po wdrożeniu szablonu sprawdź stan szyfrowania maszyny wirtualnej przy użyciu preferowanej metody:
     - Sprawdź za pomocą interfejsu wiersza polecenia platformy Azure za pomocą [polecenia az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Zweryfikuj Azure PowerShell za pomocą polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Wybierz maszynę wirtualną, a następnie kliknij pozycję **Dyski** pod **nagłówkiem Ustawienia,** aby zweryfikować stan szyfrowania w portalu. Na wykresie w obszarze **Szyfrowanie** zobaczysz, czy jest ono włączone. 
           ![Azure Portal — włączone szyfrowanie dysków](../media/disk-encryption/disk-encryption-fig2.png)

W poniższej tabeli wymieniono Resource Manager szablonu dla nowych maszyn wirtualnych ze scenariusza z witryny Marketplace przy użyciu identyfikatora klienta usługi Azure AD:

| Parametr | Opis | 
| --- | --- |
| adminUserName | Nazwa użytkownika administratora maszyny wirtualnej. |
| adminPassword | Hasło użytkownika administratora dla maszyny wirtualnej. |
| newStorageAccountName | Nazwa konta magazynu do przechowywania dysków VHD systemu operacyjnego i danych. |
| vmSize | Rozmiar maszyny wirtualnej. Obecnie obsługiwane są tylko serie Standard A, D i G. |
| virtualNetworkName | Nazwa sieci wirtualnej, do której powinna należeć karta sieciowa maszyny wirtualnej. |
| subnetName | Nazwa podsieci w sieci wirtualnej, do której powinna należeć karta sieciowa maszyny wirtualnej. |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu wpisów tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu wpisów tajnych w magazynie kluczy. |
| keyVaultURL | Adres URL magazynu kluczy, do który ma zostać przekazany klucz funkcji BitLocker. Można go pobrać przy użyciu polecenia cmdlet lub `(Get-AzKeyVault -VaultName "MyKeyVault&quot; -ResourceGroupName &quot;MyKeyVaultResourceGroupName").VaultURI` interfejsu wiersza polecenia platformy Azure `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używanego do szyfrowania wygenerowanego klucza funkcji BitLocker (opcjonalnie). </br> </br>KeyEncryptionKeyURL jest opcjonalnym parametrem. Możesz użyć własnego klucza szyfrowania kluczy, aby dodatkowo zabezpieczyć klucz szyfrowania danych (klucz tajny hasła) w magazynie kluczy. |
| keyVaultResourceGroup | Grupa zasobów magazynu kluczy. |
| vmName | Nazwa maszyny wirtualnej, na podstawie których ma zostać wykonana operacja szyfrowania. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Włączanie szyfrowania na istniejących lub uruchomionych maszyn wirtualnych IaaS z systemem Windows
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. W poniższych sekcjach bardziej szczegółowo opisano sposób włączania Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Włączanie szyfrowania istniejących lub uruchomionych maszyn wirtualnych przy użyciu Azure PowerShell 
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure. Aby uzyskać informacje o włączaniu szyfrowania za pomocą programu Azure Disk Encryption przy użyciu poleceń cmdlet programu PowerShell, zobacz wpisy w blogu Explore Azure Disk Encryption with Azure PowerShell - Part 1 (Eksplorowanie usługi Azure Disk Encryption za pomocą poleceń cmdlet programu PowerShell) w blogu [Explore Azure Disk Encryption with Azure PowerShell - Part 1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) (Eksplorowanie usługi Azure Disk Encryption za pomocą programu Azure PowerShell — część [2).](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2)

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension cmdlet. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja usługi AAD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret swoimi wartościami.
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
- **Zaszyfruj uruchamianą maszynę wirtualną przy użyciu klucza KEK w celu opakwania klucza tajnego klienta:** Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy w celu opakwania wpisów tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Po podano klucz szyfrowania klucza, Azure Disk Encryption używa tego klucza do opakowywania wpisów tajnych szyfrowania przed zapisem w Key Vault. 

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
   > Składnia wartości parametru disk-encryption-keyvault to ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa-magazynu-kluczy]</br> Składnia wartości parametru key-encryption-key to pełny identyfikator URI klucza szyfrowania klucza, jak w: https://[nazwa_magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Włączanie szyfrowania na istniejących lub uruchomionych maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj polecenia [az vm encryption enable,](/cli/azure/vm/encryption#az_vm_encryption_enable) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

- **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Zaszyfruj uruchamianą maszynę wirtualną przy użyciu klucza KEK w celu opakwania klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Składnia wartości parametru disk-encryption-keyvault to pełny ciąg identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu-kluczy] </br> Składnia dla wartości parametru key-encryption-key jest pełnym identyfikatorem URI klucza szyfrowania kluczy, jak w: https://[nazwa magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj [polecenia az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj [polecenia az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Korzystanie z Resource Manager szablonu
Szyfrowanie dysków można włączyć na istniejących lub działających maszynach wirtualnych IaaS z systemem Windows na platformie Azure przy użyciu szablonu Resource Manager do szyfrowania [uruchomionej maszyny wirtualnej z systemem Windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)


1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Deploy to Azure (Wd wdrażaj na platformie Azure).**

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowę. Kliknij **pozycję Kup,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono Resource Manager szablonu dla istniejących lub uruchomionych maszyn wirtualnych, które używają identyfikatora klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu wpisów tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu wpisów tajnych w magazynie kluczy. |
| keyVaultName | Nazwa magazynu kluczy, do który ma zostać przekazany klucz funkcji BitLocker. Można go pobrać za pomocą polecenia cmdlet lub `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` polecenia interfejsu wiersza polecenia platformy Azure `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używanego do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny, jeśli wybierzesz **pozycję nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na UseExistingKek listy rozwijanej, należy wprowadzić _wartość keyEncryptionKeyURL._ |
| typ woluminu | Typ woluminu, na który jest wykonywana operacja szyfrowania. Prawidłowe wartości to _OS,_ _Data_ i _All._ |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Zwiększa ten numer wersji za każdym razem, gdy operacja szyfrowania dysków jest wykonywana na tej samej maszynie wirtualnej. |
| vmName | Nazwa maszyny wirtualnej, na podstawie których ma zostać wykonana operacja szyfrowania. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nowe maszyny wirtualne IaaS utworzone na podstawie zaszyfrowanego przez klienta wirtualnego dysku twardego i kluczy szyfrowania
W tym scenariuszu szyfrowanie można włączyć przy użyciu szablonu Resource Manager, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. W poniższych sekcjach bardziej szczegółowo opisano Resource Manager szablonu i interfejsu wiersza polecenia. 

Skorzystaj z instrukcji w dodatku dotyczącym przygotowywania wstępnie zaszyfrowanych obrazów, które mogą być używane na platformie Azure. Po utworzeniu obrazu możesz wykonać kroki opisane w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"></a> Szyfrowanie maszyn wirtualnych wstępnie zaszyfrowanymi dyskami VHD za pomocą Azure PowerShell
Szyfrowanie dysków na zaszyfrowanym dysku VHD można włączyć za pomocą polecenia cmdlet programu PowerShell [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) Poniższy przykład zawiera kilka typowych parametrów. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włączanie szyfrowania na nowo dodanym dysku danych
Nowy dysk [można dodać do maszyny wirtualnej z](attach-disk-ps.md)systemem Windows przy użyciu programu PowerShell lub za [pośrednictwem Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włączanie szyfrowania na nowo dodanym dysku przy użyciu Azure PowerShell
 W przypadku szyfrowania nowego dysku dla maszyn wirtualnych z systemem Windows przy użyciu programu PowerShell należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. W niektórych przypadkach nowo dodany dysk danych może zostać automatycznie zaszyfrowany przez rozszerzenie Azure Disk Encryption danych. Automatyczne szyfrowanie zwykle występuje, gdy maszyna wirtualna jest uruchamiana ponownie, gdy nowy dysk jest w trybie online. Jest to zwykle spowodowane tym, że dla typu woluminu określono wartość "Wszystkie", gdy wcześniej na maszynie wirtualnej było stosowane szyfrowanie dysków. Jeśli automatyczne szyfrowanie występuje na nowo dodanym dysku danych, zalecamy uruchomienie polecenia cmdlet Set-AzVmDiskEncryptionExtension z nową wersją sekwencji. Jeśli nowy dysk danych jest automatycznie szyfrowany i nie chcesz go szyfrować, najpierw odszyfruj wszystkie dyski, a następnie ponownie szyfruj przy użyciu nowej wersji sekwencji, określając system operacyjny dla typu woluminu. 
 

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia Set-AzVMDiskEncryptionExtension cmdlet. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja usługi AAD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret swoimi wartościami. W tym przykładzie użyto wartości "All" dla parametru -VolumeType, który obejmuje woluminy systemu operacyjnego i danych. Jeśli chcesz zaszyfrować tylko wolumin systemu operacyjnego, użyj wartości "OS" dla parametru -VolumeType. 

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
- **Zaszyfruj uruchamianą maszynę wirtualną przy użyciu klucza KEK w celu opakwania klucza tajnego klienta:** Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy w celu opakwania wpisów tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Po podano klucz szyfrowania klucza, Azure Disk Encryption używa tego klucza do opakowywania wpisów tajnych szyfrowania przed zapisem w Key Vault. W tym przykładzie użyto wartości "All" dla parametru -VolumeType, który obejmuje woluminy systemu operacyjnego i danych. Jeśli chcesz zaszyfrować tylko wolumin systemu operacyjnego, użyj wartości "OS" dla parametru -VolumeType. 

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
    > Składnia wartości parametru disk-encryption-keyvault to pełny ciąg identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu-kluczy]</br> Składnia dla wartości parametru key-encryption-key jest pełnym identyfikatorem URI klucza szyfrowania kluczy, jak w: https://[nazwa magazynu_kluczy].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włączanie szyfrowania na nowo dodanym dysku przy użyciu interfejsu wiersza polecenia platformy Azure
  Polecenie interfejsu wiersza polecenia platformy Azure automatycznie udostępni nową wersję sekwencji po uruchomieniu polecenia w celu włączenia szyfrowania. Dopuszczalne wartości dla parametru volume-yype to Wszystkie, System operacyjny i Dane. Może być konieczne zmiana parametru typu woluminu na system operacyjny lub dane, jeśli szyfruje się tylko jeden typ dysku dla maszyny wirtualnej. W przykładach parametru typu woluminu używa się wartości "All". 

-  **Szyfrowanie uruchomionej maszyny wirtualnej przy użyciu klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Zaszyfruj uruchamianą maszynę wirtualną przy użyciu klucza KEK w celu opakwania klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Włącz szyfrowanie przy użyciu uwierzytelniania opartego na certyfikatach klienta usługi Azure AD.
Można użyć uwierzytelniania certyfikatu klienta z użyciem lub bez KEK. Przed rozpoczęciem korzystania ze skryptów programu PowerShell certyfikat powinien już zostać przekazany do magazynu kluczy i wdrożony na maszynie wirtualnej. Jeśli używasz również KEK, KEK powinien już istnieć. Aby uzyskać więcej informacji, zobacz  [sekcję Uwierzytelnianie](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) oparte na certyfikatach dla usługi Azure AD w artykule wymagań wstępnych.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Włączanie szyfrowania przy użyciu uwierzytelniania opartego na certyfikatach przy użyciu Azure PowerShell

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
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Włączanie szyfrowania przy użyciu uwierzytelniania opartego na certyfikatach i klucza szyfrowania kluczy przy użyciu Azure PowerShell

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

## <a name="disable-encryption"></a>Wyłączanie szyfrowania
Szyfrowanie można wyłączyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub Resource Manager szablonu. 

- **Wyłącz szyfrowanie dysków za pomocą Azure PowerShell:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj [polecenia az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie za pomocą Resource Manager szablonu:** 

    1. Kliknij **pozycję Deploy to Azure (Wd** wdrażaj na platformie Azure) w szablonie Disable disk encryption on running Windows VM (Wyłącz szyfrowanie [dysków na uruchomionej maszynie wirtualnej z systemem Windows).](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, postanowienia prawne i umowę.
    3.  Kliknij **pozycję Kup,** aby wyłączyć szyfrowanie dysków na uruchomionej maszynie wirtualnej z systemem Windows. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Azure Disk Encryption omówienie](disk-encryption-overview.md)
