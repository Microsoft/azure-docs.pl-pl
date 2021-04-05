---
title: Tworzenie pamięci podręcznej platformy Azure w warstwie Premium dla usługi Redis za pomocą klastrowania
description: Ten przykładowy kod interfejsu wiersza polecenia platformy Azure przedstawia sposób tworzenia pamięci podręcznej systemu Azure w warstwie Premium o pojemności 6 GB dla Redis z włączoną obsługą klastrów i dwoma fragmentów.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb5a6ac082ebaf978023321f15341ec7f35779a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184228"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Tworzenie pamięci podręcznej systemu Azure w warstwie Premium dla Redis przy użyciu klastrowania

W tym scenariuszu dowiesz się, jak utworzyć pamięć podręczną systemu Azure w warstwie Premium o pojemności 6 GB dla Redis z włączoną obsługą klastrów i dwoma fragmentów.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia grupy zasobów i pamięci podręcznej platformy Azure dla usługi Redis z włączonym klastrowaniem. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [AZ Redis Create](/cli/azure/redis) | Utwórz wystąpienie usługi Azure cache for Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi Azure cache for Redis znajdują się w [pamięci podręcznej platformy Azure w celu uzyskania dokumentacji Redis](../cli-samples.md).