---
title: Kopiowanie i Przekształcanie danych w Azure Database for MySQL
description: Uzyskaj informacje na temat kopiowania i przekształcania danych w Azure Database for MySQL przy użyciu Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 4d13f6f435a21b467cae1b8e14211a001792787f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012612"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych w Azure Database for MySQL przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do Azure Database for MySQL oraz do przekształcania danych w Azure Database for MySQL. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

Ten łącznik jest wyspecjalizowany dla [usługi Azure Database for MySQL](../mysql/overview.md). Aby skopiować dane z ogólnej bazy danych MySQL znajdującej się lokalnie lub w chmurze, użyj [łącznika MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Azure Database for MySQL jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika Azure Database for MySQL.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla Azure Database for MySQL połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **AzureMySql** | Tak |
| Parametry połączenia | Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem Azure Database for MySQL. <br/> Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `password` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

Typowe parametry połączenia to `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Więcej właściwości, które można ustawić dla danego przypadku:

| Właściwość | Opis | Opcje | Wymagane |
|:--- |:--- |:--- |:--- |
| SSLMode | Ta opcja określa, czy sterownik używa szyfrowania i weryfikacji TLS podczas łączenia z bazą danych MySQL. Na przykład `SSLMode=<0/1/2/3/4>`| WYŁĄCZONE (0)/PREFEROWANe (1) **(wartość domyślna)** /wymagane (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | Nie |
| UseSystemTrustStore | Ta opcja umożliwia określenie, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu, czy z określonego pliku PEM. Na przykład `UseSystemTrustStore=<0/1>;`| Włączone (1)/wyłączone (0) **(wartość domyślna)** | Nie |

**Przykład:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Przechowuj hasło w Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Azure Database for MySQL.

Aby skopiować dane z Azure Database for MySQL, ustaw właściwość Type zestawu danych na **AzureMySqlTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **AzureMySqlTable** | Tak |
| tableName | Nazwa tabeli w bazie danych MySQL. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przykład**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez Azure Database for MySQL źródła i ujścia.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL jako źródło

Aby skopiować dane z Azure Database for MySQL, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **AzureMySqlSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |
| queryCommandTimeout | Czas oczekiwania przed upływem limitu czasu żądania zapytania. Wartość domyślna to 120 minut (02:00:00) | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL jako ujścia

Aby skopiować dane do Azure Database for MySQL, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość: **AzureMySqlSink** | Tak |
| preCopyScript | Określ zapytanie SQL dla działania kopiowania, które ma zostać wykonane przed zapisaniem danych w Azure Database for MySQL w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie |
| writeBatchSize | Wstawia dane do tabeli Azure Database for MySQL, gdy rozmiar buforu osiągnie writeBatchSize.<br>Dozwolona wartość to liczba całkowita reprezentująca liczbę wierszy. | Nie (domyślnie 10 000) |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu.<br>Dozwolone wartości to TimeSpan. Przykładem jest 00:30:00 (30 minut). | Nie (domyślnie 00:00:30) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można odczytywać i zapisywać w tabelach z Azure Database for MySQL. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [przekształcanie ujścia](data-flow-sink.md) w mapowaniu przepływów danych. Można wybrać użycie zestawu danych Azure Database for MySQL lub [wbudowanego zestawu danych](data-flow-source.md#inline-datasets) jako źródła i typu ujścia.

### <a name="source-transformation"></a>Transformacja źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez Azure Database for MySQL źródło. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabela | Jeśli wybierzesz opcję tabela jako dane wejściowe, przepływ danych pobierze wszystkie dane z tabeli określonej w zestawie danych. | Nie | - |*(tylko w przypadku wbudowanego zestawu danych)*<br>tableName |
| Zapytanie | Jeśli wybierzesz pozycję zapytanie jako dane wejściowe, określ zapytanie SQL, aby pobrać dane ze źródła, które zastępuje każdą tabelę określoną w zestawie danych. Korzystanie z zapytań jest świetnym sposobem zredukowania wierszy do testowania i wyszukiwania.<br><br>Klauzula **order by** nie jest obsługiwana, ale można ustawić pełną instrukcję SELECT FROM. Można również użyć funkcji tabeli zdefiniowanej przez użytkownika. **SELECT * FROM udfGetData ()** to format UDF w języku SQL, który zwraca tabelę, której można użyć w przepływie danych.<br>Przykład zapytania: `select * from mytable where customerId > 1000 and customerId < 2000` lub `select * from "MyTable"` .| Nie | Ciąg | query |
| Rozmiar partii | Określ rozmiar partii, aby podzielić duże ilości danych na partie. | Nie | Liczba całkowita | batchSize |
| Poziom izolacji | Wybierz jeden z następujących poziomów izolacji:<br>-Odczytaj zatwierdzone<br>-Odczytaj niezatwierdzone (wartość domyślna)<br>— Odczyt powtarzalny<br>— Możliwy do serializacji<br>-Brak (Ignoruj poziom izolacji) | Nie | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>DAWAJ</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Przykład skryptu źródłowego Azure Database for MySQL

W przypadku użycia Azure Database for MySQL jako typu źródła skojarzony skrypt przepływu danych jest:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Przekształcanie ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez Azure Database for MySQL ujścia. Można edytować te właściwości na karcie **Opcje ujścia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update — Metoda | Określ, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwala na operacje wstawiania.<br>Aby zaktualizować, upsert lub usunąć wiersze, [przekształcenie ALTER Row](data-flow-alter-row.md) jest wymagane, aby można było oznaczyć wiersze dla tych działań. | Tak | `true` lub `false` | usuwaln <br/>wstawialny <br/>aktualizowalne <br/>upsertable |
| Kolumny klucza | W przypadku aktualizacji, upserts i usunięć należy ustawić kolumny kluczy, aby określić, który wiersz ma być zmieniany.<br>Nazwa kolumny, która jest wybierana jako klucz, będzie używana jako część kolejnej aktualizacji, Upsert, Usuń. W związku z tym należy wybrać kolumnę, która istnieje w mapowaniu ujścia. | Nie | Tablica | keys |
| Pomiń zapisywanie kolumn klucza | Jeśli chcesz, aby nie zapisywać wartości w kolumnie klucz, wybierz pozycję "Pomiń zapisywanie kolumn klucza". | Nie | `true` lub `false` | skipKeyWrites |
| Akcja tabeli |Określa, czy należy ponownie utworzyć lub usunąć wszystkie wiersze z tabeli docelowej przed zapisem.<br>- **Brak**: w tabeli nie zostanie wykonana żadna akcja.<br>- **Utwórz ponownie**: tabela zostanie porzucona i utworzona ponownie. Wymagane w przypadku dynamicznego tworzenia nowej tabeli.<br>- **Obcinanie**: wszystkie wiersze z tabeli docelowej zostaną usunięte. | Nie | `true` lub `false` | Utwórz ponownie<br/>obciąć |
| Rozmiar partii | Określ liczbę wierszy, które są zapisywane w każdej partii. Większe rozmiary partii zwiększają optymalizację kompresji i pamięci, ale grozi wyjątkami dotyczącymi pamięci podczas buforowania danych. | Nie | Liczba całkowita | batchSize |
| Pre i post skrypty SQL | Określ wielowierszowe skrypty SQL, które będą wykonywane przed (przed przetwarzaniem) i po wykonaniu (po przetworzeniu) dane są zapisywane w bazie danych ujścia. | Nie | Ciąg | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Przykład Azure Database for MySQL skryptu ujścia

Gdy używasz Azure Database for MySQL jako typu ujścia, skojarzony skrypt przepływu danych to:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapowanie typu danych dla Azure Database for MySQL

Podczas kopiowania danych z Azure Database for MySQL następujące mapowania są używane z typów danych MySQL do Azure Data Factory pośrednich typów danych. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych Azure Database for MySQL | Typ danych pośrednich fabryki danych |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
