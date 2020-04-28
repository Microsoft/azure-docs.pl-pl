---
title: Pamięć podręczna interfejsu wiersza polecenia platformy Azure dla przykładów Redis
description: 'Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure cache for Redis: tworzenie pamięci podręcznej, usuwanie pamięci podręcznej, uzyskiwanie szczegółowych informacji, nazwy hosta, portów i kluczy, łączenie aplikacji sieci Web.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.openlocfilehash: c43e23b4bf46258cc91b06a0912d03e85a5c7a14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75411355"
---
# <a name="azure-cli-samples-for-azure-cache-for-redis"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure cache for Redis

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| | |
|---|---|
|**Tworzenie pamięci podręcznej**||
| [Tworzenie pamięci podręcznej](./scripts/create-cache.md) | Tworzy grupę zasobów i pamięć podręczną Azure w warstwie Podstawowa dla Redis. |
| [Tworzenie pamięci podręcznej Premium przy użyciu klastrowania](./scripts/create-premium-cache-cluster.md) | Tworzy grupę zasobów i pamięć podręczną warstwy Premium z włączoną obsługą klastrowania.|
| [Pobierz szczegóły pamięci podręcznej](./scripts/show-cache.md) | Pobiera szczegóły wystąpienia usługi Azure cache for Redis, w tym stanu aprowizacji. |
| [Pobieranie nazwy hosta, portów i kluczy](./scripts/cache-keys-ports.md) | Pobiera nazwę hosta, porty i klucze dla wystąpienia usługi Azure cache for Redis. |
|**Aplikacja sieci Web plus pamięć podręczna**||
| [Łączenie aplikacji internetowej z pamięcią podręczną Azure Cache for Redis](./../app-service/scripts/cli-connect-to-redis.md) | Tworzy aplikację internetową platformy Azure i pamięć podręczną platformy Azure dla usługi Redis, a następnie dodaje szczegóły połączenia Redis do ustawień aplikacji. |
|**Usuń pamięć podręczną**||
| [Usuwanie pamięci podręcznej](./scripts/delete-cache.md) | Usuwa wystąpienie usługi Azure cache for Redis  |
| | |

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i [wprowadzenie do interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
