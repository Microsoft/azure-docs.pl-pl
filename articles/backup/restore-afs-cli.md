---
title: Przywracanie udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure przywrócić kopie zapasowe udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: be744fdb79f442eaf0ef632952d9c0b9e709d908
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325015"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Przywracanie udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure zapewnia obsługę systemu Azure w programie. To doskonałe narzędzie do tworzenia niestandardowych automatyzacji do korzystania z zasobów platformy Azure. W tym artykule wyjaśniono, jak przywrócić cały udział plików lub określone pliki z punktu przywracania utworzonego przez [Azure Backup](./backup-overview.md) przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu programu [Azure PowerShell](./backup-azure-afs-automation.md) lub w witrynie [Azure Portal](backup-afs.md).

Na końcu tego artykułu dowiesz się, jak wykonywać następujące operacje za pomocą interfejsu wiersza polecenia platformy Azure:

* Wyświetl punkty przywracania dla kopii zapasowej udziału plików platformy Azure.
* Przywróć pełny udział plików platformy Azure.
* Przywróć pojedyncze pliki lub foldery.

>[!NOTE]
> Azure Backup teraz obsługuje przywracanie wielu plików lub folderów do lokalizacji oryginalnej lub alternatywnej przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej, zapoznaj się z sekcją [przywracanie wielu plików lub folderów do oryginalnej lub alternatywnej lokalizacji](#restore-multiple-files-or-folders-to-original-or-alternate-location) tego dokumentu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować interfejs wiersza polecenia lokalnie i korzystać z niego, należy korzystać z interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Aby znaleźć wersję interfejsu wiersza polecenia, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz już udział plików platformy Azure, którego kopia zapasowa została utworzona przez Azure Backup. Jeśli go nie masz, zobacz Tworzenie kopii zapasowych [udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia](backup-afs-cli.md) w celu skonfigurowania usługi Backup dla udziału plików. W tym artykule opisano użycie następujących zasobów:

| Udział plików  | Konto magazynu | Region | Szczegóły                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *migracji pamięci*  | *afsaccount*      | EastUS | Oryginalne źródło kopii zapasowej przy użyciu Azure Backup                 |
| *azurefiles1* | *afaccount1*      | EastUS | Źródło docelowe używane do odzyskiwania lokalizacji alternatywnej |

Możesz użyć podobnej struktury dla udziałów plików, aby wypróbować różne typy przywracania wyjaśnione w tym artykule.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Pobierz punkty odzyskiwania dla udziału plików platformy Azure

Użyj polecenia [AZ Backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) , aby wyświetlić listę wszystkich punktów odzyskiwania kopii zapasowej udziału plików.

Poniższy przykład pobiera listę punktów odzyskiwania dla udziału plików *migracji pamięci* na koncie magazynu *afsaccount* .

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Możesz również uruchomić poprzednie polecenie cmdlet przy użyciu przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--Backup-Management-Type**: *azurestorage*
* **--Typ obciążenia**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Zestaw wyników to lista punktów odzyskiwania wraz ze szczegółami dotyczącymi czasu i spójności poszczególnych punktów przywracania.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Atrybut **name** w danych wyjściowych odpowiada nazwie punktu odzyskiwania, który może być używany jako wartość parametru **--RP-Name** w operacji odzyskiwania.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Odzyskiwanie pełnego udziału przy użyciu interfejsu wiersza polecenia platformy Azure

Można użyć tej opcji przywracania, aby przywrócić pełny udział plików w lokalizacji oryginalnej lub alternatywnej.

Zdefiniuj następujące parametry, aby wykonać operacje przywracania:

* **--Container-Name**: nazwa konta magazynu, które hostuje kopię zapasową oryginalnego udziału plików. Aby pobrać nazwę lub przyjazną nazwę kontenera, użyj polecenia [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name**: Nazwa kopii zapasowej oryginalnego pliku, który ma być używany dla operacji przywracania. Aby pobrać nazwę lub przyjazną nazwę elementu kopii zapasowej, użyj polecenia [AZ Backup Item list](/cli/azure/backup/item#az-backup-item-list) .

### <a name="restore-a-full-share-to-the-original-location"></a>Przywróć pełny udział do oryginalnej lokalizacji

W przypadku przywracania do oryginalnej lokalizacji nie trzeba określać parametrów związanych z elementem docelowym. Należy podać tylko **konflikt** .

W poniższym przykładzie zastosowano polecenie [AZ Backup Restore Restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) z trybem przywracania ustawionym na *originallocation* , aby przywrócić udział plików *migracji pamięci* w oryginalnej lokalizacji. Używany jest punkt odzyskiwania 932883129628959823, który uzyskano w obszarze [pobieranie punktów odzyskiwania dla udziału plików platformy Azure](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Przywróć pełny udział w lokalizacji alternatywnej

Możesz użyć tej opcji, aby przywrócić udział plików w alternatywnej lokalizacji i zachować oryginalny udział plików jako. Określ następujące parametry odzyskiwania lokalizacji alternatywnej:

* **--Target-Storage-account**: konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **--docelowy udział plików**: udział plików w docelowym koncie magazynu, do którego zostanie przywrócona zawartość kopii zapasowej.
* **--Target-folder**: folder w udziale plików, do którego przywracane są dane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, nadaj wartości folderu docelowego jako pusty ciąg.
* **--Rozwiąż konflikt**: instrukcje w przypadku konfliktu z przywróconymi danymi. Akceptuje **zastępowanie** lub **pomijanie**.

Poniższy przykład używa [AZ Backup Restore Restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) z trybem przywracania jako *alternatelocation* , aby przywrócić udział plików *migracji pamięci* na koncie magazynu *afsaccount* do udziału plików *azurefiles1* na koncie magazynu *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="item-level-recovery"></a>Odzyskiwanie na poziomie elementu

Ta opcja przywracania służy do przywracania pojedynczych plików lub folderów w lokalizacji oryginalnej lub alternatywnej.

Zdefiniuj następujące parametry, aby wykonać operacje przywracania:

* **--Container-Name**: nazwa konta magazynu, które hostuje kopię zapasową oryginalnego udziału plików. Aby pobrać nazwę lub przyjazną nazwę kontenera, użyj polecenia [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name**: Nazwa kopii zapasowej oryginalnego pliku, który ma być używany dla operacji przywracania. Aby pobrać nazwę lub przyjazną nazwę elementu kopii zapasowej, użyj polecenia [AZ Backup Item list](/cli/azure/backup/item#az-backup-item-list) .

Określ następujące parametry dla elementów, które mają zostać odzyskane:

* **Sourcefilepath**: ścieżka bezwzględna pliku, która ma zostać przywrócona w udziale plików jako ciąg. Ta ścieżka jest tą samą ścieżką, która jest używana w pliku [AZ Storage File Download](/cli/azure/storage/file#az-storage-file-download) lub [AZ Storage File show](/cli/azure/storage/file#az-storage-file-show) interfejsu wiersza polecenia.
* **SourceFileType**: Wybierz, czy wybrano katalog lub plik. Akceptuje **katalog** lub **plik**.
* **ResolveConflict**: instrukcja, jeśli wystąpił konflikt z przywróconymi danymi. Akceptuje **zastępowanie** lub **pomijanie**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Przywróć pojedyncze pliki lub foldery do oryginalnej lokalizacji

Użyj polecenia [AZ Backup Restore Restore-migracji pamięci](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) z trybem przywracania ustawionym na *originallocation* w celu przywrócenia określonych plików lub folderów do ich oryginalnej lokalizacji.

Poniższy przykład przywraca plik *RestoreTest.txt* w jego pierwotnej lokalizacji: udział plików *migracji pamięci* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Przywracanie pojedynczych plików lub folderów do lokalizacji alternatywnej

Aby przywrócić określone pliki lub foldery do innej lokalizacji, użyj polecenia [AZ Backup Restore Restore-migracji pamięci](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet z trybem przywracania ustawionym na *alternatelocation* i określ następujące parametry powiązane z elementem docelowym:

* **--Target-Storage-account**: konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **--docelowy udział plików**: udział plików w docelowym koncie magazynu, do którego zostanie przywrócona zawartość kopii zapasowej.
* **--Target-folder**: folder w udziale plików, do którego przywracane są dane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, nadaj jej wartość jako pusty ciąg.

Poniższy przykład przywraca plik *RestoreTest.txt* pierwotnie obecny w udziale plików *migracji pamięci* do lokalizacji alternatywnej: folder *restoredata* w udziale plików *azurefiles1* hostowanym na koncie magazynu *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Przywracanie wielu plików lub folderów do lokalizacji oryginalnej lub alternatywnej

Aby wykonać przywracanie dla wielu elementów, należy przekazać wartość parametru **Source-File-Path** jako ścieżki **oddzielone spacjami** wszystkich plików lub folderów, które mają zostać przywrócone.

Poniższy przykład przywraca pliki Report.docx*Restore.txt* i *testów AFS * w ich oryginalnej lokalizacji.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

Dane wyjściowe będą mieć postać podobną do następującej:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

Jeśli chcesz przywrócić wiele elementów do lokalizacji alternatywnej, użyj powyższego polecenia, określając parametry powiązane z elementem docelowym, jak wyjaśniono w sekcji [przywracanie pojedynczych plików lub folderów do alternatywnej lokalizacji](#restore-individual-files-or-folders-to-an-alternate-location) .

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak zarządzać kopiami zapasowymi udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](manage-afs-backup-cli.md).
