---
title: Przywracanie udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak przy użyciu interfejsu wiersza polecenia platformy Azure przywrócić udziały plików platformy Azure z kopii zapasowej w magazynie usługi Recovery Services
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 2edc2281c29023bb8dfe0268f23eacfe081d413e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768520"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Przywracanie udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure udostępnia środowisko wiersza polecenia do zarządzania zasobami platformy Azure. Jest to doskonałe narzędzie do tworzenia automatyzacji niestandardowej do korzystania z zasobów platformy Azure. W tym artykule wyjaśniono, jak przywrócić cały udział plików lub określone pliki z punktu przywracania utworzonego przez program [Azure Backup](./backup-overview.md) użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu programu [Azure PowerShell](./backup-azure-afs-automation.md) lub w witrynie [Azure Portal](backup-afs.md).

Po zakończeniu pracy z tym artykułem dowiesz się, jak wykonywać następujące operacje przy użyciu interfejsu wiersza polecenia platformy Azure:

* Wyświetlanie punktów przywracania dla kopii zapasowej udziału plików platformy Azure.
* Przywracanie pełnego udziału plików platformy Azure.
* Przywracanie pojedynczych plików lub folderów.

>[!NOTE]
> Azure Backup obsługuje teraz przywracanie wielu plików lub folderów do oryginalnej lub alternatywnej lokalizacji przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej, zapoznaj się z sekcją Przywracanie wielu plików lub [folderów](#restore-multiple-files-or-folders-to-original-or-alternate-location) do oryginalnej lub alternatywnej lokalizacji w tym dokumencie.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz już udział plików platformy Azure, dla których jest dzielona Azure Backup. Jeśli ich nie masz, zobacz Tworzenie kopii zapasowej udziałów plików platformy Azure za pomocą interfejsu wiersza [polecenia,](backup-afs-cli.md) aby skonfigurować kopię zapasową udziału plików. W tym artykule są dostępne następujące zasoby:

| Udział plików | Konto magazynu | Region (Region) | Szczegóły |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | Kopię zapasową oryginalnego źródła można chować przy użyciu Azure Backup |
| *azurefiles1* | *afaccount1* | EastUS | Źródło docelowe używane do odzyskiwania lokalizacji alternatywnej |

Możesz użyć podobnej struktury dla udziałów plików, aby wypróbować różne typy przywracania objaśnione w tym artykule.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Pobieranie punktów odzyskiwania dla udziału plików platformy Azure

Użyj polecenia [cmdlet az backup recoverypoint list,](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) aby wyświetlić listę wszystkich punktów odzyskiwania dla udziału plików kopii zapasowej.

Poniższy przykład pobiera listę punktów odzyskiwania dla udziału *plików azurefiles* na koncie *magazynu afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Poprzednie polecenie cmdlet można również uruchomić przy użyciu przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Zestaw wyników to lista punktów odzyskiwania ze szczegółami czasu i spójności dla każdego punktu przywracania.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie punktu odzyskiwania, która może służyć jako wartość parametru **--rp-name** w operacjach odzyskiwania.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Odzyskiwanie pełnego udziału przy użyciu interfejsu wiersza polecenia platformy Azure

Ta opcja przywracania umożliwia przywrócenie pełnego udziału plików w lokalizacji oryginalnej lub alternatywnej.

Zdefiniuj następujące parametry, aby wykonać operacje przywracania:

* **--container-name:** nazwa konta magazynu, które hostuje oryginalny udział plików z kopii zapasowej. Aby pobrać nazwę lub przyjazną nazwę kontenera, użyj [polecenia az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** nazwa oryginalnego udziału plików kopii zapasowej, którego chcesz użyć do operacji przywracania. Aby pobrać nazwę lub przyjazną nazwę elementu kopii zapasowej, użyj [polecenia az backup item list.](/cli/azure/backup/item#az_backup_item_list)

### <a name="restore-a-full-share-to-the-original-location"></a>Przywracanie pełnego udziału do oryginalnej lokalizacji

Podczas przywracania do oryginalnej lokalizacji nie trzeba określać parametrów związanych z obiektem docelowym. Należy **podać tylko informacje o rozwiązywaniu** konfliktów.

W poniższym przykładzie użyto polecenia cmdlet [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) z trybem przywracania ustawionym na *originallocation,* aby *przywrócić udział plików azurefiles* w oryginalnej lokalizacji. Użyj punktu odzyskiwania 932883129628959823, który został uzyskany w ramach tematu Pobieranie punktów odzyskiwania dla udziału [plików platformy Azure:](#fetch-recovery-points-for-the-azure-file-share)

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="restore-a-full-share-to-an-alternate-location"></a>Przywracanie pełnego udziału do lokalizacji alternatywnej

Ta opcja umożliwia przywrócenie udziału plików do lokalizacji alternatywnej i zachowanie oryginalnego udziału plików w takim stanie, w jaki jest. Określ następujące parametry odzyskiwania lokalizacji alternatywnej:

* **--target-storage-account:** konto magazynu, do którego jest przywracana zawartość kopii zapasowej. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **--target-file-share:** udział plików w ramach docelowego konta magazynu, do którego jest przywracana zawartość kopii zapasowej.
* **--target-folder:** folder w ramach udziału plików, do którego są przywracane dane. Jeśli zawartość kopii zapasowej ma zostać przywrócona do folderu głównego, należy podać wartości folderu docelowego jako pusty ciąg.
* **--resolve-conflict:** Instrukcja w przypadku konfliktu z przywróconych danych. Akceptuje pozycję **Zastąp** lub **Pomiń**.

W poniższym przykładzie użyto narzędzia [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) z trybem przywracania jako *alternatelocation,* aby przywrócić udział plików *azurefiles* na koncie magazynu *afsaccount* do udziału plików *azurefiles1"* na koncie magazynu *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="item-level-recovery"></a>Odzyskiwanie na poziomie elementu

Ta opcja przywracania umożliwia przywracanie pojedynczych plików lub folderów w lokalizacji oryginalnej lub alternatywnej.

Zdefiniuj następujące parametry, aby wykonać operacje przywracania:

* **--container-name:** nazwa konta magazynu, które hostuje oryginalny udział plików z kopii zapasowej. Aby pobrać nazwę lub przyjazną nazwę kontenera, użyj [polecenia az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** nazwa oryginalnego udziału plików kopii zapasowej, którego chcesz użyć do operacji przywracania. Aby pobrać nazwę lub przyjazną nazwę elementu kopii zapasowej, użyj [polecenia az backup item list.](/cli/azure/backup/item#az_backup_item_list)

Określ następujące parametry dla elementów, które chcesz odzyskać:

* **SourceFilePath:** ścieżka bezwzględna pliku, który ma zostać przywrócony w ramach udziału plików, jako ciąg. Ta ścieżka jest tą samą ścieżką, która jest używana w [poleceniach interfejsu](/cli/azure/storage/file#az_storage_file_download) wiersza polecenia az storage file download lub az storage file [show.](/cli/azure/storage/file#az_storage_file_show)
* **SourceFileType:** wybierz, czy wybrano katalog, czy plik. Akceptuje katalog **lub** **plik**.
* **ResolveConflict:** instrukcja w przypadku konfliktu z przywróconych danych. Akceptuje **zastępowanie** lub **pomijanie**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Przywracanie poszczególnych plików lub folderów do oryginalnej lokalizacji

Użyj [polecenia cmdlet az backup restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) z trybem przywracania ustawionym na *originallocation,* aby przywrócić określone pliki lub foldery do ich oryginalnej lokalizacji.

Poniższy przykład przywraca plik *RestoreTest.txt* w oryginalnej lokalizacji: *udział plików azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Przywracanie pojedynczych plików lub folderów do lokalizacji alternatywnej

Aby przywrócić określone pliki lub foldery do lokalizacji alternatywnej, użyj polecenia cmdlet [az backup restore restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) z trybem przywracania ustawionym na *alternatelocation* i określ następujące parametry związane z obiektem docelowym:

* **--target-storage-account:** konto magazynu, do którego jest przywracana zawartość kopii zapasowej. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **--target-file-share:** udział plików w ramach docelowego konta magazynu, do którego jest przywracana zawartość kopii zapasowej.
* **--target-folder:** folder w ramach udziału plików, do którego są przywracane dane. Jeśli zawartość kopii zapasowej ma zostać przywrócona do folderu głównego, podaj wartość folderu docelowego jako pusty ciąg.

Poniższy przykład przywraca plik *RestoreTest.txt* pierwotnie obecny w udziałach *plików azurefiles* do lokalizacji alternatywnej: folder *restoredata* w udziałach plików *azurefiles1* hostowanych na koncie magazynu *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Przywracanie wielu plików lub folderów do lokalizacji oryginalnej lub alternatywnej

Aby wykonać przywracanie dla wielu elementów, przekaż wartość parametru  **source-file-path** jako spacje rozdzielone ścieżkami wszystkich plików lub folderów, które chcesz przywrócić.

Poniższy przykład przywraca pliki *Restore.txt* i *AFS* Report.docxplików w ich oryginalnej lokalizacji.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

Dane wyjściowe będą mieć postać podobną do następującej:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

Jeśli chcesz przywrócić wiele elementów do lokalizacji alternatywnej, użyj powyższego polecenia, określając parametry związane z obiektem docelowym, jak wyjaśniono w sekcji Przywracanie pojedynczych plików lub folderów do lokalizacji [alternatywnej.](#restore-individual-files-or-folders-to-an-alternate-location)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zarządzać kopiami zapasami udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.](manage-afs-backup-cli.md)
