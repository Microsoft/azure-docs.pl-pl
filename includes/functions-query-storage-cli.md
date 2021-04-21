---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 642989df8dca9d4ae121d80e30a9fb6d8dd06286
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800193"
---
### <a name="query-the-storage-queue"></a>Wykonywanie zapytań w kolejce usługi Storage

Możesz użyć polecenia , aby wyświetlić kolejki usługi [`az storage queue list`](/cli/azure/storage/queue#az_storage_queue_list) Storage na swoim koncie, jak w poniższym przykładzie:

```azurecli-interactive
az storage queue list --output tsv
```

Dane wyjściowe tego polecenia zawierają kolejkę o nazwie , która jest kolejką utworzoną `outqueue` podczas działania funkcji.

Następnie użyj polecenia , aby wyświetlić komunikaty w tej [`az storage message peek`](/cli/azure/storage/message#az_storage_message_peek) kolejce, jak w poniższym przykładzie:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Zwrócony ciąg powinien być taki sam jak komunikat wysłany w celu przetestowania funkcji.

> [!NOTE]  
> Poprzedni przykład dekoduje zwrócony ciąg z base64. Jest to spowodowane tym, że powiązania usługi Queue Storage zapis w usłudze Azure Storage i odczyt z tej usługi są odczytywane jako [ciągi base64.](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)