---
title: Interfejs API Azure Cosmos DB dla MongoDB (wersja 3,6) obsługiwane funkcje i składnia
description: Dowiedz się więcej o obsługiwanych funkcjach i składni interfejsu API Azure Cosmos DB (wersja 3,6).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 0b6f85a675dc98928309870ea177629203db39e7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557339"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB (wersja 3.6): obsługiwane funkcje i składnia
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Można komunikować się z interfejsem API Azure Cosmos DB dla MongoDB przy użyciu dowolnego ze [sterowników](https://docs.mongodb.org/ecosystem/drivers)klienta MongoDB typu open source. Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB umożliwia korzystanie z istniejących sterowników klienta dzięki przestrzeganiu [protokołu przewodowego](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Za pomocą interfejsu API Azure Cosmos DB dla MongoDB można korzystać z zalet MongoDB, z których korzystasz, ze wszystkimi możliwościami przedsiębiorstwa, które Cosmos DB oferuje: [globalna dystrybucja](distribute-data-globally.md), [Automatyczna fragmentowania](partitioning-overview.md), dostępność i gwarancje opóźnienia, szyfrowanie w spoczynku, tworzenie kopii zapasowych i wiele innych.

> [!NOTE]
> Wersja 3,6 interfejsu API Cosmos DB dla MongoDB nie ma bieżących planów dla końca cyklu życia (EOL). Minimalna wartość powiadomienia w przyszłości EOL wynosi trzy lata.

## <a name="protocol-support"></a>Obsługa protokołu

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB jest domyślnie zgodny z wersją **3.6** serwera MongoDB dla nowych kont. Poniżej wymieniono obsługiwane operacje wraz z ewentualnymi ograniczeniami lub wyjątkami. Dowolny sterownik klienta działający zgodnie z tymi protokołami umożliwia połączenie z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB. Należy pamiętać, że w przypadku korzystania z interfejsu API Azure Cosmos DB dla kont MongoDB wersja 3,6 konta ma punkt końcowy w formacie, a `*.mongo.cosmos.azure.com` 3,2 wersja konta ma punkt końcowy w formacie `*.documents.azure.com` .

## <a name="query-language-support"></a>Obsługa języka zapytań

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB zapewnia niemal pełną obsługę konstrukcji języka zapytań bazy danych MongoDB. W poniższych sekcjach przedstawiono szczegółową listę operacji serwera, operatorów, etapów, poleceń i opcji aktualnie obsługiwanych przez Azure Cosmos DB.

> [!NOTE]
> W tym artykule wymieniono tylko obsługiwane polecenia serwera i wykluczono funkcje otoki po stronie klienta. Funkcje otoki po stronie klienta, takie jak `deleteMany()` i `updateMany()` wewnętrznie `delete()` używają `update()` poleceń i serwera. Funkcje wykorzystujące obsługiwane polecenia serwera są zgodne z interfejsem API Azure Cosmos DB dla MongoDB.

## <a name="database-commands"></a>Polecenia bazy danych

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB obsługuje następujące polecenia bazy danych:

### <a name="query-and-write-operation-commands"></a>Polecenia operacji zapytań i zapisu

| Polecenie | Obsługiwane |
|---------|---------|
| [Zmień strumienie](mongodb-change-streams.md) | Tak |
| delete | Tak |
| powiadomienie | Nie |
| find | Tak |
| findAndModify | Tak |
| getLastError | Tak |
| getMore | Tak |
| getPrevError | Nie |
| insert | Tak |
| parallelCollectionScan | Nie |
| resetError | Nie |
| update | Tak |

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
| Nazwa nazwy | Nie |


### <a name="diagnostics-commands"></a>Polecenia diagnostyki

| Polecenie | Obsługiwane |
|---------|---------|
| buildInfo | Tak |
| collStats | Tak |
| connPoolStats | Nie |
| connectionStatus | Nie |
| dataSize | Nie |
| dbhash | Nie |
| dbStats | Tak |
| uzasadnieni | Tak |
| funkcje | Nie |
| hostInfo | Tak |
| listDatabases | Tak |
| listCommands | Nie |
| profilera | Nie |
| serverStatus | Nie |
| top (pierwsze) | Nie |
| whatsmyuri | Tak |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Potok agregacji</a>

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
> `$lookup` Program nie obsługuje jeszcze funkcji [nieskorelowanych podkwerend](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#join-conditions-and-uncorrelated-sub-queries) wprowadzonych w serwerze w wersji 3,6. Zostanie wyświetlony błąd z komunikatem zawierającym `let is not supported` próbę użycia `$lookup` operatora z `let` i `pipeline` pól.

### <a name="boolean-expressions"></a>Wyrażenia logiczne

| Polecenie | Obsługiwane |
|---------|---------|
| $and | Tak |
| $not | Tak |
| $or | Tak |

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
| $ $CURRENT | Tak |
| $ $DESCEND | Tak |
| $ $KEEP | Tak |
| $ $PRUNE | Tak |
| $ $REMOVE | Tak |
| $ $ROOT | Tak |

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

### <a name="accumulator-expressions"></a>Wyrażenia akumulowana

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
| 64-bitowa liczba całkowita (Long) | Tak |
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
| Indeks jednego pola | Tak |
| Indeks złożony | Tak |
| Indeks MultiKey | Tak |
| Indeks tekstu | Nie |
| 2dsphere | Tak |
| Indeks 2D | Nie |
| Indeks z wartością skrótu | Tak |

### <a name="index-properties"></a>Właściwości indeksu

| Polecenie | Obsługiwane |
|---------|---------|
| TTL | Tak |
| Unikatowe | Tak |
| Częściowe | Nie |
| Bez uwzględniania wielkości liter | Nie |
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

### <a name="element-operators"></a>Operatory elementu

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
| $text | Nie (nieobsługiwane. Użyj zamiast tego $regex.)| 
| $where | Nie | 

W zapytaniach $regex wyrażenia zakotwiczone w lewo umożliwiają wyszukiwanie indeksu. Jednak użycie modyfikatora „i” (wielkość liter nie ma znaczenia) oraz modyfikatora „m” (wiele wierszy) powoduje skanowanie kolekcji we wszystkich wyrażeniach.

Jeśli istnieje potrzeba dołączenia „$” lub „|”, najlepiej utworzyć dwa lub więcej zapytań regex. Na przykład, uwzględniając następujące oryginalne zapytanie: ```find({x:{$regex: /^abc$/})``` , należy je zmodyfikować w następujący sposób:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```

Pierwsza część użyje indeksu, aby ograniczyć wyszukiwanie do dokumentów, które rozpoczynają się od ^abc, a druga część będzie dokładnie dopasowywana do wpisów. Operator kreski „|” działa jako funkcja „or” — zapytanie ```find({x:{$regex: /^abc |^def/})``` pasuje do dokumentów, w których pole „x” ma wartości zaczynające się od „abc” lub „def”. Aby korzystać z indeksu, zaleca się podzielenie zapytania na dwa różne zapytania połączone operatorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

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


#### <a name="update-modifiers"></a>Aktualizuj Modyfikatory

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

[Unikatowe indeksy](mongodb-indexing.md#unique-indexes) zapewniają, że określone pole nie ma zduplikowanych wartości we wszystkich dokumentach w kolekcji, podobnie jak w przypadku domyślnego klucza "_id". Można utworzyć unikatowe indeksy w Cosmos DB przy użyciu `createIndex` polecenia z `unique` parametrem ograniczenia:

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

[Indeksy złożone](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) zapewniają sposób tworzenia indeksu dla grup pól dla maksymalnie 8 pól. Ten typ indeksu różni się od natywnych indeksów złożonych MongoDB. W Azure Cosmos DB indeksy złożone są używane do sortowania operacji, które są stosowane do wielu pól. Aby utworzyć indeks złożony, należy określić więcej niż jedną właściwość jako parametr:

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

Azure Cosmos DB obsługuje GridFS za poorednictwem dowolnego sterownika MongoDB zgodnego z GridFS.

## <a name="replication"></a>Replikacja

Usługa Cosmos DB obsługuje automatyczną, natywną replikację na najniższych warstwach. Ta logika została rozszerzona, aby osiągnąć małe opóźnienia oraz replikację globalną. Usługa Cosmos DB nie obsługuje poleceń dotyczących replikacji ręcznej.





## <a name="retryable-writes"></a>Ponowne zapisywanie

Azure Cosmos DB nie obsługuje jeszcze ponawiania zapisów. Sterowniki klientów muszą `retryWrites=false` zostać dodane do ich parametrów połączenia.

## <a name="sharding"></a>Dzielenie na fragmenty

Usługa Azure Cosmos DB obsługuje automatyczne dzielenie na fragmenty po stronie serwera. Zarządza on fragmentu tworzeniem, umieszczaniem i zrównoważeniem automatycznie. Azure Cosmos DB nie obsługuje ręcznych poleceń fragmentowania, co oznacza, że nie trzeba Wywoływanie poleceń takich jak addShard, balancerStart, moveChunk itp. Wystarczy określić klucz fragmentu podczas tworzenia kontenerów lub wykonywania zapytań dotyczących danych.

## <a name="sessions"></a>Sesje

Azure Cosmos DB nie obsługuje jeszcze poleceń sesji po stronie serwera.

## <a name="time-to-live-ttl"></a>Czas wygaśnięcia (TTL)

Azure Cosmos DB obsługuje czas wygaśnięcia (TTL) w oparciu o sygnaturę czasową dokumentu. Wartość TTL można włączyć dla kolekcji z [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Zarządzanie użytkownikami i rolami

Usługa Azure Cosmos DB nie obsługuje jeszcze użytkowników i ról. Obsługuje ona jednak funkcję kontroli dostępu opartej na rolach (Azure RBAC) oraz hasła do odczytu i zapisu oraz klucze, które można uzyskać za pośrednictwem okienka parametry połączenia w [Azure Portal](https://portal.azure.com).

## <a name="write-concern"></a>Ustawienie Write Concern

Niektóre aplikacje polegają na ustawieniu [Write Concern](https://docs.mongodb.com/manual/reference/write-concern/), które określa liczbę odpowiedzi wymaganych podczas operacji zapisu. Ze względu na to, jak Azure Cosmos DB obsługuje replikację, wszystkie zapisy są domyślnie automatycznie z większością kworum przy użyciu silnej spójności. Ustawienia Write Concern określone w kodzie klienta są ignorowane. Aby dowiedzieć się więcej, zobacz [Używanie poziomów spójności w celu zmaksymalizowania dostępności i wydajności](consistency-levels.md) artykułu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [funkcje wersji Mongo 3,6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
