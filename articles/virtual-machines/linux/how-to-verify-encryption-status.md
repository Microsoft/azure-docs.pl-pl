---
title: Weryfikowanie stanu szyfrowania dla systemu Linux
description: Ten artykuł zawiera instrukcje dotyczące weryfikowania stanu szyfrowania z poziomu platformy i systemu operacyjnego.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123424"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Weryfikowanie stanu szyfrowania dla systemu Linux 

**Ten scenariusz ma zastosowanie w przypadku rozszerzeń z podwójnym przebiegiem i jednokrotnym przekazaniem.**  
Ten zakres dokumentu polega na sprawdzeniu stanu szyfrowania maszyny wirtualnej przy użyciu różnych metod.

### <a name="environment"></a>Środowisko

- Dystrybucje systemu Linux

### <a name="procedure"></a>Procedura

Maszyna wirtualna została zaszyfrowana przy użyciu podwójnego przebiegu lub pojedynczego przebiegu.

Stan szyfrowania można sprawdzić w trakcie lub po szyfrowaniu przy użyciu różnych metod.

>[!NOTE] 
>Stosujemy zmienne w całym dokumencie, zastępując odpowiednio wartości.

### <a name="verification"></a>Weryfikacja

Weryfikację można przeprowadzić za pomocą portalu, programu PowerShell, polecenia AZ CLI i lub z systemu operacyjnego maszyny wirtualnej. 

Tę weryfikację można przeprowadzić przez sprawdzenie dysków dołączonych do określonej maszyny wirtualnej. 

Lub przez przeszukiwanie ustawień szyfrowania na poszczególnych dyskach, niezależnie od tego, czy dysk jest dołączony, czy niedołączony.

Poniżej różnych metod sprawdzania poprawności:

## <a name="using-the-portal"></a>Korzystanie z portalu

Sprawdź poprawność stanu szyfrowania, sprawdzając sekcję rozszerzenia na Azure Portal.

W sekcji **rozszerzeń** zobaczysz rozszerzenie ADE. 

Kliknij go i obejrzyj **komunikat o stanie**, wskazując bieżący stan szyfrowania:

![Numer sprawdzania portalu 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Na liście rozszerzeń zobaczysz odpowiednią wersję rozszerzenia ADE. Wersja 0. x odpowiada podwójnym serwerowi ADE i wersji 1. x odpowiada jednokrotnemu zajściu.

Aby uzyskać więcej szczegółów, kliknij rozszerzenie, a następnie *Wyświetl szczegółowy stan*.

Zostanie wyświetlony bardziej szczegółowy stan procesu szyfrowania w formacie JSON:

![Numer sprawdzania portalu 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Numer sprawdzania portalu 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Innym sposobem sprawdzania poprawności stanu szyfrowania jest zapoznaj się z sekcją **dysków** .

![Numer sprawdzania portalu 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Ten stan oznacza, że dyski mają ustawione ustawienia szyfrowania, ale nie są w rzeczywistości szyfrowane na poziomie systemu operacyjnego. Po zaprojektowaniu dyski są najpierw znakowane i szyfrowane w późniejszym czasie. Jeśli proces szyfrowania zakończy się niepowodzeniem, dyski mogą kończyć się znakiem, ale nie zaszyfrowanym. Aby sprawdzić, czy dyski są rzeczywiście zaszyfrowane, możesz dwukrotnie sprawdzić szyfrowanie każdego dysku na poziomie systemu operacyjnego.

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

**Ogólny** stan szyfrowania zaszyfrowanej maszyny wirtualnej można sprawdzić za pomocą następujących poleceń programu PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Sprawdź program PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Ustawienia szyfrowania można przechwycić z poszczególnych dysków przy użyciu następujących poleceń programu PowerShell:

### <a name="single-pass"></a>Pojedynczy przebieg
W przypadku pojedynczego przebiegu ustawienia szyfrowania są sygnaturami poszczególnych dysków (system operacyjny i dane), a ustawienia szyfrowania dysków systemu operacyjnego można przechwycić w jednym przebiegu w następujący sposób:

``` powershell
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
![Weryfikuj pojedynczy przebieg systemu operacyjnego 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Jeśli dysk nie ma sygnatury ustawień szyfrowania, dane wyjściowe będą puste, jak pokazano poniżej:

![Ustawienia szyfrowania systemu operacyjnego 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Ustawienia szyfrowania dysków danych przechwytywania:

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
![Weryfikuj dane pojedynczej PS 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Podwójny przebieg
W przypadku dwóch przebiegów ustawienia szyfrowania są znakowane w modelu maszyny wirtualnej, a nie na poszczególnych dyskach.

Aby sprawdzić, czy ustawienia szyfrowania zostały oznaczone jako podwójne, można użyć następujących poleceń:

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
![Weryfikowanie podwójnego przebiegu programu PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

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
## <a name="using-az-cli"></a>Korzystanie z polecenia AZ CLI

**Ogólny** stan szyfrowania zaszyfrowanej maszyny wirtualnej można sprawdzić przy użyciu następujących poleceń AZ CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Sprawdzanie ogólnych przy użyciu interfejsu wiersza polecenia ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Pojedynczy przebieg
Można sprawdzić poprawność ustawień szyfrowania poszczególnych dysków przy użyciu następujących poleceń AZ CLI:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Ustawienia szyfrowania danych](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Na wypadek, gdy dysk nie ma sygnatury ustawień szyfrowania, będzie wyświetlany jako "dysk nie jest szyfrowany"

Szczegółowe ustawienia stanu i szyfrowania:

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

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Dyski danych:

```bash
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

![Pojedynczy interfejs wiersza polecenia danych ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Podwójna Pass

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Sprawdź ogólne podwójne użycie interfejsu ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) wiersza polecenia, możesz również sprawdzić ustawienia szyfrowania w profilu magazynu modelu maszyny wirtualnej dysku systemu operacyjnego:

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

![Sprawdzanie profilu maszyny wirtualnej Dual using przy użyciu interfejsu wiersza polecenia ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

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

Aby uzyskać szczegółowe informacje o określonym dysku, należy podać:

Identyfikator konta magazynu zawierającego dysk.
Parametry połączenia dla danego konta magazynu.
Nazwa kontenera, w którym jest przechowywany dysk.
Nazwa dysku.

To polecenie wyświetla listę wszystkich identyfikatorów dla wszystkich kont magazynu:

```bash
az storage account list --query [].[id] -o tsv
```
Identyfikatory kont magazynu są wymienione w następującej postaci:

Identyfikator\<subskrypcji/subscriptions/>/ResourceGroups/\<nazwa grupy zasobów>/providers/microsoft.storage/storageaccounts/\<nazwa konta magazynu>

Wybierz odpowiedni identyfikator i Zapisz go na zmiennej:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Parametry połączenia.

To polecenie pobiera parametry połączenia dla jednego konkretnego konta magazynu i zapisuje je w zmiennej:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Nazwa kontenera.

Następujące polecenie wyświetla listę wszystkich kontenerów w ramach konta magazynu:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Kontener używany dla dysków ma zwykle nazwę "VHD"

Przechowywanie nazwy kontenera w zmiennej 
```bash
ContainerName="name of the container"
```

Nazwa dysku.

Użyj tego polecenia, aby wyświetlić listę wszystkich obiektów BLOB w konkretnym kontenerze
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Wybierz dysk, który ma być wysyłany do zapytania, i Zapisz jego nazwę na zmiennej.
```bash
DiskName="diskname.vhd"
```
Tworzenie zapytań dotyczących ustawień szyfrowania dysku
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Z systemu operacyjnego
Sprawdź, czy partycje dysku danych są szyfrowane (a dysk systemu operacyjnego nie jest)

Gdy partycja/dysk jest zaszyfrowana, jest wyświetlana jako typ **Crypt** , gdy nie jest zaszyfrowana, jest wyświetlana jako typ **części/dysku**

``` bash
lsblk
```

![Warstwa Crypt systemu operacyjnego ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Więcej szczegółów można uzyskać, korzystając z następujących wariantów "lsblk". 

Zobaczysz warstwę typu **Crypt** , która jest instalowana przez rozszerzenie.

W poniższym przykładzie przedstawiono woluminy logiczne i normalne dyski z "**kryptografią\_luks fstype**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![OS Crypt — warstwa 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Dodatkowy krok można także sprawdzić, czy dysk danych ma załadowane klucze

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

A które urządzenia DM są wyświetlane jako Crypt

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
