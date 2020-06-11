---
title: Tworzenie i wyświetlanie tematów systemu Azure Event Grid oraz zarządzanie nimi za pomocą interfejsu wiersza polecenia
description: W tym artykule pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure tworzyć, wyświetlać i usuwać tematy systemowe.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 19a22a0a3b528a9a72fdd51c589e42bf2fba5ce7
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669940"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Tworzenie i wyświetlanie Event Grid tematów systemowych przy użyciu interfejsu wiersza polecenia platformy Azure oraz zarządzanie nimi
W tym artykule pokazano, jak tworzyć tematy systemowe i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure. Aby zapoznać się z omówieniem tematów systemowych, zobacz [Tematy systemowe](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure
W przypadku interfejsu wiersza polecenia platformy Azure wymagane jest [rozszerzenie Event Grid](/cli/azure/azure-cli-extensions-list).

W CloudShell:

- Jeśli wcześniej zainstalowano rozszerzenie, zaktualizuj je:`az extension update -n eventgrid`
- Jeśli rozszerzenie nie zostało wcześniej zainstalowane, zainstaluj je:`az extension add -n eventgrid`

W przypadku instalacji lokalnej:

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Upewnij się, że masz najnowszą wersję, sprawdzając polecenie AZ--Version.
2. Odinstaluj poprzednie wersje rozszerzenia:`az extension remove -n eventgrid`
3. Zainstaluj rozszerzenie eventgrid z`az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Tworzenie tematu systemu

- Aby najpierw utworzyć temat systemowy w źródle platformy Azure, a następnie utworzyć subskrypcję zdarzeń dla tego tematu, zobacz następujące tematy dotyczące odwołań:
    - [AZ eventgrid system-temat Create](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli
        az eventgrid system-topic create \
            -g myResourceGroup \
            --name systemtopic1 \
            --location westus2 \
            --topic-type microsoft.storage.storageaccounts \
            --source /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/storagegaccountname
        ```
    - [zdarzenie AZ eventgrid system-temat-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli
        az eventgrid system-topic event-subscription create --name es1 \
            -g rg1 --system-topic-name systemtopic1 \
            --endpoint https://contoso.azurewebsites.net/api/f1?code=code         
          ```
- To create a system topic (implicitly) when creating an event subscription for an Azure source, use the [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) method. Here's an example:
    
    ```azurecli
    storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
    endpoint=https://$sitename.azurewebsites.net/api/updates

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <event_subscription_name> \
      --endpoint $endpoint
    ```
    Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku, zobacz [subskrybowanie do konta magazynu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Wyświetl wszystkie tematy dotyczące systemu
Aby wyświetlić wszystkie tematy systemowe i szczegóły wybranego tematu systemu, użyj następujących poleceń:

- [AZ eventgrid system-temat list](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli
    az eventgrid system-topic list   
     ```
- [AZ eventgrid system-temat show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli
    az eventgrid system-topic show -g rg1 -n systemtopic1    
     ```

## <a name="delete-a-system-topic"></a>Usuwanie tematu systemu
Aby usunąć temat systemowy, użyj następującego polecenia: 

- [AZ eventgrid system-temat Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli
    az eventgrid system-topic delete -g myResourceGroup --name systemtopic1  
     ```

## <a name="next-steps"></a>Następne kroki
Zobacz [Tematy systemowe w Azure Event Grid](system-topics.md) sekcji, aby dowiedzieć się więcej o tematach systemowych i typach tematów obsługiwanych przez Azure Event Grid. 
