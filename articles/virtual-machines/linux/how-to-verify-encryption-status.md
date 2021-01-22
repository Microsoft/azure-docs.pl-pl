---
title: Sprawdź stan szyfrowania dla systemu Linux — Azure Disk Encryption
description: Ten artykuł zawiera instrukcje dotyczące weryfikowania stanu szyfrowania na poziomach platformy i systemu operacyjnego.
author: kailashmsft
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 42b1aed2f6c66dbfc0f04759b232855f3b7f0a2a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676822"
---
# <a name="verify-encryption-status-for-linux"></a>Sprawdź stan szyfrowania dla systemu Linux 

Zakres tego artykułu polega na sprawdzeniu stanu szyfrowania maszyny wirtualnej przy użyciu różnych metod: Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub systemu operacyjnego maszyny wirtualnej. 

Podczas szyfrowania lub po nim można sprawdzić stan szyfrowania, wykonując następujące polecenie:

- Sprawdzanie dysków dołączonych do określonej maszyny wirtualnej. 
- Przeszukiwanie ustawień szyfrowania na każdym dysku, niezależnie od tego, czy dysk jest dołączony, czy niedołączony.

Ten scenariusz ma zastosowanie do Azure Disk Encryption rozszerzeń z podwójnym przebiegiem i jednokrotnym przekazaniem. Dystrybucje systemu Linux są jedynym środowiskiem w tym scenariuszu.

>[!NOTE] 
>Stosujemy zmienne w całym artykule. Zastąp odpowiednie wartości.

## <a name="portal"></a>Portal

W Azure Portal w sekcji **rozszerzenia** wybierz rozszerzenie Azure Disk Encryption na liście. Informacje dotyczące **komunikatu o stanie** wskazują bieżący stan szyfrowania:

![Sprawdź, czy stan, wersja i komunikat o stanie zostały wyróżnione w portalu](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Na liście rozszerzeń zobaczysz odpowiednią wersję rozszerzenia Azure Disk Encryption. Wersja 0. x odpowiada Azure Disk Encryption podwójnego przebiegu, a wersja 1. x odpowiada Azure Disk Encryption pojedynczym przebiegu.

Aby uzyskać więcej szczegółów, wybierz rozszerzenie, a następnie wybierz pozycję **Wyświetl szczegółowy stan**. Szczegółowy stan procesu szyfrowania jest wyświetlany w formacie JSON.

![Sprawdzanie portalu z wyróżnionym linkiem "Wyświetl stan szczegółów"](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Szczegółowy stan w formacie JSON](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Innym sposobem sprawdzenia stanu szyfrowania jest przejrzenie sekcji **Ustawienia dysku** .

![Stan szyfrowania dysku systemu operacyjnego i dysków z danymi](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Ten stan oznacza, że dyski mają ustawione ustawienia szyfrowania, a nie zaszyfrowane na poziomie systemu operacyjnego.
>
> Po zaprojektowaniu dyski są najpierw znakowane i szyfrowane w późniejszym czasie. Jeśli proces szyfrowania zakończy się niepowodzeniem, dyski mogą kończyć się znakiem, ale nie zaszyfrowanym. 
>
> Aby sprawdzić, czy dyski są rzeczywiście zaszyfrowane, możesz dwukrotnie sprawdzić szyfrowanie każdego dysku na poziomie systemu operacyjnego.

## <a name="powershell"></a>PowerShell

Aby sprawdzić *ogólny* stan szyfrowania zaszyfrowanej maszyny wirtualnej, można użyć następujących poleceń programu PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Ogólny stan szyfrowania w programie PowerShell](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Ustawienia szyfrowania można przechwycić z każdego dysku za pomocą następujących poleceń programu PowerShell.

### <a name="single-pass"></a>Pojedynczy przebieg
W jednym przebiegu ustawienia szyfrowania są znakowane na wszystkich dyskach (system operacyjny i dane). Ustawienia szyfrowania dla dysku systemu operacyjnego można przechwycić w jednym przebiegu w następujący sposób:

```powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Ustawienia szyfrowania dla dysku systemu operacyjnego](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Jeśli dysk nie ma sygnatury ustawień szyfrowania, dane wyjściowe będą puste:

![Puste dane wyjściowe](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Użyj następujących poleceń, aby przechwycić ustawienia szyfrowania dla dysków danych:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Ustawienia szyfrowania dla dysków z danymi](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Podwójna Pass
W przypadku podwójnego przebiegu ustawienia szyfrowania są znakowane w modelu maszyny wirtualnej, a nie na poszczególnych dyskach.

Aby sprawdzić, czy ustawienia szyfrowania zostały oznaczone przy użyciu dwóch przebiegów, użyj następujących poleceń:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Ustawienia szyfrowania w podwójnym przebiegu](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Niedołączone dyski

Sprawdź ustawienia szyfrowania dysków, które nie są dołączone do maszyny wirtualnej.

### <a name="managed-disks"></a>Dyski zarządzane
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby sprawdzić *ogólny* stan szyfrowania zaszyfrowanej maszyny wirtualnej, można użyć następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Ogólny stan szyfrowania z poziomu wiersza polecenia platformy Azure ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Pojedynczy przebieg
Można sprawdzić poprawność ustawień szyfrowania dla każdego dysku, korzystając z następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Ustawienia szyfrowania danych](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Jeśli dysk nie ma sygnatury ustawień szyfrowania, zobaczysz, że dysk tekstowy **nie jest szyfrowany**.

Użyj następujących poleceń, aby uzyskać szczegółowe ustawienia stanu i szyfrowania.

Dysk systemu operacyjnego:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Szczegółowy stan i ustawienia szyfrowania dla dysku systemu operacyjnego](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Dyski danych:

```azurecli
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Szczegółowe ustawienia stanu i szyfrowania dla dysków danych](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Podwójna Pass

```azurecli
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Ogólne ustawienia szyfrowania dla podwójnego przekazywania za pośrednictwem interfejsu wiersza polecenia platformy Azure](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

Ustawienia szyfrowania można także sprawdzić w profilu magazynu modelu maszyny wirtualnej dysku systemu operacyjnego:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Profil maszyny wirtualnej dla podwójnego przebiegu za pośrednictwem interfejsu wiersza polecenia platformy Azure](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Niedołączone dyski

Sprawdź ustawienia szyfrowania dysków, które nie są dołączone do maszyny wirtualnej.

### <a name="managed-disks"></a>Dyski zarządzane

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Dyski niezarządzane

Dyski niezarządzane to pliki VHD, które są przechowywane jako stronicowe obiekty blob na kontach usługi Azure Storage.

Aby uzyskać szczegółowe informacje dotyczące określonego dysku, należy podać:

- Identyfikator konta magazynu zawierającego dysk.
- Parametry połączenia dla danego konta magazynu.
- Nazwa kontenera, w którym jest przechowywany dysk.
- Nazwa dysku.

To polecenie wyświetla listę wszystkich identyfikatorów dla wszystkich kont magazynu:

```azurecli
az storage account list --query [].[id] -o tsv
```
Identyfikatory kont magazynu są wymienione w następującej postaci:

/subscriptions/ \<subscription id> /ResourceGroups/ \<resource group name> /providers/Microsoft.Storage/storageAccounts/\<storage account name>

Wybierz odpowiedni identyfikator i Zapisz go na zmiennej:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

To polecenie pobiera parametry połączenia dla jednego konkretnego konta magazynu i zapisuje je w zmiennej:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Następujące polecenie wyświetla listę wszystkich kontenerów w ramach konta magazynu:
```azurecli
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Kontener używany dla dysków ma zwykle nazwę "VHD".

Zapisz nazwę kontenera w zmiennej: 
```bash
ContainerName="name of the container"
```

Użyj tego polecenia, aby wyświetlić listę wszystkich obiektów BLOB w określonym kontenerze:
```azurecli 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Wybierz dysk, który chcesz wyszukać i zapisać jego nazwę na zmiennej:
```bash
DiskName="diskname.vhd"
```
Wykonaj zapytanie dotyczące ustawień szyfrowania dysku:
```azurecli
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>System operacyjny
Sprawdź, czy partycje dysku danych są szyfrowane (i nie jest to dysk systemu operacyjnego).

Gdy partycja lub dysk jest szyfrowany, jest on wyświetlany jako typ **Crypt** . Gdy nie jest szyfrowany, jest on wyświetlany jako typ **części/dysku** .

```bash
lsblk
```

![Warstwa Crypt systemu operacyjnego dla partycji](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Więcej informacji można uzyskać, korzystając z następującego wariantu **lsblk** . 

Zobaczysz warstwę typu **Crypt** , która jest instalowana przez rozszerzenie. W poniższym przykładzie przedstawiono woluminy logiczne i normalne dyski z **kryptografią \_ luks fstype**.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Warstwa Crypt systemu operacyjnego dla woluminów logicznych i zwykłych dysków](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Dodatkowy krok polega na sprawdzeniu, czy dysk danych ma załadowane klucze:

```bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

```bash
cryptsetup luksDump /dev/sdd1
```

Można też sprawdzić, które urządzenia **DM** są wyświetlane jako **Crypt**:

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
