---
title: Kopiowanie i Przekształcanie danych w Azure Database for PostgreSQL
description: Dowiedz się, jak kopiować i przekształcać dane w Azure Database for PostgreSQL przy użyciu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/01/2021
ms.openlocfilehash: 8b1177278583bdb46f17119eb59235e70c58e806
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223099"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych w Azure Database for PostgreSQL przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do Azure Database for PostgreSQL oraz do przekształcania danych w Azure Database for PostgreSQL. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

Ten łącznik jest wyspecjalizowany dla [usługi Azure Database for PostgreSQL](../postgresql/overview.md). Aby skopiować dane z ogólnej bazy danych PostgreSQL znajdującej się lokalnie lub w chmurze, użyj [łącznika PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Azure Database for PostgreSQL jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika Azure Database for PostgreSQL.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla Azure Database for PostgreSQL połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **AzurePostgreSql**. | Tak |
| Parametry połączenia | Parametry połączenia ODBC do połączenia z Azure Database for PostgreSQL.<br/>Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `password` konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz następujące przykłady i [przechowywanie poświadczeń w Azure Key Vault](store-credentials-in-key-vault.md) . | Tak |
| Właściwością connectvia | Ta właściwość reprezentuje [środowisko Integration Runtime](concepts-integration-runtime.md) , które ma być używane do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

Typowe parametry połączenia to `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Poniżej przedstawiono więcej właściwości, które można ustawić dla danego przypadku:

| Właściwość | Opis | Opcje | Wymagane |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda wykorzystywana przez sterownik do szyfrowania danych przesyłanych między sterownikiem a serwerem bazy danych. Na przykład  `EncryptionMethod=<0/1/6>;`| 0 (bez szyfrowania) **(wartość domyślna)** /1 (SSL)/6 (RequestSSL) | Nie |
| ValidateServerCertificate (VSC) | Określa, czy sterownik sprawdza poprawność certyfikatu wysyłanego przez serwer bazy danych, gdy włączone jest szyfrowanie SSL (metoda szyfrowania = 1). Na przykład  `ValidateServerCertificate=<0/1>;`| 0 (wyłączone) **(wartość domyślna)** /1 (włączone) | Nie |

**Przykład:**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Przykład:**

**_Zapisz hasło w Azure Key Vault_* _

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [zestawy danych w Azure Data Factory](concepts-datasets-linked-services.md). Ta sekcja zawiera listę właściwości, które Azure Database for PostgreSQL obsługiwane w zestawach danych.

Aby skopiować dane z Azure Database for PostgreSQL, ustaw właściwość Type zestawu danych na _ * AzurePostgreSqlTable * *. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość **AzurePostgreSqlTable** | Tak |
| tableName | Nazwa tabeli | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przykład:**

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [potoki i działania w Azure Data Factory](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Azure Database for PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Usługa Azure Database for PostgreSql jako źródło

Aby skopiować dane z Azure Database for PostgreSQL, ustaw typ źródła w działaniu Copy na **AzurePostgreSqlSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość **AzurePostgreSqlSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `SELECT * FROM mytable` lub `SELECT * FROM "MyTable"` . Uwaga w PostgreSQL Nazwa jednostki jest traktowana jako niezależna od wielkości liter, jeśli nie jest ujęta w cudzysłów. | Nie (Jeśli określono Właściwość TableName w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL jako ujścia

Aby skopiować dane do Azure Database for PostgreSQL, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **AzurePostgreSQLSink**. | Tak |
| preCopyScript | Określ zapytanie SQL dla działania kopiowania, które ma zostać wykonane, zanim zapiszesz dane w Azure Database for PostgreSQL w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie |
| writeMethod | Metoda służąca do zapisywania danych w Azure Database for PostgreSQL.<br>Dozwolone wartości to: **CopyCommand** (wersja zapoznawcza, która jest bardziej wydajna), **BulkInsert** (wartość domyślna). | Nie |
| writeBatchSize | Liczba wierszy załadowanych do Azure Database for PostgreSQL na partię.<br>Dozwolona wartość jest liczbą całkowitą reprezentującą liczbę wierszy. | Nie (domyślnie 1 000 000) |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu.<br>Dozwolone wartości to ciągi TimeSpan. Przykładem jest 00:30:00 (30 minut). | Nie (domyślnie 00:30:00) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można odczytywać i zapisywać w tabelach z Azure Database for PostgreSQL. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [przekształcanie ujścia](data-flow-sink.md) w mapowaniu przepływów danych. Można wybrać użycie zestawu danych Azure Database for PostgreSQL lub [wbudowanego zestawu danych](data-flow-source.md#inline-datasets) jako źródła i typu ujścia.

### <a name="source-transformation"></a>Transformacja źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez Azure Database for PostgreSQL źródło. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabela | Jeśli wybierzesz opcję tabela jako dane wejściowe, przepływ danych pobierze wszystkie dane z tabeli określonej w zestawie danych. | Nie | - |*(tylko w przypadku wbudowanego zestawu danych)*<br>tableName |
| Zapytanie | Jeśli wybierzesz pozycję zapytanie jako dane wejściowe, określ zapytanie SQL, aby pobrać dane ze źródła, które zastępuje każdą tabelę określoną w zestawie danych. Korzystanie z zapytań jest świetnym sposobem zredukowania wierszy do testowania i wyszukiwania.<br><br>Klauzula **order by** nie jest obsługiwana, ale można ustawić pełną instrukcję SELECT FROM. Można również użyć funkcji tabeli zdefiniowanej przez użytkownika. **SELECT * FROM udfGetData ()** to format UDF w języku SQL, który zwraca tabelę, której można użyć w przepływie danych.<br>Przykład zapytania: `select * from mytable where customerId > 1000 and customerId < 2000` lub `select * from "MyTable"` . Uwaga w PostgreSQL Nazwa jednostki jest traktowana jako niezależna od wielkości liter, jeśli nie jest ujęta w cudzysłów.| Nie | Ciąg | query |
| Rozmiar partii | Określ rozmiar partii, aby podzielić duże ilości danych na partie. | Nie | Liczba całkowita | batchSize |
| Poziom izolacji | Wybierz jeden z następujących poziomów izolacji:<br>-Odczytaj zatwierdzone<br>-Odczytaj niezatwierdzone (wartość domyślna)<br>— Odczyt powtarzalny<br>— Możliwy do serializacji<br>-Brak (Ignoruj poziom izolacji) | Nie | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>DAWAJ</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Przykład skryptu źródłowego Azure Database for PostgreSQL

W przypadku użycia Azure Database for PostgreSQL jako typu źródła skojarzony skrypt przepływu danych jest:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Przekształcanie ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez Azure Database for PostgreSQL ujścia. Można edytować te właściwości na karcie **Opcje ujścia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update — Metoda | Określ, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwala na operacje wstawiania.<br>Aby zaktualizować, upsert lub usunąć wiersze, [przekształcenie ALTER Row](data-flow-alter-row.md) jest wymagane, aby można było oznaczyć wiersze dla tych działań. | Tak | `true` lub `false` | usuwaln <br/>wstawialny <br/>aktualizowalne <br/>upsertable |
| Kolumny klucza | W przypadku aktualizacji, upserts i usunięć należy ustawić kolumny kluczy, aby określić, który wiersz ma być zmieniany.<br>Nazwa kolumny, która jest wybierana jako klucz, będzie używana jako część kolejnej aktualizacji, Upsert, Usuń. W związku z tym należy wybrać kolumnę, która istnieje w mapowaniu ujścia. | Nie | Tablica | keys |
| Pomiń zapisywanie kolumn klucza | Jeśli chcesz, aby nie zapisywać wartości w kolumnie klucz, wybierz pozycję "Pomiń zapisywanie kolumn klucza". | Nie | `true` lub `false` | skipKeyWrites |
| Akcja tabeli |Określa, czy należy ponownie utworzyć lub usunąć wszystkie wiersze z tabeli docelowej przed zapisem.<br>- **Brak**: w tabeli nie zostanie wykonana żadna akcja.<br>- **Utwórz ponownie**: tabela zostanie porzucona i utworzona ponownie. Wymagane w przypadku dynamicznego tworzenia nowej tabeli.<br>- **Obcinanie**: wszystkie wiersze z tabeli docelowej zostaną usunięte. | Nie | `true` lub `false` | Utwórz ponownie<br/>obciąć |
| Rozmiar partii | Określ liczbę wierszy, które są zapisywane w każdej partii. Większe rozmiary partii zwiększają optymalizację kompresji i pamięci, ale grozi wyjątkami dotyczącymi pamięci podczas buforowania danych. | Nie | Liczba całkowita | batchSize |
| Pre i post skrypty SQL | Określ wielowierszowe skrypty SQL, które będą wykonywane przed (przed przetwarzaniem) i po wykonaniu (po przetworzeniu) dane są zapisywane w bazie danych ujścia. | Nie | Ciąg | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Przykład Azure Database for PostgreSQL skryptu ujścia

Gdy używasz Azure Database for PostgreSQL jako typu ujścia, skojarzony skrypt przepływu danych to:

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
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby uzyskać więcej informacji o właściwościach, zobacz [aktywność Lookup w Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
