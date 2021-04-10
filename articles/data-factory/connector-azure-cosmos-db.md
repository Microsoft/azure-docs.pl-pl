---
title: Kopiowanie i Przekształcanie danych w Azure Cosmos DB (interfejs API SQL)
description: Dowiedz się, jak kopiować dane do i z Azure Cosmos DB (SQL API) oraz przekształcać dane w Azure Cosmos DB (SQL API) przy użyciu Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: d42f30ebd72dca81255ddc02a9440db19979536d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608072"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiowanie i przekształcanie danych w usłudze Azure Cosmos DB (interfejs API SQL) za pomocą usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-documentdb-connector.md)
> * [Bieżąca wersja](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych Azure Cosmos DB (interfejs API SQL) oraz używania przepływu danych do przekształcania danych w bazie danych Azure Cosmos DB (interfejs API SQL). Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).



>[!NOTE]
>Ten łącznik obsługuje tylko Cosmos DB interfejsu API SQL. W przypadku interfejsu API MongoDB zapoznaj się z [łącznikiem dla interfejsu api Azure Cosmos DB MongoDB](connector-azure-cosmos-db-mongodb-api.md). Inne typy interfejsów API nie są obecnie obsługiwane.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Azure Cosmos DB (SQL API) jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

W przypadku działania kopiowania ten łącznik Azure Cosmos DB (interfejs SQL API) obsługuje:

- Skopiuj dane z i do Azure Cosmos DB [interfejsu API SQL](../cosmos-db/introduction.md).
- Zapisz do Azure Cosmos DB jako **INSERT** lub **upsert**.
- Importuj i Eksportuj dokumenty JSON jako-is lub Kopiuj dane z lub do tabelarycznego zestawu danych. Przykładami mogą być baza danych SQL i plik CSV. Aby skopiować dokumenty jako-is do lub z plików JSON lub z innej kolekcji Azure Cosmos DB, zobacz [Importowanie i eksportowanie dokumentów JSON](#import-and-export-json-documents).

Data Factory integruje się z [Azure Cosmos DB zbiorczą biblioteką wykonawców](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) , aby zapewnić najlepszą wydajność podczas zapisywania do Azure Cosmos DB.

> [!TIP]
> Film dotyczący [migracji danych](https://youtu.be/5-SRNiC_qOU) przeprowadzi Cię przez kroki kopiowania danych z usługi Azure Blob storage do Azure Cosmos DB. Film wideo opisuje również zagadnienia dotyczące dostrajania wydajności dotyczące pozyskiwania danych do Azure Cosmos DB.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania Data Factory jednostek, które są specyficzne dla Azure Cosmos DB (interfejs API SQL).

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Azure Cosmos DB (interfejsu API SQL) są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **CosmosDb**. | Tak |
| Parametry połączenia |Określ informacje wymagane do nawiązania połączenia z bazą danych Azure Cosmos DB.<br />**Uwaga**: należy określić informacje o bazie danych w parametrach połączenia, jak pokazano w poniższym przykładzie. <br/> Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć `accountKey` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, zostanie użyta domyślna Azure Integration Runtime. |Nie |

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

**Przykład: Zapisz klucz konta w Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md).

Następujące właściwości są obsługiwane dla zestawu danych Azure Cosmos DB (SQL API): 

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** zestawu danych musi być ustawiona na wartość **CosmosDbSqlApiCollection**. |Tak |
| CollectionName |Nazwa Azure Cosmos DB kolekcji dokumentów. |Tak |

Jeśli używasz zestawu danych typu "DocumentDbCollection", nadal jest on obsługiwany w przypadku zgodności z poprzednimi wersjami dla działania kopiowania i wyszukiwania, nie jest to obsługiwane w przypadku przepływu danych. Zamierzasz użyć nowego modelu do przodu.

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

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które są obsługiwane przez Azure Cosmos DB (interfejs API SQL) i obiekty sink. Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (interfejs API SQL) jako źródło

Aby skopiować dane z Azure Cosmos DB (interfejs API SQL), ustaw typ **źródła** w działaniu Kopiuj na **DocumentDbCollectionSource**. 

W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **CosmosDbSqlApiSource**. |Tak |
| query |Określ zapytanie Azure Cosmos DB, aby odczytywać dane.<br/><br/>Przykład:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, ta instrukcja SQL jest wykonywana: `select <columns defined in structure> from mycollection` |
| preferredRegions | Preferowana lista regionów do połączenia podczas pobierania danych z Cosmos DB. | Nie |
| pageSize | Liczba dokumentów na stronie wyniku zapytania. Wartość domyślna to "-1", co oznacza użycie dynamicznego rozmiaru strony po stronie usługi do 1000. | Nie |
| detectDatetime | Określa, czy ma zostać wykryta wartość DateTime z wartości ciągu w dokumentach. Dozwolone wartości to: **true** (wartość domyślna), **Fałsz**. | Nie |

Jeśli używasz źródła typu "DocumentDbCollectionSource", jest ono nadal obsługiwane w przypadku zgodności z poprzednimi wersjami. Zalecane jest użycie nowego modelu do przesyłania dalej, który zapewnia bogatsze możliwości kopiowania danych z Cosmos DB.

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

Przy kopiowaniu danych z Cosmos DB, chyba że chcesz [eksportować dokumenty JSON jako-is](#import-and-export-json-documents), najlepszym rozwiązaniem jest określenie mapowania w działaniu kopiowania. Data Factory przestrzega mapowania określonego w działaniu — Jeśli wiersz nie zawiera wartości dla kolumny, dla wartości kolumny zostanie podana wartość null. Jeśli nie określisz mapowania, Data Factory wnioskuje schemat przy użyciu pierwszego wiersza w danych. Jeśli pierwszy wiersz nie zawiera pełnego schematu, w wyniku operacji działania będzie brakować niektórych kolumn.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (interfejs API SQL) jako ujścia

Aby skopiować dane do Azure Cosmos DB (interfejs API SQL), ustaw typ **ujścia** w działaniu Copy na **DocumentDbCollectionSink**. 

W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** ujścia działania Copy musi być ustawiona na wartość **CosmosDbSqlApiSink**. |Tak |
| writeBehavior |Opisuje sposób zapisywania danych w Azure Cosmos DB. Dozwolone wartości: **INSERT** i **upsert**.<br/><br/>Zachowanie **upsert** polega na zastępowaniu dokumentu, jeśli dokument o takim samym identyfikatorze już istnieje; w przeciwnym razie Wstaw dokument.<br /><br />**Uwaga**: Data Factory automatycznie generuje identyfikator dla dokumentu, jeśli nie określono identyfikatora w oryginalnym dokumencie lub w mapowaniu kolumn. Oznacza to, że aby program **upsert** działał zgodnie z oczekiwaniami, dokument ma identyfikator. |Nie<br />(wartość domyślna to **INSERT**) |
| writeBatchSize | Data Factory używa [Azure Cosmos DB zbiorczej biblioteki](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) do zapisywania danych w Azure Cosmos DB. Właściwość **writeBatchSize** kontroluje rozmiar dokumentów dostarczanych przez ADF do biblioteki. Możesz spróbować zwiększyć wartość **writeBatchSize** , aby zwiększyć wydajność i zmniejszyć wartość w przypadku dużego rozmiaru dokumentu — Zobacz poniżej porady. |Nie<br />(wartość domyślna to **10 000**) |
| disableMetricsCollection | Data Factory zbiera metryki, takie jak Cosmos DB jednostek ru na potrzeby optymalizacji wydajności kopiowania i zaleceń. W przypadku tego zachowania należy określić, `true` aby je wyłączyć. | Nie (domyślnie `false` ) |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie |


>[!TIP]
>Aby zaimportować dokumenty JSON, należy zapoznać się z sekcją [Importowanie lub eksportowanie dokumentów JSON](#import-and-export-json-documents) . Aby skopiować dane w formie tabelarycznej, zapoznaj się z tematem [Migrowanie z relacyjnej bazy danych do Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB ogranicza rozmiar pojedynczego żądania do 2 MB. Formuła ma rozmiar żądania = rozmiar pojedynczego dokumentu * Zapisz rozmiar partii. Jeśli wystąpi błąd mówiący, **"rozmiar żądania jest zbyt duży"**, **Zmniejsz `writeBatchSize` wartość** w konfiguracji ujścia kopiowania.

Jeśli używasz źródła typu "DocumentDbCollectionSink", jest ono nadal obsługiwane w przypadku zgodności z poprzednimi wersjami. Zalecane jest użycie nowego modelu do przesyłania dalej, który zapewnia bogatsze możliwości kopiowania danych z Cosmos DB.

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

Aby skopiować dane z Azure Cosmos DB do tabelarycznego ujścia lub odwrotnie, zapoznaj się z [mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można odczytywać i zapisywać kolekcje w Cosmos DB. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [przekształcanie ujścia](data-flow-sink.md) w mapowaniu przepływów danych.

### <a name="source-transformation"></a>Transformacja źródła

Ustawienia specyficzne dla Azure Cosmos DB są dostępne na karcie **Opcje źródła** transformacji źródłowej. 

**Uwzględnij kolumny systemowe:** Jeśli wartość to true, ```id``` , ```_ts``` i inne kolumny systemowe zostaną uwzględnione w metadanych przepływu danych z CosmosDB. Podczas aktualizowania kolekcji należy uwzględnić to, aby można było uzyskać istniejący identyfikator wiersza.

**Rozmiar strony:** Liczba dokumentów na stronie wyniku zapytania. Wartość domyślna to "-1", która używa strony dynamicznej usługi do 1000.

**Przepływność:** Ustaw opcjonalną wartość liczby jednostek ru, która ma zostać zastosowana do kolekcji CosmosDB dla każdego wykonywania tego przepływu danych podczas operacji odczytu. Wartość minimalna to 400.

**Preferowane regiony:** Wybierz preferowane regiony odczytu dla tego procesu.

#### <a name="json-settings"></a>Ustawienia JSON

**Pojedynczy dokument:** Wybierz tę opcję, jeśli ADF ma traktować cały plik jako pojedynczy dokument JSON.

**Nazwy kolumn bez cudzysłowu:** Wybierz tę opcję, jeśli nazwy kolumn w formacie JSON nie są ujęte w cudzysłów.

**Ma Komentarze:** Użyj tego wyboru, jeśli dokumenty JSON mają komentarze w danych.

**Pojedyncze cudzysłowy:** Ta wartość powinna zostać wybrana, jeśli kolumny i wartości w dokumencie są cytowane przy użyciu pojedynczych cudzysłowów.

**Odwrócony ukośnik odwrotny:** Jeśli używasz ukośników odwrotnych do znaków ucieczki w formacie JSON, wybierz tę opcję.

### <a name="sink-transformation"></a>Przekształcanie ujścia

Ustawienia specyficzne dla Azure Cosmos DB są dostępne na karcie **Ustawienia** transformacji ujścia.

**Metoda aktualizacji:** Określa, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwala na operacje wstawiania. Aby zaktualizować, upsert lub usunąć wiersze, przekształcenie ALTER Row jest wymagane do tagowania wierszy dla tych działań. W przypadku aktualizacji, upserts i usunięć należy ustawić kolumnę klucza lub kolumny, aby określić, który wiersz ma być zmieniany.

**Akcja zbierania:** Określa, czy utworzyć ponownie kolekcję docelową przed zapisem.
* Brak: nie zostanie wykonana żadna akcja do kolekcji.
* Utwórz ponownie: kolekcja zostanie porzucona i utworzona ponownie

**Rozmiar wsadu**: liczba całkowita reprezentująca liczbę obiektów, które są zapisywane do Cosmos DB kolekcji w każdej partii. Zwykle jest to wystarczające od domyślnego rozmiaru partii. Aby dodatkowo dostroić tę wartość, Uwaga:

- Cosmos DB ogranicza rozmiar pojedynczego żądania do 2 MB. Formuła ma wartość "rozmiar żądania = rozmiar pojedynczego dokumentu * rozmiar wsadu". Jeśli wystąpi błąd mówiący, "rozmiar żądania jest zbyt duży", zmniejsz wartość rozmiaru partii.
- Im większy rozmiar wsadu, tym większa przepływność APD można osiągnąć, a jednocześnie należy przydzielić wystarczającą ilość jednostek ru, aby zwiększyć obciążenie.

**Klucz partycji:** Wprowadź ciąg, który reprezentuje klucz partycji dla kolekcji. Przykład: ```/movies/title```

**Przepływność:** Ustaw opcjonalną wartość liczby jednostek ru, która ma zostać zastosowana do kolekcji CosmosDB dla każdego wykonywania tego przepływu danych. Wartość minimalna to 400.

**Budżet przepływności zapisu:** Liczba całkowita reprezentująca jednostek ru, która ma zostać przydzielona dla tej operacji zapisu przepływu danych, poza łączną przepływność przydzieloną do kolekcji.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="import-and-export-json-documents"></a>Importowanie i eksportowanie dokumentów JSON

Za pomocą tego łącznika Azure Cosmos DB (interfejsu SQL API) można łatwo:

* Kopiuj dokumenty między dwoma kolekcjami Azure Cosmos DB.
* Zaimportuj dokumenty JSON z różnych źródeł do Azure Cosmos DB, w tym z usługi Azure Blob Storage, Azure Data Lake Store i innych magazynów opartych na plikach, które Azure Data Factory obsługiwane przez program.
* Eksportuj dokumenty JSON z kolekcji Azure Cosmos DB do różnych magazynów opartych na plikach.

Aby uzyskać kopię schematu niezależny od:

* Korzystając z narzędzia Kopiowanie danych, wybierz opcję **Eksportuj jako plik JSON lub Cosmos DB kolekcji** .
* W przypadku korzystania z funkcji tworzenia działań wybierz format JSON z odpowiednim magazynem plików dla źródła lub ujścia.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrowanie z relacyjnej bazy danych do Cosmos DB

W przypadku migrowania z relacyjnej bazy danych, np. SQL Server do Azure Cosmos DB, działanie kopiowania może łatwo mapować dane tabelaryczne ze źródła na spłaszczone dokumenty JSON w Cosmos DB. W niektórych przypadkach możesz chcieć ponownie zaprojektować model danych, aby zoptymalizować go do NoSQL przypadków użycia zgodnie z [modelem danych w Azure Cosmos DB](../cosmos-db/modeling-data.md), na przykład w celu denormalizacji danych, osadzając wszystkie powiązane elementy podrzędne w ramach jednego dokumentu JSON. W takim przypadku należy zapoznać się z [tym artykułem](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) , korzystając z informacji o tym, jak to osiągnąć za pomocą działania kopiowania Azure Data Factory.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia w Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).