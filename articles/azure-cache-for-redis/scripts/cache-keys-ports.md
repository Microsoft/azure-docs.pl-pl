---
title: Pobieranie nazwy hosta, portów, kluczy — Azure cache for Redis — interfejs wiersza polecenia platformy Azure
description: Ten przykładowy kod interfejsu wiersza polecenia platformy Azure pokazuje, jak pobrać nazwę hosta, porty i klucze dla wystąpienia usługi Azure cache for Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: d3e8e359a97c091e025049ac8a978e1beca1d759
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184227"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Pobieranie nazwy hosta, portów i kluczy dla usługi Azure cache for Redis

W tym scenariuszu dowiesz się, jak pobrać nazwę hosta, porty i klucze używane do nawiązania połączenia z usługą Azure cache for Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu pobrania nazwy hosta, kluczy i portów pamięci podręcznej platformy Azure dla wystąpienia Redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ Redis show](/cli/azure/redis) | Pobierz szczegóły wystąpienia usługi Azure cache for Redis. |
| [AZ Redis list-Keys](/cli/azure/redis) | Pobierz klucze dostępu dla wystąpienia usługi Azure cache for Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi Azure cache for Redis znajdują się w [pamięci podręcznej platformy Azure w celu uzyskania dokumentacji Redis](../cli-samples.md).