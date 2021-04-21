---
title: Back up Azure file shares with Azure CLI
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do kopii zapasowej udziałów plików platformy Azure w magazynie usługi Recovery Services
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: a5f7472c511a5a50415a6ceb47497dd6f4f1e60b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773625"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Back up Azure file shares with Azure CLI

Interfejs wiersza polecenia platformy Azure udostępnia środowisko wiersza polecenia do zarządzania zasobami platformy Azure. Jest to doskonałe narzędzie do tworzenia niestandardowych automatyzacji w celu korzystania z zasobów platformy Azure. W tym artykule szczegółowo opisano sposób kopii zapasowej udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu programu [Azure PowerShell](./backup-azure-afs-automation.md) lub w witrynie [Azure Portal](backup-afs.md).

Po zakończeniu tego samouczka dowiesz się, jak wykonać poniższe operacje przy użyciu interfejsu wiersza polecenia platformy Azure:

* Tworzenie magazynu usługi Recovery Services
* Włączanie tworzenia kopii zapasowej udziałów plików platformy Azure
* Wyzwalanie kopii zapasowej udziałów plików na żądanie

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usługi Recovery Services to jednostka, która zapewnia skonsolidowany widok i możliwość zarządzania wszystkimi elementami kopii zapasowej. Gdy zadanie tworzenia kopii zapasowej chronionego zasobu zostaje uruchomione, tworzony jest punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Wykonaj następujące kroki, aby utworzyć magazyn usługi Recovery Services:

1. Magazyn jest umieszczany w grupie zasobów. Jeśli nie masz istniejącej grupy zasobów, utwórz nową grupę za pomocą az [group create](/cli/azure/group#az_group_create) . W tym samouczku utworzymy nową grupę zasobów *azurefiles* w regionie Wschodnie usa.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Użyj polecenia [cmdlet az backup vault create,](/cli/azure/backup/vault#az_backup_vault_create) aby utworzyć magazyn. Określ tę samą lokalizację magazynu, która została użyta dla grupy zasobów.

    Poniższy przykład tworzy magazyn usługi Recovery Services o *nazwie azurefilesvault* w regionie Wschodnie usa.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Włączanie tworzenia kopii zapasowej udziałów plików platformy Azure

W tej sekcji założono, że masz już udział plików platformy Azure, dla którego chcesz skonfigurować kopię zapasową. Jeśli go nie masz, utwórz udział plików platformy Azure za pomocą [polecenia az storage share create.](/cli/azure/storage/share#az_storage_share_create)

Aby włączyć tworzenie kopii zapasowej udziałów plików, należy utworzyć zasady ochrony, które definiują czas wykonywania zadania tworzenia kopii zapasowej i czas przechowywania punktów odzyskiwania. Zasady tworzenia kopii zapasowych można utworzyć za pomocą [polecenia cmdlet az backup policy create.](/cli/azure/backup/policy#az_backup_policy_create)

W poniższym przykładzie użyto polecenia cmdlet [az backup protection enable-for-azurefileshare,](/cli/azure/backup/protection#az_backup_protection_enable_for_azurefileshare) aby włączyć tworzenie kopii zapasowej udziału plików *azurefiles* na koncie magazynu *afsaccount* przy użyciu zasad tworzenia kopii zapasowych harmonogramu *1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji **włączania kopii zapasowej.** Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Wyzwalanie kopii zapasowej udziału plików na żądanie

Jeśli chcesz wyzwolić kopię zapasową udziału plików na żądanie, zamiast czekać na uruchomienie zadania przez zasady tworzenia kopii zapasowej w zaplanowanym czasie, użyj polecenia cmdlet [az backup protection backup-now.](/cli/azure/backup/protection#az_backup_protection_backup_now)

Aby wyzwolić tworzenie kopii zapasowej na żądanie, należy zdefiniować następujące parametry:

* **--container-name** to nazwa konta magazynu hostowania udziału plików. Aby pobrać **nazwę lub** **przyjazną nazwę** kontenera, użyj polecenia az backup container [list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name** to nazwa udziału plików, dla którego chcesz wyzwolić kopię zapasową na żądanie. Aby pobrać **nazwę lub** **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia az backup item [list.](/cli/azure/backup/item#az_backup_item_list)
* **--retain-until** określa datę do momentu zachowania punktu odzyskiwania. Wartość powinna być ustawiona w formacie czasu UTC (dd-mm-yyyy).

Poniższy przykład wyzwala kopię zapasową na żądanie dla udział plików *azurefiles* na koncie *magazynu afsaccount* z przechowywaniem do *20-01-2020.*

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Atrybut **Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji tworzenia kopii zapasowej na żądanie. Aby śledzić stan zadania, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić udziały plików platformy Azure za pomocą interfejsu wiersza polecenia](restore-afs-cli.md)
* Dowiedz się, jak [zarządzać kopiami zapasami udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia](manage-afs-backup-cli.md)
