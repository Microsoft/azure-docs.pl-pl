---
title: Kopiowanie i przekształcanie danych w usłudze Azure Cosmos DB (SQL API)
description: Dowiedz się, jak kopiować dane do i z usługi Azure Cosmos DB (SQL API) i przekształcać dane w usłudze Azure Cosmos DB (SQL API) przy użyciu usługi Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: f0aa70333454b327a0ca76beef2985062ce56715
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415380"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiowanie i przekształcanie danych w usłudze Azure Cosmos DB (SQL API) przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-documentdb-connector.md)
> * [Bieżąca wersja](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych Azure Cosmos DB (interfejs API SQL) oraz używania przepływu danych do przekształcania danych w bazie danych Azure Cosmos DB (interfejs API SQL). Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).



>[!NOTE]
>Ten łącznik obsługuje tylko interfejs API SQL usługi Cosmos DB. W przypadku interfejsu API mongodb należy zapoznać się [z łącznika dla interfejsu API usługi Azure Cosmos DB dla usługi MongoDB](connector-azure-cosmos-db-mongodb-api.md). Inne typy interfejsu API nie są teraz obsługiwane.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Azure Cosmos DB (SQL API) jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

W przypadku działania kopiowania ten łącznik usługi Azure Cosmos DB (SQL API) obsługuje:

- Kopiowanie danych z i do [interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction)usługi Azure Cosmos DB .
- Zapisuj do usługi Azure Cosmos DB jako **wstawianie** lub **upsert**.
- Importowanie i eksportowanie dokumentów JSON w stanie stanu istnieje lub kopiowanie danych z lub do tabelaryczny zestaw danych. Przykłady obejmują bazę danych SQL i plik CSV. Aby skopiować dokumenty w stanie stan niezowym do lub z plików JSON lub do lub z innej kolekcji usługi Azure Cosmos DB, zobacz [Importowanie i eksportowanie dokumentów JSON](#import-and-export-json-documents).

Usługa Data Factory integruje się z [biblioteką zbiorczą executora usługi Azure Cosmos DB,](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) aby zapewnić najlepszą wydajność podczas pisania w usłudze Azure Cosmos DB.

> [!TIP]
> Klip [wideo migracji danych](https://youtu.be/5-SRNiC_qOU) przeprowadzi Cię przez kroki kopiowania danych z magazynu obiektów Blob platformy Azure do usługi Azure Cosmos DB. W klipie wideo opisano również zagadnienia dotyczące dostrajania wydajności do pozyskiwania danych do usługi Azure Cosmos DB w ogóle.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach podano szczegółowe informacje o właściwościach, których można użyć do zdefiniowania jednostek usługi Data Factory, które są specyficzne dla usługi Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej usługi Azure Cosmos DB (SQL API):

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** musi być ustawiona na **CosmosDb**. | Tak |
| Parametry połączenia |Określ informacje, które są wymagane do nawiązania połączenia z bazą danych usługi Azure Cosmos DB.<br />**Uwaga:** Należy określić informacje o bazie danych w ciągu połączenia, jak pokazano w kolejnych przykładach. <br/> Można również umieścić klucz konta w usłudze Azure Key Vault i wyciągnąć konfigurację `accountKey` z ciągu połączenia. Więcej informacji można znaleźć w poniższych przykładach i [poświadczeniach sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| connectVia | [Środowisko wykonawcze integracji](concepts-integration-runtime.md) do nawiązywania połączenia z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

**Przykład**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: klucz konta magazynu w usłudze Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Zestawy danych i połączone usługi](concepts-datasets-linked-services.md).

Następujące właściwości są obsługiwane dla zestawu danych usługi Azure Cosmos DB (SQL API): 

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** zestawu danych musi być ustawiona na **CosmosDbSqlApiCollection**. |Tak |
| Collectionname |Nazwa kolekcji dokumentów usługi Azure Cosmos DB. |Tak |

Jeśli używasz zestawu danych typu "DocumentDbCollection", jest on nadal obsługiwany jako — zgodność wsteczna dla działania kopiowania i odnośnika, nie jest obsługiwany dla przepływu danych. Zaleca się, aby użyć nowego modelu w przyszłości.

**Przykład**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Ta sekcja zawiera listę właściwości, które usługi Azure Cosmos DB (SQL API) źródło i obsługa ujścia. Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Usługa Azure Cosmos DB (SQL API) jako źródło

Aby skopiować dane z usługi Azure Cosmos DB (SQL API), należy ustawić typ **źródła** w działaniu kopiowania na **DocumentDbCollectionSource**. 

Następujące właściwości są obsługiwane w sekcji **Źródło** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **CosmosDbSqlApiSource**. |Tak |
| query |Określ kwerendę usługi Azure Cosmos DB do odczytu danych.<br/><br/>Przykład:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, ta instrukcja SQL jest wykonywana:`select <columns defined in structure> from mycollection` |
| preferowaneRegiony | Preferowana lista regionów do łączenia się podczas pobierania danych z usługi Cosmos DB. | Nie |
| Pagesize | Liczba dokumentów na stronie wyniku kwerendy. Wartość domyślna to "-1", co oznacza, że używa dynamicznego rozmiaru strony po stronie usługi do 1000. | Nie |

Jeśli używasz źródła typu "DocumentDbCollectionSource", jest on nadal obsługiwany jako — jest zgodny z wstecznym. Zaleca się użycie nowego modelu w przyszłości, które zapewniają bogatsze możliwości kopiowania danych z usługi Cosmos DB.

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Podczas kopiowania danych z usługi Cosmos DB, chyba że chcesz [wyeksportować dokumenty JSON w stanie, w jakim jest,](#import-and-export-json-documents)najlepszym rozwiązaniem jest określenie mapowania w działaniu kopiowania. Fabryka danych honoruje mapowanie określone dla działania — jeśli wiersz nie zawiera wartości dla kolumny, wartość null jest podana dla wartości kolumny. Jeśli nie określisz mapowania, fabryka danych wywnioskuje schemat przy użyciu pierwszego wiersza w danych. Jeśli pierwszy wiersz nie zawiera pełnego schematu, w wyniku operacji działania brakuje niektórych kolumn.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Usługa Azure Cosmos DB (SQL API) jako ujście

Aby skopiować dane do usługi Azure Cosmos DB (SQL API), należy ustawić typ **ujścia** w działaniu kopiowania na **DocumentDbCollectionSink**. 

Następujące właściwości są obsługiwane w sekcji **Źródło** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** ujścia działania kopiowania musi być ustawiona na **CosmosDbSqlApiSink**. |Tak |
| writeZachody |W tym artykule opisano sposób zapisywania danych w usłudze Azure Cosmos DB. Dozwolone wartości: **insert** i **upsert**.<br/><br/>Zachowanie **upsert** jest zastąpienie dokumentu, jeśli dokument o tym samym identyfikatorze już istnieje; w przeciwnym razie wstaw dokument.<br /><br />**Uwaga:** Fabryka danych automatycznie generuje identyfikator dokumentu, jeśli identyfikator nie jest określony ani w oryginalnym dokumencie, ani w mapowaniu kolumn. Oznacza to, że należy upewnić się, że **aby upsert** działał zgodnie z oczekiwaniami, dokument ma identyfikator. |Nie<br />(domyślnie jest **wstawić)** |
| writeBatchSize | Usługa Data Factory używa [biblioteki zbiorczego executora usługi Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) do zapisywania danych w usłudze Azure Cosmos DB. Właściwość **writeBatchSize** steruje rozmiarem dokumentów udostępnianych biblioteki przez podajnika ADF. Możesz spróbować zwiększyć wartość **writeBatchSize,** aby zwiększyć wydajność i zmniejszyć wartość, jeśli rozmiar dokumentu jest duży — zobacz poniższe wskazówki. |Nie<br />(wartość domyślna to **10 000**) |
| disableMetricsCollection | Usługa Data Factory zbiera metryki, takie jak procesory RU usługi Cosmos DB, w celu optymalizacji wydajności kopiowania i zaleceń. Jeśli chodzi o to zachowanie, `true` określ, aby go wyłączyć. | Nie (domyślnie jest) `false` |

>[!TIP]
>Aby zaimportować dokumenty JSON w stanie, w jakim są, zapoznaj się [z sekcją Importuj lub eksportuj dokumenty JSON;](#import-and-export-json-documents) aby skopiować z danych w kształcie tabelarycznym, należy zapoznać się [z poleceniem Migruj z relacyjnej bazy danych do bazy danych Usługi Cosmos.](#migrate-from-relational-database-to-cosmos-db)

>[!TIP]
>Usługa Cosmos DB ogranicza rozmiar pojedynczego żądania do 2 MB. Formuła to Rozmiar żądania = Pojedynczy rozmiar dokumentu * Rozmiar partii zapisu. Jeśli zostanie wyświetlony błąd **mówiący "Rozmiar żądania jest zbyt duży."**, **zmniejsz `writeBatchSize` wartość** w konfiguracji ujścia kopii.

Jeśli używasz źródła typu "DocumentDbCollectionSink", nadal jest obsługiwany jako — jest dla zgodności z powrotem. Zaleca się użycie nowego modelu w przyszłości, które zapewniają bogatsze możliwości kopiowania danych z usługi Cosmos DB.

**Przykład**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Mapowanie schematu

Aby skopiować dane z usługi Azure Cosmos DB do ujścia tabelaryczne lub odwrócone, zapoznaj się [z mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w przepływie danych mapowania, można odczytywać i zapisywać do kolekcji w usłudze Cosmos DB. Aby uzyskać więcej informacji, zobacz [transformację źródła](data-flow-source.md) i [transformację ujścia](data-flow-sink.md) w przepływach danych mapowania.

### <a name="source-transformation"></a>Transformacja źródła

Ustawienia specyficzne dla usługi Azure Cosmos DB są dostępne na karcie **Opcje źródłowe** transformacji źródła. 

**Dołącz kolumny systemowe:** Jeśli ```id```true, ```_ts```, i inne kolumny systemowe zostaną uwzględnione w metadanych przepływu danych z usługi CosmosDB. Podczas aktualizowania kolekcji, ważne jest, aby uwzględnić to, dzięki czemu można pobrać istniejący identyfikator wiersza.

**Rozmiar strony:** Liczba dokumentów na stronie wyniku kwerendy. Wartość domyślna to "-1", która używa dynamicznej strony usługi do 1000.

**Przepustowość:** Ustaw wartość opcjonalną dla liczby procesorów operacyjnych, które chcesz zastosować do kolekcji usługi CosmosDB dla każdego wykonania tego przepływu danych podczas operacji odczytu. Minimum to 400.

**Preferowane regiony:** Wybierz preferowane regiony odczytu dla tego procesu.

#### <a name="json-settings"></a>Ustawienia JSON

**Pojedynczy dokument:** Wybierz tę opcję, jeśli podajnik ADF ma traktować cały plik jako pojedynczy dok.

**Niecytowane nazwy kolumn:** Tę opcję należy zaznaczyć, jeśli nazwy kolumn w json jako niecytowane.

**Ma uwagi:** Użyj tego wyboru, jeśli dokumenty JSON mają komentarze w danych.

**Cytowane pojedynczo:** Należy to zaznaczyć, jeśli kolumny i wartości w dokumencie są cytowane z pojedynczymi cudzysłowami.

**Ukośnik odwrotny uciekł:** Jeśli w json używa się ukośników odwrotnych, wybierz tę opcję.

### <a name="sink-transformation"></a>Transformacja zlewu

Ustawienia specyficzne dla usługi Azure Cosmos DB są dostępne na karcie **Ustawienia** transformacji ujścia.

**Metoda aktualizacji:** Określa, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwalaj tylko na wstawia. Aby zaktualizować, upsert lub usunąć wiersze, transformacja alter-rowu jest wymagana do oznaczania wierszy dla tych akcji. W przypadku aktualizacji, upserts i deletes, kolumna klucza lub kolumny muszą być ustawione, aby określić, który wiersz do zmiany.

**Akcja zbierania:** Określa, czy kolekcja docelowa ma być ponownietworzona przed zapisaniem.
* Brak: Nie będzie wykonywane żadne działania w kolekcji.
* Odtwórz: Kolekcja zostanie porzucona i odtworzona

**Rozmiar partii**: Określa, ile wierszy jest zapisywanych w każdym zasobniku. Większe rozmiary partii poprawić kompresji i optymalizacji pamięci, ale ryzyko z wyjątków pamięci podczas buforowania danych.

**Klucz partycji:** Wprowadź ciąg, który reprezentuje klucz partycji dla kolekcji. Przykład: ```/movies/title```

**Przepustowość:** Ustaw opcjonalną wartość dla liczby programów RU, które chcesz zastosować do kolekcji usługi CosmosDB dla każdego wykonania tego przepływu danych. Minimum to 400.

**Budżet przepływności zapisu:** Liczba całkowita reprezentująca liczbę ru, które chcesz przydzielić do zadania spark pozyskiwania zbiorczego. Ta liczba jest z całkowitej przepływności przydzielonej do kolekcji.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importowanie i eksportowanie dokumentów JSON

Można użyć tego łącznika usługi Azure Cosmos DB (SQL API), aby łatwo:

* Kopiowanie dokumentów między dwiema kolekcjami usługi Azure Cosmos DB w stanie — jest.
* Importowanie dokumentów JSON z różnych źródeł do usługi Azure Cosmos DB, w tym z magazynu obiektów Blob platformy Azure, usługi Azure Data Lake Store i innych magazynów opartych na plikach, które obsługuje usługa Azure Data Factory.
* Eksportuj dokumenty JSON z kolekcji usługi Azure Cosmos DB do różnych magazynów opartych na plikach.

Aby uzyskać kopię niezależną od schematu:

* Korzystając z narzędzia Kopiuj dane, wybierz opcję **Eksportuj jako jest do plików JSON lub Kolekcji usługi Cosmos DB.**
* Korzystając z tworzenia działań, wybierz format JSON z odpowiednim magazynem plików dla źródła lub ujścia.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrowanie z relacyjnej bazy danych do bazy danych usługi Cosmos DB

Podczas migracji z relacyjnej bazy danych np SQL Server do usługi Azure Cosmos DB, aktywność kopiowania można łatwo mapować dane tabelaryczne ze źródła do spłaszczenia dokumentów JSON w usłudze Cosmos DB. W niektórych przypadkach można przeprojektować model danych, aby zoptymalizować go dla przypadków użycia NoSQL zgodnie z [modelowaniem danych w usłudze Azure Cosmos DB](../cosmos-db/modeling-data.md), na przykład, aby zdenormalize danych przez osadzanie wszystkich powiązanych elementów podrzędnych w jednym dokumencie JSON. W takim przypadku zapoznaj się z [tym artykułem](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) z instruktażem na temat sposobu osiągnięcia go przy użyciu działania kopiowania usługi Azure Data Factory.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działanie kopiowania obsługuje jako źródła i pochłaniacze w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
