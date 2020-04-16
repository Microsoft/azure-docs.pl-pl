---
title: Kopiowanie danych do i z programu SQL Server
description: Dowiedz się, jak przenosić dane do i z bazy danych programu SQL Server, która jest lokalna lub na maszynie Wirtualnej platformy Azure przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 063ac32c98d4eb64b676247c0a16f98fa7d1702d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416687"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopiowanie danych do i z programu SQL Server przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję usługi Azure Data Factory, której używasz:"]
> * [Wersja 1](v1/data-factory-sqlserver-connector.md)
> * [Aktualna wersja](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych programu SQL Server. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik programu SQL Server jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

Dane z bazy danych programu SQL Server można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Można też skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do bazy danych programu SQL Server. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła lub pochłaniacze przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik programu SQL Server obsługuje:

- Wersje programu SQL Server 2016, 2014, 2012, 2008 R2, 2008 i 2005.
- Kopiowanie danych przy użyciu uwierzytelniania SQL lub systemu Windows.
- Jako źródło pobieranie danych przy użyciu kwerendy SQL lub procedury składowanej.
- Jako ujście dołączanie danych do tabeli docelowej lub wywoływanie procedury składowanej z niestandardową logiką podczas kopiowania.

[Usługa SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) nie jest obsługiwana.

>[!NOTE]
>SQL Server [Zawsze szyfrowane](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) nie jest obsługiwany przez ten łącznik teraz. Aby obejść, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownika ODBC programu SQL Server. Postępuj zgodnie z [tymi wskazówkami](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) z konfiguracjami pobierania sterowników ODBC i ciągów połączeń.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika bazy danych programu SQL Server.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej programu SQL Server:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **SqlServer**. | Tak |
| Parametry połączenia |Określ **connectionString** informacje, które są potrzebne do połączenia się z bazą danych programu SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. Zapoznaj się z poniższymi przykładami.<br/>Można również umieścić hasło w usłudze Azure Key Vault. Jeśli jest to uwierzytelnianie `password` SQL, pociągnij konfigurację z ciągu połączenia. Aby uzyskać więcej informacji, zobacz przykład JSON po tabeli i [poświadczenia sklepu w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| userName |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykładem jest **\\nazwa_domeny .** |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w usłudze Azure Data Factory. Możesz też [odwołać się do klucza tajnego przechowywanego w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Nie |
| connectVia | To [środowisko wykonawcze integracji](concepts-integration-runtime.md) jest używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

>[!TIP]
>Jeśli natkniesz się na błąd z kodem błędu "UserErrorFailedToConnectToSqlServer" i komunikatem typu `Pooling=false` "Limit sesji dla bazy danych jest XXX i został osiągnięty", dodaj do ciągu połączenia i spróbuj ponownie.

**Przykład 1: Użyj uwierzytelniania SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: Używanie uwierzytelniania SQL z hasłem w usłudze Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
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

**Przykład 3: Korzystanie z uwierzytelniania systemu Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu SQL Server.

Aby skopiować dane z i do bazy danych programu SQL Server, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na **SqlServerTable**. | Tak |
| Schematu | Nazwa schematu. |Nie dla źródła, tak dla zlewu  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, tak dla zlewu  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. W przypadku nowego `schema` `table`obciążenia, użycia i programu . | Nie dla źródła, tak dla zlewu |

**Przykład**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do użycia do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło programu SQL Server i ujście.

### <a name="sql-server-as-a-source"></a>SQL Server jako źródło

Aby skopiować dane z programu SQL Server, ustaw typ źródła w działaniu kopiowania na **SqlSource**. Następujące właściwości są obsługiwane w sekcji źródła działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na **SqlSource**. | Tak |
| sqlReaderQuery |Użyj niestandardowej kwerendy SQL, aby odczytać dane. Może to być na przykład `select * from MyTable`. |Nie |
| nazwa sqlReaderStoredProcedureName |Ta właściwość jest nazwą procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry te są dla procedury składowanej.<br/>Dozwolone wartości to pary nazw lub wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| Isolationlevel | Określa zachowanie blokowania transakcji dla źródła SQL. Dozwolone wartości to: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Więcej informacji można znaleźć [w tym dok.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Nie |

**Uwagi do uwag:**

- Jeśli **sqlReaderQuery** jest określony dla **SqlSource**, działanie kopiowania uruchamia tę kwerendę względem źródła programu SQL Server, aby uzyskać dane. Można również określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters,** jeśli procedura składowana przyjmuje parametry.
- Jeśli nie określisz **sqlReaderQuery** lub **sqlReaderStoredProcedureName**, kolumny zdefiniowane w sekcji "struktura" zestawu danych JSON są używane do konstruowania kwerendy. Kwerenda `select column1, column2 from mytable` jest uruchamiana względem programu SQL Server. Jeśli definicja zestawu danych nie ma "struktury", wszystkie kolumny są wybierane z tabeli.

**Przykład: Użyj kwerendy SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Przykład: użyj procedury składowanej**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definicja procedury składowanej**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server jako zlew

> [!TIP]
> Dowiedz się więcej o obsługiwanych zachowaniach zapisu, konfiguracjach i najlepszych praktykach z [najlepszych rozwiązań dotyczących ładowania danych do programu SQL Server.](#best-practice-for-loading-data-into-sql-server)

Aby skopiować dane do programu SQL Server, ustaw typ ujścia w działaniu kopiowania na **SqlSink**. Następujące właściwości są obsługiwane w sekcji ujście działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na **SqlSink**. | Tak |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL *na partię*.<br/>Dozwolone wartości są liczbami całkowitymi dla liczby wierszy. Domyślnie usługa Azure Data Factory dynamicznie określa odpowiedni rozmiar partii na podstawie rozmiaru wiersza. |Nie |
| writeBatchTimeout |Ta właściwość określa czas oczekiwania dla operacji wstawiania partii, aby zakończyć przed jej limit czasu.<br/>Dozwolone wartości są dla timespan. Przykładem jest "00:30:00" przez 30 minut. Jeśli nie określono żadnej wartości, limit czasu domyślnie wynosi "02:00:00". |Nie |
| preCopyScript |Ta właściwość określa kwerendę SQL dla działania kopiowania, które ma być uruchamiane przed zapisaniem danych w programie SQL Server. Jest wywoływana tylko raz na uruchomienie kopii. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. |Nie |
| nazwa sqlWriterStoredProcedureName | Nazwa procedury składowanej, która definiuje sposób stosowania danych źródłowych do tabeli docelowej. <br/>Ta procedura *składowana jest wywoływana na partię*. W przypadku operacji, które są uruchamiane tylko raz i nie mają nic `preCopyScript` wspólnego z danymi źródłowymi, na przykład usuń lub obcięć, użyj właściwości. | Nie |
| przechowywaneProcedureTableTypeParameterName |Nazwa parametru typu tabeli określonego w procedurze składowanej.  |Nie |
| sqlWriterTableType (Typ tabeli maszyn sqlWriterTableType) |Nazwa typu tabeli, która ma być używana w procedurze składowanej. Działanie kopiowania sprawia, że dane są przenoszone dostępne w tabeli tymczasowej z tego typu tabeli. Kod procedury składowanej można następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw i wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. | Nie |
| tableOpcja | Określa, czy tabela ujścia ma być automatycznie tworzędliwa, jeśli nie istnieje na podstawie schematu źródłowego. Automatyczne tworzenie tabeli nie jest obsługiwane, gdy zlew określa procedurę składowaną lub kopia etapowa jest skonfigurowana w działaniu kopiowania. Dozwolone wartości to: `none` (domyślnie), `autoCreate`. |Nie |

**Przykład 1: Dołączanie danych**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Przykład 2: Wywołanie procedury składowanej podczas kopiowania**

Dowiedz się więcej szczegółów z [Wywołać procedurę składowaną z ujścia SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Najlepsze rozwiązanie dotyczące ładowania danych do programu SQL Server

Podczas kopiowania danych do programu SQL Server może być wymagane inne zachowanie zapisu:

- [Dołącz](#append-data): Moje dane źródłowe mają tylko nowe rekordy.
- [Upsert](#upsert-data): Moje dane źródłowe mają zarówno wstawia i aktualizacje.
- [Zastąp](#overwrite-the-entire-table): Chcę za każdym razem przeładować całą tabelę wymiarów.
- [Pisanie z niestandardową logiką:](#write-data-with-custom-logic)Potrzebuję dodatkowego przetwarzania przed ostatecznym wstawieniem do tabeli docelowej.

Zobacz odpowiednie sekcje, jak skonfigurować w usłudze Azure Data Factory i najlepsze rozwiązania.

### <a name="append-data"></a>Dołączanie danych

Dołączanie danych jest domyślnym zachowaniem tego łącznika ujścia programu SQL Server. Usługa Azure Data Factory wykonuje zbiorcze wstawianie, aby skutecznie zapisywać w tabeli. Można skonfigurować źródło i sink odpowiednio w działania kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Wariant 1:** Jeśli masz dużą ilość danych do skopiowania, użyj następującej metody, aby wykonać ofertę dodatkową: 

- Najpierw użyj [tabeli tymczasowej,](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) aby zbiorczo załadować wszystkie rekordy przy użyciu działania kopiowania. Ponieważ operacje względem tabel tymczasowych nie są rejestrowane, można załadować miliony rekordów w ciągu kilku sekund.
- Uruchom działanie procedury składowanej w usłudze Azure Data Factory, aby zastosować instrukcję [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) lub INSERT/UPDATE. Użyj tabeli tymczasowej jako źródła do wykonywania wszystkich aktualizacji lub wstawia jako pojedynczej transakcji. W ten sposób zmniejsza się liczba operacji w obie strony i dzienników. Po zakończeniu działania procedury składowanej tabela tymczasowa może zostać obcięta, aby była gotowa do następnego cyklu upsert.

Na przykład w usłudze Azure Data Factory można utworzyć potok z **działaniem kopiowania** połączone z **działaniem procedury przechowywanej.** Pierwszy kopiuje dane z magazynu źródłowego do tabeli tymczasowej bazy danych, na przykład **##UpsertTempTable**, jako nazwę tabeli w zestawie danych. Następnie ten ostatni wywołuje procedurę składowaną, aby scalić dane źródłowe z tabeli tymczasowej do tabeli docelowej i oczyścić tabelę tymczasową.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

W bazie danych należy zdefiniować procedurę składowaną z logiką SCALANIA, podobnie jak w poniższym przykładzie, który jest wskazywany na poprzednie działanie procedury składowanej. Załóżmy, że celem jest **tabela Marketing** z trzema kolumnami: **ProfileID**, **State**i **Category**. Wykonaj upsert na podstawie **kolumny ProfileID.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Wariant 2:** Można również [wywołać procedurę składowaną w ramach działania kopiowania](#invoke-a-stored-procedure-from-a-sql-sink). To podejście uruchamia każdy wiersz w tabeli źródłowej zamiast przy użyciu zbiorczego wstawiania jako domyślnego podejścia w działaniu kopiowania, co nie jest odpowiednie dla dużych skalowania upsert.

### <a name="overwrite-the-entire-table"></a>Zastępowanie całej tabeli

Właściwość **preCopyScript** można skonfigurować w ujściu działania kopiowania. W takim przypadku dla każdego działania kopiowania, który jest uruchamiany, usługa Azure Data Factory uruchamia najpierw skrypt. Następnie uruchamia kopię, aby wstawić dane. Na przykład, aby zastąpić całą tabelę najnowszymi danymi, określ skrypt, aby najpierw usunąć wszystkie rekordy przed zbiorczym załadowaniem nowych danych ze źródła.

### <a name="write-data-with-custom-logic"></a>Zapis danych za pomocą niestandardowej logiki

Kroki zapisywania danych za pomocą logiki niestandardowej są podobne do opisanych w sekcji [Dane Upsert.](#upsert-data) Jeśli musisz zastosować dodatkowe przetwarzanie przed ostatecznym wstawieniem danych źródłowych do tabeli docelowej, na dużą skalę można wykonać jedną z dwóch czynności: 

- Wczytaj do tabeli tymczasowej, a następnie wywołaj procedurę składowaną. 
- Wywołać procedurę składowaną podczas kopiowania.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Wywoływanie procedury składowanej z ujścia SQL

Podczas kopiowania danych do bazy danych programu SQL Server można również skonfigurować i wywołać procedurę składowaną określoną przez użytkownika z dodatkowymi parametrami. Funkcja procedury składowanej korzysta z [parametrów wycenionych w tabeli](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Wywoływanie procedury składowanej przetwarza wiersz danych według wiersza zamiast przy użyciu operacji zbiorczej, która nie jest zalecana dla kopiowania na dużą skalę. Dowiedz się więcej z [najlepszych rozwiązań dotyczących ładowania danych do programu SQL Server](#best-practice-for-loading-data-into-sql-server).

Można użyć procedury składowanej, gdy wbudowane mechanizmy kopiowania nie służą do celu. Przykładem jest, gdy chcesz zastosować dodatkowe przetwarzanie przed ostatecznym wstawieniem danych źródłowych do tabeli docelowej. Niektóre dodatkowe przykłady przetwarzania są, gdy chcesz scalić kolumny, wyszukać dodatkowe wartości i wstawić dane do więcej niż jednej tabeli.

W poniższym przykładzie pokazano, jak użyć procedury składowanej, aby wykonać upsert do tabeli w bazie danych programu SQL Server. Załóżmy, że dane wejściowe i zlew **Tabela marketingowa** mają po trzy kolumny: **ProfileID**, **State**i **Category**. Wykonaj upsert na podstawie **kolumny ProfileID** i zastosuj ją tylko dla określonej kategorii o nazwie "ProductA".

1. W bazie danych zdefiniuj typ tabeli o takiej samej nazwie jak **sqlWriterTableType**. Schemat typu tabeli jest taki sam jak schemat zwracany przez dane wejściowe.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. W bazie danych zdefiniuj procedurę składowaną o tej samej nazwie co **sqlWriterStoredProcedureName**. Obsługuje dane wejściowe z określonego źródła i scala się z tabelą danych wyjściowych. Nazwa parametru typu tabeli w procedurze składowanej jest taka sama jak **nazwa tabeli** zdefiniowana w zestawie danych.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. W usłudze Azure Data Factory zdefiniuj sekcję **ujścia SQL** w działaniu kopiowania w następujący sposób:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mapowanie typu danych dla programu SQL Server

Podczas kopiowania danych z i do programu SQL Server, następujące mapowania są używane z typów danych programu SQL Server do usługi Azure Data Factory tymczasowych typów danych. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

| Typ danych programu SQL Server | Tymczasowy typ danych usługi Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binarny |Bajt[] |
| bit |Wartość logiczna |
| char |Ciąg, Char[] |
| date |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Wartość dziesiętna |Wartość dziesiętna |
| Atrybut FILESTREAM (varbinary(max)) |Bajt[] |
| Liczba zmiennoprzecinkowa |Double |
| image |Bajt[] |
| int |Int32 |
| pieniędzy |Wartość dziesiętna |
| nchar |Ciąg, Char[] |
| Ntext |Ciąg, Char[] |
| numeryczne |Wartość dziesiętna |
| nvarchar |Ciąg, Char[] |
| rzeczywiste |Single |
| Rowversion |Bajt[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Wartość dziesiętna |
| Sql_variant |Obiekt |
| tekst |Ciąg, Char[] |
| time |przedział_czasu |
| sygnatura czasowa |Bajt[] |
| tinyint |Int16 |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Bajt[] |
| varchar |Ciąg, Char[] |
| xml |Xml |

>[!NOTE]
> Dla typów danych, które mapują do dziesiętnego typu tymczasowego, obecnie Usługa Azure Data Factory obsługuje dokładność do 28. Jeśli masz dane, które wymagają precyzji większej niż 28, należy rozważyć konwersję na ciąg w kwerendzie SQL.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Rozwiązywanie problemów z połączeniem

1. Skonfiguruj wystąpienie programu SQL Server tak, aby akceptować połączenia zdalne. Uruchom **program SQL Server Management Studio**, kliknij prawym przyciskiem myszy **serwer**i wybierz polecenie **Właściwości**. Z listy **wybierz pozycję Połączenia** i zaznacz pole wyboru **Zezwalaj na połączenia zdalne** z tym serwerem.

    ![Włącz połączenia zdalne](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Aby uzyskać szczegółowe kroki, zobacz [Konfigurowanie opcji konfiguracji serwera dostępu zdalnego](https://msdn.microsoft.com/library/ms191464.aspx).

2. Uruchom **program SQL Server Configuration Manager**. Rozwiń **węzeł Konfiguracja sieci programu SQL Server** dla odpowiedniego wystąpienia i wybierz pozycję **Protokoły dla serwera MSSQLSERVER**. Protokoły są wyświetlane w prawym okienku. Włącz protokół TCP/IP, klikając prawym przyciskiem myszy **TCP/IP** i wybierając polecenie **Włącz**.

    ![Włączanie protokołu TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Aby uzyskać więcej informacji i alternatywne sposoby włączania protokołu TCP/IP, zobacz [Włączanie lub wyłączanie protokołu sieciowego serwera](https://msdn.microsoft.com/library/ms191294.aspx).

3. W tym samym oknie kliknij dwukrotnie pozycję **TCP/IP,** aby uruchomić okno **Właściwości protokołu TCP/IP.**
4. Przełącz się na kartę **Adresy IP.** Przewiń w dół, aby wyświetlić sekcję **IPAll.** Zapisz **port TCP**. Wartość domyślna to **1433**.
5. Utwórz **regułę Zapory systemu Windows** na komputerze, aby zezwolić na ruch przychodzący przez ten port. 
6. **Sprawdź połączenie:** Aby połączyć się z programem SQL Server przy użyciu w pełni kwalifikowanej nazwy, użyj programu SQL Server Management Studio z innego komputera. Może to być na przykład `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
