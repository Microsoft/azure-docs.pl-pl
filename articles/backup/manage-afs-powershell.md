---
title: Zarządzanie kopiami zapasowymi udziałów plików platformy Azure przy użyciu programu PowerShell
description: Dowiedz się, jak używać programu PowerShell do zarządzania udziałami plików platformy Azure i ich monitorować za pomocą usługi Azure Backup.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e2f07e56fb9a8715b1b53165ab5f4b45b4e20ccb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000230"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Zarządzanie kopiami zapasowymi udziałów plików platformy Azure przy użyciu programu PowerShell

W tym artykule opisano, jak za pomocą usługi Azure PowerShell zarządzać udziałami plików platformy Azure, których kopie zapasowe mają być tworzone przez usługę Azure Backup.

> [!WARNING]
> Upewnij się, że wersja programu PowerShell została uaktualniona do wersji minimalnej dla polecenia "AZ. RecoveryServices 2.6.0" dla kopii zapasowych AFS. Aby uzyskać więcej informacji, zapoznaj się z [sekcją](backup-azure-afs-automation.md#important-notice-backup-item-identification) , w której znajduje się wymóg zmiany.

## <a name="modify-the-protection-policy"></a>Modyfikowanie zasad ochrony

Aby zmienić zasady służące do tworzenia kopii zapasowych udziału plików platformy Azure, użyj polecenie [enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Określ odpowiedni element kopii zapasowej i nowe zasady tworzenia kopii zapasowych.

Poniższy przykład zmienia zasady ochrony **testAzureFS** z **dailyafs** na **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Śledź zadania tworzenia kopii zapasowej i przywracania

Operacje tworzenia kopii zapasowej i przywracania na żądanie zwracają zadanie wraz z IDENTYFIKATORem, jak pokazano w przypadku [uruchamiania kopii zapasowej na żądanie](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) , aby śledzić postęp zadania i szczegóły.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>Zatrzymywanie ochrony udziału plików

Istnieją dwa sposoby na zatrzymanie ochrony udziałów plików platformy Azure:

* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i *Usuń* wszystkie punkty odzyskiwania
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej, ale *Pozostaw* punkty odzyskiwania

W ramach magazynu mogą występować koszty związane z opuszczeniem punktów odzyskiwania, ponieważ źródłowe migawki utworzone za pomocą Azure Backup zostaną zachowane. Jednak korzyść opuszczenia punktów odzyskiwania umożliwia przywrócenie udziału plików później, w razie potrzeby. Aby uzyskać informacje o kosztu opuszczenia punktów odzyskiwania, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/storage/files/). Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie możesz przywrócić udziału plików.

## <a name="stop-protection-and-retain-recovery-points"></a>Zatrzymywanie ochrony i zachowywanie punktów odzyskiwania

Aby zatrzymać ochronę podczas zachowywania danych, należy użyć polecenia cmdlet [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) .

Poniższy przykład zatrzymuje ochronę udziału plików *afsfileshare* , ale zachowuje wszystkie punkty odzyskiwania:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

Atrybut ID zadania w danych wyjściowych odpowiada IDENTYFIKATORowi zadania zadania, które zostało utworzone przez usługę kopii zapasowej dla operacji "Zatrzymaj ochronę". Aby śledzić stan zadania, należy użyć polecenia cmdlet [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) .

## <a name="stop-protection-without-retaining-recovery-points"></a>Zatrzymaj ochronę bez zachowywania punktów odzyskiwania

Aby zatrzymać ochronę bez zachowywania punktów odzyskiwania, należy użyć polecenia cmdlet [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) i dodać parametr **-RemoveRecoveryPoints** .

Poniższy przykład zatrzymuje ochronę udziału plików *afsfileshare* bez zachowywania punktów odzyskiwania:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Następne kroki

[Więcej](manage-afs-backup.md) informacji na temat zarządzania kopiami zapasowymi udziałów plików platformy Azure w Azure Portal.
