---
title: Azure Backup do SQL Server na maszynie wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak zarejestrować Azure Backup w SQL Server na maszynie wirtualnej platformy Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: v-amallick
ms.author: v-amallick
ms.collection: windows
ms.date: 07/05/2019
ms.openlocfilehash: c10be941206dd60887c9d82025506d1ea15c51a2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517253"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup do SQL Server na maszynie wirtualnej platformy Azure

Azure Backup, między innymi, zapewnia obsługę kopii zapasowej obciążeń, takich jak SQL Server uruchomione na platformie Azure. Ponieważ aplikacja SQL jest uruchomiona na maszynie wirtualnej platformy Azure, usługa kopii zapasowej musi mieć uprawnienia dostępu do aplikacji i pobiera niezbędne szczegóły.
W tym celu program Azure Backup rozszerzenie **AzureBackupWindowsWorkload** na maszynie wirtualnej, na której działa SQL Server, podczas procesu rejestracji wyzwolonego przez użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać listę obsługiwanych scenariuszy, zapoznaj się z macierzą [możliwości obsługi](../../backup/sql-support-matrix.md#scenario-support) obsługiwaną przez Azure Backup.

## <a name="network-connectivity"></a>Łączność sieciowa

Azure Backup obsługuje tagi sieciowej organizacji sieciowej, wdrażając serwer proxy lub wymienione zakresy adresów IP; Aby uzyskać szczegółowe informacje na temat każdej z metod, zapoznaj się z tym [artykułem.](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity)

## <a name="extension-schema"></a>Schemat rozszerzenia

Wartości schematu rozszerzenia i właściwości to wartości konfiguracji (ustawienia środowiska uruchomieniowego), które usługa przechodzi do interfejsu API CRP. Te wartości konfiguracji są używane podczas rejestracji i uaktualniania. **Rozszerzenie AzureBackupWindowsWorkload** również używa tego schematu. Schemat jest wstępnie ustawiony; Nowy parametr można dodać w polu objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Poniższy kod JSON przedstawia schemat rozszerzenia WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Wartości właściwości

Nazwa | Wartość/przykład | Typ danych
 --- | --- | ---
locale | en-us  |  ciąg
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | ciąg
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhND PojemneM2 IiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzEz OTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | ciąg
commandStartTimeUTCTicks | "636967192566036845"  | ciąg
Typ maszyny wirtualnej  | "microsoft.compute/virtualmachines"  | ciąg
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhND OpisM2 WiiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEz OTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZtctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcF Należyci6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | ciąg
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | ciąg
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | ciąg

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Zalecamy dodanie rozszerzenia AzureBackupWindowsWorkload do maszyny wirtualnej przez włączenie SQL Server kopii zapasowej na maszynie wirtualnej. Można to osiągnąć za pomocą szablonu [Resource Manager przeznaczonego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) do automatyzacji tworzenia kopii zapasowych na SQL Server wirtualnej.

## <a name="powershell-deployment"></a>Wdrażanie przy użyciu programu PowerShell

Musisz zarejestrować maszynę wirtualną platformy Azure, która zawiera aplikację SQL w magazynie usługi Recovery Services. Podczas rejestracji rozszerzenie AzureBackupWindowsWorkload jest instalowane na maszynie wirtualnej. Użyj polecenia cmdlet [Register-AzRecoveryServicesBackupContainerPS,](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) aby zarejestrować maszynę wirtualną.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Polecenie zwróci kontener kopii **zapasowych** tego zasobu, a stan zostanie **zarejestrowany.**

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej na](../../backup/backup-sql-server-azure-troubleshoot.md) temat Azure SQL rozwiązywania problemów z tworzeniem kopii zapasowych maszyn wirtualnych serwera
- [Często zadawane pytania](../../backup/faq-backup-sql-server.yml) dotyczące kopii SQL Server baz danych, które działają na maszynach wirtualnych platformy Azure i które używają Azure Backup wirtualnej.
