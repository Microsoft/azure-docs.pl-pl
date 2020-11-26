---
title: Kopiowanie danych z programu Teradata Vantage przy użyciu Azure Data Factory
description: Łącznik programu Teradata usługi Data Factory umożliwia kopiowanie danych z programu Teradata Vantage do magazynów danych obsługiwanych przez Data Factory jako ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2020
ms.author: jingwang
ms.openlocfilehash: a48ac86e8f9814adef9be2360b2446335d368447
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296560"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Kopiowanie danych z programu Teradata Vantage przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
>
> * [Wersja 1](v1/data-factory-onprem-teradata-connector.md)
> * [Bieżąca wersja](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z programu Teradata Vantage. Kompilacja jest oparta na [przeglądzie działania kopiowania](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik programu Teradata jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z programu Teradata Vantage można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W każdym przypadku ten łącznik programu Teradata obsługuje:

- Program Teradata w **wersji 14,10, 15,0, 15,10, 16,0, 16,10 i 16,20**.
- Kopiowanie danych przy użyciu **podstawowego**, **systemu Windows** lub uwierzytelniania **LDAP** .
- Równoległe kopiowanie ze źródła programu Teradata. Aby uzyskać szczegółowe informacje, zobacz sekcję [Kopiowanie równoległe z programu Teradata](#parallel-copy-from-teradata) .

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

W przypadku korzystania z Integration Runtime samodzielnego należy zauważyć, że zawiera on wbudowany sterownik Teradata, zaczynając od wersji 3,18. Nie musisz ręcznie instalować żadnego sterownika. Sterownik wymaga "Visual C++ redystrybucyjny 2012 Update 4" na komputerze, na którym działa środowisko Integration Runtime. Jeśli jeszcze tego nie zainstalowano, Pobierz go z tego [miejsca](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika programu Teradata.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Połączona usługa programu Teradata obsługuje następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **Teradata**. | Tak |
| Parametry połączenia | Określa informacje, które są konieczne do nawiązania połączenia z wystąpieniem programu Teradata. Zapoznaj się z poniższymi przykładami.<br/>Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `password` konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz temat [poświadczenia sklepu w Azure Key Vault](store-credentials-in-key-vault.md) . | Tak |
| nazwa użytkownika | Określ nazwę użytkownika, aby połączyć się z programem Teradata. Ma zastosowanie w przypadku korzystania z uwierzytelniania systemu Windows. | Nie |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Możesz również wybrać odwołanie do [wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). <br>Ma zastosowanie w przypadku korzystania z uwierzytelniania systemu Windows lub odwoływania się do hasła w Key Vault na potrzeby uwierzytelniania podstawowego. | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

Więcej właściwości połączenia, które można ustawić w parametrach połączenia dla danego przypadku:

| Właściwość | Opis | Wartość domyślna |
|:--- |:--- |:--- |
| UseDataEncryption | Określa, czy ma być szyfrowana cała komunikacja z bazą danych programu Teradata. Dozwolone wartości to 0 lub 1.<br><br/>- **0 (wyłączone, domyślnie)**: szyfruje wyłącznie informacje o uwierzytelnianiu.<br/>- **1 (włączone)**: szyfruje wszystkie dane przesyłane między sterownikiem a bazą danych. | `0` |
| CharacterSet | Zestaw znaków, który ma być używany przez sesję. Np `CharacterSet=UTF16` ..<br><br/>Ta wartość może być zestawem znaków zdefiniowanym przez użytkownika lub jednym z następujących wstępnie zdefiniowanych zestawów znaków: <br/>-ASCII<br/>-UTF8<br/>-UTF16<br/>-LATIN1252_0A<br/>-LATIN9_0A<br/>-LATIN1_0A<br/>-Shift-JIS (Windows, zgodne z systemem DOS, KANJISJIS_0S)<br/>-EUC (zgodne z systemem UNIX, KANJIEC_0U)<br/>-IBM mainframe (KANJIEBCDIC5035_0I)<br/>-KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>-SCHINESE936_6R0<br/>-TCHINESE950_8R0<br/>-NetworkKorean (HANGULKSC5601_2R4)<br/>-HANGUL949_7R0<br/>-ARABIC1256_6A0<br/>-CYRILLIC1251_2A0<br/>-HEBREW1255_5A0<br/>-LATIN1250_1A0<br/>-LATIN1254_7A0<br/>-LATIN1258_8A0<br/>-THAI874_4A0 | `ASCII` |
| MaxRespSize |Maksymalny rozmiar buforu odpowiedzi dla żądań SQL w kilobajtach (artykułów bazy wiedzy). Np `MaxRespSize=‭10485760‬` ..<br/><br/>W przypadku bazy danych programu Teradata w wersji 16,00 lub nowszej wartość maksymalna to 7361536. Dla połączeń korzystających ze starszych wersji wartość maksymalna to 1048576. | `65536` |
| O mechanizmie | Aby użyć protokołu LDAP do uwierzytelniania połączenia, określ `MechanismName=LDAP` . | Nie dotyczy |

**Przykład użycia uwierzytelniania podstawowego**

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

**Przykład użycia uwierzytelniania systemu Windows**

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

**Przykład użycia uwierzytelniania LDAP**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
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
> Następujący ładunek jest nadal obsługiwany. Należy jednak użyć nowej usługi.

**Poprzedni ładunek:**

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

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Teradata. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [zestawy danych](concepts-datasets-linked-services.md).

Aby skopiować dane z programu Teradata, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość `TeradataTable` . | Tak |
| database | Nazwa wystąpienia programu Teradata. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |
| table (stolik) | Nazwa tabeli w wystąpieniu programu Teradata. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

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
> `RelationalTable` Typ danych jest nadal obsługiwany. Zalecamy jednak korzystanie z nowego zestawu danych.

**Poprzedni ładunek:**

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

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło programu Teradata. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata jako źródło

>[!TIP]
>Aby efektywnie załadować dane z programu Teradata przy użyciu partycjonowania danych, Dowiedz się więcej z sekcji [Kopiowanie równoległe z programu Teradata](#parallel-copy-from-teradata) .

Aby skopiować dane z programu Teradata, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość `TeradataSource` . | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Może to być na przykład `"SELECT * FROM MyTable"`.<br>Po włączeniu obciążenia partycjonowanego należy podłączyć wszystkie odpowiednie wbudowane parametry partycji w zapytaniu. Przykłady można znaleźć w sekcji [Kopiowanie równoległe z programu Teradata](#parallel-copy-from-teradata) . | Nie (Jeśli określono tabelę w zestawie danych) |
| partitionOptions | Określa opcje partycjonowania danych używane do ładowania danych z programu Teradata. <br>Zezwalaj na wartości: **none** (wartość domyślna), **hash** i **DynamicRange**.<br>Gdy opcja partycji jest włączona (to nie jest `None` ), stopień równoległości do współbieżnego ładowania danych z programu Teradata jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie działania kopiowania. | Nie |
| partitionSettings | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja partycji nie jest `None` . | Nie |
| partitionColumnName | Określ nazwę kolumny źródłowej, która będzie używana przez partycję zakresu lub partycję skrótu dla kopii równoległej. Jeśli nie zostanie określony, podstawowy indeks tabeli zostanie automatycznie wykryty i użyty jako kolumna partycji. <br>Zastosuj, gdy opcja partycji to `Hash` lub `DynamicRange` . Jeśli używasz zapytania, aby pobrać dane źródłowe, hak `?AdfHashPartitionCondition` lub  `?AdfRangePartitionColumnName` w klauzuli WHERE. Zobacz przykład w sekcji [Kopiowanie równoległe z programu Teradata](#parallel-copy-from-teradata) . | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji, w której mają zostać skopiowane dane. <br>Zastosuj, gdy opcja partycji jest `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionUpbound` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [Kopiowanie równoległe z programu Teradata](#parallel-copy-from-teradata) . | Nie |
| partitionLowerBound | Minimalna wartość kolumny partycji, w której mają zostać skopiowane dane. <br>Zastosuj, gdy opcja partycji to `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionLowbound` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [Kopiowanie równoległe z programu Teradata](#parallel-copy-from-teradata) . | Nie |

> [!NOTE]
>
> `RelationalSource` Źródło kopii typu jest nadal obsługiwane, ale nie obsługuje nowego wbudowanego obciążenia równoległego z programu Teradata (opcje partycji). Zalecamy jednak korzystanie z nowego zestawu danych.

**Przykład: Kopiuj dane przy użyciu podstawowego zapytania bez partycji**

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

## <a name="parallel-copy-from-teradata"></a>Kopiowanie równoległe z programu Teradata

Łącznik Data Factory Teradata udostępnia wbudowane Partycjonowanie danych, które umożliwia równoległe kopiowanie danych z programu Teradata. Opcje partycjonowania danych można znaleźć w tabeli **źródłowej** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-teradata/connector-teradata-partition-options.png)

Po włączeniu kopiowania partycjonowanego Data Factory uruchamia zapytania równoległe względem źródła programu Teradata w celu załadowania danych przez partycje. Stopień równoległy jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie działania kopiowania. Jeśli na przykład ustawisz `parallelCopies` cztery, Data Factory współbieżnie generuje i uruchamia cztery zapytania w oparciu o określoną opcję partycji i ustawienia, a każde zapytanie pobiera część danych z programu Teradata.

Zalecane jest włączenie kopiowania równoległego przy użyciu partycjonowania danych, szczególnie podczas ładowania dużej ilości danych z programu Teradata. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartego na plikach, należy ponownie wykonać zapis do folderu jako wiele plików (Określ tylko nazwę folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne ładowanie z dużej tabeli.                                   | **Opcja partycji**: hash. <br><br/>Podczas wykonywania Data Factory automatycznie wykrywa podstawową kolumnę indeksu, stosuje skrót do niego i kopiuje dane przez partycje. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej.                 | **Opcja partycji**: hash.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>` .<br>**Kolumna partycji**: określ kolumnę używaną na potrzeby stosowania partycji skrótu. Jeśli nie zostanie określony, Data Factory automatycznie wykrywa kolumnę PK dla tabeli określonej w zestawie danych programu Teradata.<br><br>Podczas wykonywania Data Factory zastępuje `?AdfHashPartitionCondition` logikę partycji skrótu i wysyła do programu Teradata. |
| Załaduj dużą ilość danych przy użyciu zapytania niestandardowego, mając kolumnę Integer z równomiernie rozproszoną wartością dla partycjonowania zakresu. | **Opcje partycji**: dynamiczna partycja zakresu.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Kolumna partycji**: określ kolumnę używaną do partycjonowania danych. Można podzielić na kolumny z typem danych Integer.<br>**Górna** granica partycji i **Dolna granica partycji**: Określ, czy chcesz filtrować względem kolumny partycji, aby pobierać dane tylko między niższym i górnym zakresem.<br><br>Podczas wykonywania Data Factory zamienia `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` , i `?AdfRangePartitionLowbound` z rzeczywistą nazwą kolumny i zakresem wartości dla każdej partycji i wysyła do programu Teradata. <br>Na przykład jeśli kolumna partycji "ID" ma ustawioną dolną granicę 1 i górną granicę 80, z kopią równoległą ustawioną na wartość 4, Data Factory pobiera dane przez 4 partycje. Ich identyfikatory należą do zakresu od [1, 20], [21, 40], [41, 60] i [61, 80]. |

**Przykład: zapytanie z partycją skrótu**

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

**Przykład: zapytanie z dynamiczną partycją zakresu**

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

## <a name="data-type-mapping-for-teradata"></a>Mapowanie typu danych dla programu Teradata

Podczas kopiowania danych z programu Teradata są stosowane następujące mapowania. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych programu Teradata | Data Factory typ danych pośrednich |
|:--- |:--- |
| BigInt |Int64 |
| Obiekt blob |Byte [] |
| Byte |Byte [] |
| ByteInt |Int16 |
| Char |Ciąg |
| Obiektów CLOB |Ciąg |
| Data |DateTime |
| Liczba dziesiętna |Liczba dziesiętna |
| Double |Double |
| Zdjęć |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Integer |Int32 |
| Dzień interwału |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał od dnia do godziny |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał od dnia do minuty |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał od dnia do sekundy |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał czasu |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał od godziny do minuty |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał od godziny do sekundy |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał minut |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał od minuty do sekundy |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Miesiąc interwału |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał drugi |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Rok interwału |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Interwał od roku do miesiąca |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Liczba |Double |
| Okres (Data) |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Czas (Time) |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Okres (czas ze strefą czasową) |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Kropka (Sygnatura czasowa) |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Okres (Sygnatura czasowa ze strefą czasową) |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| SmallInt |Int16 |
| Godzina |przedział_czasu |
| Czas ze strefą czasową |przedział_czasu |
| Timestamp |DateTime |
| Sygnatura czasowa ze strefą czasową |DateTime |
| VarByte |Byte [] |
| VarChar |Ciąg |
| VarGraphic |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |
| Xml |Nieobsługiwane. Zastosuj jawne rzutowanie w zapytaniu źródłowym. |


## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
