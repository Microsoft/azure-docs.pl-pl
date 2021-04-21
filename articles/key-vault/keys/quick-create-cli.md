---
title: Tworzenie i pobieranie atrybutów klucza w usłudze Azure Key Vault — interfejs wiersza polecenia platformy Azure
description: Przewodnik Szybki start pokazujący, jak ustawić i pobrać klucz z usługi Azure Key Vault interfejsie wiersza polecenia platformy Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: f5281c02f68598b1cf121a6ab8a574f0694f5ea2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813456"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Szybki start: konfigurowanie i pobieranie klucza z usługi Azure Key Vault interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start utworzysz magazyn kluczy w usłudze Azure Key Vault interfejsie wiersza polecenia platformy Azure. Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na Key Vault, zapoznaj się z tematem [Omówienie.](../general/overview.md) Interfejs wiersza polecenia platformy Azure służy do tworzenia zasobów platformy Azure i zarządzanie nimi za pomocą poleceń lub skryptów. Po zakończeniu tego procesu będziesz przechowywać klucz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Dodawanie klucza do Key Vault

Aby dodać klucz do magazynu, wystarczy wykonać kilka dodatkowych kroków. Ten klucz może być używany przez aplikację. 

Wpisz poniższe polecenia, aby utworzyć klucz o nazwie **ExampleKey:**

```azurecli
az keyvault key create --vault-name "<your-unique-keyvault-name>" -n ExampleKey --protection software
```

Teraz możesz odwoływać się do tego klucza, który został dodany do Azure Key Vault przy użyciu jego wartości URI. Użyj **"https://<your-unique-keyvault-name>.vault.azure.net/keys/ExampleKey",** aby uzyskać bieżącą wersję. 

Aby wyświetlić wcześniej zapisany klucz:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "<your-unique-keyvault-name>"
```

Teraz utworzono nową Key Vault, przechowywano klucz i pobrano go.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Key Vault przechowywany w nim klucz. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zobacz informacje dotyczące poleceń [az keyvault interfejsu wiersza polecenia platformy Azure](/cli/azure/keyvault)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)
