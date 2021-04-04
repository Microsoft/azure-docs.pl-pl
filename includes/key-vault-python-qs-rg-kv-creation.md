---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89482141"
---
1. Użyj `az group create` polecenia, aby utworzyć grupę zasobów:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    Możesz zmienić "Wschód" do lokalizacji zbliżonej do użytkownika, jeśli wolisz.

1. Użyj, `az keyvault create` Aby utworzyć magazyn kluczy:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Zamień na `<your-unique-keyvault-name>` nazwę, która jest unikatowa w całej platformie Azure. Zwykle używasz nazwy prywatnej lub firmowej wraz z innymi numerami i identyfikatorami. 

1. Utwórz zmienną środowiskową, która dostarcza nazwę Key Vault do kodu:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
