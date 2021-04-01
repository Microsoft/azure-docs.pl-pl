---
title: Tworzenie kopii zapasowych udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia kopii zapasowych udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 34eea8daa6a0a8920c842178664055838b06a78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94565895"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Tworzenie kopii zapasowych udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure udostępnia środowisko wiersza polecenia do zarządzania zasobami platformy Azure. To doskonałe narzędzie do tworzenia niestandardowych automatyzacji do korzystania z zasobów platformy Azure. W tym artykule szczegółowo opisano sposób tworzenia kopii zapasowych udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu programu [Azure PowerShell](./backup-azure-afs-automation.md) lub w witrynie [Azure Portal](backup-afs.md).

Po zakończeniu tego samouczka dowiesz się, jak wykonać poniższe operacje przy użyciu interfejsu wiersza polecenia platformy Azure:

* Tworzenie magazynu usługi Recovery Services
* Włącz tworzenie kopii zapasowych dla udziałów plików platformy Azure
* Wyzwalanie kopii zapasowej na żądanie dla udziałów plików

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2.0.18 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services jest jednostką, która zapewnia skonsolidowany widok i możliwość zarządzania w ramach wszystkich elementów kopii zapasowej. Gdy zadanie tworzenia kopii zapasowej chronionego zasobu zostaje uruchomione, tworzony jest punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Wykonaj następujące kroki, aby utworzyć magazyn Recovery Services:

1. Magazyn znajduje się w grupie zasobów. Jeśli nie masz istniejącej grupy zasobów, Utwórz nową przy użyciu [AZ Group Create](/cli/azure/group#az-group-create) . W tym samouczku utworzymy nową grupę zasobów *migracji pamięci* w regionie Wschodnie stany USA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Użyj polecenia [AZ Backup magazynu Create](/cli/azure/backup/vault#az-backup-vault-create) , aby utworzyć magazyn. Określ tę samą lokalizację dla magazynu, który został użyty dla grupy zasobów.

    Poniższy przykład tworzy magazyn Recovery Services o nazwie *azurefilesvault* w regionie Wschodnie stany USA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Włącz tworzenie kopii zapasowych dla udziałów plików platformy Azure

W tej sekcji założono, że masz już udział plików platformy Azure, dla którego chcesz skonfigurować kopię zapasową. Jeśli go nie masz, Utwórz udział plików platformy Azure przy użyciu polecenia [AZ Storage Share Create](/cli/azure/storage/share#az-storage-share-create) .

Aby włączyć tworzenie kopii zapasowych udziałów plików, należy utworzyć zasady ochrony, które definiują, kiedy zadanie tworzenia kopii zapasowej jest uruchamiane oraz jak długo są przechowywane punkty odzyskiwania. Zasady tworzenia kopii zapasowych można utworzyć za pomocą polecenia [AZ Backup Policy Create](/cli/azure/backup/policy#az-backup-policy-create) .

W poniższym przykładzie użyto polecenia [AZ Backup Protection Enable-for-azurefileshare](/cli/azure/backup/protection#az-backup-protection-enable-for-azurefileshare) , aby włączyć tworzenie kopii zapasowej udziału plików *migracji pamięci* na koncie magazynu *afsaccount* przy użyciu zasad tworzenia kopii zapasowej *harmonogramu 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji **włączania kopii zapasowej** . Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Wyzwalanie kopii zapasowej na żądanie dla udziału plików

Jeśli chcesz wyzwolić kopię zapasową na żądanie dla udziału plików zamiast czekać, aż zasady tworzenia kopii zapasowych uruchomią zadanie w zaplanowanym czasie, użyj polecenia [AZ Backup Protection Backup-Now](/cli/azure/backup/protection#az-backup-protection-backup-now) .

Należy zdefiniować następujące parametry, aby wyzwolić kopię zapasową na żądanie:

* **--Container-Name** to nazwa konta magazynu obsługującego udział plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name** to nazwa udziału plików, dla którego chcesz wyzwolić kopię zapasową na żądanie. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [AZ Backup Item list](/cli/azure/backup/item#az-backup-item-list) .
* **--Zachowaj-do** określa datę, do której ma zostać zachowany punkt odzyskiwania. Wartość powinna być ustawiona w formacie czasu UTC (dd-mm-rrrr).

Poniższy przykład wyzwala tworzenie kopii zapasowych na żądanie dla udziału *migracji pamięci* na koncie magazynu *afsaccount* z przechowywaniem do *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji "kopia zapasowa na żądanie". Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić udziały plików platformy Azure przy użyciu interfejsu wiersza polecenia](restore-afs-cli.md)
* Informacje na temat [zarządzania kopiami zapasowymi udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia](manage-afs-backup-cli.md)
