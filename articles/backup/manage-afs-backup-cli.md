---
title: Zarządzanie kopiami zapasami udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure zarządzać udziałami plików platformy Azure i monitorować je, których kopię zapasową Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: e389f5cde12734ef4bf0be4ecfba69ba33f5e030
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773607"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Zarządzanie kopiami zapasami udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure udostępnia środowisko wiersza polecenia do zarządzania zasobami platformy Azure. Jest to doskonałe narzędzie do tworzenia niestandardowych automatyzacji w celu korzystania z zasobów platformy Azure. W tym artykule wyjaśniono, jak wykonywać zadania związane z zarządzaniem udziałami plików platformy Azure, których kopię zapasową wykonuje Azure Backup [.](./backup-overview.md) Te kroki można również wykonać za pomocą [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz już udział plików platformy Azure, dla Azure Backup [.](./backup-overview.md) Jeśli ich nie masz, zobacz Tworzenie kopii zapasowej udziałów plików platformy Azure za pomocą interfejsu wiersza [polecenia,](backup-afs-cli.md) aby skonfigurować kopię zapasową udziałów plików. W tym artykule są dostępne następujące zasoby:
   -  **Grupa zasobów**: *azurefiles*
   -  **RecoveryServicesVault:** *azurefilesvault*
   -  **Konto magazynu:** *afsaccount*
   -  **Udział plików:** *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="monitor-jobs"></a>Monitorowanie zadań

Po wyzwoleniu operacji tworzenia kopii zapasowej lub przywracania usługa tworzenia kopii zapasowej tworzy zadanie do śledzenia. Aby monitorować ukończone lub aktualnie uruchomione zadania, użyj [polecenia cmdlet az backup job list.](/cli/azure/backup/job#az_backup_job_list) Za pomocą interfejsu wiersza polecenia można również [wstrzymać aktualnie uruchomione zadanie lub](/cli/azure/backup/job#az_backup_job_stop) poczekać na zakończenie [zadania.](/cli/azure/backup/job#az_backup_job_wait)

W poniższym przykładzie wyświetlany jest stan zadań tworzenia kopii zapasowej dla magazynu *usługi azurefilesvault* Recovery Services:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Modyfikowanie zasad

Możesz zmodyfikować zasady tworzenia kopii zapasowych, aby zmienić częstotliwość tworzenia kopii zapasowych lub zakres przechowywania, używając [az backup item set-policy.](/cli/azure/backup/item#az_backup_item_set_policy)

Aby zmienić zasady, zdefiniuj następujące parametry:

* **--container-name:** nazwa konta magazynu, które hostuje udział plików. Aby pobrać **nazwę lub** **przyjazną nazwę** kontenera, użyj polecenia az backup container [list.](/cli/azure/backup/container#az_backup_container_list)
* **--name:** nazwa udziału plików, dla którego chcesz zmienić zasady. Aby pobrać **nazwę lub** **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia az backup item [list.](/cli/azure/backup/item#az_backup_item_list)
* **--policy-name:** nazwa zasad kopii zapasowych, które chcesz ustawić dla udziału plików. Możesz użyć az [backup policy list,](/cli/azure/backup/policy#az_backup_policy_list) aby wyświetlić wszystkie zasady dla magazynu.

Poniższy przykład ustawia zasady tworzenia kopii *zapasowej schedule2* dla *udziału plików azurefiles* obecnego na *koncie magazynu afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Można również uruchomić poprzednie polecenie przy użyciu przyjaznych nazw kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę kopii zapasowej dla operacji zasad zmiany. Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="stop-protection-on-a-file-share"></a>Zatrzymywanie ochrony udziału plików

Istnieją dwa sposoby na zatrzymanie ochrony udziałów plików platformy Azure:

* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej *i usuń* wszystkie punkty odzyskiwania.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii *zapasowej, ale pozostaw* punkty odzyskiwania.

Pozostawienie punktów odzyskiwania w magazynie może wiązać się z kosztami, ponieważ bazowe migawki utworzone przez program Azure Backup zostaną zachowane. Zaletą pozostawienia punktów odzyskiwania jest opcja późniejszego przywrócenia udziału plików, jeśli chcesz. Aby uzyskać informacje o kosztach opuszczania punktów odzyskiwania, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/storage/files). Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie możesz przywrócić udziału plików.

Aby zatrzymać ochronę udziału plików, zdefiniuj następujące parametry:

* **--container-name:** nazwa konta magazynu, które hostuje udział plików. Aby pobrać **nazwę lub** **przyjazną nazwę** kontenera, użyj polecenia az backup container [list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** nazwa udziału plików, dla którego chcesz zatrzymać ochronę. Aby pobrać **nazwę lub** **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia az backup item [list.](/cli/azure/backup/item#az_backup_item_list)

### <a name="stop-protection-and-retain-recovery-points"></a>Zatrzymywanie ochrony i zachowywanie punktów odzyskiwania

Aby zatrzymać ochronę przy zachowaniu danych, użyj [polecenia cmdlet az backup protection disable.](/cli/azure/backup/protection#az_backup_protection_disable)

Poniższy przykład zatrzymuje ochronę udziału *plików azurefiles,* ale zachowuje wszystkie punkty odzyskiwania.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Możesz również uruchomić poprzednie polecenie, używając przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji zatrzymania ochrony. Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="stop-protection-without-retaining-recovery-points"></a>Zatrzymywanie ochrony bez zachowania punktów odzyskiwania

Aby zatrzymać ochronę bez zachowania punktów odzyskiwania, użyj polecenia cmdlet [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) z opcją **delete-backup-data** ustawioną na **wartość true**.

Poniższy przykład zatrzymuje ochronę udziału *plików azurefiles* bez zachowania punktów odzyskiwania.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Możesz również uruchomić poprzednie polecenie, używając przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Wznawianie ochrony udziału plików

Jeśli zatrzymano ochronę udziału plików platformy Azure, ale zachowane punkty odzyskiwania, możesz wznowić ochronę później. Jeśli nie zachowasz punktów odzyskiwania, nie możesz wznowić ochrony.

Aby wznowić ochronę udziału plików, zdefiniuj następujące parametry:

* **--container-name:** nazwa konta magazynu, które hostuje udział plików. Aby pobrać **nazwę lub** **przyjazną nazwę** kontenera, użyj polecenia az backup container [list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** nazwa udziału plików, dla którego chcesz wznowić ochronę. Aby pobrać **nazwę lub** **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia az backup item [list.](/cli/azure/backup/item#az_backup_item_list)
* **--policy-name:** nazwa zasad kopii zapasowych, dla których chcesz wznowić ochronę udziału plików.

W poniższym przykładzie użyto polecenia cmdlet [az backup protection resume,](/cli/azure/backup/protection#az_backup_protection_resume) aby wznowić ochronę udziału *plików azurefiles* przy użyciu zasad tworzenia kopii zapasowych *schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Możesz również uruchomić poprzednie polecenie, używając przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji wznawiania ochrony. Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="unregister-a-storage-account"></a>Wyrejestrowanie konta magazynu

Jeśli chcesz chronić udziały plików na określonym koncie magazynu przy użyciu innego magazynu usługi Recovery Services, najpierw zatrzymaj ochronę wszystkich udziałów plików [na](#stop-protection-on-a-file-share) tym koncie magazynu. Następnie wyrejestruj konto z magazynu usługi Recovery Services używanego obecnie do ochrony.

Musisz podać nazwę kontenera, aby wyrejestrować konto magazynu. Aby pobrać **nazwę lub** **przyjazną nazwę** kontenera, użyj polecenia az backup container [list.](/cli/azure/backup/container#az_backup_container_list)

Poniższy przykład wyrejestruje *konto magazynu afsaccount* z usługi *azurefilesvault* przy użyciu polecenia cmdlet [az backup container unregister.](/cli/azure/backup/container#az_backup_container_unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Poprzednie polecenie cmdlet można również uruchomić przy użyciu przyjaznej nazwy kontenera, podając następujący dodatkowy parametr:

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Troubleshoot Azure file shares backup (Rozwiązywanie problemów z kopią zapasową udziałów plików platformy Azure).](troubleshoot-azure-files.md)
