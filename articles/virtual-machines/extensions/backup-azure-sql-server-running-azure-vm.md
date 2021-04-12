---
title: Azure Backup SQL Server uruchomionego na maszynie wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak zarejestrować Azure Backup w SQL Server uruchomionym na maszynie wirtualnej platformy Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: v-amallick
ms.author: v-amallick
ms.collection: windows
ms.date: 07/05/2019
ms.openlocfilehash: 171a438b8ac2a74437c15a749d6974945877a1ee
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293757"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup SQL Server uruchomionego na maszynie wirtualnej platformy Azure

Azure Backup, między innymi ofertami, zapewnia obsługę tworzenia kopii zapasowych obciążeń, takich jak SQL Server uruchomione na maszynach wirtualnych platformy Azure. Ponieważ aplikacja SQL jest uruchomiona na maszynie wirtualnej platformy Azure, usługa tworzenia kopii zapasowych musi mieć uprawnienia dostępu do aplikacji i pobrać niezbędne szczegóły.
W tym celu Azure Backup instaluje rozszerzenie **AzureBackupWindowsWorkload** na maszynie wirtualnej, w której SQL Server jest uruchomiony, podczas procesu rejestracji wyzwalanego przez użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

Listę obsługiwanych scenariuszy można znaleźć w [macierzy obsługi](../../backup/sql-support-matrix.md#scenario-support) obsługiwanej przez Azure Backup.

## <a name="network-connectivity"></a>Łączność sieciowa

Azure Backup obsługuje Tagi sieciowej grupy zabezpieczeń, wdrażając serwer proxy lub wymienione zakresy adresów IP; Aby uzyskać szczegółowe informacje na temat każdej z tych metod, zapoznaj się z tym [artykułem](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="extension-schema"></a>Schemat rozszerzenia

Wartości konfiguracyjne schematu i właściwości (ustawienia środowiska uruchomieniowego) są przekazywane do interfejsu API usługi CRP. Te wartości konfiguracji są używane podczas rejestracji i uaktualniania. Rozszerzenie **AzureBackupWindowsWorkload** używa również tego schematu. Schemat jest wstępnie ustawiony; w polu objectStr można dodać nowy parametr

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

Poniższy kod JSON pokazuje schemat rozszerzenia WorkloadBackup.  

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
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | ciąg
commandStartTimeUTCTicks | "636967192566036845"  | ciąg
vmType  | "Microsoft. COMPUTE/virtualmachines"  | ciąg
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | ciąg
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | ciąg
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | ciąg

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Zalecamy dodanie rozszerzenia AzureBackupWindowsWorkload do maszyny wirtualnej, aby umożliwić tworzenie kopii zapasowych SQL Server na maszynie wirtualnej. Można to osiągnąć za pomocą [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) zaprojektowanego do automatyzacji tworzenia kopii zapasowych na SQL Server maszynie wirtualnej.

## <a name="powershell-deployment"></a>Wdrażanie przy użyciu programu PowerShell

Należy "zarejestrować" maszynę wirtualną platformy Azure zawierającą aplikację SQL z magazynem usługi Recovery Services. Podczas rejestracji rozszerzenie AzureBackupWindowsWorkload zostanie zainstalowane na maszynie wirtualnej. Zarejestruj maszynę wirtualną za pomocą polecenia cmdlet [register-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) .

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Polecenie zwróci **kontener kopii zapasowej** tego zasobu, a stan zostanie **zarejestrowany**.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](../../backup/backup-sql-server-azure-troubleshoot.md) na temat wskazówek dotyczących rozwiązywania problemów z usługą Azure SQL Server VM
- [Często zadawane pytania](../../backup/faq-backup-sql-server.md) dotyczące tworzenia kopii zapasowych baz danych SQL Server działających na maszynach wirtualnych platformy Azure i korzystających z usługi Azure Backup.
