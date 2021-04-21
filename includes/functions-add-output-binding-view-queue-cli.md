---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 921127ffdd35007cc3fa2eaaa95cdb3fac8bbe15
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782293"
---
Kolejkę można wyświetlić w Azure Portal [lub](../articles/storage/queues/storage-quickstart-queues-portal.md) w Eksplorator usługi Microsoft Azure Storage [.](https://storageexplorer.com/) Kolejkę można również wyświetlić w interfejsie wiersza polecenia platformy Azure, zgodnie z opisem w następujących krokach:

1. Otwórz plik projektu funkcji *local.setting.js* i skopiuj wartość parametrów połączenia. W terminalu lub oknie polecenia uruchom następujące polecenie, aby utworzyć zmienną środowiskową o nazwie , wklejając określone `AZURE_STORAGE_CONNECTION_STRING` ciągi połączenia w miejsce . `<MY_CONNECTION_STRING>` (Ta zmienna środowiskowa oznacza, że nie trzeba dostarczać parametrów połączenia do każdego kolejnego polecenia przy użyciu `--connection-string` argumentu ).

    # <a name="bash"></a>[Bash](#tab/bash)
    
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
    
1. (Opcjonalnie) Użyj polecenia [`az storage queue list`](/cli/azure/storage/queue#az_storage_queue_list) , aby wyświetlić kolejki usługi Storage na swoim koncie. Dane wyjściowe tego polecenia powinny zawierać kolejkę o nazwie , która została utworzona, gdy funkcja zapisała swój pierwszy komunikat `outqueue` do tej kolejki.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Użyj polecenia , aby odczytać komunikat z tej kolejki, która powinna być pierwszą nazwą używaną [`az storage message get`](/cli/azure/storage/message#az_storage_message_get) wcześniej podczas testowania funkcji. Polecenie odczytuje i usuwa pierwszy komunikat z kolejki. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
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

    Ten skrypt używa narzędzia certutil do dekodowania kolekcji komunikatów zakodowanej w formacie base64 z lokalnego pliku tymczasowego. Jeśli nie ma żadnych danych wyjściowych, spróbuj usunąć ze skryptu, aby zatrzymać pomijanie danych wyjściowych narzędzia certutil w przypadku wystąpienia `> NUL` błędu. 
    
    ---
    
    Ponieważ treść komunikatu jest przechowywana w formacie [base64,](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)komunikat musi zostać zdekodowany, zanim zostanie wyświetlony. Po wykonaniu `az storage message get` polecenia komunikat zostanie usunięty z kolejki. Jeśli w poleceniu znajduje się tylko jeden komunikat , komunikat nie zostanie pobrany po drugim uruchomieniu tego polecenia i zamiast tego `outqueue` zostanie wyświetlony błąd.
