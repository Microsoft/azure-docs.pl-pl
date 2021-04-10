---
title: Kopiowanie danych z Netezza za pomocą Azure Data Factory
description: Informacje o kopiowaniu danych z programu Netezza do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jingwang
ms.openlocfilehash: 4b12c1f24f389634004de3d487a693bc588a7241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374358"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopiowanie danych z Netezza za pomocą Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z Netezza. Artykuł jest oparty na [działaniu kopiowania w Azure Data Factory](copy-activity-overview.md), co przedstawia ogólne omówienie działania kopiowania.

>[!TIP]
>Aby uzyskać informacje na temat scenariusza migracji danych z programu Netezza do platformy Azure, Dowiedz się więcej na temat [używania Azure Data Factory do migrowania danych z lokalnego serwera Netezza na platformę Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Netezza jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)


Dane z Netezza można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).

Łącznik Netezza obsługuje równoległe kopiowanie ze źródła. Więcej informacji zawiera sekcja [Parallel Copy from Netezza](#parallel-copy-from-netezza) .

Azure Data Factory udostępnia wbudowany sterownik umożliwiający nawiązywanie połączeń. Nie musisz ręcznie instalować żadnego sterownika, aby użyć tego łącznika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

Można utworzyć potok używający działania kopiowania przy użyciu zestawu .NET SDK, zestawu SDK języka Python, Azure PowerShell, interfejsu API REST lub szablonu Azure Resource Manager. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku z działaniem kopiowania, zobacz [Samouczek dotyczący działania kopiowania](quickstart-create-data-factory-dot-net.md) .

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania jednostek Data Factory, które są specyficzne dla łącznika Netezza.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Netezza są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **Netezza**. | Tak |
| Parametry połączenia | Parametry połączenia ODBC do połączenia z Netezza. <br/>Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `pwd` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

Typowe parametry połączenia to `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` . W poniższej tabeli opisano więcej właściwości, które można ustawić:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| SecurityLevel | Poziom zabezpieczeń wykorzystywany przez sterownik do nawiązywania połączenia z magazynem danych. Sterownik obsługuje połączenia SSL z uwierzytelnianiem jednokierunkowym przy użyciu protokołu SSL w wersji 3. <br>Przykład: `SecurityLevel=preferredSecured`. Obsługiwane są następujące wartości:<br/>- **Tylko niezabezpieczone** (**onlyUnSecured**): sterownik nie używa protokołu SSL.<br/>- **Preferowana niezabezpieczona (preferredUnSecured) (domyślnie)**: Jeśli serwer zawiera wybór, sterownik nie używa protokołu SSL. <br/>- **Preferowany zabezpieczony (preferredSecured)**: Jeśli serwer zawiera wybór, sterownik używa protokołu SSL. <br/>- **Zabezpieczone tylko (onlySecured)**: sterownik nie jest połączony, chyba że dostępne jest połączenie SSL. | Nie |
| PlikCertUC | Pełna ścieżka do certyfikatu SSL, który jest używany przez serwer. Przykład: `CaCertFile=<cert path>;`| Tak, jeśli jest włączony protokół SSL |

**Przykład**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
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

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Netezza.

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych](concepts-datasets-linked-services.md).

Aby skopiować dane z Netezza, ustaw właściwość **Type** zestawu danych na **NetezzaTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **NetezzaTable** | Tak |
| schema | Nazwa schematu. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| tabela | Nazwa tabeli. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. Użyj `schema` i `table` dla nowego obciążenia. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przykład**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Netezza.

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza jako źródło

>[!TIP]
>Aby skutecznie ładować dane z Netezza przy użyciu partycjonowania danych, Dowiedz się więcej z sekcji [Kopiowanie równoległe z Netezza](#parallel-copy-from-netezza) .

Aby skopiować dane z Netezza, ustaw typ **źródła** w działaniu Kopiuj na **NetezzaSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **NetezzaSource**. | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Przykład: `"SELECT * FROM MyTable"` | Nie (Jeśli określono "TableName" w zestawie danych) |
| partitionOptions | Określa opcje partycjonowania danych używane do ładowania danych z Netezza. <br>Zezwalaj na wartości: **none** (wartość domyślna), **dataslice** i **DynamicRange**.<br>Gdy opcja partycji jest włączona (to nie jest `None` ), stopień równoległości do współbieżnego ładowania danych z bazy danych Netezza jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie dla działania kopiowania. | Nie |
| partitionSettings | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja partycji nie jest `None` . | Nie |
| partitionColumnName | Określ nazwę kolumny źródłowej **w typie liczb całkowitych** , która będzie używana przez partycjonowanie zakresu do kopiowania równoległego. Jeśli nie zostanie określony, klucz podstawowy tabeli zostanie automatycznie wykryty i użyty jako kolumna partycji. <br>Zastosuj, gdy opcja partycji to `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionColumnName` w klauzuli WHERE. Zobacz przykład w sekcji [Kopiowanie równoległe z Netezza](#parallel-copy-from-netezza) . | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji, w której mają zostać skopiowane dane. <br>Zastosuj, gdy opcja partycji jest `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionUpbound` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [copy Parallel from Netezza](#parallel-copy-from-netezza) . | Nie |
| partitionLowerBound | Minimalna wartość kolumny partycji, w której mają zostać skopiowane dane. <br>Zastosuj, gdy opcja partycji to `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionLowbound` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [copy Parallel from Netezza](#parallel-copy-from-netezza) . | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Równoległa kopia z Netezza

Łącznik Data Factory Netezza zapewnia wbudowane Partycjonowanie danych do kopiowania danych ze Netezza równolegle. Opcje partycjonowania danych można znaleźć w tabeli **źródłowej** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-netezza/connector-netezza-partition-options.png)

Po włączeniu kopiowania partycjonowanego Data Factory uruchamia zapytania równoległe względem źródła Netezza w celu załadowania danych przez partycje. Stopień równoległy jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie działania kopiowania. Jeśli na przykład ustawisz `parallelCopies` cztery, Data Factory współbieżnie generuje i uruchamia cztery zapytania w oparciu o określoną opcję partycji i ustawienia, a każde zapytanie pobiera część danych z bazy danych Netezza.

Przed załadowaniem dużej ilości danych z bazy danych Netezza zaleca się włączenie kopiowania równoległego przy użyciu partycjonowania danych. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartego na plikach, należy ponownie wykonać zapis do folderu jako wiele plików (Określ tylko nazwę folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne ładowanie z dużej tabeli.                                   | **Opcja partycji**: wycinek danych. <br><br/>Podczas wykonywania Data Factory automatycznie dzieli dane na podstawie [wbudowanych wycinków danych Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)i kopiuje dane przez partycje. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej.                 | **Opcja partycji**: wycinek danych.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>` .<br>Podczas wykonywania Data Factory zastępuje `?AdfPartitionCount` (z liczbą równoległą kopiowania ustawioną w działaniu kopiowania) i `?AdfDataSliceCondition` z logiką partycji wycinka danych i wysyła do Netezza. |
| Załaduj dużą ilość danych przy użyciu zapytania niestandardowego, mając kolumnę Integer z równomiernie rozproszoną wartością dla partycjonowania zakresu. | **Opcje partycji**: dynamiczna partycja zakresu.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Kolumna partycji**: określ kolumnę używaną do partycjonowania danych. Można podzielić na kolumny z typem danych Integer.<br>**Górna** granica partycji i **Dolna granica partycji**: Określ, czy chcesz filtrować względem kolumny partycji, aby pobierać dane tylko między niższym i górnym zakresem.<br><br>Podczas wykonywania Data Factory zamienia `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` , i `?AdfRangePartitionLowbound` z rzeczywistą nazwą kolumny i zakresem wartości dla każdej partycji i wysyła do Netezza. <br>Na przykład jeśli kolumna partycji "ID" ma ustawioną dolną granicę 1 i górną granicę 80, z kopią równoległą ustawioną na wartość 4, Data Factory pobiera dane przez 4 partycje. Ich identyfikatory należą do zakresu od [1, 20], [21, 40], [41, 60] i [61, 80]. |

**Przykład: zapytanie z partycją wycinka danych**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Przykład: zapytanie z dynamiczną partycją zakresu**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia w Azure Data Factory, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).
