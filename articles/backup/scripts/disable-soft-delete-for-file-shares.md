---
title: Przykładowy skrypt — wyłączenie usuwania nietrwałego dla udziału plików
description: Dowiedz się, jak za pomocą skryptu wyłączyć usuwanie nietrwałe dla udziałów plików na koncie magazynu.
ms.topic: sample
ms.date: 02/02/2020
ms.openlocfilehash: 172a7bd19ffd07ca7900a457c447362343e5f185
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84122796"
---
# <a name="disable-soft-delete-for-file-shares-in-a-storage-account"></a>Wyłącz usuwanie nietrwałe dla udziałów plików na koncie magazynu

W tym dokumencie opisano proces wyłączania nietrwałego usuwania udziałów plików na koncie magazynu.

Wykonaj następujące kroki:

1. Zainstaluj armclient. Aby dowiedzieć się, jak go zainstalować, odwiedź [link](https://github.com/projectkudu/ARMClient).

2. Zapisz następujące dwa pliki treści żądania do folderu na komputerze.

    ```json
    rqbody-enableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":true,
        "days": 14
        }
    },
    "cors": {
        "corsRules": []
    }
    }

    rqbody-disableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":false,
        "days": 0
        }
    },
    "cors": {
        "corsRules": []
    }
    }
    ```

3. Zachowaj przydatny identyfikator konta magazynu Azure Resource Manager (ARM). Na przykład: `/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare/providers/Microsoft.Storage/storageAccounts/inquirytest`

4. Zaloguj się przy użyciu swoich poświadczeń, uruchamiając **login armclient**.

5. Pobierz bieżące właściwości usuwania nietrwałego udziałów plików na koncie magazynu.

    Następująca operacja GET pobiera właściwości usuwania nietrwałego dla udziałów plików na koncie *inquirytest* :

    ```cmd
    armclient get /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01
    ```

    ```output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "cors": {
        "corsRules": []
        },
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```

6. Wyłącz usuwanie nietrwałe dla udziałów plików na koncie magazynu.

    Następująca operacja PUT wyłącza właściwości usuwania nietrwałego dla udziałów plików w ramach konta *inquirytest* :

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-disableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": false,
        "days": 0
        }
    }
    }
    ```

7. Jeśli chcesz ponownie włączyć usuwanie nietrwałe, użyj poniższego przykładu.

    Następująca operacja PUT włącza właściwości usuwania nietrwałego dla udziałów plików w ramach konta "inquirytest".

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-EnableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```
