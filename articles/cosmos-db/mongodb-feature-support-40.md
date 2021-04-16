---
title: Obsługiwane funkcje i składnia wersji 4.0 serwera w interfejsie API Azure Cosmos DB dla bazy danych MongoDB
description: Dowiedz się Azure Cosmos DB obsługiwanych funkcji i składni interfejsu API usługi MongoDB 4.0 server. Dowiedz się więcej na temat poleceń bazy danych, obsługi języka zapytań, typów danych, poleceń potoku agregacji i obsługiwanych operatorów.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 74e08cce381d173e0c5e1458ae99167a0c8c697f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504514"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-40-server-version-supported-features-and-syntax"></a>Azure Cosmos DB API dla bazy danych MongoDB (wersja serwera 4.0): obsługiwane funkcje i składnia
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Możesz komunikować się z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB przy użyciu dowolnych sterowników klienta bazy danych MongoDB typu open [source.](https://docs.mongodb.org/ecosystem/drivers) Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB umożliwia korzystanie z istniejących sterowników klienta dzięki przestrzeganiu [protokołu przewodowego](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Korzystając z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, można korzystać z zalet bazy danych MongoDB, która była wcześniej używana, wraz ze wszystkimi funkcjami przedsiębiorstwa, które oferuje usługa [Cosmos DB:](distribute-data-globally.md)dystrybucja globalna, [](partitioning-overview.md)automatyczne fragmentowanie, gwarancje dostępności i opóźnień, szyfrowanie danych w spoczynku, kopie zapasowe i wiele innych.

## <a name="protocol-support"></a>Obsługa protokołu

Poniżej wymieniono obsługiwane operacje wraz z ewentualnymi ograniczeniami lub wyjątkami. Dowolny sterownik klienta działający zgodnie z tymi protokołami umożliwia połączenie z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB. W przypadku korzystania z interfejsu API usługi Azure Cosmos DB dla kont bazy danych MongoDB punkty końcowe w ponad 3.6 wersjach mają format punktu końcowego, natomiast konta w wersji 3.2 mają punkt końcowy w `*.mongo.cosmos.azure.com` formacie `*.documents.azure.com` .

> [!NOTE]
> W tym artykule wymieniono tylko obsługiwane polecenia serwera i wykluczane są funkcje otoki po stronie klienta. Funkcje otoki po stronie klienta, takie jak `deleteMany()` i wewnętrzne korzystanie z poleceń serwera i `updateMany()` `delete()` `update()` . Funkcje korzystające z obsługiwanych poleceń serwera są zgodne Azure Cosmos DB API usługi MongoDB.

## <a name="query-language-support"></a>Obsługa języka zapytań

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB zapewnia niemal pełną obsługę konstrukcji języka zapytań bazy danych MongoDB. Poniżej znajduje się szczegółowa lista aktualnie obsługiwanych operacji, operatorów, etapów, poleceń i opcji.

## <a name="database-commands"></a>Polecenia bazy danych

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB obsługuje następujące polecenia bazy danych:

### <a name="query-and-write-operation-commands"></a>Polecenia operacji zapytań i zapisu

| Polecenie | Obsługiwane |
|---------|---------|
| [zmienianie strumieni](mongodb-change-streams.md) | Tak |
| delete | Tak |
| Eval | Nie |
| find | Tak |
| findAndModify | Tak |
| getLastError | Tak |
| getMore | Tak |
| getPrevError | Nie |
| insert | Tak |
| parallelCollectionScan | Nie |
| resetError | Nie |
| update | Tak |

### <a name="transaction-commands"></a>Polecenia transakcji

| Polecenie | Obsługiwane |
|---------|---------|
| abortTransaction | Tak |
| Committransaction | Tak |

### <a name="authentication-commands"></a>Polecenia uwierzytelniania

| Polecenie | Obsługiwane |
|---------|---------|
| authenticate | Tak |
| getnonce | Tak |
| logout | Tak |

### <a name="administration-commands"></a>Polecenia administracyjne

| Polecenie | Obsługiwane |
|---------|---------|
| cloneCollectionAsCapped | Nie |
| collMod | Nie |
| connectionStatus | Nie |
| convertToCapped | Nie |
| copydb | Nie |
| create | Tak |
| createIndexes | Tak |
| currentOp | Tak |
| drop | Tak |
| dropDatabase | Tak |
| dropIndexes | Tak |
| filemd5 | Tak |
| killCursors | Tak |
| killOp | Nie |
| listCollections | Tak |
| listDatabases | Tak |
| listIndexes | Tak |
| reIndex | Tak |
| renameCollection | Nie |

### <a name="diagnostics-commands"></a>Polecenia diagnostyki

| Polecenie | Obsługiwane |
|---------|---------|
| buildInfo | Tak |
| collStats | Tak |
| connPoolStats | Nie |
| connectionStatus | Nie |
| Datasize | Nie |
| dbHash | Nie |
| dbStats | Tak |
| Wyjaśnić | Tak |
| funkcje | Nie |
| hostInfo | Tak |
| listDatabases | Tak |
| listCommands | Nie |
| Profiler | Nie |
| serverStatus | Nie |
| top (pierwsze) | Nie |
| whatsmyuri | Tak |

## <a name="aggregation-pipeline"></a><a name="aggregation-pipeline"></a>Potok agregacji

### <a name="aggregation-commands"></a>Polecenia agregacji

| Polecenie | Obsługiwane |
|---------|---------|
| aggregate | Tak |
| count | Tak |
| distinct | Tak |
| mapReduce | Nie |

### <a name="aggregation-stages"></a>Etapy agregacji

| Polecenie | Obsługiwane |
|---------|---------|
| $addFields | Tak |
| $bucket | Nie |
| $bucketAuto | Nie |
| $changeStream | Tak |
| $collStats | Nie |
| $count | Tak |
| $currentOp | Nie |
| $facet | Tak |
| $geoNear | Tak |
| $graphLookup | Tak |
| $group | Tak |
| $indexStats | Nie |
| $limit | Tak |
| $listLocalSessions | Nie |
| $listSessions | Nie |
| $lookup | Częściowe |
| $match | Tak |
| $out | Tak |
| $project | Tak |
| $redact | Tak |
| $replaceRoot | Tak |
| $replaceWith | Nie |
| $sample | Tak |
| $skip | Tak |
| $sort | Tak |
| $sortByCount | Tak |
| $unwind | Tak |

> [!NOTE]
> `$lookup` nie obsługuje jeszcze funkcji [niepowiązanych podzapytań](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#join-conditions-and-uncorrelated-sub-queries) wprowadzonej w wersji 3.6 serwera. Jeśli spróbujemy użyć operatora z polami i , zostanie wyświetlony komunikat o `let is not supported` `$lookup` `let` `pipeline` błędzie z komunikatem.

### <a name="boolean-expressions"></a>Wyrażenia logiczne

| Polecenie | Obsługiwane |
|---------|---------|
| $and | Tak |
| $not | Tak |
| $or | Tak |

### <a name="conversion-expressions"></a>Wyrażenia konwersji

| Polecenie | Obsługiwane |
|---------|---------|
| $convert | Tak |
| $toBool | Tak |
| $toDate | Tak |
| $toDecimal | Tak |
| $toDouble | Tak |
| $toInt | Tak |
| $toLong | Tak |
| $toObjectId | Tak |
| $toString | Tak |

### <a name="set-expressions"></a>Stałe wyrażenia

| Polecenie | Obsługiwane |
|---------|---------|
| $setEquals | Tak |
| $setIntersection | Tak |
| $setUnion | Tak |
| $setDifference | Tak |
| $setIsSubset | Tak |
| $anyElementTrue | Tak |
| $allElementsTrue | Tak |

### <a name="comparison-expressions"></a>Wyrażenia porównania

| Polecenie | Obsługiwane |
|---------|---------|
| $cmp | Tak |
| $eq | Tak | 
| $gt | Tak | 
| $gte | Tak | 
| $lt | Tak |
| $lte | Tak | 
| $ne | Tak | 
| $in | Tak | 
| $nin | Tak | 

### <a name="arithmetic-expressions"></a>Wyrażenia arytmetyczne

| Polecenie | Obsługiwane |
|---------|---------|
| $abs | Tak |
| $add | Tak |
| $ceil | Tak |
| $divide | Tak |
| $exp | Tak |
| $floor | Tak |
| $ln | Tak |
| $log | Tak |
| $log10 | Tak |
| $mod | Tak |
| $multiply | Tak |
| $pow | Tak |
| $sqrt | Tak |
| $subtract | Tak |
| $trunc | Tak |

### <a name="string-expressions"></a>Wyrażenia ciągu

| Polecenie | Obsługiwane |
|---------|---------|
| $concat | Tak |
| $indexOfBytes | Tak |
| $indexOfCP | Tak |
| $ltrim | Tak |
| $rtrim | Tak |
| $trim | Tak |
| $split | Tak |
| $strLenBytes | Tak |
| $strLenCP | Tak |
| $strcasecmp | Tak |
| $substr | Tak |
| $substrBytes | Tak |
| $substrCP | Tak |
| $toLower | Tak |
| $toUpper | Tak |

### <a name="text-search-operator"></a>Operator wyszukiwania tekstu

| Polecenie | Obsługiwane |
|---------|---------|
| $meta | Nie |

### <a name="array-expressions"></a>Wyrażenia tablicy

| Polecenie | Obsługiwane |
|---------|---------|
| $arrayElemAt | Tak |
| $arrayToObject | Tak |
| $concatArrays | Tak |
| $filter | Tak |
| $indexOfArray | Tak |
| $isArray | Tak |
| $objectToArray | Tak |
| $range | Tak |
| $reverseArray | Tak |
| $reduce | Tak |
| $size | Tak |
| $slice | Tak |
| $zip | Tak |
| $in | Tak |

### <a name="variable-operators"></a>Operatory zmiennych

| Polecenie | Obsługiwane |
|---------|---------|
| $map | Tak |
| $let | Tak |

### <a name="system-variables"></a>Zmienne systemu

| Polecenie | Obsługiwane |
|---------|---------|
| $$CURRENT | Tak |
| $$DESCEND | Tak |
| $$KEEP | Tak |
| $$PRUNE | Tak |
| $$REMOVE | Tak |
| $$ROOT | Tak |

### <a name="literal-operator"></a>Operator literału

| Polecenie | Obsługiwane |
|---------|---------|
| $literal | Tak |

### <a name="date-expressions"></a>Wyrażenia daty

| Polecenie | Obsługiwane |
|---------|---------|
| $dayOfYear | Tak |
| $dayOfMonth | Tak |
| $dayOfWeek | Tak |
| $year | Tak |
| $month | Tak | 
| $week | Tak |
| $hour | Tak |
| $minute | Tak | 
| $second | Tak |
| $millisecond | Tak | 
| $dateToString | Tak |
| $isoDayOfWeek | Tak |
| $isoWeek | Tak |
| $dateFromParts | Nie | 
| $dateToParts | Nie |
| $dateFromString | Nie |
| $isoWeekYear | Tak |

### <a name="conditional-expressions"></a>Wyrażenia warunkowe

| Polecenie | Obsługiwane |
|---------|---------|
| $cond | Tak |
| $ifNull | Tak |
| $switch | Tak |

### <a name="data-type-operator"></a>Operator typu danych

| Polecenie | Obsługiwane |
|---------|---------|
| $type | Tak |

### <a name="accumulator-expressions"></a>Wyrażenia skumulowane

| Polecenie | Obsługiwane |
|---------|---------|
| $sum | Tak |
| $avg | Tak |
| $first | Tak |
| $last | Tak |
| $max | Tak |
| $min | Tak |
| $push | Tak |
| $addToSet | Tak |
| $stdDevPop | Tak |
| $stdDevSamp | Tak |

### <a name="merge-operator"></a>Operator scalania

| Polecenie | Obsługiwane |
|---------|---------|
| $mergeObjects | Tak |

## <a name="data-types"></a>Typy danych

Azure Cosmos DB API usługi MongoDB obsługuje dokumenty zakodowane w formacie BSON bazy danych MongoDB. Wersja 4.0 interfejsu API rozszerza wewnętrzne użycie tego formatu w celu zwiększenia wydajności i obniżenia kosztów. Dokumenty napisane lub zaktualizowane za pośrednictwem punktu końcowego z systemem 4.0 korzystają z tej korzyści.
 
W [scenariuszu](mongodb-version-upgrade.md)uaktualniania dokumenty napisane przed uaktualnieniem do wersji 4.0 nie będą korzystać z ulepszonej wydajności, dopóki nie zostaną zaktualizowane za pośrednictwem operacji zapisu za pośrednictwem punktu końcowego 4.0.

| Polecenie | Obsługiwane |
|---------|---------|
| Double | Tak |
| Ciąg | Tak |
| Obiekt | Tak |
| Tablica | Tak |
| Dane binarne | Tak | 
| ObjectId | Tak |
| Wartość logiczna | Tak |
| Date (Data) | Tak |
| Zero | Tak |
| 32-bitowa liczba całkowita (int) | Tak |
| Znacznik czasu | Tak |
| 64-bitowa liczba całkowita (długa) | Tak |
| MinKey | Tak |
| MaxKey | Tak |
| Decimal128 | Tak | 
| Wyrażenie regularne | Tak |
| JavaScript | Tak |
| JavaScript (z zakresem)| Tak |
| Niezdefiniowane | Tak |

## <a name="indexes-and-index-properties"></a>Indeksy i właściwości indeksu

### <a name="indexes"></a>Indeksy

| Polecenie | Obsługiwane |
|---------|---------|
| Indeks pojedynczego pola | Tak |
| Indeks złożony | Tak |
| Indeks wielokluczowy | Tak |
| Indeks tekstu | Nie |
| 2dsphere | Tak |
| Indeks 2d | Nie |
| Indeks skrótów | Tak |

### <a name="index-properties"></a>Właściwości indeksu

| Polecenie | Obsługiwane |
|---------|---------|
| TTL | Tak |
| Unikatowe | Tak |
| Częściowe | Nie |
| Bez uwzględniania liter | Nie |
| Rozrzedzone | Nie |
| Tło | Tak |

## <a name="operators"></a>Operatory

### <a name="logical-operators"></a>Operatory logiczne

| Polecenie | Obsługiwane |
|---------|---------|
| $or | Tak |
| $and | Tak |
| $not | Tak |
| $nor | Tak | 

### <a name="element-operators"></a>Operatory elementów

| Polecenie | Obsługiwane |
|---------|---------|
| $exists | Tak |
| $type | Tak |

### <a name="evaluation-query-operators"></a>Operatory zapytań oceny

| Polecenie | Obsługiwane |
|---------|---------|
| $expr | Nie |
| $jsonSchema | Nie |
| $mod | Tak |
| $regex | Tak |
| $text | Nie (nie jest obsługiwane. Zamiast $regex użyj $regex).| 
| $where | Nie | 

W zapytaniach $regex wyrażenia zakotwiczone po lewej stronie umożliwiają wyszukiwanie indeksów. Jednak użycie modyfikatora „i” (wielkość liter nie ma znaczenia) oraz modyfikatora „m” (wiele wierszy) powoduje skanowanie kolekcji we wszystkich wyrażeniach.

Jeśli istnieje potrzeba dołączenia „$” lub „|”, najlepiej utworzyć dwa lub więcej zapytań regex. Na przykład, biorąc pod uwagę następujące oryginalne zapytanie: `find({x:{$regex: /^abc$/})` , należy je zmodyfikować w następujący sposób:

`find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})`

Pierwsza część użyje indeksu, aby ograniczyć wyszukiwanie do dokumentów, które rozpoczynają się od ^abc, a druga część będzie dokładnie dopasowywana do wpisów. Operator kreski „|” działa jako funkcja „or” — zapytanie `find({x:{$regex: /^abc |^def/})` pasuje do dokumentów, w których pole „x” ma wartości zaczynające się od „abc” lub „def”. Aby korzystać z indeksu, zaleca się podzielenie zapytania na dwa różne zapytania połączone operatorem $or: `find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })`.

### <a name="array-operators"></a>Operatory tablic

| Polecenie | Obsługiwane | 
|---------|---------|
| $all | Tak | 
| $elemMatch | Tak | 
| $size | Tak | 

### <a name="comment-operator"></a>Operator komentarza

| Polecenie | Obsługiwane | 
|---------|---------|
| $comment | Tak | 

### <a name="projection-operators"></a>Operatory projekcji

| Polecenie | Obsługiwane |
|---------|---------|
| $elemMatch | Tak |
| $meta | Nie |
| $slice | Tak |

### <a name="update-operators"></a>Operatory aktualizacji

#### <a name="field-update-operators"></a>Operatory do aktualizacji pól

| Polecenie | Obsługiwane |
|---------|---------|
| $inc | Tak |
| $mul | Tak |
| $rename | Tak |
| $setOnInsert | Tak |
| $set | Tak |
| $unset | Tak |
| $min | Tak |
| $max | Tak |
| $currentDate | Tak |

#### <a name="array-update-operators"></a>Operatory do aktualizacji tablic

| Polecenie | Obsługiwane |
|---------|---------|
| $ | Tak |
| $[]| Tak |
| $[<identifier>]| Tak |
| $addToSet | Tak |
| $pop | Tak |
| $pullAll | Tak |
| $pull | Tak |
| $push | Tak |
| $pushAll | Tak |

#### <a name="update-modifiers"></a>Modyfikatory aktualizacji

| Polecenie | Obsługiwane |
|---------|---------|
| $each | Tak |
| $slice | Tak |
| $sort | Tak |
| $position | Tak |

#### <a name="bitwise-update-operator"></a>Operator do aktualizacji Bitwise

| Polecenie | Obsługiwane |
|---------|---------|
| $bit | Tak | 
| $bitsAllSet | Nie |
| $bitsAnySet | Nie |
| $bitsAllClear | Nie |
| $bitsAnyClear | Nie |

### <a name="geospatial-operators"></a>Operatory danych geoprzestrzennych

Operator | Obsługiwane | 
--- | --- |
$geoWithin | Tak |
$geoIntersects | Tak | 
$near | Tak |
$nearSphere | Tak |
$geometry | Tak |
$minDistance | Tak |
$maxDistance | Tak |
$center | Nie |
$centerSphere | Nie |
$box | Nie |
$polygon | Nie |

## <a name="sort-operations"></a>Operacje sortowania

W przypadku używania operacji `findOneAndUpdate` obsługiwane są operacje sortowania względem pojedynczego pola, ale nie względem wielu pól.

## <a name="unique-indexes"></a>Indeksy unikatowe

[Indeksy unikatowe zapewniają,](mongodb-indexing.md#unique-indexes) że określone pole nie ma zduplikowanych wartości we wszystkich dokumentach w kolekcji, podobnie jak unikatowość w przypadku domyślnego klucza "_id". Możesz utworzyć unikatowe indeksy w Azure Cosmos DB za pomocą `createIndex` polecenia z `unique` parametrem ograniczenia:

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Indeksy złożone

[Indeksy złożone](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) zapewniają sposób tworzenia indeksu dla grup pól dla maksymalnie ośmiu pól. Ten typ indeksu różni się od natywnych indeksów złożonych bazy danych MongoDB. W Azure Cosmos DB indeksy złożone są używane do operacji sortowania, które są stosowane do wielu pól. Aby utworzyć indeks złożony, należy określić więcej niż jedną właściwość jako parametr:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
    "createdCollectionAutomatically" : false, 
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 2,
    "ok" : 1
}
```

## <a name="gridfs"></a>Kolekcja GridFS

Azure Cosmos DB obsługuje system plików GridFS za pośrednictwem dowolnego sterownika Mongo zgodnego z systemem plików GridFS.

## <a name="replication"></a>Replikacja

Usługa Azure Cosmos DB obsługuje automatyczną, natywną replikację na najniższych warstwach. Ta logika została rozszerzona, aby osiągnąć małe opóźnienia oraz replikację globalną. Usługa Cosmos DB nie obsługuje poleceń dotyczących replikacji ręcznej.

## <a name="retryable-writes"></a>Ponowne próby zapisu

Cosmos DB nie obsługuje jeszcze ponownych prób zapisu. Sterowniki klienta muszą dodać parametr adresu URL "retryWrites=false" do parametrów połączenia. Więcej parametrów adresu URL można dodać, poprzedzając je prefiksem "&". 

## <a name="sharding"></a>Dzielenie na fragmenty

Usługa Azure Cosmos DB obsługuje automatyczne dzielenie na fragmenty po stronie serwera. Automatycznie zarządza tworzeniem, umieszczaniem i równoważeniem fragmentów. Azure Cosmos DB nie obsługuje poleceń ręcznego fragmentowania, co oznacza, że nie trzeba wywoływać poleceń, takich jak addShard, balancerStart, moveChunk itp. Klucz fragmentu należy określić tylko podczas tworzenia kontenerów lub wykonywania zapytań o dane.

## <a name="sessions"></a>Sesje

Azure Cosmos DB nie obsługuje jeszcze poleceń sesji po stronie serwera.

## <a name="time-to-live-ttl"></a>Czas wygaśnięcia (TTL)

Azure Cosmos DB obsługuje czas wygaśnięcia (TTL) na podstawie sygnatury czasowej dokumentu. Czas wygaśnięcia można włączyć dla kolekcji, przechodząc do witryny [Azure Portal](https://portal.azure.com).

## <a name="transactions"></a>Transakcje

Transakcje z wieloma dokumentami są obsługiwane w ramach kolekcji bez fragmentów. Transakcje z wieloma dokumentami nie są obsługiwane w kolekcjach ani kolekcjach podzielonych na fragmenty. Limit czasu transakcji wynosi stałe 5 sekund.

## <a name="user-and-role-management"></a>Zarządzanie użytkownikami i rolami

Usługa Azure Cosmos DB nie obsługuje jeszcze użytkowników i ról. Usługa Cosmos DB obsługuje jednak kontrolę dostępu opartą na rolach (RBAC) platformy Azure oraz hasła/klucze tylko do odczytu i zapisu, które można uzyskać za pośrednictwem Azure Portal (strona [Parametrów](https://portal.azure.com) połączenia).

## <a name="write-concern"></a>Ustawienie Write Concern

Niektóre aplikacje korzystają z write [concern](https://docs.mongodb.com/manual/reference/write-concern/), który określa liczbę odpowiedzi wymaganych podczas operacji zapisu. Ze względu na sposób obsługi replikacji w tle w usłudze Cosmos DB wszystkie operacje zapisu mają domyślnie automatycznie ustawioną opcję Quorum (Kworum). Ustawienia Write Concern określone w kodzie klienta są ignorowane. Aby dowiedzieć się więcej, zobacz [Maksymalizowanie dostępności i wydajności za pomocą poziomów spójności](consistency-levels.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
