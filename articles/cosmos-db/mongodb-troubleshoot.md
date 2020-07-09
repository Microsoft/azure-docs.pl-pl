---
title: Rozwiązywanie typowych błędów w interfejsie API Azure Cosmos DB dla usługi Mongo DB
description: W tym dokumencie omówiono sposoby rozwiązywania typowych problemów napotykanych w interfejsie API Azure Cosmos DB MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75941845"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Rozwiązywanie typowych problemów z interfejsem API Azure Cosmos DB MongoDB

Azure Cosmos DB implementuje protokoły sieciowe wspólnych baz danych NoSQL, w tym MongoDB. Ze względu na implementację protokołu sieci można w sposób przezroczysty korzystać z Azure Cosmos DB przy użyciu istniejących zestawów SDK klienta, sterowników i narzędzi, które współpracują z bazami danych NoSQL. Azure Cosmos DB nie używa żadnego kodu źródłowego baz danych do udostępniania interfejsów API zgodnych z okablowaniem dla dowolnych baz danych NoSQL. Każdy sterownik klienta MongoDB, który zrozumie wersje protokołu sieci, może łączyć się z Azure Cosmos DB.

Chociaż interfejs API Azure Cosmos DB dla MongoDB jest zgodny z wersją 3,2 protokołu MongoDB (operatory zapytań i funkcje dodane w wersji 3,4 są obecnie dostępne jako wersja zapoznawcza), istnieją pewne niestandardowe kody błędów, które odnoszą się do Azure Cosmos DB określonych błędów. W tym artykule opisano różne błędy, kody błędów i kroki umożliwiające rozwiązanie tych błędów.

## <a name="common-errors-and-solutions"></a>Typowe błędy i rozwiązania

| Błąd               | Kod  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Całkowita liczba zużytych jednostek żądania jest większa niż stawka żądania aprowizacji dla kolekcji i została ograniczona. | Rozważ przeskalowanie przepływności przypisanej do kontenera lub zestawu kontenerów z Azure Portal lub Ponów próbę wykonania operacji. |
| ExceededMemoryLimit | 16501 | W ramach usługi wielodostępnej Operacja przekroczyła przydział pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnych kryteriów zapytania lub skontaktuj się z pomocą techniczną z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Przykład: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Ścieżka indeksu odpowiadająca określonemu elementowi order-by jest wykluczona/zapytanie order by nie ma odpowiedniego indeksu złożonego, z którego może być obsługiwane. | 2 | Zapytanie żąda sortowania dla pola, które nie jest indeksowane. | Utwórz zgodny indeks (lub indeks złożony) dla podjętych kwerend sortowania. |
| Problemy dotyczące wersji protokołu Wire bazy danych MongoDB | - | Starsze wersje sterowników MongoDB nie mogą wykryć nazwy konta usługi Azure Cosmos w parametrach połączenia. | Dołącz *nazwa_aplikacji = @**AccountName** @ * na końcu interfejsu API Cosmos DB dla parametrów połączenia MongoDB, gdzie ***AccountName*** jest nazwą konta Cosmos DB. |


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

