---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77474282"
---
### <a name="query-the-storage-queue"></a>Wykonywanie zapytań względem kolejki magazynu

Możesz użyć [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia, aby wyświetlić kolejki magazynu na koncie, tak jak w poniższym przykładzie:

```azurecli-interactive
az storage queue list --output tsv
```

Dane wyjściowe tego polecenia obejmują kolejkę o nazwie `outqueue` , która jest kolejką utworzoną podczas uruchamiania funkcji.

Następnie użyj polecenia, [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) Aby wyświetlić komunikaty w tej kolejce, jak w tym przykładzie:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Zwracany ciąg powinien być taki sam jak komunikat wysłany do przetestowania funkcji.

> [!NOTE]  
> Poprzedni przykład dekoduje zwracany ciąg z base64. Wynika to z faktu, że powiązania magazynu kolejek zapisują i odczytują dane z usługi Azure Storage jako [ciągi Base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).