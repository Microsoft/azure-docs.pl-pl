---
title: Zarządzanie indeksowaniem w interfejsie API Azure Cosmos DB dla MongoDB
description: W tym artykule omówiono możliwości indeksowania Azure Cosmos DB przy użyciu interfejsu API Azure Cosmos DB dla MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 10/21/2020
author: timsander1
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 6e084a890dd5c772fbf576ddc50fd26b2d1774f0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487385"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Zarządzanie indeksowaniem w interfejsie API Azure Cosmos DB dla MongoDB

Interfejs API Azure Cosmos DB dla MongoDB wykorzystuje podstawowe możliwości zarządzania indeksami Azure Cosmos DB. W tym artykule opisano sposób dodawania indeksów przy użyciu interfejsu API Azure Cosmos DB dla MongoDB. Można także zapoznać się z [omówieniem indeksowania w Azure Cosmos DB](index-overview.md) , które są odpowiednie dla wszystkich interfejsów API.

## <a name="indexing-for-mongodb-server-version-36"></a>Indeksowanie dla serwera MongoDB w wersji 3,6

Interfejs API Azure Cosmos DB dla serwera MongoDB w wersji 3,6 automatycznie indeksuje `_id` pole, którego nie można usunąć. Automatycznie Wymusza unikatowość `_id` pola na klucz fragmentu. W interfejsie API Azure Cosmos DB MongoDB, fragmentowania i indeksowanie są oddzielnymi koncepcjami. Nie musisz indeksować klucza fragmentu. Jednak, podobnie jak w przypadku każdej innej właściwości w dokumencie, jeśli ta właściwość jest typowym filtrem w zapytaniach, zalecamy indeksowanie klucza fragmentu.

Aby zindeksować dodatkowe pola, zastosuj polecenia MongoDB służące do zarządzania indeksowaniem. Podobnie jak w MongoDB, interfejs API Azure Cosmos DB dla MongoDB automatycznie indeksuje `_id` tylko pole. Te domyślne zasady indeksowania różnią się od interfejsu API Azure Cosmos DB SQL, który domyślnie indeksuje wszystkie pola.

Aby zastosować sortowanie do zapytania, należy utworzyć indeks dla pól używanych w operacji sortowania.

## <a name="index-types"></a>Typy indeksów

### <a name="single-field"></a>Pojedyncze pole

Indeksy można utworzyć dla dowolnego pojedynczego pola. Kolejność sortowania w indeksie pojedynczego pola nie ma znaczenia. Następujące polecenie tworzy indeks w polu `name` :

`db.coll.createIndex({name:1})`

Jedno zapytanie używa wielu indeksów pojedynczego pola, jeśli są dostępne. Można utworzyć maksymalnie 500 indeksów jednego pola na kontener.

### <a name="compound-indexes-mongodb-server-version-36"></a>Indeksy złożone (MongoDB Server wersja 3,6)

Interfejs API Azure Cosmos DB dla MongoDB obsługuje indeksy złożone dla kont, które korzystają z protokołu sieci w wersji 3,6. Można uwzględnić maksymalnie osiem pól w indeksie złożonym. W przeciwieństwie do programu w MongoDB, należy utworzyć indeks złożony tylko wtedy, gdy zapytanie musi efektywnie sortować w wielu polach jednocześnie. W przypadku zapytań z wieloma filtrami, które nie muszą sortować, należy utworzyć wiele indeksów jednego pola zamiast pojedynczego indeksu złożonego. 

> [!NOTE]
> Nie można tworzyć indeksów złożonych dla zagnieżdżonych właściwości lub tablic.

Następujące polecenie tworzy indeks złożony dla pól `name` i `age` :

`db.coll.createIndex({name:1,age:1})`

Indeksów złożonych można używać do bardziej wydajnego sortowania wielu pól, jak pokazano w następującym przykładzie:

`db.coll.find().sort({name:1,age:1})`

Można również użyć powyższego indeksu złożonego do wydajnego sortowania zapytania z odwrotną kolejnością sortowania dla wszystkich pól. Oto przykład:

`db.coll.find().sort({name:-1,age:-1})`

Jednak sekwencja ścieżek w indeksie złożonym musi dokładnie pasować do zapytania. Oto przykład zapytania, które będzie wymagało dodatkowego indeksu złożonego:

`db.coll.find().sort({age:1,name:1})`

> [!NOTE]
> Indeksy złożone są używane tylko w zapytaniach, które sortują wyniki. W przypadku zapytań, które mają wiele filtrów, które nie muszą sortować, Utwórz Multipe indeksy pojedynczego pola.

### <a name="multikey-indexes"></a>Indeksy MultiKey

Azure Cosmos DB tworzy indeksy MultiKey do indeksowania zawartości przechowywanej w tablicach. Jeśli indeksuje pole z wartością tablicy, Azure Cosmos DB automatycznie indeksuje każdy element w tablicy.

### <a name="geospatial-indexes"></a>Indeksy geograficzne

Wiele operatorów geoprzestrzennych będzie korzystać z indeksów geoprzestrzennych. Obecnie interfejs API usługi Azure Cosmos DB dla MongoDB obsługuje `2dsphere` indeksy. Interfejs API nie obsługuje jeszcze `2d` indeksów.

Oto przykład tworzenia indeksu geoprzestrzennego w `location` polu:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Indeksy tekstu

Interfejs API Azure Cosmos DB dla MongoDB nie obsługuje obecnie indeksów tekstowych. W przypadku zapytań wyszukiwania tekstu w ciągach należy używać integracji z [usługą Azure wyszukiwanie poznawcze](../search/search-howto-index-cosmosdb.md) z Azure Cosmos DB. 

## <a name="wildcard-indexes"></a>Indeksy wieloznaczne

Za pomocą indeksów wieloznacznych można obsługiwać zapytania względem nieznanych pól. Załóżmy, że masz kolekcję, która przechowuje dane dotyczące rodzin.

Oto część przykładowego dokumentu w tej kolekcji:

```json
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "grade": "5"
     }
  ]
```

Oto inny przykład, tym razem z nieco innym zestawem właściwości w `children` :

```json
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
      },
      {
        "familyName": "Merriam",
        "givenName": "John",
      }
  ]
```

W tej kolekcji dokumenty mogą mieć wiele różnych możliwych właściwości. Jeśli chcesz zindeksować wszystkie dane w `children` tablicy, masz dwie opcje: Utwórz oddzielne indeksy dla każdej właściwości pojedynczej lub Utwórz jeden indeks wieloznaczny dla całej `children` tablicy.

### <a name="create-a-wildcard-index"></a>Tworzenie indeksu wieloznacznego

Następujące polecenie tworzy indeks symboli wieloznacznych na wszystkich właściwościach w `children` :

`db.coll.createIndex({"children.$**" : 1})`

W **przeciwieństwie do MongoDB, indeksy symboli wieloznacznych mogą obsługiwać wiele pól w predykatach zapytań**. W przypadku użycia jednego indeksu wieloznacznego zamiast tworzenia oddzielnego indeksu dla każdej właściwości nie będzie różnica w wydajności zapytań.

Można utworzyć następujące typy indeksów przy użyciu składni symboli wieloznacznych:

- Pojedyncze pole
- Dane geoprzestrzenne

### <a name="indexing-all-properties"></a>Indeksowanie wszystkich właściwości

Oto jak można utworzyć indeks wieloznaczny we wszystkich polach:

`db.coll.createIndex( { "$**" : 1 } )`

> [!NOTE]
> Jeśli dopiero zaczynasz opracowywanie, **zdecydowanie** zalecamy wyłączenie z wieloznacznym indeksem dla wszystkich pól. Upraszcza to programowanie i ułatwia optymalizację zapytań.

Dokumenty z wieloma polami mogą zawierać opłaty za operacje zapisu i aktualizacji o wysokim poziomie żądania (RU). W związku z tym, jeśli używasz dużego obciążenia, należy wybrać pojedyncze ścieżki indeksu zamiast używać symboli wieloznacznych.

### <a name="limitations"></a>Ograniczenia

Indeksy wieloznaczne nie obsługują żadnego z następujących typów indeksu lub właściwości:

- Złożonego
- TTL
- Unikatowe

W **przeciwieństwie do MongoDB w**interfejsie API Azure Cosmos DB dla MongoDB **nie** można używać symboli wieloznacznych dla:

- Tworzenie indeksu wieloznacznego, który zawiera wiele konkretnych pól

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection " :
        {
           "children.givenName" : 1,
           "children.grade" : 1
        }
    }
)`

- Tworzenie indeksu wieloznacznego, który wyklucza wiele konkretnych pól

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection" :
        {
           "children.givenName" : 0,
           "children.grade" : 0
        }
    }
)`

Alternatywnie można utworzyć wiele indeksów wieloznacznych.

## <a name="index-properties"></a>Właściwości indeksu

Następujące operacje są wspólne dla kont obsługujących protokół komunikacyjny w wersji 3,6 i kont obsługujących wcześniejsze wersje. Możesz dowiedzieć się więcej na temat [obsługiwanych indeksów i właściwości indeksowanych](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Indeksy unikatowe

[Indeksy unikatowe](unique-keys.md) są przydatne w przypadku wymuszania, że co najmniej dwa dokumenty nie zawierają tej samej wartości dla indeksowanych pól.

> [!IMPORTANT]
> Unikalne indeksy można utworzyć tylko wtedy, gdy kolekcja jest pusta (nie zawiera dokumentów).

Następujące polecenie tworzy unikatowy indeks w polu `student_id` :

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

Następujące polecenia tworzą kolekcję podzielonej na fragmenty ```coll``` (klucz fragmentu to ```university``` ) z unikatowym indeksem pól `student_id` i `university` :

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

- Operacja indeksowania w kolekcji "foo" i bazie danych "bar", która jest 60 procent, będzie miała następujący dokument wyjściowy. `Inprog[0].progress.total`Pole wskazuje 100 jako procent realizacji docelowej.

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

## <a name="background-index-updates"></a>Aktualizacje indeksu w tle

Niezależnie od wartości określonej dla właściwości indeks w **tle** , aktualizacje indeksów są zawsze wykonywane w tle. Ponieważ aktualizacje indeksu zużywają jednostki żądań (jednostek ru) o niższym priorytecie niż inne operacje bazy danych, zmiany indeksów nie spowodują przestoju operacji zapisu, aktualizacji lub usunięcia.

Podczas dodawania nowego indeksu nie ma wpływu na dostępność. Zapytania będą korzystać tylko z nowych indeksów po zakończeniu przekształcania indeksu. Podczas przekształcania indeksu aparat zapytań będzie kontynuował korzystanie z istniejących indeksów, więc zobaczysz podobną wydajność odczytu podczas transformacji indeksowania do zaobserwowanego przed zainicjowaniem zmiany indeksowania. Przy dodawaniu nowych indeksów nie jest również ryzykowne żadne niekompletne lub niespójne wyniki zapytania.

W przypadku usuwania indeksów i natychmiastowego wykonywania zapytań filtry mają filtrów dla usuniętych indeksów, wyniki mogą być niespójne i niekompletne do momentu zakończenia transformacji indeksu. W przypadku usunięcia indeksów aparat zapytań nie zapewnia spójnych ani pełnych wyników, gdy zapytania filtrują te nowo usuniętych indeksów. Większość deweloperów nie porzuca indeksów, a następnie natychmiast próbuje wykonać zapytania do nich, w przeciwnym razie ta sytuacja jest mało prawdopodobne.

> [!NOTE]
> [Postęp indeksowania można śledzić](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Migrowanie kolekcji z indeksami

Obecnie można tworzyć unikatowe indeksy tylko wtedy, gdy kolekcja nie zawiera dokumentów. Popularne Narzędzia migracji MongoDB próbują utworzyć unikatowe indeksy po zaimportowaniu danych. Aby obejść ten problem, można ręcznie utworzyć odpowiednie kolekcje i indeksy unikatowe zamiast zezwalać na wypróbowanie narzędzia migracji. (To zachowanie można osiągnąć przy ```mongorestore``` użyciu `--noIndexRestore` flagi w wierszu polecenia).

## <a name="indexing-for-mongodb-version-32"></a>Indeksowanie dla MongoDB w wersji 3,2

Dostępne funkcje indeksowania i ustawienia domyślne są inne dla kont usługi Azure Cosmos, które są zgodne z wersją 3,2 protokołu telekomunikacyjnych MongoDB. Możesz [sprawdzić wersję konta](mongodb-feature-support-36.md#protocol-support) i [uaktualnić ją do wersji 3,6](mongodb-version-upgrade.md).

Jeśli używasz wersji 3,2, w tej sekcji przedstawiono kluczowe różnice w wersji 3,6.

### <a name="dropping-default-indexes-version-32"></a>Usuwanie indeksów domyślnych (wersja 3,2)

W przeciwieństwie do wersji 3,6 interfejsu API Azure Cosmos DB dla MongoDB, wersja 3,2 domyślnie indeksuje każdą właściwość. Aby usunąć te domyślne indeksy dla kolekcji (), można użyć następującego polecenia ```coll``` :

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Po porzucenie indeksów domyślnych można dodać więcej indeksów, tak jak w wersji 3,6.

### <a name="compound-indexes-version-32"></a>Indeksy złożone (wersja 3,2)

Indeksy złożone przechowują odwołania do wielu pól dokumentu. Jeśli chcesz utworzyć indeks złożony, [Uaktualnij go do wersji 3,6](mongodb-version-upgrade.md).

### <a name="wildcard-indexes-version-32"></a>Indeksy wieloznaczne (wersja 3,2)

Jeśli chcesz utworzyć indeks symboli wieloznacznych, [Uaktualnij do wersji 3,6](mongodb-version-upgrade.md).

## <a name="next-steps"></a>Następne kroki

* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)
* Aby dowiedzieć się więcej o relacji między partycjonowaniem i indeksem, zobacz How to [Query The Azure Cosmos Container](how-to-query-container.md) .