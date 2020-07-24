---
title: Rozwiązywanie typowych błędów w interfejsie API Azure Cosmos DB dla usługi Mongo DB
description: W tym dokumencie omówiono sposoby rozwiązywania typowych problemów napotykanych w interfejsie API Azure Cosmos DB MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: lbosq
ms.openlocfilehash: f75374fc88923a0f131d513bebf0ffe1feeca359
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076765"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Rozwiązywanie typowych problemów z interfejsem API Azure Cosmos DB MongoDB

W tym artykule opisano typowe błędy i rozwiązania dla baz danych przy użyciu interfejsu API Azure Cosmos DB dla MongoDB.

>[!Note]
> Azure Cosmos DB nie hostuje aparatu MongoDB. Zapewnia ona implementację [protokołu MongoDB Wire w wersji 3,6](mongodb-feature-support-36.md) i starszej obsługi [protokołu w wersji 3,2](mongodb-feature-support.md), dlatego niektóre z tych błędów są dostępne tylko w interfejsie API Azure Cosmos DB dla MongoDB. 

## <a name="common-errors-and-solutions"></a>Typowe błędy i rozwiązania

| Błąd               | Kod  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | Żądanie przekroczyło limit czasu wynoszący 60 sekund wykonania. | Może istnieć wiele przyczyn tego błędu. Jedną z przyczyn jest to, że bieżąca przypisana pojemność jednostki żądania nie jest wystarczająca do wykonania żądania. Można to rozwiązać przez zwiększenie liczby jednostek żądania tej kolekcji lub bazy danych. W innych przypadkach ten błąd można obejść, dzieląc duże żądanie na mniejsze. |
| TooManyRequests     | 16500 | Całkowita liczba zużytych jednostek żądania jest większa niż stawka żądania aprowizacji dla kolekcji i została ograniczona. | Rozważ przeskalowanie przepływności przypisanej do kontenera lub zestawu kontenerów z Azure Portal lub Ponów próbę wykonania operacji. |
| ExceededMemoryLimit | 16501 | W ramach usługi wielodostępnej Operacja przekroczyła przydział pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnych kryteriów zapytania lub skontaktuj się z pomocą techniczną z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Przykład: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Ścieżka indeksu odpowiadająca określonemu elementowi order-by jest wykluczona/zapytanie order by nie ma odpowiedniego indeksu złożonego, z którego może być obsługiwane. | 2 | Zapytanie żąda sortowania dla pola, które nie jest indeksowane. | Utwórz zgodny indeks (lub indeks złożony) dla podjętych kwerend sortowania. |
| Problemy dotyczące wersji protokołu Wire bazy danych MongoDB | - | Starsze wersje sterowników MongoDB nie mogą wykryć nazwy konta usługi Azure Cosmos w parametrach połączenia. | Dołącz *nazwa_aplikacji = @**AccountName** @ * na końcu interfejsu API Cosmos DB dla parametrów połączenia MongoDB, gdzie ***AccountName*** jest nazwą konta Cosmos DB. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

