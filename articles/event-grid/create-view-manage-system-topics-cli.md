---
title: Tworzenie i wyświetlanie tematów systemu operacyjnego oraz Azure Event Grid nimi za pomocą interfejsu wiersza polecenia
description: W tym artykule pokazano, jak tworzyć, wyświetlać i usuwać tematy systemowe za pomocą interfejsu wiersza polecenia platformy Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34a098406762fd57dc9dc4b58fc375286f5d5b13
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874301"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Tworzenie i wyświetlanie tematów systemu operacyjnego oraz Event Grid nimi za pomocą interfejsu wiersza polecenia platformy Azure
W tym artykule pokazano, jak tworzyć tematy systemowe i zarządzać nimi przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać omówienie tematów systemowych, zobacz [Tematy dotyczące systemu](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Instalowanie rozszerzenia dla interfejsu wiersza polecenia platformy Azure
W przypadku interfejsu wiersza polecenia platformy Azure [Event Grid rozszerzenie](/cli/azure/azure-cli-extensions-list).

W Cloud Shell:

- Jeśli rozszerzenie zostało zainstalowane wcześniej, zaktualizuj je: `az extension update -n eventgrid`
- Jeśli rozszerzenie nie zostało wcześniej zainstalowane, zainstaluj je:  `az extension add -n eventgrid`

W przypadku instalacji lokalnej:

1. [Zainstaluj interfejs wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Upewnij się, że masz najnowszą wersję, sprawdzając za `az --version` pomocą .
2. Odinstaluj poprzednie wersje rozszerzenia: `az extension remove -n eventgrid`
3. Zainstaluj rozszerzenie eventgrid za pomocą `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Tworzenie tematu systemowego

- Aby najpierw utworzyć temat systemowy w źródle platformy Azure, a następnie utworzyć subskrypcję zdarzeń dla tego tematu, zobacz następujące tematy referencyjne:
    - [az eventgrid system-topic create](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_create)

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

        Aby uzyskać listę wartości, których można użyć `topic-type` do utworzenia tematu systemowego, uruchom następujące polecenie. Te wartości typu tematu reprezentują źródła zdarzeń, które obsługują tworzenie tematów systemowych. `Microsoft.EventGrid.Topics`Zignoruj `Microsoft.EventGrid.Domains` i z listy. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid system-topic event-subscription create](/cli/azure/eventgrid/system-topic/event-subscription#az_eventgrid_system_topic_event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Aby utworzyć temat systemowy (niejawnie) podczas tworzenia subskrypcji zdarzeń dla źródła platformy Azure, użyj metody [az eventgrid event-subscription create.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) Oto przykład:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Aby uzyskać samouczek z instrukcjami krok po kroku, zobacz [Subskrybowanie konta magazynu.](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)

## <a name="view-all-system-topics"></a>Wyświetl wszystko systemowe tematy
Aby wyświetlić wszystkie tematy systemowe i szczegóły wybranego tematu systemowego, użyj następujących poleceń:

- [az eventgrid system-topic list](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid system-topic show](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Usuwanie tematu systemowego
Aby usunąć temat systemowy, użyj następującego polecenia: 

- [az eventgrid system-topic delete](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o [systemowych tematach](system-topics.md) i typach tematów obsługiwanych przez Azure Event Grid, zobacz tematy systemowe w Azure Event Grid sekcji. 