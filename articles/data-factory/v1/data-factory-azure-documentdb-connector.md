---
title: Przenieś dane do/z Azure Cosmos DB
description: Dowiedz się, jak przenosić dane do/z kolekcji Azure Cosmos DB przy użyciu Azure Data Factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
robots: noindex
ms.openlocfilehash: a90b26c677c3d53794bce3c2ff48c618415e4cf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379373"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Przenoszenie danych do i z Azure Cosmos DB przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-documentdb-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zapoznaj się z tematem [Azure Cosmos DB Connector w wersji 2](../connector-azure-cosmos-db.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane do/z Azure Cosmos DB (interfejs API SQL). Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Możesz skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do Azure Cosmos DB lub z Azure Cosmos DB do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

> [!IMPORTANT]
> Łącznik Azure Cosmos DB obsługuje tylko interfejs API SQL.

Aby skopiować dane jako-is do/z plików JSON lub innej kolekcji Cosmos DB, zobacz [Import/Export Documents JSON](#importexport-json-documents).

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które przenosi dane do/z Azure Cosmos DB przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Przykłady zawierające definicje JSON dla Data Factory jednostek, które są używane do kopiowania danych do/z Cosmos DB, zobacz sekcję [przykłady JSON](#json-examples) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania Data Factory jednostek specyficznych dla Cosmos DB:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla Azure Cosmos DB połączonej usługi.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **DocumentDb** |Tak |
| Parametry połączenia |Określ informacje, które są konieczne do nawiązania połączenia z bazą danych Azure Cosmos DB. |Tak |

Przykład:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zapoznaj się z artykułem [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych, są podobne do wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja typeProperties zestawu danych typu **DocumentDbCollection** ma następujące właściwości.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| CollectionName |Nazwa kolekcji dokumentów Cosmos DB. |Tak |

Przykład:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Schemat według Data Factory
W przypadku magazynów danych bez schematu, takich jak Azure Cosmos DB, Usługa Data Factorya wnioskuje schemat w jeden z następujących sposobów:

1. Jeśli określisz strukturę danych przy użyciu właściwości **Structure** w definicji zestawu danych, Usługa Data Factory uznaje tę strukturę za schemat. W takim przypadku, jeśli wiersz nie zawiera wartości dla kolumny, zostanie dla niego podana wartość null.
2. Jeśli struktura danych nie zostanie określona przy użyciu właściwości **Structure** w definicji zestawu danych, Usługa Data Factory wnioskuje schemat przy użyciu pierwszego wiersza w danych. W takim przypadku, jeśli pierwszy wiersz nie zawiera pełnego schematu, w wyniku operacji kopiowania brakuje niektórych kolumn.

W związku z tym najlepszym rozwiązaniem dla źródeł danych bez schematu jest określenie struktury danych przy użyciu właściwości **Structure** .

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełna lista sekcji & właściwości dostępnych do definiowania działań można znaleźć w artykule [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jedno wejście i generuje tylko jedno wyjście.

Właściwości dostępne w sekcji typeProperties działania z drugiej strony różnią się w zależności od typu działania i w przypadku działania kopiowania, które różnią się zależnie od typów źródeł i ujścia.

W przypadku działania kopiowania, gdy źródło jest typu **DocumentDbCollectionSource** , w sekcji **typeProperties** są dostępne następujące właściwości:

| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| query |Określ zapytanie, aby odczytać dane. |Ciąg zapytania obsługiwany przez Azure Cosmos DB. <br/><br/>Przykład: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znak specjalny wskazujący, że dokument jest zagnieżdżony |Dowolny znak. <br/><br/>Azure Cosmos DB to Magazyn NoSQL dla dokumentów JSON, w którym zagnieżdżone struktury są dozwolone. Azure Data Factory umożliwia użytkownikowi odróżnienie hierarchii za pośrednictwem nestingSeparator, czyli "." w powyższych przykładach. Po separatorze działanie kopiowania będzie generować obiekt "name" z trzema elementami podrzędnymi od pierwszej, Środkowej i ostatnich, zgodnie z parametrami "name. First", "name. Middle" i "name. Last" w definicji tabeli. |Nie |

**DocumentDbCollectionSink** obsługuje następujące właściwości:

| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| nestingSeparator |Znak specjalny w nazwie kolumny źródłowej, aby wskazać, że jest wymagany dokument zagnieżdżony. <br/><br/>Na przykład powyżej: `Name.First` w tabeli danych wyjściowych powstaje następująca struktura JSON w Cosmos DB dokumencie:<br/><br/>"Name": {<br/>    "First": "Jan"<br/>}, |Znak używany do rozdzielania poziomów zagnieżdżenia.<br/><br/>Wartość domyślna to `.` (kropka). |Znak używany do rozdzielania poziomów zagnieżdżenia. <br/><br/>Wartość domyślna to `.` (kropka). |
| writeBatchSize |Liczba równoległych żądań do Azure Cosmos DB usługi do tworzenia dokumentów.<br/><br/>Można dostosować wydajność podczas kopiowania danych do/z Cosmos DB za pomocą tej właściwości. Po zwiększeniu writeBatchSize można oczekiwać większej wydajności, ponieważ wysyłane są bardziej równoległe żądania do Cosmos DB. Należy jednak unikać ograniczania, które może zgłosić komunikat o błędzie: "częstotliwość żądań jest duża".<br/><br/>Ograniczanie przepustowości zależy od wielu czynników, w tym rozmiaru dokumentów, liczby warunków w dokumentach, zasad indeksowania kolekcji docelowej itd. W przypadku operacji kopiowania można użyć lepszej kolekcji (np. S3), aby uzyskać dostęp do najbardziej dostępnej przepływności (2 500 jednostek żądań/sekundę). |Liczba całkowita |Nie (domyślnie: 5) |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji przed upływem limitu czasu. |zakres czasu<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |

## <a name="importexport-json-documents"></a>Importuj/Eksportuj dokumenty JSON
Za pomocą tego łącznika Cosmos DB można łatwo

* Importowanie dokumentów JSON z różnych źródeł do Cosmos DB, w tym obiektów blob platformy Azure, Azure Data Lake, lokalnego systemu plików lub innych magazynów opartych na plikach obsługiwanych przez Azure Data Factory.
* Eksportuj dokumenty JSON z kolekcji Cosmos DB do różnych magazynów opartych na plikach.
* Migruj dane między dwiema kolekcjami Cosmos DB.

Aby uzyskać taką niezależny Odą kopię schematu,
* W przypadku korzystania z Kreatora kopiowania zaznacz opcję **"Eksportuj jako pliki JSON lub kolekcje Cosmos DB"** .
* W przypadku korzystania z edycji JSON nie należy określać sekcji "Structure" w Cosmos DB zestawach danych ani właściwości "nestingSeparator" na Cosmos DB źródłowym/ujścia w działaniu kopiowania. Aby zaimportować z plików JSON lub wyeksportować do nich, w zestawie danych magazynu plików Określ typ formatu jako "formatu jsonformat", config "filePattern" i Pomiń ustawienia formatu REST, zobacz sekcję [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format) , aby uzyskać szczegółowe informacje.

## <a name="json-examples"></a>Przykłady JSON
W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one sposób kopiowania danych do i z Azure Cosmos DB i Blob Storage platformy Azure. Można jednak skopiować dane **bezpośrednio** z dowolnego źródła do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Przykład: kopiowanie danych z Azure Cosmos DB do obiektu blob platformy Azure
Poniższy przykład pokazuje:

1. Połączona usługa typu [DocumentDb](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [DocumentDbCollection](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [DocumentDbCollectionSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane w Azure Cosmos DB do obiektu blob platformy Azure. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Azure Cosmos DB połączona usługa:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Połączona usługa Azure Blob Storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Wejściowy zestaw danych dokumentów platformy Azure:**

W przykładzie założono, że masz kolekcję o nazwie **Person** w bazie danych Azure Cosmos DB.

Ustawienie "External": "true" i Określanie informacji o zasadach externalData usługa Azure Data Factory, która znajduje się w tej tabeli poza fabryką danych i nie jest generowana przez działanie w fabryce danych.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure:**

Dane są kopiowane do nowego obiektu BLOB co godzinę ze ścieżką dla obiektu BLOB odzwierciedlającą konkretny element DateTime z dokładnością do godziny.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Przykładowy dokument JSON w kolekcji osób w Cosmos DBej bazie danych:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB obsługuje zapytania o dokumenty przy użyciu składni podobnej do języka SQL na dokumentach hierarchicznych JSON.

Przykład:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Poniższy potok kopiuje dane z kolekcji Person w bazie danych Azure Cosmos DB do obiektu blob platformy Azure. W ramach działania kopiowania określono zestawy danych wejściowych i wyjściowych.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Przykład: Kopiuj dane z obiektu blob platformy Azure do Azure Cosmos DB
Poniższy przykład pokazuje:

1. Połączona usługa typu DocumentDb.
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu DocumentDbCollection.
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i DocumentDbCollectionSink.

Przykład kopiuje dane z obiektu blob platformy Azure do Azure Cosmos DB. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure Blob Storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Cosmos DB połączona usługa:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Zestaw danych wejściowych obiektów blob platformy Azure:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB wyjściowy zestaw danych:**

Przykład kopiuje dane do kolekcji o nazwie "Person".

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Poniższy potok kopiuje dane z obiektu blob platformy Azure do kolekcji Person w Cosmos DB. W ramach działania kopiowania określono zestawy danych wejściowych i wyjściowych.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Jeśli przykładowe dane wejściowe obiektu BLOB są takie same jak

```
1,John,,Doe
```
Następnie wyjściowy kod JSON w Cosmos DB będzie:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB to Magazyn NoSQL dla dokumentów JSON, w którym zagnieżdżone struktury są dozwolone. Azure Data Factory umożliwia użytkownikowi odróżnienie hierarchii za pośrednictwem **nestingSeparator**, czyli "." w tym przykładzie. Po separatorze działanie kopiowania będzie generować obiekt "name" z trzema elementami podrzędnymi od pierwszej, Środkowej i ostatnich, zgodnie z parametrami "name. First", "name. Middle" i "name. Last" w definicji tabeli.

## <a name="appendix"></a>Dodatek
1. **Pytanie:** Czy działanie kopiowania obsługuje aktualizację istniejących rekordów?

    **Odpowiedź:** Nie.
2. **Pytanie:** Jak ponowienie kopii w celu Azure Cosmos DB transakcji z już skopiowanymi rekordami?

    **Odpowiedź:** Jeśli rekordy mają pole "ID", a operacja kopiowania próbuje wstawić rekord o takim samym IDENTYFIKATORze, operacja kopiowania zgłosi błąd.
3. **Pytanie:** Czy Data Factory obsługiwać [zakres lub partycjonowanie danych oparte na skrótach](../../cosmos-db/partitioning-overview.md)?

    **Odpowiedź:** Nie.
4. **Pytanie:** Czy można określić więcej niż jedną kolekcję Azure Cosmos DB dla tabeli?

    **Odpowiedź:** Nie. W tej chwili można określić tylko jedną kolekcję.

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.