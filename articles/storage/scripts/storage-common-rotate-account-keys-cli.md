---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — rotowanie kluczy dostępu do konta magazynu | Microsoft Docs
description: Tworzenie konta usługi Azure Storage oraz pobieranie i rotowanie kluczy dostępu do konta.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 10/20/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 08e1b3837863b197f8463a0d969e78afab2b9858
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370410"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Tworzenie konta magazynu i rotowanie kluczy dostępu do konta

Ten skrypt tworzy konto usługi Azure Storage, wyświetla klucze dostępu do nowego konta magazynu, a następnie odnawia (rotuje) te klucze.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, konto magazynu oraz wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do utworzenia konta magazynu oraz pobrania i rotowania kluczy dostępu do tego konta. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account) | Tworzy konto usługi Azure Storage we wskazanej grupie zasobów. |
| [az storage account keys list](/cli/azure/storage/account/keys) | Wyświetla klucze dostępu do podanego konta magazynu. |
| [az storage account keys renew](/cli/azure/storage/account/keys) | Ponownie generuje podstawowy lub pomocniczy klucz dostępu do konta. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Storage można znaleźć na stronie [Azure CLI samples for Azure Blob Storage (Przykłady skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Blob Storage)](../blobs/storage-samples-blobs-cli.md).
