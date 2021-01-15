---
title: 'Szybki Start: Tworzenie obszaru roboczego Synapse przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Utwórz obszar roboczy usługi Azure Synapse za pomocą interfejsu wiersza polecenia platformy Azure, wykonując kroki opisane w tym przewodniku.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 2658240e670e617f7296881f733ff369b9bf8f87
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219032"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Szybki Start: Tworzenie obszaru roboczego usługi Azure Synapse przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to środowisko wiersza polecenia platformy Azure do zarządzania jej zasobami. Można używać go w przeglądarce za pośrednictwem usługi Azure Cloud Shell. Istnieje także możliwość zainstalowania go w systemach macOS, Linux lub Windows, a następnie uruchomienia z poziomu wiersza polecenia.

W tym przewodniku szybki start dowiesz się, jak utworzyć obszar roboczy Synapse przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz i zainstaluj [JQ](https://stedolan.github.io/jq/download/), lekki i elastyczny procesor JSON w wierszu polecenia
- [Konto magazynu Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > Obszar roboczy usługi Azure Synapse musi mieć możliwość odczytu i zapisu na wybranym koncie ADLS Gen2. Ponadto dla dowolnego konta magazynu, które można połączyć jako konto magazynu podstawowego, należy włączyć **hierarchiczną przestrzeń nazw**  podczas tworzenia konta magazynu, zgodnie z opisem na stronie [Tworzenie magazynu AccOUT](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) . 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Tworzenie obszaru roboczego usługi Azure Synapse za pomocą interfejsu wiersza polecenia platformy Azure

1. Zdefiniuj niezbędne zmienne środowiskowe, aby utworzyć zasoby dla obszaru roboczego usługi Azure Synapse.

    | Nazwa zmiennej środowiskowej | Opis |
    |---|---|---|
    |StorageAccountName| Nazwa istniejącego konta magazynu ADLS Gen2.|
    |StorageAccountResourceGroup| Nazwa istniejącej grupy zasobów konta magazynu ADLS Gen2. |
    |FileShareName| Nazwa istniejącego systemu plików magazynu.|
    |SynapseResourceGroup| Wybierz nową nazwę grupy zasobów usługi Azure Synapse. |
    |Region (Region)| Wybierz jeden z [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
    |SynapseWorkspaceName| Wybierz unikatową nazwę dla nowego obszaru roboczego usługi Azure Synapse. |
    |Sqluser| Wybierz wartość nowej nazwy użytkownika.|
    |SqlPassword| Wybierz bezpieczne hasło.|
    |||

2. Utwórz grupę zasobów jako kontener dla obszaru roboczego usługi Azure Synapse:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. Pobierz klucz konta magazynu ADLS generacji 2:
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. Pobierz adres URL punktu końcowego magazynu ADLS generacji 2:
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. Obowiązkowe Zawsze możesz sprawdzić, do czego służy klucz konta magazynu ADLS Gen2 i punkt końcowy:
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. Utwórz obszar roboczy usługi Azure Synapse:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. Pobierz adres URL sieci Web i deweloperski dla obszaru roboczego usługi Azure Synapse:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. Utwórz regułę zapory, aby zezwolić na dostęp do obszaru roboczego usługi Azure Synapse z maszyny:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. Otwórz adres URL sieci Web obszaru roboczego usługi Azure Synapse przechowywany w zmiennej środowiskowej `WorkspaceWeb` , aby uzyskać dostęp do obszaru roboczego:

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [![Sieć Web ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) obszaru roboczego usługi Azure Synapse](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wykonaj poniższe kroki, aby usunąć obszar roboczy usługi Azure Synapse.
> [!WARNING]
> Usunięcie obszaru roboczego usługi Azure Synapse spowoduje usunięcie aparatów analizy i danych przechowywanych w bazie danych zawartych pul SQL i metadanych obszaru roboczego. Nie będzie już możliwe łączenie się z punktami końcowymi SQL lub Apache Spark. Wszystkie artefakty kodu zostaną usunięte (zapytania, notesy, definicje zadań i potoki).
>
> Usunięcie obszaru roboczego **nie** wpłynie na dane w Data Lake Store Gen2 połączonym z obszarem roboczym.

Jeśli chcesz usunąć obszar roboczy usługi Azure Synapse, wykonaj następujące polecenie:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Następnie możesz [utworzyć pule SQL](quickstart-create-sql-pool-studio.md) lub [utworzyć pule Apache Spark](quickstart-create-apache-spark-pool-studio.md) , aby rozpocząć analizowanie i Eksplorowanie danych.