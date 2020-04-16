---
title: Kopiowanie danych z sap hana
description: Dowiedz się, jak skopiować dane z usługi SAP HANA do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: 74462b68bea38e4d84219adeedb7c3bb0893bbb4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417241"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiowanie danych z systemu SAP HANA przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-sap-hana-connector.md)
> * [Aktualna wersja](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z bazy danych SAP HANA. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

>[!TIP]
>Aby dowiedzieć się, że usługa ADF jest obsługiwana w scenariuszu integracji danych SAP, zobacz [integracja danych SAP przy użyciu oficjalnych dokumentów usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ze szczegółowym wprowadzeniem, porównaniem i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik SAP HANA jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z bazy danych SAP HANA do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze SAP HANA obsługuje:

- Kopiowanie danych z dowolnej wersji bazy danych SAP HANA.
- Kopiowanie danych z **modeli informacyjnych HANA** (takich jak widoki analityczne i obliczeniowe) oraz **tabel wierszy/kolumn**.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **uwierzytelniania systemu Windows.**
- Kopiowanie równoległe ze źródła SAP HANA. Zobacz [kopiowanie równoległe z SAP HANA](#parallel-copy-from-sap-hana) sekcji, aby uzyskać szczegółowe informacje.

> [!TIP]
> Aby **skopiować** dane do magazynu danych SAP HANA, należy użyć ogólnego łącznika ODBC. Zobacz [SAP HANA umywalka](connector-odbc.md#sap-hana-sink) ze szczegółami. Należy zauważyć, że połączone usługi dla łącznika SAP HANA i łącznika ODBC są innego typu, dlatego nie można ponownie używać.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z tego łącznika SAP HANA, należy:

- Konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie. Zobacz [self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.
- Zainstaluj sterownik SAP HANA ODBC na komputerze integration runtime. Sterownik ODBC platformy SAP HANA możesz pobrać z witryny [SAP Software Download Center (Centrum pobierania oprogramowania SAP)](https://support.sap.com/swdc). Szukaj za pomocą słowa kluczowego **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika SAP HANA.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej SAP HANA:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **SapHana** | Tak |
| Parametry połączenia | Określ informacje potrzebne do nawiązania połączenia z sap HANA przy użyciu **uwierzytelniania podstawowego** lub **uwierzytelniania systemu Windows**. Zapoznaj się z poniższymi przykładami.<br>W ciągu połączenia serwer/port jest obowiązkowy (domyślny port to 30015), a nazwa użytkownika i hasło są obowiązkowe podczas korzystania z uwierzytelniania podstawowego. Dodatkowe ustawienia zaawansowane można znaleźć we [właściwościach połączenia SAP HANA ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Można również umieścić hasło w usłudze Azure Key Vault i wyciągnąć konfigurację hasła z ciągu połączenia. Więcej informacji można znaleźć [w artykule Poświadczenia sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| userName | Określ nazwę użytkownika podczas korzystania z uwierzytelniania systemu Windows. Przykład: `user@domain.com` | Nie |
| hasło | Określ hasło dla konta użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Nie |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Środowisko wykonawcze integracji hostowane samodzielnie jest wymagane, jak wspomniano w [wymaganiach wstępnych.](#prerequisites) |Tak |

**Przykład: użyj uwierzytelniania podstawowego**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: użyj uwierzytelniania systemu Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Jeśli używasz usługi połączonej SAP HANA z następującym ładunkiem, nadal jest obsługiwana w stanie, w jakim jest, podczas gdy zaleca się użycie nowej usługi w przyszłości.

**Przykład:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP HANA.

Aby skopiować dane z sap HANA, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **SapHanaTable** | Tak |
| Schematu | Nazwa schematu w bazie danych SAP HANA. | Nie (jeśli określono "zapytanie" w źródle działania) |
| tabela | Nazwa tabeli w bazie danych SAP HANA. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz `RelationalTable` wpisanego zestawu danych, jest on nadal obsługiwany w stanie gotowym do użycia, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA jako źródło

>[!TIP]
>Aby skutecznie pozyskiwania danych z sap HANA przy użyciu partycjonowania danych, dowiedz się więcej z [kopiowania równoległego z sekcji SAP HANA.](#parallel-copy-from-sap-hana)

Aby skopiować dane z sap HANA, następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **SapHanaSource** | Tak |
| query | Określa kwerendę SQL do odczytu danych z wystąpienia SAP HANA. | Tak |
| partitionOptions (opcje partycji) | Określa opcje partycjonowania danych używane do pozyskiwania danych z sap HANA. Dowiedz się więcej z [kopiowania równoległego z sekcji SAP HANA.](#parallel-copy-from-sap-hana)<br>Wartości zezwalania to: **Brak** (domyślnie), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Dowiedz się więcej z [kopiowania równoległego z sekcji SAP HANA.](#parallel-copy-from-sap-hana) `PhysicalPartitionsOfTable`można używać tylko podczas kopiowania danych z tabeli, ale nie kwerendy. <br>Gdy opcja partycji jest włączona `None`(czyli nie), stopień równoległości równoczesnych ładowania danych z [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) SAP HANA jest kontrolowany przez ustawienie w działaniu kopiowania. | False |
| podziałY | Określ grupę ustawień partycjonowania danych.<br>Zastosuj, gdy `SapHanaDynamicRange`opcja partycji jest . | False |
| partitionColumnName | Określ nazwę kolumny źródłowej, która będzie używana przez partycję do kopiowania równoległego. Jeśli nie zostanie określony, indeks lub klucz podstawowy tabeli jest wykrywany automatycznie i używany jako kolumna partycji.<br>Zastosuj, gdy opcja `SapHanaDynamicRange`partycji jest . Jeśli używasz kwerendy do pobierania danych `?AdfHanaDynamicRangePartitionCondition` źródłowych, należy podłączyć w klauzuli WHERE. Zobacz przykład [w kopiowanie równoległe z sekcji SAP HANA.](#parallel-copy-from-sap-hana) | Tak podczas `SapHanaDynamicRange` korzystania z partycji. |
| rozmiar pakietu | Określa rozmiar pakietu sieciowego (w kilobajtach) do dzielenia danych na wiele bloków. Jeśli masz dużą ilość danych do skopiowania, zwiększenie rozmiaru pakietu może zwiększyć szybkość odczytu z SAP HANA w większości przypadków. Podczas dostosowywania rozmiaru pakietu zaleca się testowanie wydajności. | Nie.<br>Wartość domyślna to 2048 (2MB). |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz `RelationalSource` wpisanego źródła kopii, jest ono nadal obsługiwane w stanie as-is, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="parallel-copy-from-sap-hana"></a>Kopia równoległa z SAP HANA

Łącznik SAP HANA fabryki danych zapewnia wbudowane partycjonowanie danych w celu równoległego kopiowania danych z systemu SAP HANA. Opcje partycjonowania danych można znaleźć w tabeli **Źródło** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Po włączeniu kopiowania podzielonego na partycje usługa Data Factory uruchamia równoległe kwerendy względem źródła SAP HANA w celu pobierania danych przez partycje. Stopień równoległy jest [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kontrolowany przez ustawienie działania kopiowania. Na przykład jeśli `parallelCopies` ustawisz cztery, usługa Data Factory jednocześnie generuje i uruchamia cztery kwerendy na podstawie określonej opcji partycji i ustawień, a każda kwerenda pobiera część danych z sap HANA.

Zaleca się włączenie kopiowania równoległego z partycjonowania danych, zwłaszcza podczas pozyskiwania dużej ilości danych z sap HANA. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartych na plikach zaleca się zapisywanie w folderze jako wielu plików (określanie tylko nazwy folderu), w którym to przypadku wydajność jest lepsza niż zapisywanie do jednego pliku.

| Scenariusz                                           | Sugerowane ustawienia                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Pełne obciążenie z dużego stołu.                        | **Opcja partycji**: Partycje fizyczne tabeli. <br><br/>Podczas wykonywania usługa Data Factory automatycznie wykrywa typ partycji fizycznej określonej tabeli SAP HANA i wybiera odpowiednią strategię partycji:<br>- **Partycjonowanie zakresu:** Pobierz kolumnę partycji i zakresy partycji zdefiniowane dla tabeli, a następnie skopiuj dane według zakresu. <br>- **Partycjonowanie skrótów:** Użyj klucza partycji mieszania jako kolumny partycji, a następnie partycji i skopiować dane na podstawie zakresów obliczeniowych ADF. <br>- **Partycjonowanie okrężne** lub **brak partycji:** Użyj klucza podstawowego jako kolumny partycji, a następnie partycjonuj i skopiuj dane na podstawie zakresów obliczeniowych ADF. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej. | **Opcja partycji**: Partycja zakresu dynamicznego.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Kolumna partycji**: Określ kolumnę używaną do zastosowania partycji zakresu dynamicznego. <br><br>Podczas wykonywania data factory najpierw oblicza zakresy wartości określonej kolumny partycji, równomiernie rozmieszcza wiersze w wielu zasobnikach zgodnie `?AdfHanaDynamicRangePartitionCondition` z liczbą odrębnych wartości kolumn partycji i ustawieniem kopiowania równoległego ADF, a następnie zastępuje filtrowanie zakresu wartości kolumny partycji dla każdej partycji i wysyła do SAP HANA.<br><br>Jeśli chcesz użyć wielu kolumn jako kolumny partycji, możesz połącz wartości każdej kolumny jako jedną kolumnę w kwerendzie i określić ją jako kolumnę partycji w podajniku ADF, na przykład `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`. |

**Przykład: kwerenda z partycjami fizycznymi tabeli**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Przykład: kwerenda z partycją zakresu dynamicznego**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapowanie typu danych dla sap HANA

Podczas kopiowania danych z sap HANA, następujące mapowania są używane z typów danych SAP HANA do usługi Azure Data Factory tymczasowych typów danych. Zobacz [Mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych SAP HANA | Tymczasowy typ danych fabryki danych |
| ------------------ | ------------------------------ |
| ALFANUM           | Ciąg                         |
| Bigint             | Int64                          |
| Binarnym             | Bajt[]                         |
| TEKST BINTEXT            | Ciąg                         |
| Blob               | Bajt[]                         |
| Bool               | Byte                           |
| Clob               | Ciąg                         |
| DATE               | DateTime                       |
| Dziesiętnych            | Wartość dziesiętna                        |
| Podwójne             | Double                         |
| Float              | Double                         |
| LICZBA CAŁKOWITA            | Int32                          |
| Nclob              | Ciąg                         |
| Nvarchar           | Ciąg                         |
| LICZBA RZECZYWISTA               | Single                         |
| ODDELEGOWKA         | DateTime                       |
| KRÓTKI TEKST          | Ciąg                         |
| SMALLDECIMAL (MAŁE ROZMIARY)       | Wartość dziesiętna                        |
| Smallint           | Int16                          |
| Stgeometrytype     | Bajt[]                         |
| STPOINTTYPE (TYP STPOINTTYPE)        | Bajt[]                         |
| TEKST               | Ciąg                         |
| CZAS               | przedział_czasu                       |
| Tinyint            | Byte                           |
| Varchar            | Ciąg                         |
| Sygnatury czasowej          | DateTime                       |
| Varbinary          | Bajt[]                         |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
