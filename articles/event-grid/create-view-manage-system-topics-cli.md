---
title: Tworzenie i wyświetlanie tematów systemu Azure Event Grid oraz zarządzanie nimi za pomocą interfejsu wiersza polecenia
description: W tym artykule pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure tworzyć, wyświetlać i usuwać tematy systemowe.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c1c847c7f25e3a656b798e186a408e560b9ee9e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633226"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Tworzenie i wyświetlanie Event Grid tematów systemowych przy użyciu interfejsu wiersza polecenia platformy Azure oraz zarządzanie nimi
W tym artykule pokazano, jak tworzyć tematy systemowe i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure. Aby zapoznać się z omówieniem tematów systemowych, zobacz [Tematy systemowe](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure
W przypadku interfejsu wiersza polecenia platformy Azure wymagane jest [rozszerzenie Event Grid](/cli/azure/azure-cli-extensions-list).

W Cloud Shell:

- Jeśli wcześniej zainstalowano rozszerzenie, zaktualizuj je: `az extension update -n eventgrid`
- Jeśli rozszerzenie nie zostało wcześniej zainstalowane, zainstaluj je:  `az extension add -n eventgrid`

W przypadku instalacji lokalnej:

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Upewnij się, że masz najnowszą wersję, sprawdzając ją `az --version` .
2. Odinstaluj poprzednie wersje rozszerzenia: `az extension remove -n eventgrid`
3. Zainstaluj rozszerzenie eventgrid z `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Tworzenie tematu systemu

- Aby najpierw utworzyć temat systemowy w źródle platformy Azure, a następnie utworzyć subskrypcję zdarzeń dla tego tematu, zobacz następujące tematy dotyczące odwołań:
    - [AZ eventgrid system-temat Create](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        Aby uzyskać listę `topic-type` wartości, których można użyć do utworzenia tematu systemowego, uruchom następujące polecenie. Te wartości typu tematu reprezentują źródła zdarzeń, które obsługują tworzenie tematów systemowych. Zignoruj `Microsoft.EventGrid.Topics` i wybierz `Microsoft.EventGrid.Domains` z listy. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [zdarzenie AZ eventgrid system-temat-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Aby utworzyć temat systemowy (niejawnie) podczas tworzenia subskrypcji zdarzeń dla źródła platformy Azure, użyj metody [AZ eventgrid Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) . Oto przykład:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku, zobacz [subskrybowanie do konta magazynu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Wyświetl wszystkie tematy dotyczące systemu
Aby wyświetlić wszystkie tematy systemowe i szczegóły wybranego tematu systemu, użyj następujących poleceń:

- [AZ eventgrid system-temat list](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [AZ eventgrid system-temat show](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Usuwanie tematu systemu
Aby usunąć temat systemowy, użyj następującego polecenia: 

- [AZ eventgrid system-temat Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Następne kroki
Zobacz [Tematy systemowe w Azure Event Grid](system-topics.md) sekcji, aby dowiedzieć się więcej o tematach systemowych i typach tematów obsługiwanych przez Azure Event Grid. 