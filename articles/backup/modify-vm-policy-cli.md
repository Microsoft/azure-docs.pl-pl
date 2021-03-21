---
title: Aktualizowanie istniejących zasad kopii zapasowych maszyny wirtualnej przy użyciu interfejsu wiersza polecenia
description: Dowiedz się, jak zaktualizować istniejące zasady tworzenia kopii zapasowych maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98728582"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>Aktualizowanie istniejących zasad kopii zapasowych maszyny wirtualnej przy użyciu interfejsu wiersza polecenia

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby zaktualizować istniejące zasady kopii zapasowej maszyny wirtualnej. W tym artykule wyjaśniono, jak wyeksportować istniejące zasady do pliku JSON, zmodyfikować plik, a następnie zaktualizować zasady przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="modify-an-existing-policy"></a>Modyfikacja istniejącej zasady

Aby zmodyfikować istniejące zasady kopii zapasowej maszyny wirtualnej, wykonaj następujące kroki:

1. Wykonaj polecenie [AZ Backup Policy show](/cli/azure/backup/policy#az_backup_policy_show) , aby pobrać Szczegóły zasad, które chcesz zaktualizować.

    Przykład:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    W powyższym przykładzie przedstawiono szczegóły dotyczące zasad maszyny wirtualnej o nazwie *testing123*.

    Dane wyjściowe:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Zapisz powyższe dane wyjściowe w pliku JSON. Na przykład zapiszmy ją jako *Policy.js*.
1. Zaktualizuj plik JSON zgodnie z wymaganiami i Zapisz zmiany.

    Przykład: Aby zaktualizować cotygodniowe przechowywanie do 60 dni, zaktualizuj następującą sekcję pliku JSON, zmieniając liczbę na 60.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Zapisz zmiany.
1. Wykonaj polecenie [AZ Backup Policy Set](/cli/azure/backup/policy#az_backup_policy_set) i przekaż pełną ścieżkę zaktualizowanego pliku JSON jako wartość parametru **--Policy** .

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>Możesz również pobrać przykładowe zasady JSON, wykonując polecenie [AZ Backup Policy Get-default-for-VM](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) .

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup](backup-azure-manage-vms.md)