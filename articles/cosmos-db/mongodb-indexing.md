---
title: Zarządzanie indeksowaniami w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB
description: W tym artykule przedstawiono omówienie możliwości indeksowania usługi Azure Cosmos DB przy użyciu interfejsu API usługi MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732703"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Zarządzanie indeksowaniami w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB

Interfejs API usługi Azure Cosmos DB dla usługi MongoDB korzysta z podstawowych możliwości zarządzania indeksem usługi Azure Cosmos DB. W tym artykule skupiono się na dodaniu indeksów przy użyciu interfejsu API usługi Azure Cosmos DB dla usługi MongoDB. Można również przeczytać [omówienie indeksowania w usłudze Azure Cosmos DB,](index-overview.md) który jest odpowiedni dla wszystkich interfejsów API.

## <a name="indexing-for-mongodb-server-version-36"></a>Indeksowanie dla serwera MongoDB w wersji 3.6

Interfejs API usługi Azure Cosmos DB dla serwera MongoDB w `_id` wersji 3.6 automatycznie indeksuje pole, którego nie można upuścić. Automatycznie wymusza unikatowość `_id` pola na klucz niezależnego fragmentu.

Aby zaindeksować dodatkowe pola, należy zastosować polecenia zarządzania indeksem Bazy Danych MongoDB. Podobnie jak w mongodb, interfejs API usługi Azure Cosmos DB dla MongoDB automatycznie indeksuje tylko `_id` pole. Ta domyślna zasada indeksowania różni się od interfejsu API SQL usługi Azure Cosmos DB, który domyślnie indeksuje wszystkie pola.

Aby zastosować sortowanie do kwerendy, należy utworzyć indeks pól używanych w operacji sortowania.

## <a name="index-types"></a>Typy indeksów

### <a name="single-field"></a>Pojedyncze pole

Indeksy można tworzyć w dowolnym pojedynczym polu. Kolejność sortowania indeksu pojedynczego pola nie ma znaczenia. Następujące polecenie tworzy indeks w `name`polu:

`db.coll.createIndex({name:1})`

Jedna kwerenda używa wielu indeksów pojedynczych pól, jeśli są dostępne. Można utworzyć maksymalnie 500 pojedynczych indeksów pól na kontener.

### <a name="compound-indexes-mongodb-server-version-36"></a>Indeksy złożone (wersja serwera MongoDB 3.6)

Interfejs API usługi Azure Cosmos DB dla usługi MongoDB obsługuje indeksy złożone dla kont korzystających z protokołu przewodowego w wersji 3.6. W indeksie złożonym można uwzględnić maksymalnie osiem pól. W przeciwieństwie do MongoDB, należy utworzyć indeks złożony tylko wtedy, gdy kwerenda musi skutecznie sortować na wielu polach jednocześnie. W przypadku kwerend z wieloma filtrami, które nie muszą sortować, utwórz wiele indeksów pojedynczych pól zamiast pojedynczego indeksu złożonego.

Następujące polecenie tworzy indeks złożony `name` na `age`polach i:

`db.coll.createIndex({name:1,age:1})`

Indeksy złożone umożliwiają efektywne sortowanie w wielu polach jednocześnie, jak pokazano w poniższym przykładzie:

`db.coll.find().sort({name:1,age:1})`

Można również użyć poprzedniego indeksu złożonego, aby skutecznie sortować kwerendę z odwrotną kolejnością sortowania we wszystkich polach. Oto przykład:

`db.coll.find().sort({name:-1,age:-1})`

Jednak sekwencja ścieżek w indeksie złożonym musi dokładnie odpowiadać kwerendzie. Oto przykład kwerendy, która wymagałaby dodatkowego indeksu złożonego:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Indeksy multikey

Usługa Azure Cosmos DB tworzy indeksy multikey do indeksowania zawartości przechowywanej w tablicach. Jeśli indeksujesz pole z wartością tablicy, usługa Azure Cosmos DB automatycznie indeksuje każdy element w tablicy.

### <a name="geospatial-indexes"></a>Indeksy geoprzestrzenne

Wielu operatorów geoprzestrzennych skorzysta z indeksów geoprzestrzennych. Obecnie interfejs API usługi Azure Cosmos DB `2dsphere` dla mongodb obsługuje indeksy. Interfejs API nie `2d` obsługuje jeszcze indeksów.

Oto przykład tworzenia indeksu geoprzestrzennego w `location` polu:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Indeksy tekstowe

Interfejs API usługi Azure Cosmos DB dla usługi MongoDB nie obsługuje obecnie indeksów tekstowych. W przypadku zapytań tekstowych dotyczących ciągów należy użyć integracji [usługi Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) z usługą Azure Cosmos DB.

## <a name="index-properties"></a>Właściwości indeksu

Następujące operacje są typowe dla kont obsługujących protokół przewodowy w wersji 3.6 i kont obsługujących starsze wersje. Możesz dowiedzieć się więcej o [obsługiwanych indeksach i właściwościach indeksowanych](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Indeksy unikatowe

[Unikatowe indeksy](unique-keys.md) są przydatne do wymuszania, że dwa lub więcej dokumentów nie zawierają tej samej wartości dla pól indeksowanych.

> [!IMPORTANT]
> Unikatowe indeksy można utworzyć tylko wtedy, gdy kolekcja jest pusta (nie zawiera żadnych dokumentów).

Następujące polecenie tworzy unikatowy indeks `student_id`w polu:

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Dla kolekcji podzielonej na fragmenty, należy podać klucz niezależnego fragmentu (partycji), aby utworzyć unikatowy indeks. Ujmując to innymi słowami, wszystkie indeksy unikatowe w kolekcji udostępnionej są indeksami złożonymi, w których jedno z pól jest kluczem partycji.

Następujące polecenia tworzą ```coll``` kolekcję podzieloną na fragmenty ```university```(klucz niezależnego fragmentu) z unikatowym indeksem pól `student_id` i: `university`

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

W poprzednim przykładzie pominięcie ```"university":1``` klauzuli zwraca błąd z następującym komunikatem:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Indeksy czasu wygaśnięcia

Aby włączyć wygasanie dokumentu w określonej kolekcji, należy utworzyć [indeks czasu wygaśnięcia (TTL).](../cosmos-db/time-to-live.md) Indeks TTL jest indeksem `_ts` w `expireAfterSeconds` polu o wartości.

Przykład:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Poprzednie polecenie usuwa wszystkie dokumenty ```db.coll``` w kolekcji, które nie zostały zmodyfikowane w ciągu ostatnich 10 sekund.

> [!NOTE]
> Pole **_ts** jest specyficzne dla usługi Azure Cosmos DB i nie jest dostępne dla klientów MongoDB. Jest to właściwość reserved (system), która zawiera sygnaturę czasową ostatniej modyfikacji dokumentu.

## <a name="track-index-progress"></a>Śledzenie postępu indeksu

Wersja 3.6 interfejsu API usługi Azure Cosmos DB `currentOp()` dla mongodb obsługuje polecenie śledzenia postępu indeksu w wystąpieniu bazy danych. To polecenie zwraca dokument zawierający informacje o operacjach w toku wystąpienia bazy danych. Polecenie służy `currentOp` do śledzenia wszystkich operacji w toku w natywnej mongodb. W interfejsie API usługi Azure Cosmos DB dla mongodb to polecenie obsługuje tylko śledzenie operacji indeksu.

Oto kilka przykładów, które pokazują, jak używać `currentOp` polecenia do śledzenia postępu indeksu:

* Pobierz postęp indeksu dla kolekcji:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Pobierz postęp indeksu dla wszystkich kolekcji w bazie danych:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Pobierz postęp indeksu dla wszystkich baz danych i kolekcji na koncie usługi Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Przykłady danych wyjściowych postępu indeksu

Szczegóły postępu indeksu pokazują procent postępu dla bieżącej operacji indeksu. Oto przykład, który pokazuje format dokumentu wyjściowego dla różnych etapów postępu indeksu:

- Operacja indeksu na kolekcji "foo" i "bar" bazy danych, która jest 60 procent kompletne będzie miał następujący dokument wyjściowy. Pole `Inprog[0].progress.total` zawiera 100 jako docelowy procent ukończenia.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

- Jeśli operacja indeksu właśnie rozpoczęła się na "foo" kolekcji i "bar" bazy danych, dokument wyjściowy może pokazać 0 procent postępu, dopóki nie osiągnie wymierny poziom.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

- Po zakończeniu operacji indeksu w toku dokument `inprog` wyjściowy pokazuje puste operacje.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Aktualizacje indeksu w tle

Niezależnie od wartości określonej dla **właściwości indeksu tła** aktualizacje indeksu są zawsze wykonywane w tle. Ponieważ aktualizacje indeksu zużywają jednostki żądań (RUs) o niższym priorytecie niż inne operacje bazy danych, zmiany indeksu nie spowoduje żadnych przestojów dla zapisów, aktualizacji lub usuwania.

Po dodaniu nowego indeksu kwerendy natychmiast użyją indeksu. Oznacza to, że kwerendy mogą nie zwracać wszystkich pasujących wyników i będą to robić bez zwracania błędów. Po zakończeniu transformacji indeksu wyniki kwerendy będą spójne. Można [śledzić postęp indeksu](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Migrowanie kolekcji z indeksami

Obecnie można tworzyć unikatowe indeksy tylko wtedy, gdy kolekcja nie zawiera żadnych dokumentów. Popularne narzędzia do migracji MongoDB próbują utworzyć unikatowe indeksy po zaimportowaniu danych. Aby obejść ten problem, można ręcznie utworzyć odpowiednie kolekcje i unikatowe indeksy zamiast zezwalać narzędziu migracji na wypróbowanie. (To zachowanie można ```mongorestore``` osiągnąć za `--noIndexRestore` pomocą flagi w wierszu polecenia).

## <a name="indexing-for-mongodb-version-32"></a>Indeksowanie dla MongoDB w wersji 3.2

Dostępne funkcje indeksowania i wartości domyślne są różne dla kont usługi Azure Cosmos, które są zgodne z wersją 3.2 protokołu przewodowego MongoDB. Możesz [sprawdzić wersję swojego konta](mongodb-feature-support-36.md#protocol-support). Możesz uaktualnić do wersji 3.6, składając [wniosek o pomoc techniczną.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

Jeśli używasz wersji 3.2, w tej sekcji opisano kluczowe różnice w wersji 3.6.

### <a name="dropping-default-indexes-version-32"></a>Upuszczanie indeksów domyślnych (wersja 3.2)

W przeciwieństwie do wersji 3.6 interfejsu API usługi Azure Cosmos DB dla mongodb, wersja 3.2 indeksuje każdą właściwość domyślnie. Aby usunąć te domyślne indeksy dla kolekcji(```coll```):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Po upuszczeniu indeksów domyślnych można dodać więcej indeksów, tak jak w wersji 3.6.

### <a name="compound-indexes-version-32"></a>Indeksy złożone (wersja 3.2)

Indeksy złożone przechowują odwołania do wielu pól dokumentu. Jeśli chcesz utworzyć indeks złożony, uaktualnij do wersji 3.6, składając [żądanie pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Następne kroki

* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)
