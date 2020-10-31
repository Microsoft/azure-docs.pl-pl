---
title: MongoDB polecenia rozszerzenia do zarządzania danymi w interfejsie API Azure Cosmos DB dla MongoDB
description: W tym artykule opisano sposób używania poleceń rozszerzenia MongoDB do zarządzania danymi przechowywanymi w interfejsie API Azure Cosmos DB dla MongoDB.
author: jasonwhowell
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2020
ms.author: jasonh
ms.custom: devx-track-js
ms.openlocfilehash: 68f7c9331423fa4ef350bd7915ad85e3152c6885
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096550"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Użyj poleceń rozszerzenia MongoDB, aby zarządzać danymi przechowywanymi w interfejsie API Azure Cosmos DB dla MongoDB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Następujący dokument zawiera polecenia akcji niestandardowych, które są specyficzne dla interfejsu API Azure Cosmos DB dla MongoDB. Te polecenia mogą służyć do tworzenia i uzyskiwania zasobów bazy danych, które są specyficzne dla [modelu wydajności Azure Cosmos DB](account-databases-containers-items.md).

Za pomocą interfejsu API Azure Cosmos DB dla MongoDB można korzystać z korzyści Cosmos DB takich jak dystrybucja globalna, automatyczna fragmentowania, wysoka dostępność, gwarancje opóźnienia, automatyczne, szyfrowanie w stanie spoczynku, kopie zapasowe i wiele innych, przy zachowaniu inwestycji w aplikację MongoDB. Można komunikować się z interfejsem API Azure Cosmos DB dla MongoDB przy użyciu dowolnego ze [sterowników klienta MongoDB](https://docs.mongodb.org/ecosystem/drivers)typu open source. Interfejs API Azure Cosmos DB dla MongoDB umożliwia korzystanie z istniejących sterowników klienta, przestrzegając [protokołu MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

## <a name="mongodb-protocol-support"></a>Obsługa protokołu MongoDB

Interfejs API Azure Cosmos DB dla MongoDB jest zgodny z serwerem MongoDB w wersji 3,2 i 3,6. Aby uzyskać więcej informacji [, zobacz Obsługiwane funkcje i składnia](mongodb-feature-support.md) . 

Następujące polecenia rozszerzeń zapewniają możliwość tworzenia i modyfikowania zasobów specyficznych dla Azure Cosmos DB za pośrednictwem żądań bazy danych:

* [Utwórz bazę danych](#create-database)
* [Aktualizowanie bazy danych](#update-database)
* [Pobieranie bazy danych](#get-database)
* [Utwórz kolekcję](#create-collection)
* [Aktualizowanie kolekcji](#update-collection)
* [Pobierz kolekcję](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Utwórz bazę danych

Polecenie CREATE DATABASE Extension tworzy nową bazę danych MongoDB. Nazwa bazy danych może być używana z poziomu kontekstu bazy danych ustawionego za pomocą `use database` polecenia. W poniższej tabeli opisano parametry w poleceniu:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| `customAction`   |  `string`  |   Nazwa polecenia niestandardowego, musi to być "isdatabase".      |
| `offerThroughput` | `int`  | Elastyczna przepływność ustawiona dla bazy danych programu. Ten parametr jest opcjonalny. |
| `autoScaleSettings` | `Object` | Wymagane dla [trybu skalowania automatycznego](provision-throughput-autoscale.md). Ten obiekt zawiera ustawienia skojarzone z trybem pojemności skalowania automatycznego. Można skonfigurować `maxThroughput` wartość, która opisuje największą liczbę jednostek żądania, które zostaną zwiększone do dynamicznie. |

### <a name="output"></a>Dane wyjściowe

Jeśli polecenie zakończy się pomyślnie, zwróci następujące odpowiedzi:

```javascript
{ "ok" : 1 }
```

Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

#### <a name="create-a-database"></a>Tworzenie bazy danych

Aby utworzyć bazę danych o nazwie `"test"` używającej wszystkich wartości domyślnych, użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

To polecenie spowoduje utworzenie bazy danych bez przepływności na poziomie bazy danych. Oznacza to, że kolekcje w tej bazie danych muszą określać ilość przepływności, która ma być używana.

#### <a name="create-a-database-with-throughput"></a>Tworzenie bazy danych o przepływności

Aby utworzyć bazę danych o nazwie `"test"` i określić przepustą przepływność na [poziomie bazy danych](set-throughput.md#set-throughput-on-a-database) 1000 jednostek ru, użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

Spowoduje to utworzenie bazy danych i skonfigurowanie dla niej przepływności. Wszystkie kolekcje w tej bazie danych będą współużytkować ustawioną przepływność, chyba że kolekcje są tworzone z [określonym poziomem przepływności](set-throughput.md#set-throughput-on-a-database-and-a-container).

#### <a name="create-a-database-with-autoscale-throughput"></a>Tworzenie bazy danych o przepływności automatycznego skalowania

Aby utworzyć bazę danych o nazwie `"test"` i określić maksymalną przepływność skalowania do 20 000 ru/s na [poziomie bazy danych](set-throughput.md#set-throughput-on-a-database), użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Aktualizowanie bazy danych

Polecenie Aktualizuj rozszerzenie bazy danych aktualizuje właściwości skojarzone z określoną bazą danych. W poniższej tabeli opisano parametry w poleceniu:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| `customAction`    |    `string`     |   Nazwa polecenia niestandardowego. Musi być "UpdateDatabase".      |
|  `offerThroughput`   |  `int`       |     Nowa obsługiwana przepływność, która ma zostać ustawiona w bazie danych, jeśli baza danych używa [przepływności na poziomie bazy danych](set-throughput.md#set-throughput-on-a-database)  |
| `autoScaleSettings` | `Object` | Wymagane dla [trybu skalowania automatycznego](provision-throughput-autoscale.md). Ten obiekt zawiera ustawienia skojarzone z trybem pojemności skalowania automatycznego. Można skonfigurować `maxThroughput` wartość, która opisuje największą liczbę jednostek żądania, które zostaną dynamicznie zwiększone do rozmiaru bazy danych. |

To polecenie używa bazy danych określonej w kontekście sesji. Jest to baza danych użyta w `use <database>` poleceniu. W tej chwili nie można zmienić nazwy bazy danych za pomocą tego polecenia.

### <a name="output"></a>Dane wyjściowe

Jeśli polecenie zakończy się pomyślnie, zwróci następujące odpowiedzi:

```javascript
{ "ok" : 1 }
```

Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>Aktualizuj zainicjowaną przepływność skojarzoną z bazą danych

Aby zaktualizować zainicjowaną przepływność bazy danych o nazwie `"test"` do 1200 jednostek ru, użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>Aktualizowanie przepływności skalowania automatycznego związanego z bazą danych

Aby zaktualizować zainicjowaną przepływność bazy danych o nazwie `"test"` do 20 000 jednostek ru lub przekształcić ją na [poziom przepływności skalowania automatycznego](provision-throughput-autoscale.md), użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Pobieranie bazy danych

Polecenie Get Database Extension zwraca obiekt bazy danych. Nazwa bazy danych jest używana z kontekstu bazy danych, względem którego jest wykonywane polecenie.

```javascript
{
  customAction: "GetDatabase"
}
```

W poniższej tabeli opisano parametry w poleceniu:


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Nazwa polecenia niestandardowego. Musi być "getdatabase"|
        
### <a name="output"></a>Dane wyjściowe

Jeśli polecenie zakończy się pomyślnie, odpowiedź zawiera dokument o następujących polach:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stan odpowiedzi. 1 = = sukces. 0 = = niepowodzenie.      |
| `database`    |    `string`        |   Nazwa bazy danych.      |
|   `provisionedThroughput`  |    `int`      |    Elastyczna przepływność ustawiona dla bazy danych, jeśli baza danych korzysta z  [ręcznej przepływności na poziomie bazy danych](set-throughput.md#set-throughput-on-a-database)     |
| `autoScaleSettings` | `Object` | Ten obiekt zawiera parametry pojemności skojarzone z bazą danych, jeśli używa [trybu skalowania automatycznego](provision-throughput-autoscale.md). `maxThroughput`Wartość opisuje największą liczbę jednostek żądania, które zostaną zwiększone do dynamicznej bazy danych. |

Jeśli polecenie nie powiedzie się, zostanie zwrócona domyślna odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

#### <a name="get-the-database"></a>Pobieranie bazy danych

Aby uzyskać obiekt bazy danych dla bazy danych o nazwie `"test"` , użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Jeśli baza danych nie ma skojarzonej przepływności, dane wyjściowe byłyby następujące:

```javascript
{ "database" : "test", "ok" : 1 }
```

Jeśli do bazy danych jest skojarzona [Ręczna przepływność na poziomie bazy danych](set-throughput.md#set-throughput-on-a-database) , w danych wyjściowych zostaną wyświetlone `provisionedThroughput` wartości:

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Jeśli baza danych ma skojarzoną [przepływność skalowania automatycznego na poziomie bazy danych](provision-throughput-autoscale.md) , w danych wyjściowych zostanie wyświetlona wartość `provisionedThroughput` , która opisuje minimalną wartość ru/s dla bazy danych, a `autoScaleSettings` także obiekt `maxThroughput` , który opisuje maksymalną liczbę jednostek ru/s dla bazy danych.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Utwórz kolekcję

Polecenie CREATE Collection Extension tworzy nową kolekcję MongoDB. Nazwa bazy danych jest używana z kontekstu baz danych ustawionych przez `use database` polecenie. Format polecenia CreateCollection jest następujący:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

W poniższej tabeli opisano parametry w poleceniu:

| **Pole** | **Typ** | **Wymagane** | **Opis** |
|---------|---------|---------|---------|
| `customAction` | `string` | Wymagane | Nazwa polecenia niestandardowego. Musi być "CreateCollection".|
| `collection` | `string` | Wymagane | Nazwa kolekcji. Nie są dozwolone żadne znaki specjalne ani spacje.|
| `offerThroughput` | `int` | Opcjonalne | Zainicjowana przepływność do ustawienia w bazie danych. Jeśli ten parametr nie zostanie podany, wartość domyślna to 400 RU/s. * Aby określić przepływność poza 10 000 RU/s, `shardKey` parametr jest wymagany.|
| `shardKey` | `string` | Wymagane dla kolekcji o dużej przepływności | Ścieżka do klucza fragmentu dla kolekcji podzielonej na fragmenty. Ten parametr jest wymagany, jeśli w programie ustawiono więcej niż 10 000 RU/s `offerThroughput` .  Jeśli jest określony, wszystkie dokumenty, które zostały wstawione, będą wymagały tego klucza i wartości. |
| `autoScaleSettings` | `Object` | Wymagane dla [trybu skalowania automatycznego](provision-throughput-autoscale.md) | Ten obiekt zawiera ustawienia skojarzone z trybem pojemności skalowania automatycznego. Można skonfigurować `maxThroughput` wartość, która opisuje największą liczbę jednostek żądania, które zostaną zwiększone do dynamicznie. |

### <a name="output"></a>Dane wyjściowe

Zwraca domyślną odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

#### <a name="create-a-collection-with-the-minimum-configuration"></a>Tworzenie kolekcji z konfiguracją minimalną

Aby utworzyć nową kolekcję o nazwie `"testCollection"` i wartości domyślne, użyj następującego polecenia: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Spowoduje to powstanie nowej, unsharded kolekcji z 400RU/s i indeksu w `_id` polu tworzonym automatycznie. Ten typ konfiguracji będzie również stosowany podczas tworzenia nowych kolekcji za pośrednictwem `insert()` funkcji. Przykład: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Tworzenie kolekcji unsharded

Aby utworzyć kolekcję unsharded o nazwie `"testCollection"` i zainicjowanej przepływności 1000 jednostek ru, użyj następującego polecenia: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

Można utworzyć kolekcję z maksymalnie 10 000 RUmi/s jako `offerThroughput` bez konieczności określania klucza fragmentu. W przypadku kolekcji o większej przepływności zapoznaj się z następną sekcją.

#### <a name="create-a-sharded-collection"></a>Tworzenie kolekcji podzielonej na fragmenty

Aby utworzyć kolekcję podzielonej na fragmenty o nazwie `"testCollection"` i zainicjowanej przepływności 11 000 jednostek ru i `shardkey` Właściwości "a. b", użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

To polecenie wymaga teraz `shardKey` parametru, ponieważ więcej niż 10 000 ru/s określono w `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Tworzenie kolekcji skalowania automatycznego unsharded

Aby utworzyć kolekcję unsharded o nazwie `'testCollection'` , która używa [przepustowości skalowania automatycznego](provision-throughput-autoscale.md) o wartości 4 000 ru/s, użyj następującego polecenia:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

Dla `autoScaleSettings.maxThroughput` wartości można określić zakres od 4 000 ru/s do 10 000 ru/s bez klucza fragmentu. W przypadku wyższej przepływności automatycznego skalowania należy określić `shardKey` parametr.

#### <a name="create-a-sharded-autoscale-collection"></a>Tworzenie kolekcji skalowania automatycznego podzielonej na fragmenty

Aby utworzyć kolekcję podzielonej na fragmenty o nazwie `'testCollection'` z kluczem fragmentu o nazwie `'a.b'` i która korzysta z przepustowości [skalowania automatycznego](provision-throughput-autoscale.md) o wartości 20 000 ru/s, użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Aktualizowanie kolekcji

Polecenie rozszerzenia kolekcji aktualizacji aktualizuje właściwości skojarzone z określoną kolekcją.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

W poniższej tabeli opisano parametry w poleceniu:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Nazwa polecenia niestandardowego. Musi być "Updatecollection".      |
|  `collection`   |   `string`      |   Nazwa kolekcji.       |
| `offerThroughput` | `int` |   Zainicjowana przepływność do ustawienia w kolekcji.|
| `autoScaleSettings` | `Object` | Wymagane dla [trybu skalowania automatycznego](provision-throughput-autoscale.md). Ten obiekt zawiera ustawienia skojarzone z trybem pojemności skalowania automatycznego. `maxThroughput`Wartość opisuje największą liczbę jednostek żądania, które zostaną zwiększone do dynamicznie. |

## <a name="output"></a>Dane wyjściowe

Zwraca domyślną odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>Aktualizuj zainicjowaną przepływność skojarzoną z kolekcją

Aby zaktualizować zainicjowaną przepływność kolekcji o nazwie `"testCollection"` do 1200 jednostek ru, użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Pobierz kolekcję

Polecenie Pobierz kolekcję niestandardową zwraca obiekt kolekcji.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

W poniższej tabeli opisano parametry w poleceniu:


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| `customAction`    |   `string`      |   Nazwa polecenia niestandardowego. Musi być "getcollection".      |
| `collection`    |    `string`     |    Nazwa kolekcji.     |

### <a name="output"></a>Dane wyjściowe

Jeśli polecenie zakończy się pomyślnie, odpowiedź zawiera dokument z następującymi polami


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stan odpowiedzi. 1 = = sukces. 0 = = niepowodzenie.      |
| `database`    |    `string`     |   Nazwa bazy danych.      |
| `collection`    |    `string`     |    Nazwa kolekcji.     |
|  `shardKeyDefinition`   |   `document`      |  Dokument specyfikacji indeksu używany jako klucz fragmentu. Jest to opcjonalny parametr odpowiedzi.       |
|  `provisionedThroughput`   |   `int`      |    Zainicjowana przepływność do ustawienia w kolekcji. Jest to opcjonalny parametr odpowiedzi.     |
| `autoScaleSettings` | `Object` | Ten obiekt zawiera parametry pojemności skojarzone z bazą danych, jeśli używa [trybu skalowania automatycznego](provision-throughput-autoscale.md). `maxThroughput`Wartość opisuje największą liczbę jednostek żądania, które zostaną zwiększone do dynamicznie. |

Jeśli polecenie nie powiedzie się, zostanie zwrócona domyślna odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

#### <a name="get-the-collection"></a>Pobierz kolekcję

Aby uzyskać obiekt kolekcji dla kolekcji o nazwie `"testCollection"` , użyj następującego polecenia:

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Jeśli kolekcja ma skojarzoną z nią pojemność przepływności, będzie zawierać `provisionedThroughput` wartość, a dane wyjściowe byłyby następujące:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Jeśli kolekcja ma skojarzoną przepływność automatycznego skalowania, będzie zawierać `autoScaleSettings` obiekt z `maxThroughput` parametrem, który określa maksymalną przepływność, która będzie bardziej dynamicznie zwiększać rozmiar kolekcji. Ponadto zostanie również uwzględniona `provisionedThroughput` wartość określająca minimalną przepływność, z którą zostanie zredukowana ta kolekcja, jeśli nie ma żadnych żądań w kolekcji: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Jeśli kolekcja udostępnia [przepływność na poziomie bazy danych](set-throughput.md#set-throughput-on-a-database), w trybie automatycznego skalowania lub ręcznie, dane wyjściowe byłyby następujące:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Domyślne dane wyjściowe polecenia niestandardowego

Jeśli nie zostanie określony, niestandardowa odpowiedź zawiera dokument z następującymi polami:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stan odpowiedzi. 1 = = sukces. 0 = = niepowodzenie.      |
| `code`    |   `int`      |   Zwracane tylko wtedy, gdy polecenie nie powiodło się (tj. ok = = 0). Zawiera kod błędu MongoDB. Jest to opcjonalny parametr odpowiedzi.      |
|  `errMsg`   |  `string`      |    Zwracane tylko wtedy, gdy polecenie nie powiodło się (tj. ok = = 0). Zawiera przyjazny dla użytkownika komunikat o błędzie. Jest to opcjonalny parametr odpowiedzi.      |

Przykład:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Następne kroki

Następnie możesz uzyskać informacje na temat następujących pojęć Azure Cosmos DB: 

* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)
