---
title: Kopiowanie danych z programu Netezza przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak skopiować dane z programu Netezza do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 89efa8dc9989f693964415741299042c63f93780
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418120"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopiowanie danych z programu Netezza przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z programu Netezza. Artykuł opiera się na [copy activity w usłudze Azure Data Factory](copy-activity-overview.md), który przedstawia ogólne omówienie działania kopiowania.

>[!TIP]
>Aby uzyskać scenariusz migracji danych z netezzy na platformę Azure, dowiedz się więcej od [Użyj usługi Azure Data Factory do migracji danych z lokalnego serwera Netezza na platformę Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Netezza jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)


Można skopiować dane z Netezza do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i pochłaniacze, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).

Złącze Netezza obsługuje kopiowanie równoległe ze źródła. Zobacz [kopię równoległą z Netezza](#parallel-copy-from-netezza) sekcji, aby uzyskać szczegółowe informacje.

Usługa Azure Data Factory udostępnia wbudowany sterownik umożliwiający łączność. Aby użyć tego łącznika, nie trzeba ręcznie instalować żadnego sterownika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

Można utworzyć potok, który używa działania kopiowania przy użyciu .NET SDK, Zestaw SDK języka Python, Azure PowerShell, interfejs API REST lub szablonu usługi Azure Resource Manager. Zobacz [samouczek Działania kopiowania,](quickstart-create-data-factory-dot-net.md) aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku, który ma działanie kopiowania.

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, których można użyć do zdefiniowania jednostek fabryki danych, które są specyficzne dla łącznika Netezza.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej Netezza:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** musi być ustawiona na **Netezza**. | Tak |
| Parametry połączenia | Ciąg połączenia ODBC do połączenia z Netezzą. <br/>Można również umieścić hasło w usłudze `pwd` Azure Key Vault i wyciągnąć konfigurację z ciągu połączenia. Więcej informacji można znaleźć w poniższych przykładach i [poświadczeniach sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | [Środowisko wykonawcze integracji](concepts-integration-runtime.md) do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

Typowym ciągiem `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`połączenia jest . W poniższej tabeli opisano więcej właściwości, które można ustawić:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| Poziom zabezpieczeń | Poziom zabezpieczeń (SSL/TLS), który jest używany przez sterownik do połączenia z magazynem danych. Przykład: `SecurityLevel=preferredSecured`. Obsługiwane wartości to:<br/>- **Tylko niezabezpieczone** **(tylkoNiezabezpieczone):** Sterownik nie używa protokołu TLS.<br/>- **Preferowane niezabezpieczone (preferowaneNiezabezpieczone) (domyślnie)**: Jeśli serwer zapewnia wybór, sterownik nie używa protokołu TLS. <br/>- **Preferowane zabezpieczone (preferowanezabezpieczone)**: Jeśli serwer zapewnia wybór, sterownik używa protokołu TLS. <br/>- **Tylko zabezpieczone (tylkoZabezpieczone)**: Sterownik nie łączy się, chyba że dostępne jest połączenie TLS. | Nie |
| CaCertFile (Plik CaCert) | Pełna ścieżka do certyfikatu TLS/SSL używanego przez serwer. Przykład: `CaCertFile=<cert path>;`| Tak, jeśli tls jest włączony |

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

**Przykład: hasło magazynu w usłudze Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
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

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych Netezza.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Zestawy danych](concepts-datasets-linked-services.md).

Aby skopiować dane z netezzy, ustaw właściwość **typu** zestawu danych na **NetezzaTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **NetezzaTable** | Tak |
| Schematu | Nazwa schematu. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tabela | Nazwa tabeli. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. Użyj `schema` `table` i dla nowego obciążenia. | Nie (jeśli określono "zapytanie" w źródle działania) |

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

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Ta sekcja zawiera listę właściwości, które obsługuje źródło Netezza.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza jako źródło

>[!TIP]
>Aby skutecznie załadować dane z Netezzy przy użyciu partycjonowania danych, dowiedz się więcej z [kopiowania równoległego z sekcji Netezza.](#parallel-copy-from-netezza)

Aby skopiować dane z netezzy, ustaw typ **źródła** w copy activity na **NetezzaSource**. Następujące właściwości są obsługiwane w sekcji **Źródło** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **NetezzaSource**. | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Przykład: `"SELECT * FROM MyTable"` | Nie (jeśli określono "nazwa tabela" w zestawie danych) |
| partitionOptions (opcje partycji) | Określa opcje partycjonowania danych używane do ładowania danych z programu Netezza. <br>Wartości zezwalania to: **Brak** (domyślnie), **DataSlice**i **DynamicRange**.<br>Gdy opcja partycji jest włączona `None`(czyli nie), stopień równoległości równoczesnych ładowania danych z bazy [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) danych Netezza jest kontrolowany przez ustawienie działania kopiowania. | Nie |
| podziałY | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja `None`partycji nie jest . | Nie |
| partitionColumnName | Określ nazwę kolumny źródłowej **w typie liczby całkowitej,** która będzie używana przez partycjonowanie zakresu dla kopiowania równoległego. Jeśli nie zostanie określony, klucz podstawowy tabeli jest automatycznie odkrytą i używana jako kolumna partycji. <br>Zastosuj, gdy opcja `DynamicRange`partycji jest . Jeśli używasz kwerendy do pobierania danych `?AdfRangePartitionColumnName` źródłowych, należy podłączyć w klauzuli WHERE. Zobacz przykład [w kopiowanie równoległe z sekcji Netezza.](#parallel-copy-from-netezza) | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji do skopiowania danych. <br>Zastosuj, gdy `DynamicRange`opcja partycji jest . Jeśli używasz kwerendy do pobierania `?AdfRangePartitionUpbound` danych źródłowych, należy podłączyć w klauzuli WHERE. Na przykład zobacz [kopiowanie równoległe z Netezza](#parallel-copy-from-netezza) sekcji. | Nie |
| partycjaLowerBound | Minimalna wartość kolumny partycji do skopiowania danych. <br>Zastosuj, gdy opcja `DynamicRange`partycji jest . Jeśli używasz kwerendy do pobierania danych `?AdfRangePartitionLowbound` źródłowych, należy podłączyć w klauzuli WHERE. Na przykład zobacz [kopiowanie równoległe z Netezza](#parallel-copy-from-netezza) sekcji. | Nie |

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

## <a name="parallel-copy-from-netezza"></a>Kopia równoległa z Netezza

Łącznik Netezza fabryki danych zapewnia wbudowane partycjonowanie danych w celu kopiowania danych z Netezza równolegle. Opcje partycjonowania danych można znaleźć w tabeli **Źródło** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-netezza/connector-netezza-partition-options.png)

Po włączeniu kopii podzielonej na partycje usługa Data Factory uruchamia równoległe kwerendy względem źródła Netezza w celu załadowania danych przez partycje. Stopień równoległy jest [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kontrolowany przez ustawienie działania kopiowania. Na przykład jeśli `parallelCopies` ustawisz cztery, data factory jednocześnie generuje i uruchamia cztery kwerendy na podstawie określonej opcji partycji i ustawień, a każda kwerenda pobiera część danych z bazy danych Netezza.

Zaleca się włączenie kopiowania równoległego z partycjonowania danych, zwłaszcza podczas ładowania dużej ilości danych z bazy danych Netezza. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartych na plikach zaleca się zapisywanie w folderze jako wielu plików (określanie tylko nazwy folderu), w którym to przypadku wydajność jest lepsza niż zapisywanie do jednego pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne obciążenie z dużego stołu.                                   | **Opcja partycji**: Plasterek danych. <br><br/>Podczas wykonywania usługa Data Factory automatycznie dzieli dane na podstawie [wbudowanych wycinków danych Netezzy](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)i kopiuje dane według partycji. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej.                 | **Opcja partycji**: Plasterek danych.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Podczas wykonywania usługa Data `?AdfPartitionCount` Factory zastępuje (z numerem `?AdfDataSliceCondition` kopii równoległej ustawioną na działania kopiowania) i logiką partycji plasterka danych i wysyła do Netezzy. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej, mając kolumnę całkowitą o równomiernie rozłożonej wartości do partycjonowania zakresu. | **Opcje partycji**: Partycja zakresu dynamicznego.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Kolumna partycji**: Określ kolumnę używaną do partycjonowania danych. Można podzielić na kolumnę z typem danych liczby całkowitej.<br>**Górna granica partycji** i **dolna granica partycji**: Określ, czy chcesz filtrować względem kolumny partycji, aby pobierać dane tylko między dolnym i górnym zakresem.<br><br>Podczas wykonywania usługa Data `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` zastępuje , a rzeczywista nazwa kolumny i zakresy wartości dla każdej partycji i wysyła do Netezza. <br>Na przykład jeśli kolumna partycji "ID" ustawiona z dolną granicą jako 1 i górną granicą jako 80, z kopią równoległą ustawioną jako 4, usługa Data Factory pobiera dane przez 4 partycje. Ich identyfikatory znajdują się odpowiednio między [1,20], [21, 40], [41, 60] i [61, 80]. |

**Przykład: kwerenda z partycją plasterka danych**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Przykład: kwerenda z partycją zakresu dynamicznego**

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

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działanie kopiowania obsługuje jako źródła i pochłaniacze w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).
