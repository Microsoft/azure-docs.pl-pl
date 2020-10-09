---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606466"
---
Kolejkę można wyświetlić w [Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) lub  [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/). Możesz również wyświetlić kolejkę w interfejsie wiersza polecenia platformy Azure, zgodnie z opisem w następujących krokach:

1. Otwórzlocal.setting.jsprojektu funkcji * w* pliku i skopiuj wartość parametrów połączenia. W terminalu lub w oknie polecenia Uruchom następujące polecenie, aby utworzyć zmienną środowiskową o nazwie `AZURE_STORAGE_CONNECTION_STRING` , wklejając określone parametry połączenia w miejscu  `<MY_CONNECTION_STRING>` . (Ta zmienna środowiskowa oznacza, że nie trzeba podawać parametrów połączenia do każdego kolejnego polecenia przy użyciu `--connection-string` argumentu).

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[Program PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. Obowiązkowe Użyj [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia, aby wyświetlić kolejki magazynu na Twoim koncie. Dane wyjściowe tego polecenia powinny zawierać kolejkę o nazwie `outqueue` , która została utworzona, gdy funkcja zapisała swój pierwszy komunikat do tej kolejki.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Użyj [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) polecenia, aby odczytać komunikat z tej kolejki, która powinna być pierwszą nazwą użytą podczas testowania funkcji wcześniej. Polecenie odczytuje i usuwa pierwszy komunikat z kolejki. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[Program PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Ten skrypt używa narzędzia Certutil do zdekodowania kolekcji komunikatów kodowanej algorytmem Base64 z lokalnego pliku tymczasowego. Jeśli nie ma żadnych danych wyjściowych, spróbuj usunąć `> NUL` ze skryptu, aby zatrzymać pomijanie danych wyjściowych narzędzia Certutil, w przypadku wystąpienia błędu. 
    
    ---
    
    Ponieważ treść komunikatu jest przechowywana [zakodowana w formacie base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding), komunikat musi zostać zdekodowany przed wyświetleniem. Po wykonaniu `az storage message get` tej operacji wiadomość zostanie usunięta z kolejki. Jeśli w programie wystąpił tylko jeden komunikat `outqueue` , nie będzie on pobierany po drugim uruchomieniu tego polecenia, a zamiast tego zostanie wyświetlony komunikat o błędzie.
