---
title: Przywracanie Azure Files przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak przywrócić Azure Files przy użyciu usługi Azure Backup i programu PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021388"
---
# <a name="restore-azure-files-with-powershell"></a>Przywracanie Azure Files przy użyciu programu PowerShell

W tym artykule wyjaśniono, jak przywrócić cały udział plików lub określone pliki z punktu przywracania utworzonego przez usługę [Azure Backup](backup-overview.md) przy użyciu Azure PowerShell.

Można przywrócić cały udział plików lub konkretne pliki w udziale. Można przywrócić do oryginalnej lokalizacji lub do alternatywnej lokalizacji.

> [!WARNING]
> Upewnij się, że wersja programu PowerShell została uaktualniona do wersji minimalnej dla polecenia "AZ. RecoveryServices 2.6.0" dla kopii zapasowych AFS. Aby uzyskać więcej informacji, zobacz [sekcję](backup-azure-afs-automation.md#important-notice-backup-item-identification) dotyczącą wymagania tej zmiany.

>[!NOTE]
>Azure Backup teraz obsługuje przywracanie wielu plików lub folderów do lokalizacji oryginalnej lub alternatywnej przy użyciu programu PowerShell. Zapoznaj się z [tą sekcją](#restore-multiple-files-or-folders-to-original-or-alternate-location) dokumentu, aby dowiedzieć się, jak.

## <a name="fetch-recovery-points"></a>Pobierz punkty odzyskiwania

Użyj polecenie [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) , aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej.

W poniższym skrypcie:

* Zmienna **$RP** jest tablicą punktów odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni.
* Tablica jest posortowana w odwrotnej kolejności czasu z najnowszym punktem odzyskiwania pod indeksem **0**.
* Użyj standardowego indeksowania tablicy programu PowerShell, aby wybrać punkt odzyskiwania.
* W przykładzie **$RP [0]** wybiera najnowszy punkt odzyskiwania.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

Dane wyjściowe są podobne do następujących.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Po wybraniu odpowiedniego punktu odzyskiwania przywracasz udział plików lub plik do oryginalnej lokalizacji lub do lokalizacji alternatywnej.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Przywracanie udziału plików platformy Azure w alternatywnej lokalizacji

Aby przywrócić do wybranego punktu odzyskiwania, użyj [instrukcji RESTORE-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) . Określ te parametry, aby zidentyfikować alternatywną lokalizację:

* **TargetStorageAccountName**: konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **TargetFileShareName**: udziały plików w docelowym koncie magazynu, do którego zostanie przywrócona zawartość kopii zapasowej.
* **TargetFolder**: folder w udziale plików, do którego przywracane są dane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, nadaj wartości folderu docelowego jako pusty ciąg.
* **ResolveConflict**: instrukcja, jeśli wystąpił konflikt z przywróconymi danymi. Akceptuje **zastępowanie** lub **pomijanie**.

Uruchom polecenie cmdlet z parametrami w następujący sposób:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Polecenie zwraca zadanie z IDENTYFIKATORem, który ma być śledzony, jak pokazano w poniższym przykładzie.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Przywracanie pliku platformy Azure do lokalizacji alternatywnej

Aby przywrócić do wybranego punktu odzyskiwania, użyj [instrukcji RESTORE-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) . Określ te parametry, aby zidentyfikować alternatywną lokalizację i jednoznacznie zidentyfikować plik, który chcesz przywrócić.

* **TargetStorageAccountName**: konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **TargetFileShareName**: udziały plików w docelowym koncie magazynu, do którego zostanie przywrócona zawartość kopii zapasowej.
* **TargetFolder**: folder w udziale plików, do którego przywracane są dane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, nadaj wartości folderu docelowego jako pusty ciąg.
* **Sourcefilepath**: ścieżka bezwzględna pliku, która ma zostać przywrócona w udziale plików jako ciąg. Ta ścieżka jest tą samą ścieżką używaną w poleceniu cmdlet **Get-AzStorageFile** programu PowerShell.
* **SourceFileType**: czy wybrano katalog lub plik. Akceptuje **katalog** lub **plik**.
* **ResolveConflict**: instrukcja, jeśli wystąpił konflikt z przywróconymi danymi. Akceptuje **zastępowanie** lub **pomijanie**.

Dodatkowe parametry (SourceFilePath i SourceFileType) są powiązane tylko z pojedynczym plikiem, który ma zostać przywrócony.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

To polecenie zwraca zadanie z IDENTYFIKATORem, który ma być śledzony, jak pokazano w poprzedniej sekcji.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Przywracanie udziałów plików i plików platformy Azure do oryginalnej lokalizacji

Podczas przywracania do oryginalnej lokalizacji nie trzeba określać parametrów docelowych i docelowych. Należy podać tylko **ResolveConflict** .

### <a name="overwrite-an-azure-file-share"></a>Zastąp udział plików platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Zastąp plik platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Przywracanie wielu plików lub folderów do lokalizacji oryginalnej lub alternatywnej

Użyj polecenia [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) przez przekazanie ścieżki wszystkich plików lub folderów, które chcesz przywrócić jako wartość parametru **MultipleSourceFilePath** .

### <a name="restore-multiple-files"></a>Przywracanie wielu plików

W poniższym skrypcie próbujemy przywrócić pliki *FileSharePage.png* i *MyTestFile.txt* .

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Przywracanie wielu katalogów

W poniższym skrypcie podjęto próbę przywrócenia katalogów *zrs1_restore* i *przywracania* .

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

Dane wyjściowe będą mieć postać podobną do następującej:

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

Jeśli chcesz przywrócić wiele plików lub folderów do lokalizacji alternatywnej, użyj powyższych skryptów przez określenie docelowych wartości parametrów związanych z lokalizacją, jak wyjaśniono powyżej w temacie [przywracanie pliku platformy Azure do lokalizacji alternatywnej](#restore-an-azure-file-to-an-alternate-location).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej na temat](restore-afs.md) przywracania Azure Files w Azure Portal.
