---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — usuwanie kontenerów na podstawie prefiksu | Microsoft Docs
description: Usuń kontenery obiektów BLOB usługi Azure Storage na podstawie prefiksu nazwy kontenera, a następnie wyczyść wdrożenie. Zobacz linki pomocy dla poleceń używanych w przykładzie skryptu.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: aeccf255004cd4512fbc591942324341504b20f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87901899"
---
# <a name="use-an-azure-cli-script-to-delete-containers-based-on-container-name-prefix"></a>Używanie skryptu interfejsu wiersza polecenia platformy Azure do usuwania kontenerów na podstawie prefiksu nazwy kontenera

Ten skrypt tworzy kilka przykładowych kontenerów w usłudze Azure Blob Storage, a następnie usuwa niektóre z nich na podstawie prefiksu nazwy kontenera.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, pozostałe kontenery i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do usunięcia kontenerów na podstawie prefiksu nazwy kontenera. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account) | Tworzy konto usługi Azure Storage we wskazanej grupie zasobów. |
| [az storage container create](/cli/azure/storage/container) | Tworzy kontener w usłudze Azure Blob Storage. |
| [az storage container list](/cli/azure/storage/container) | Zwraca listę kontenerów na koncie usługi Azure Storage. |
| [az storage container delete](/cli/azure/storage/container) | Usuwa kontenery z konta usługi Azure Storage. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Storage można znaleźć na stronie [Azure CLI samples for Azure Storage (Przykłady skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Storage)](../blobs/storage-samples-blobs-cli.md).
