---
title: Pobierz szczegóły pamięci podręcznej platformy Azure dla Redis — interfejs wiersza polecenia platformy Azure
description: Ten przykładowy kod interfejsu wiersza polecenia platformy Azure pokazuje, jak pobrać szczegóły wystąpienia usługi Azure cache for Redis, w tym jego stan aprowizacji.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8901847a1c214b257243c4da0a74897061409bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184250"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Pobierz szczegóły pamięci podręcznej platformy Azure dla usługi Redis

W tym scenariuszu dowiesz się, jak pobrać szczegóły wystąpienia usługi Azure cache for Redis, w tym jego stan aprowizacji.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu pobrania szczegółów wystąpienia usługi Azure cache for Redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ Redis show](/cli/azure/redis) | Pobierz szczegóły wystąpienia usługi Azure cache for Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi Azure cache for Redis znajdują się w [pamięci podręcznej platformy Azure w celu uzyskania dokumentacji Redis](../cli-samples.md).