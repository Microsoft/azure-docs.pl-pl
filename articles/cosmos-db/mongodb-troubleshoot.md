---
title: Rozwiązywanie typowych błędów w interfejsie API Azure Cosmos DB dla usługi Mongo DB
description: W tym dokumencie omówiono sposoby rozwiązywania typowych problemów napotykanych w interfejsie API Azure Cosmos DB MongoDB.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: fa33e2ccc5c6cca94ab4e2294a4865745145c1ce
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096329"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Rozwiązywanie typowych problemów z interfejsem API Azure Cosmos DB MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W tym artykule opisano typowe błędy i rozwiązania dla baz danych przy użyciu interfejsu API Azure Cosmos DB dla MongoDB.

>[!Note]
> Azure Cosmos DB nie hostuje aparatu MongoDB. Zapewnia ona implementację [protokołu MongoDB Wire w wersji 3,6](mongodb-feature-support-36.md) i starszej obsługi [protokołu w wersji 3,2](mongodb-feature-support.md), dlatego niektóre z tych błędów są dostępne tylko w interfejsie API Azure Cosmos DB dla MongoDB. 

## <a name="common-errors-and-solutions"></a>Typowe błędy i rozwiązania

| Błąd               | Kod  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | Żądanie przekroczyło limit czasu wykonywania wynoszący 60 sekund. | Może istnieć wiele przyczyn tego błędu. Jedną z przyczyn może być fakt, że obecnie przypisana wydajność jednostek żądania może być niedostateczna, aby ukończyć żądanie. Ten problem można rozwiązać przez zwiększenie liczby jednostek żądania dla tej kolekcji lub bazy danych. W innych przypadkach ten błąd można obejść, dzieląc duże żądanie na mniejsze. |
| TooManyRequests     | 16500 | Łączna liczba użytych jednostek żądania jest większa niż aprowizowana prędkość jednostek żądania dla tej kolekcji i zostało zastosowane ograniczanie. | Rozważ skalowanie przepływności przypisanej do kontenera lub zestawu kontenerów w witrynie Azure Portal albo spróbuj wykonać operację ponownie. |
| ExceededMemoryLimit | 16501 | W ramach usługi wielodostępnej Operacja przekroczyła przydział pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnych kryteriów zapytania lub skontaktuj się z pomocą techniczną z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Przykład: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Ścieżka indeksu odpowiadająca określonemu elementowi order-by jest wykluczona / Zapytanie order-by nie ma odpowiedniego indeksu złożonego do jego obsłużenia. | 2 | Zapytanie żąda sortowania w nieindeksowanym polu. | Utwórz zgodny indeks (lub indeks złożony) dla podjętych kwerend sortowania. |
| Problemy dotyczące wersji protokołu Wire bazy danych MongoDB | - | Starsze wersje sterowników MongoDB nie mogą wykryć nazwy konta usługi Azure Cosmos w parametrach połączenia. | Dołącz *nazwa_aplikacji = @ **AccountName** @* na końcu interfejsu API Cosmos DB dla parametrów połączenia MongoDB, gdzie ***AccountName*** jest nazwą konta Cosmos DB. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

