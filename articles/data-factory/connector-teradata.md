---
title: Kopiowanie danych z programu Teradata Vantage przy użyciu usługi Azure Data Factory
description: Łącznik Teradata usługi Data Factory umożliwia kopiowanie danych z teradata Vantage do magazynów danych obsługiwanych przez fabrykę danych jako pochłaniacze.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 4eed79210e3e39f82b892ac0681e161ebb59597e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418035"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Kopiowanie danych z programu Teradata Vantage przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
>
> * [Wersja 1](v1/data-factory-onprem-teradata-connector.md)
> * [Bieżąca wersja](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z programu Teradata Vantage. Opiera się na [przeglądzie działania kopiowania](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Teradata jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z Teradata Vantage do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze Teradata obsługuje:

- Teradata **w wersji 14.10, 15.0, 15.10, 16.0, 16.10 i 16.20**.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **uwierzytelniania systemu Windows.**
- Kopiowanie równoległe ze źródła Teradata. Zobacz [kopię równoległą z Teradata](#parallel-copy-from-teradata) sekcji, aby uzyskać szczegółowe informacje.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Jeśli używasz self-hosted Integration Runtime, należy pamiętać, że zapewnia wbudowany sterownik Teradata począwszy od wersji 3.18. Nie trzeba ręcznie instalować żadnego sterownika. Sterownik wymaga "Visual C++ Redystrybucyjne 2012 Update 4" na komputerze wykonawczego integracji hostowanego samodzielnie. Jeśli nie masz jeszcze zainstalowanego, pobierz go [stąd](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika Teradata.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Usługa połączona teradata obsługuje następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **Teradata**. | Tak |
| Parametry połączenia | Określa informacje potrzebne do nawiązania połączenia z wystąpieniem Teradata. Zapoznaj się z poniższymi przykładami.<br/>Można również umieścić hasło w usłudze Azure `password` Key Vault i wyciągnąć konfigurację z ciągu połączenia. Więcej informacji można znaleźć [w witrynie Store credentials w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| nazwa użytkownika | Określ nazwę użytkownika, aby połączyć się z Teradata. Ma zastosowanie, gdy używasz uwierzytelniania systemu Windows. | Nie |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Można również odwołać się do [klucza tajnego przechowywanego w usłudze Azure Key Vault](store-credentials-in-key-vault.md). <br>Ma zastosowanie, gdy używasz uwierzytelniania systemu Windows lub odwołujesz się do hasła w magazynie kluczy w celu uwierzytelnienia podstawowego. | Nie |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

Więcej właściwości połączenia można ustawić w ciągu połączenia w przypadku:

| Właściwość | Opis | Wartość domyślna |
|:--- |:--- |:--- |
| Characterset | Zestaw znaków do użycia w sesji. `CharacterSet=UTF16`Np.<br><br/>Ta wartość może być zdefiniowanym przez użytkownika zestawem znaków lub jednym z następujących wstępnie zdefiniowanych zestawów znaków: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS kompatybilny, KANJISJIS_0S)<br/>- EUC (kompatybilny z Unixem, KANJIEC_0U)<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Wartością `ASCII`domyślną jest . |
| Rozmiar MaxRespSize |Maksymalny rozmiar buforu odpowiedzi dla żądań SQL w kilobajtach (KB). `MaxRespSize=‭10485760‬`Np.<br/><br/>W przypadku bazy danych Teradata w wersji 16.00 lub nowszej maksymalna wartość to 7361536. W przypadku połączeń korzystających z wcześniejszych wersji maksymalna wartość to 1048576. | Wartością `65536`domyślną jest . |

**Przykład przy użyciu uwierzytelniania podstawowego**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład przy użyciu uwierzytelniania systemu Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Następujący ładunek jest nadal obsługiwany. Idąc dalej, należy jednak użyć nowego.

**Poprzednia ładowność:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
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

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Teradata. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Zestawy danych](concepts-datasets-linked-services.md).

Aby skopiować dane z teradata, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi `TeradataTable`być ustawiona na . | Tak |
| database | Nazwa wystąpienia Teradata. | Nie (jeśli określono "zapytanie" w źródle działania) |
| tabela | Nazwa tabeli w wystąpieniu Teradata. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`typ zestawu danych jest nadal obsługiwany. Zaleca się jednak użycie nowego zestawu danych.

**Poprzednia ładowność:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Teradata. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata jako źródło

>[!TIP]
>Aby efektywnie załadować dane z Teradata przy użyciu partycjonowania danych, dowiedz się więcej z [kopiowania równoległego z sekcji Teradata.](#parallel-copy-from-teradata)

Aby skopiować dane z teradata, w sekcji **źródła** działania kopiowania obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania `TeradataSource`musi być ustawiona na . | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Może to być na przykład `"SELECT * FROM MyTable"`.<br>Po włączeniu obciążenia partycjonowanego, należy podłączyć wszelkie odpowiednie wbudowane parametry partycji w zapytaniu. Przykłady można znaleźć w sekcji [Kopia równoległa z teradaty.](#parallel-copy-from-teradata) | Nie (jeśli określono tabelę w zestawie danych) |
| partitionOptions (opcje partycji) | Określa opcje partycjonowania danych używane do ładowania danych z teradata. <br>Wartości zezwalania to: **Brak** (domyślnie), **Hash** i **DynamicRange**.<br>Gdy opcja partycji jest włączona `None`(czyli nie), stopień równoległości równoczesnych ładowania danych z [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) Teradata jest kontrolowany przez ustawienie działania kopiowania. | Nie |
| podziałY | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja `None`partycji nie jest . | Nie |
| partitionColumnName | Określ nazwę kolumny źródłowej, która będzie używana przez partycję zakresu lub partycję hash dla kopiowania równoległego. Jeśli nie zostanie określony, podstawowy indeks tabeli jest wykrywany automatycznie i używany jako kolumna partycji. <br>Zastosuj, gdy opcja `Hash` `DynamicRange`partycji jest lub . Jeśli używasz kwerendy do pobierania danych `?AdfHashPartitionCondition` `?AdfRangePartitionColumnName` źródłowych, hak lub w klauzuli WHERE. Zobacz przykład [w kopiowanie równoległe z sekcji Teradata.](#parallel-copy-from-teradata) | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji do skopiowania danych. <br>Zastosuj, gdy `DynamicRange`opcja partycji jest . Jeśli używasz kwerendy do pobierania `?AdfRangePartitionUpbound` danych źródłowych, należy podłączyć w klauzuli WHERE. Na przykład zobacz [kopię równoległą z Teradata](#parallel-copy-from-teradata) sekcji. | Nie |
| partycjaLowerBound | Minimalna wartość kolumny partycji do skopiowania danych. <br>Zastosuj, gdy opcja `DynamicRange`partycji jest . Jeśli używasz kwerendy do pobierania danych `?AdfRangePartitionLowbound` źródłowych, należy podłączyć w klauzuli WHERE. Na przykład zobacz [kopię równoległą z Teradata](#parallel-copy-from-teradata) sekcji. | Nie |

> [!NOTE]
>
> `RelationalSource`typ źródła kopii jest nadal obsługiwany, ale nie obsługuje nowego wbudowanego obciążenia równoległego z Teradata (opcje partycji). Zaleca się jednak użycie nowego zestawu danych.

**Przykład: kopiowanie danych przy użyciu podstawowej kwerendy bez partycji**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Kopia równoległa z Teradata

Łącznik Teradata fabryki danych zapewnia wbudowane partycjonowanie danych w celu równoległego kopiowania danych z teradaty. Opcje partycjonowania danych można znaleźć w tabeli **Źródło** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-teradata/connector-teradata-partition-options.png)

Po włączeniu kopii podzielonej na partycje usługa Data Factory uruchamia równoległe kwerendy względem źródła Teradata w celu załadowania danych według partycji. Stopień równoległy jest [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kontrolowany przez ustawienie działania kopiowania. Na przykład jeśli `parallelCopies` ustawisz cztery, data factory jednocześnie generuje i uruchamia cztery kwerendy na podstawie określonej opcji partycji i ustawień, a każda kwerenda pobiera część danych z Teradata.

Zaleca się włączenie kopiowania równoległego z partycjonowanie danych, zwłaszcza podczas ładowania dużej ilości danych z Teradata. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartych na plikach zaleca się zapisywanie w folderze jako wielu plików (określanie tylko nazwy folderu), w którym to przypadku wydajność jest lepsza niż zapisywanie do jednego pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne obciążenie z dużego stołu.                                   | **Opcja partycji**: Skrót. <br><br/>Podczas wykonywania usługa Data Factory automatycznie wykrywa kolumnę indeksu podstawowego, stosuje względem niej skrót i kopiuje dane według partycji. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej.                 | **Opcja partycji**: Skrót.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Kolumna partycji**: Określ kolumnę używaną do stosowania partycji mieszania. Jeśli nie zostanie określony, usługa Data Factory automatycznie wykrywa kolumnę PK tabeli określonej w zestawie danych Teradata.<br><br>Podczas wykonywania usługa Data `?AdfHashPartitionCondition` Factory zastępuje logikę partycji mieszania i wysyła do Teradata. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej, mając kolumnę całkowitą o równomiernie rozłożonej wartości do partycjonowania zakresu. | **Opcje partycji**: Partycja zakresu dynamicznego.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Kolumna partycji**: Określ kolumnę używaną do partycjonowania danych. Można podzielić na kolumnę z typem danych liczby całkowitej.<br>**Górna granica partycji** i **dolna granica partycji**: Określ, czy chcesz filtrować względem kolumny partycji, aby pobierać dane tylko między dolnym i górnym zakresem.<br><br>Podczas wykonywania usługa Data `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` zastępuje , a rzeczywista nazwa kolumny i zakresy wartości dla każdej partycji i wysyła do Teradata. <br>Na przykład jeśli kolumna partycji "ID" ustawiona z dolną granicą jako 1 i górną granicą jako 80, z kopią równoległą ustawioną jako 4, usługa Data Factory pobiera dane przez 4 partycje. Ich identyfikatory znajdują się odpowiednio między [1,20], [21, 40], [41, 60] i [61, 80]. |

**Przykład: kwerenda z partycją mieszania**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Przykład: kwerenda z partycją zakresu dynamicznego**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Mapowanie typu danych dla teradata

Podczas kopiowania danych z Teradata stosuje się następujące mapowania. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

| Typ danych Teradata | Tymczasowy typ danych data factory |
|:--- |:--- |
| Bigint |Int64 |
| Obiekt blob |Bajt[] |
| Byte |Bajt[] |
| BajtInt |Int16 |
| Char |Ciąg |
| Clob |Ciąg |
| Date |DateTime |
| Wartość dziesiętna |Wartość dziesiętna |
| Double |Double |
| Graficzny |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Liczba całkowita |Int32 |
| Dzień interwału |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Interwał dzień na godzinę |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Interwał dzień na minutę |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Interwał od dnia do drugiego |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Interwał Godzina |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Interwał od godziny do minuty |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Interwał od godziny do sekundy |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Minuta interwału |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Interwał od minuty do sekundy |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Miesiąc interwału |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Interwał drugi |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Rok interwałowy |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Interwał rok do miesiąca |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Liczba |Double |
| Okres (data) |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Okres (czas) |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Okres (czas ze strefą czasową) |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Okres (sygnatura czasowa) |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Okres (sygnatura czasowa ze strefą czasową) |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Smallint |Int16 |
| Time |przedział_czasu |
| Czas ze strefą czasową |przedział_czasu |
| Znacznik czasu |DateTime |
| Sygnatura czasowa ze strefą czasową |DateTime |
| VarByte ( VarByte ) |Bajt[] |
| Varchar |Ciąg |
| Grafika vargraficzna |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |
| Xml |Bez pomocy technicznej. Stosowanie jawnego rzutu w kwerendzie źródłej. |


## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w fabryce danych, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
