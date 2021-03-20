---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — obliczanie rozmiaru kontenera obiektów blob | Microsoft Docs
description: Obliczanie rozmiaru kontenera w usłudze Azure Blob Storage przez zsumowanie rozmiaru obiektów blob w tym kontenerze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 45712632ebfb2da4b713038503965ce908c1dfc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87498892"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Obliczanie rozmiaru kontenera w usłudze Blob Storage

Ten skrypt oblicza rozmiar kontenera w usłudze Azure Blob Storage przez zsumowanie rozmiaru obiektów blob w tym kontenerze.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Ten skrypt interfejsu wiersza polecenia określa szacunkowy rozmiar kontenera i nie należy go używać w obliczeniach na potrzeby rozliczeń.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, kontener i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do obliczenia rozmiaru kontenera w usłudze Blob Storage. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage blob upload](/cli/azure/storage/account) | Przekazuje pliki lokalne do kontenera w usłudze Azure Blob Storage. |
| [az storage blob list](/cli/azure/storage/account/keys) | Zwraca listę obiektów blob w kontenerze usługi Azure Blob Storage. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Storage można znaleźć na stronie [Azure CLI samples for Azure Blob Storage (Przykłady skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Blob Storage)](../blobs/storage-samples-blobs-cli.md).
