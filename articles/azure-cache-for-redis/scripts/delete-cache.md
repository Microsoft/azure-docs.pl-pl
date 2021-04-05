---
title: Usuwanie pamięci podręcznej platformy Azure dla programu Redis — interfejs wiersza polecenia platformy Azure
description: Ten przykładowy kod interfejsu wiersza polecenia platformy Azure pokazuje, jak usunąć wystąpienie usługi Azure cache for Redis za pomocą polecenia AZ Redis Delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ce6e20e5e4866bc4daa3f331fa2a8612ac6c260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184165"
---
# <a name="delete-an-azure-cache-for-redis"></a>Usuwanie pamięci podręcznej platformy Azure dla usługi Redis

W tym scenariuszu dowiesz się, jak usunąć pamięć podręczną platformy Azure dla Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do usuwania wystąpienia usługi Azure cache for Redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ Redis Delete](/cli/azure/redis) | Usuń usługę Azure cache for Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi Azure cache for Redis znajdują się w [pamięci podręcznej platformy Azure w celu uzyskania dokumentacji Redis](../cli-samples.md).