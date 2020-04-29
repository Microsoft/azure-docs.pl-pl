---
title: MongoDB polecenia rozszerzenia do zarządzania danymi w interfejsie API Azure Cosmos DB dla MongoDB
description: W tym artykule opisano sposób używania poleceń rozszerzenia MongoDB do zarządzania danymi przechowywanymi w interfejsie API Azure Cosmos DB dla MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80583578"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Użyj poleceń rozszerzenia MongoDB, aby zarządzać danymi przechowywanymi w interfejsie API Azure Cosmos DB dla MongoDB 

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Można komunikować się z interfejsem API Azure Cosmos DB dla MongoDB przy użyciu dowolnego ze [sterowników klienta MongoDB](https://docs.mongodb.org/ecosystem/drivers)typu open source. Interfejs API Azure Cosmos DB dla MongoDB umożliwia korzystanie z istniejących sterowników klienta, przestrzegając [protokołu MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Za pomocą interfejsu API Azure Cosmos DB dla MongoDB można korzystać z korzyści Cosmos DB takich jak dystrybucja globalna, automatyczna fragmentowania, wysoka dostępność, gwarancje opóźnienia, automatyczne, szyfrowanie w stanie spoczynku, kopie zapasowe i wiele innych, przy zachowaniu inwestycji w aplikację MongoDB.

## <a name="mongodb-protocol-support"></a>Obsługa protokołu MongoDB

Domyślnie interfejs API Azure Cosmos DB dla MongoDB jest zgodny z serwerem MongoDB w wersji 3,2, aby uzyskać więcej informacji, zobacz [obsługiwane funkcje i składnia](mongodb-feature-support.md). Funkcje lub operatory zapytań dodane w MongoDB w wersji 3,4 są obecnie dostępne jako wersja zapoznawcza w interfejsie API Azure Cosmos DB dla MongoDB. Następujące polecenia rozszerzeń obsługują konkretne funkcje Azure Cosmos DB podczas wykonywania operacji CRUD na danych przechowywanych w interfejsie API Azure Cosmos DB dla MongoDB:

* [Utwórz bazę danych](#create-database)
* [Aktualizowanie bazy danych](#update-database)
* [Pobieranie bazy danych](#get-database)
* [Utwórz kolekcję](#create-collection)
* [Aktualizowanie kolekcji](#update-collection)
* [Pobierz kolekcję](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Utwórz bazę danych

Polecenie CREATE DATABASE Extension tworzy nową bazę danych MongoDB. Nazwa bazy danych jest używana z kontekstu baz danych, względem którego jest wykonywane polecenie. Format polecenia "isdatabase" jest następujący:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

W poniższej tabeli opisano parametry w poleceniu:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| Standard   |  ciąg  |   Nazwa polecenia niestandardowego, musi to być "isdatabase".      |
| offerThroughput | int  | Elastyczna przepływność ustawiona dla bazy danych programu. Ten parametr jest opcjonalny. |

### <a name="output"></a>Dane wyjściowe

Zwraca domyślną odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Tworzenie bazy danych**

Aby utworzyć bazę danych o nazwie "test", użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Tworzenie bazy danych o przepływności**

Aby utworzyć bazę danych o nazwie "test" i zainicjowanej przepływności 1000 jednostek ru, użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Aktualizowanie bazy danych

Polecenie Aktualizuj rozszerzenie bazy danych aktualizuje właściwości skojarzone z określoną bazą danych. Obecnie można aktualizować tylko właściwość "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

W poniższej tabeli opisano parametry w poleceniu:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| Standard    |    ciąg     |   Nazwa polecenia niestandardowego. Musi być "UpdateDatabase".      |
|  offerThroughput   |  int       |     Nowa zainicjowana przepływność, która ma zostać ustawiona w bazie danych.    |

### <a name="output"></a>Dane wyjściowe

Zwraca domyślną odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Aktualizuj zainicjowaną przepływność skojarzoną z bazą danych**

Aby zaktualizować zainicjowaną przepływność bazy danych o nazwie "test" do 1200 jednostek ru, użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Pobieranie bazy danych

Polecenie Get Database Extension zwraca obiekt bazy danych. Nazwa bazy danych jest używana z kontekstu bazy danych, względem którego jest wykonywane polecenie.

```
{
  customAction: "GetDatabase"
}
```

W poniższej tabeli opisano parametry w poleceniu:


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  Standard   |   ciąg      |   Nazwa polecenia niestandardowego. Musi być "getdatabase"|
        
### <a name="output"></a>Dane wyjściowe

Jeśli polecenie zakończy się pomyślnie, odpowiedź zawiera dokument o następujących polach:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stan odpowiedzi. 1 = = sukces. 0 = = niepowodzenie.      |
| `database`    |    `string`        |   Nazwa bazy danych.      |
|   `provisionedThroughput`  |    `int`      |    Tymczasowa przepływność ustawiona dla bazy danych. Jest to opcjonalny parametr odpowiedzi.     |

Jeśli polecenie nie powiedzie się, zostanie zwrócona domyślna odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Pobieranie bazy danych**

Aby uzyskać obiekt bazy danych dla bazy danych o nazwie "test", użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Utwórz kolekcję

Polecenie CREATE Collection Extension tworzy nową kolekcję MongoDB. Nazwa bazy danych jest używana z kontekstu baz danych, względem którego jest wykonywane polecenie. Format polecenia CreateCollection jest następujący:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

W poniższej tabeli opisano parametry w poleceniu:

| **Pole** | **Typ** | **Wymagane** | **Opis** |
|---------|---------|---------|---------|
| Standard | ciąg | Wymagany | Nazwa polecenia niestandardowego. Musi być "CreateCollection".|
|  — kolekcja | ciąg | Wymagany | Nazwa kolekcji. Żadne znaki specjalne nie są dozwolone.|
| offerThroughput | int | Obowiązkowe | Zainicjowana przepływność do ustawienia w bazie danych. Jeśli ten parametr nie zostanie podany, wartość domyślna to 400 RU/s. * Aby określić przepływność poza 10 000 RU/s, `shardKey` parametr jest wymagany.|
| shardKey | ciąg | Obowiązkowe | Ścieżka do klucza fragmentu dla kolekcji podzielonej na fragmenty. Ten parametr jest wymagany, jeśli w programie `offerThroughput`ustawiono więcej niż 10 000 ru/s.  Jeśli jest określony, wszystkie dokumenty, które zostały wstawione, będą wymagały tej wartości. |

### <a name="output"></a>Dane wyjściowe

Zwraca domyślną odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Tworzenie kolekcji unsharded**

Aby utworzyć kolekcję unsharded o nazwie "TestCollection" i elastyczną przepływność 1000 jednostek ru, użyj następującego polecenia: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Tworzenie kolekcji podzielonej na fragmenty**

Aby utworzyć kolekcję podzielonej na fragmenty o nazwie "TestCollection" i elastyczną przepływność 1000 jednostek ru i Właściwość shardkey "a. b", użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Aktualizowanie kolekcji

Polecenie rozszerzenia kolekcji aktualizacji aktualizuje właściwości skojarzone z określoną kolekcją.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

W poniższej tabeli opisano parametry w poleceniu:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  Standard   |   ciąg      |   Nazwa polecenia niestandardowego. Musi być "Updatecollection".      |
|   — kolekcja   |   ciąg      |   Nazwa kolekcji.       |
| offerThroughput   |int|   Zainicjowana przepływność do ustawienia w kolekcji.|

## <a name="output"></a>Dane wyjściowe

Zwraca domyślną odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Aktualizuj zainicjowaną przepływność skojarzoną z kolekcją**

Aby zaktualizować zainicjowaną przepływność kolekcji o nazwie "TestCollection" do 1200 jednostek ru, użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Pobierz kolekcję

Polecenie Pobierz kolekcję niestandardową zwraca obiekt kolekcji.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

W poniższej tabeli opisano parametry w poleceniu:


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| Standard    |   ciąg      |   Nazwa polecenia niestandardowego. Musi być "getcollection".      |
|  — kolekcja    |    ciąg     |    Nazwa kolekcji.     |

### <a name="output"></a>Dane wyjściowe

Jeśli polecenie zakończy się pomyślnie, odpowiedź zawiera dokument z następującymi polami


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stan odpowiedzi. 1 = = sukces. 0 = = niepowodzenie.      |
| `database`    |    `string`     |   Nazwa bazy danych.      |
| `collection`    |    `string`     |    Nazwa kolekcji.     |
|  `shardKeyDefinition`   |   `document`      |  Dokument specyfikacji indeksu używany jako klucz fragmentu. Jest to opcjonalny parametr odpowiedzi.       |
|  `provisionedThroughput`   |   `int`      |    Zainicjowana przepływność do ustawienia w kolekcji. Jest to opcjonalny parametr odpowiedzi.     |

Jeśli polecenie nie powiedzie się, zostanie zwrócona domyślna odpowiedź na polecenie niestandardowe. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Pobierz kolekcję**

Aby uzyskać obiekt kolekcji dla kolekcji o nazwie "TestCollection", użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Domyślne dane wyjściowe polecenia niestandardowego

Jeśli nie zostanie określony, niestandardowa odpowiedź zawiera dokument z następującymi polami:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stan odpowiedzi. 1 = = sukces. 0 = = niepowodzenie.      |
| `code`    |   `int`      |   Zwracane tylko wtedy, gdy polecenie nie powiodło się (tj. ok = = 0). Zawiera kod błędu MongoDB. Jest to opcjonalny parametr odpowiedzi.      |
|  `errMsg`   |  `string`      |    Zwracane tylko wtedy, gdy polecenie nie powiodło się (tj. ok = = 0). Zawiera przyjazny dla użytkownika komunikat o błędzie. Jest to opcjonalny parametr odpowiedzi.      |

## <a name="next-steps"></a>Następne kroki

Następnie możesz uzyskać informacje na temat następujących pojęć Azure Cosmos DB: 

* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)
