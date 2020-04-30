---
title: Zarządzanie indeksowaniem w interfejsie API Azure Cosmos DB dla MongoDB
description: W tym artykule przedstawiono omówienie możliwości indeksowania Azure Cosmos DB przy użyciu interfejsu API MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732703"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Zarządzanie indeksowaniem w interfejsie API Azure Cosmos DB dla MongoDB

Interfejs API Azure Cosmos DB dla MongoDB wykorzystuje podstawowe możliwości zarządzania indeksami Azure Cosmos DB. W tym artykule opisano sposób dodawania indeksów przy użyciu interfejsu API Azure Cosmos DB dla MongoDB. Można także zapoznać się z [omówieniem indeksowania w Azure Cosmos DB](index-overview.md) , które są odpowiednie dla wszystkich interfejsów API.

## <a name="indexing-for-mongodb-server-version-36"></a>Indeksowanie dla serwera MongoDB w wersji 3,6

Interfejs API Azure Cosmos DB dla serwera MongoDB w wersji 3,6 automatycznie indeksuje `_id` pole, którego nie można usunąć. Automatycznie Wymusza unikatowość `_id` pola na klucz fragmentu.

Aby zindeksować dodatkowe pola, należy zastosować polecenia MongoDB index-Management. Podobnie jak w MongoDB, interfejs API Azure Cosmos DB dla MongoDB automatycznie indeksuje tylko `_id` pole. Ta domyślna zasada indeksowania różni się od Azure Cosmos DB interfejsu API SQL, który domyślnie indeksuje wszystkie pola.

Aby zastosować sortowanie do zapytania, należy utworzyć indeks dla pól używanych w operacji sortowania.

## <a name="index-types"></a>Typy indeksów

### <a name="single-field"></a>Pojedyncze pole

Indeksy można utworzyć dla dowolnego pojedynczego pola. Kolejność sortowania w indeksie pojedynczego pola nie ma znaczenia. Następujące polecenie tworzy indeks w polu `name`:

`db.coll.createIndex({name:1})`

Jedno zapytanie używa wielu indeksów pojedynczego pola, jeśli są dostępne. Można utworzyć maksymalnie 500 indeksów jednego pola na kontener.

### <a name="compound-indexes-mongodb-server-version-36"></a>Indeksy złożone (MongoDB Server wersja 3,6)

Interfejs API Azure Cosmos DB dla MongoDB obsługuje indeksy złożone dla kont, które korzystają z protokołu sieci w wersji 3,6. Można uwzględnić maksymalnie osiem pól w indeksie złożonym. W przeciwieństwie do programu w MongoDB, należy utworzyć indeks złożony tylko wtedy, gdy zapytanie musi efektywnie sortować w wielu polach jednocześnie. W przypadku zapytań z wieloma filtrami, które nie muszą sortować, należy utworzyć wiele indeksów jednego pola zamiast pojedynczego indeksu złożonego.

Następujące polecenie tworzy indeks złożony dla pól `name` i: `age`

`db.coll.createIndex({name:1,age:1})`

Indeksów złożonych można używać do bardziej wydajnego sortowania wielu pól, jak pokazano w następującym przykładzie:

`db.coll.find().sort({name:1,age:1})`

Można również użyć powyższego indeksu złożonego do wydajnego sortowania zapytania z odwrotną kolejnością sortowania dla wszystkich pól. Przykład:

`db.coll.find().sort({name:-1,age:-1})`

Jednak sekwencja ścieżek w indeksie złożonym musi dokładnie pasować do zapytania. Oto przykład zapytania, które będzie wymagało dodatkowego indeksu złożonego:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Indeksy MultiKey

Azure Cosmos DB tworzy indeksy MultiKey do indeksowania zawartości przechowywanej w tablicach. Jeśli indeksuje pole z wartością tablicy, Azure Cosmos DB automatycznie indeksuje każdy element w tablicy.

### <a name="geospatial-indexes"></a>Indeksy geograficzne

Wiele operatorów geoprzestrzennych będzie korzystać z indeksów geoprzestrzennych. Obecnie interfejs API usługi Azure Cosmos DB dla MongoDB obsługuje `2dsphere` indeksy. Interfejs API nie obsługuje `2d` jeszcze indeksów.

Oto przykład tworzenia indeksu geoprzestrzennego w `location` polu:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Indeksy tekstu

Interfejs API Azure Cosmos DB dla MongoDB nie obsługuje obecnie indeksów tekstowych. W przypadku zapytań wyszukiwania tekstu w ciągach należy używać integracji z [usługą Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) z Azure Cosmos DB.

## <a name="index-properties"></a>Właściwości indeksu

Następujące operacje są wspólne dla kont obsługujących protokół komunikacyjny w wersji 3,6 i kont obsługujących wcześniejsze wersje. Możesz dowiedzieć się więcej na temat [obsługiwanych indeksów i właściwości indeksowanych](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Indeksy unikatowe

[Indeksy unikatowe](unique-keys.md) są przydatne w przypadku wymuszania, że co najmniej dwa dokumenty nie zawierają tej samej wartości dla indeksowanych pól.

> [!IMPORTANT]
> Unikalne indeksy można utworzyć tylko wtedy, gdy kolekcja jest pusta (nie zawiera dokumentów).

Następujące polecenie tworzy unikatowy indeks w polu `student_id`:

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

W przypadku kolekcji podzielonej na fragmenty należy podać klucz fragmentu (Partition), aby utworzyć unikatowy indeks. Ujmując to innymi słowami, wszystkie indeksy unikatowe w kolekcji udostępnionej są indeksami złożonymi, w których jedno z pól jest kluczem partycji.

Następujące polecenia ```coll``` tworzą kolekcję podzielonej na fragmenty (klucz fragmentu to ```university```) z unikatowym indeksem pól `student_id` i: `university`

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

W poprzednim przykładzie pominięto ```"university":1``` klauzulę zwracającą błąd z następującym komunikatem:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Indeksy czasu wygaśnięcia

Aby włączyć wygasanie dokumentów w określonej kolekcji, należy utworzyć [indeks czasu wygaśnięcia (TTL)](../cosmos-db/time-to-live.md). Indeks czasu wygaśnięcia jest indeksem `_ts` pola z `expireAfterSeconds` wartością.

Przykład:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Poprzednie polecenie usuwa wszystkie dokumenty w ```db.coll``` kolekcji, które nie zostały zmodyfikowane w ciągu ostatnich 10 sekund.

> [!NOTE]
> Pole **_ts** jest specyficzne dla Azure Cosmos DB i nie jest dostępne z poziomu klientów MongoDB. Jest to zastrzeżona Właściwość (system), która zawiera sygnaturę czasową ostatniej modyfikacji dokumentu.

## <a name="track-index-progress"></a>Postęp śledzenia indeksu

Wersja 3,6 interfejsu API Azure Cosmos DB dla MongoDB obsługuje `currentOp()` polecenie śledzenia postępu indeksowania w wystąpieniu bazy danych. To polecenie zwraca dokument zawierający informacje o operacjach w toku w wystąpieniu bazy danych. Za pomocą `currentOp` polecenia można śledzić wszystkie operacje w toku w natywnej MongoDB. W interfejsie API Azure Cosmos DB dla MongoDB, to polecenie obsługuje tylko śledzenie operacji indeksu.

Poniżej przedstawiono kilka przykładów, które pokazują, jak używać `currentOp` polecenia do śledzenia postępu indeksowania:

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

- Operacja indeksowania w kolekcji "foo" i bazie danych "bar", która jest 60 procent, będzie miała następujący dokument wyjściowy. `Inprog[0].progress.total` Pole wskazuje 100 jako procent realizacji docelowej.

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

- Jeśli operacja indeksu została rozpoczęta tylko w kolekcji "foo" i bazie danych "bar", dokument wyjściowy może wyświetlać postęp 0 procent do momentu osiągnięcia wymiernego poziomu.

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

- Po zakończeniu operacji indeksu w toku w dokumencie wyjściowym są wyświetlane puste `inprog` operacje.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Aktualizacje indeksu w tle

Niezależnie od wartości określonej dla właściwości indeks w **tle** , aktualizacje indeksów są zawsze wykonywane w tle. Ponieważ aktualizacje indeksu zużywają jednostki żądań (jednostek ru) o niższym priorytecie niż inne operacje bazy danych, zmiany indeksów nie spowodują przestoju operacji zapisu, aktualizacji lub usunięcia.

Po dodaniu nowego indeksu zapytania będą natychmiast używały indeksu. Oznacza to, że zapytania mogą nie zwracać wszystkich pasujących wyników i nie będą zwracać żadnych błędów. Po zakończeniu transformacji indeksu wyniki zapytania będą spójne. [Postęp indeksowania można śledzić](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Migrowanie kolekcji z indeksami

Obecnie można tworzyć unikatowe indeksy tylko wtedy, gdy kolekcja nie zawiera dokumentów. Popularne Narzędzia migracji MongoDB próbują utworzyć unikatowe indeksy po zaimportowaniu danych. Aby obejść ten problem, można ręcznie utworzyć odpowiednie kolekcje i indeksy unikatowe zamiast zezwalać na wypróbowanie narzędzia migracji. (To zachowanie ```mongorestore``` można osiągnąć przy użyciu `--noIndexRestore` flagi w wierszu polecenia).

## <a name="indexing-for-mongodb-version-32"></a>Indeksowanie dla MongoDB w wersji 3,2

Dostępne funkcje indeksowania i ustawienia domyślne są inne dla kont usługi Azure Cosmos, które są zgodne z wersją 3,2 protokołu telekomunikacyjnych MongoDB. Możesz [sprawdzić wersję swojego konta](mongodb-feature-support-36.md#protocol-support). Możesz przeprowadzić uaktualnienie do wersji 3,6, zgłaszając [żądanie pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Jeśli używasz wersji 3,2, w tej sekcji przedstawiono kluczowe różnice w wersji 3,6.

### <a name="dropping-default-indexes-version-32"></a>Usuwanie indeksów domyślnych (wersja 3,2)

W przeciwieństwie do wersji 3,6 interfejsu API Azure Cosmos DB dla MongoDB, wersja 3,2 domyślnie indeksuje każdą właściwość. Aby usunąć te domyślne indeksy dla kolekcji (```coll```), można użyć następującego polecenia:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Po porzucenie indeksów domyślnych można dodać więcej indeksów, tak jak w wersji 3,6.

### <a name="compound-indexes-version-32"></a>Indeksy złożone (wersja 3,2)

Indeksy złożone przechowują odwołania do wielu pól dokumentu. Jeśli chcesz utworzyć indeks złożony, Uaktualnij go do wersji 3,6 przez zgłoszenie [żądania pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Następne kroki

* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)
