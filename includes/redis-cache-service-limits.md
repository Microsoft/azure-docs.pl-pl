---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71839218"
---
| Zasób | Limit |
| --- | --- |
| Rozmiar pamięci podręcznej |1,2 TB |
| Bazy danych |64 |
| Maksymalna liczba podłączonych klientów |40 000 |
| Pamięć podręczna systemu Azure dla replik Redis w celu zapewnienia wysokiej dostępności |1 |
| Fragmentów w pamięci podręcznej Premium z klastrowaniem |10 |

Pamięć podręczna systemu Azure dla limitów i rozmiarów Redis różni się dla każdej warstwy cenowej. Aby wyświetlić warstwy cenowe i ich skojarzone rozmiary, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/).

Aby uzyskać więcej informacji na temat limitów konfiguracji usługi Azure cache for Redis, zobacz [domyślną konfigurację serwera Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Ponieważ konfiguracja i zarządzanie pamięcią podręczną platformy Azure dla wystąpień Redis jest wykonywane przez firmę Microsoft, nie wszystkie polecenia Redis są obsługiwane w usłudze Azure cache for Redis. Aby uzyskać więcej informacji, zobacz [Redis polecenia nie są obsługiwane w usłudze Azure cache for Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

