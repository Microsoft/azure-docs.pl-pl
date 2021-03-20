---
title: Zarządzanie kopiami zapasowymi udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do zarządzania udziałami plików platformy Azure i ich monitorowania za pomocą Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5a8a785016845b836a102663a959e4b2f28696b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566456"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Zarządzanie kopiami zapasowymi udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure zapewnia obsługę systemu Azure w programie. To doskonałe narzędzie do tworzenia niestandardowych automatyzacji do korzystania z zasobów platformy Azure. W tym artykule opisano sposób wykonywania zadań związanych z zarządzaniem udziałami plików platformy Azure, których kopie zapasowe są tworzone przez [Azure Backup](./backup-overview.md). Te kroki można również wykonać za pomocą [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz już kopię zapasową udziału plików platformy Azure, [Azure Backup](./backup-overview.md). Jeśli go nie masz, zobacz Tworzenie kopii zapasowych [udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia](backup-afs-cli.md) w celu skonfigurowania usługi Backup dla udziałów plików. W tym artykule opisano użycie następujących zasobów:
   -  **Grupa zasobów**: *migracji pamięci*
   -  **RecoveryServicesVault**: *azurefilesvault*
   -  **Konto magazynu**: *afsaccount*
   -  **Udział plików**: *migracji pamięci*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Ten samouczek wymaga wersji 2.0.18 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="monitor-jobs"></a>Monitorowanie zadań

Gdy Wyzwalasz operacje tworzenia kopii zapasowej lub przywracania, usługa Backup tworzy zadanie śledzenia. Aby monitorować ukończone lub aktualnie uruchomione zadania, należy użyć polecenia [AZ Backup Job list](/cli/azure/backup/job#az-backup-job-list) . Korzystając z interfejsu wiersza polecenia, można również [zawiesić aktualnie uruchomione zadanie](/cli/azure/backup/job#az-backup-job-stop) lub [poczekać na zakończenie zadania](/cli/azure/backup/job#az-backup-job-wait).

Poniższy przykład przedstawia stan zadań tworzenia kopii zapasowych dla magazynu *azurefilesvault* Recovery Services:

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

## <a name="modify-policy"></a>Modyfikuj zasady

Możesz zmodyfikować zasady tworzenia kopii zapasowej, aby zmienić częstotliwość tworzenia kopii zapasowych lub zakres przechowywania przy użyciu polecenia [AZ Backup Item Set-Policy](/cli/azure/backup/item#az-backup-item-set-policy).

Aby zmienić zasady, Zdefiniuj następujące parametry:

* **--Container-Name**: nazwa konta magazynu, w którym znajduje się udział plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) .
* **--name**: nazwa udziału plików, dla którego chcesz zmienić zasady. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [AZ Backup Item list](/cli/azure/backup/item#az-backup-item-list) .
* **--Policy-Name**: Nazwa zasad tworzenia kopii zapasowych, które chcesz ustawić dla udziału plików. Możesz użyć [AZ Backup Policy list](/cli/azure/backup/policy#az-backup-policy-list) , aby wyświetlić wszystkie zasady dla Twojego magazynu.

W poniższym przykładzie ustawiono zasady tworzenia kopii zapasowych *schedule2* dla udziału plików *migracji pamięci* , które znajdują się na koncie magazynu *afsaccount* .

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Możesz również uruchomić poprzednie polecenie przy użyciu przyjaznych nazw kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--Backup-Management-Type**: *azurestorage*
* **--Typ obciążenia**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji zmiany zasad. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>Zatrzymywanie ochrony udziału plików

Istnieją dwa sposoby na zatrzymanie ochrony udziałów plików platformy Azure:

* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i *Usuń* wszystkie punkty odzyskiwania.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej, ale *Pozostaw* punkty odzyskiwania.

Może być kosztem związanym z opuszczeniem punktów odzyskiwania w magazynie, ponieważ źródłowe migawki utworzone przez Azure Backup zostaną zachowane. Korzyścią opuszczenia punktów odzyskiwania jest możliwość późniejszego przywrócenia udziału plików. Aby uzyskać informacje o kosztu opuszczenia punktów odzyskiwania, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/storage/files). Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie możesz przywrócić udziału plików.

Aby zatrzymać ochronę udziału plików, Zdefiniuj następujące parametry:

* **--Container-Name**: nazwa konta magazynu, w którym znajduje się udział plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name**: nazwa udziału plików, dla którego chcesz zatrzymać ochronę. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [AZ Backup Item list](/cli/azure/backup/item#az-backup-item-list) .

### <a name="stop-protection-and-retain-recovery-points"></a>Zatrzymywanie ochrony i zachowywanie punktów odzyskiwania

Aby zatrzymać ochronę przy zachowaniu danych, należy użyć polecenia [AZ Backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) .

Poniższy przykład zatrzymuje ochronę udziału plików *migracji pamięci* , ale zachowuje wszystkie punkty odzyskiwania.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Możesz również uruchomić poprzednie polecenie przy użyciu przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--Backup-Management-Type**: *azurestorage*
* **--Typ obciążenia**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji zatrzymania ochrony. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Zatrzymaj ochronę bez zachowywania punktów odzyskiwania

Aby zatrzymać ochronę bez zachowywania punktów odzyskiwania, użyj polecenia [AZ Backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) z opcją **delete-Backup-Data** ustawioną na **wartość true**.

Poniższy przykład zatrzymuje ochronę udziału plików *migracji pamięci* bez zachowywania punktów odzyskiwania.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Możesz również uruchomić poprzednie polecenie przy użyciu przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--Backup-Management-Type**: *azurestorage*
* **--Typ obciążenia**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Wznawianie ochrony udziału plików

Jeśli ochrona udziału plików platformy Azure została zatrzymana, ale zachowane punkty odzyskiwania, można wznowić ochronę później. Jeśli nie zachowasz punktów odzyskiwania, nie możesz wznowić ochrony.

Aby wznowić ochronę udziału plików, Zdefiniuj następujące parametry:

* **--Container-Name**: nazwa konta magazynu, w którym znajduje się udział plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name**: nazwa udziału plików, dla którego chcesz wznowić ochronę. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [AZ Backup Item list](/cli/azure/backup/item#az-backup-item-list) .
* **--Policy-Name**: Nazwa zasad tworzenia kopii zapasowych, dla których chcesz wznowić ochronę udziału plików.

W poniższym przykładzie użyto polecenia [AZ Backup Protection Resume](/cli/azure/backup/protection#az-backup-protection-resume) w celu wznowienia ochrony udziału plików *migracji pamięci* przy użyciu zasad tworzenia kopii zapasowych *schedule1* .

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Możesz również uruchomić poprzednie polecenie przy użyciu przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--Backup-Management-Type**: *azurestorage*
* **--Typ obciążenia**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji wznawiania ochrony. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Wyrejestrowywanie konta magazynu

Jeśli chcesz chronić udziały plików na określonym koncie magazynu przy użyciu innego magazynu Recovery Services, najpierw [Zatrzymaj ochronę wszystkich udziałów plików](#stop-protection-on-a-file-share) na tym koncie magazynu. Następnie Wyrejestruj konto z magazynu Recovery Services obecnie używanego do ochrony.

Musisz podać nazwę kontenera, aby wyrejestrować konto magazynu. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) .

Poniższy przykład wyrejestrowuje konto magazynu *afsaccount* z *azurefilesvault* za pomocą polecenia [AZ Backup Container Unregister](/cli/azure/backup/container#az-backup-container-unregister) .

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Możesz również uruchomić poprzednie polecenie cmdlet przy użyciu przyjaznej nazwy kontenera, dostarczając następujący dodatkowy parametr:

* **--Backup-Management-Type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z kopiami zapasowymi udziałów plików platformy Azure](troubleshoot-azure-files.md).
