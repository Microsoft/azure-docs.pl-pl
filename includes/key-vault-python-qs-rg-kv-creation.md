---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967130"
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

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```