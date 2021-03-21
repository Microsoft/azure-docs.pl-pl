---
title: Kopiowanie danych do i z usługi Azure Table Storage
description: Informacje o kopiowaniu danych z obsługiwanych magazynów źródłowych do usługi Azure Table Storage lub z magazynu tabel do obsługiwanych magazynów ujścia przy użyciu Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 5674d656385d356f596f96cd7851d2cc05839761
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597413"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure Table Storage za pomocą usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-table-connector.md)
> * [Bieżąca wersja](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z usługi Azure Table Storage. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi Azure Table Storage jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane można kopiować z dowolnego obsługiwanego źródłowego magazynu danych do usługi Table Storage. Możesz również skopiować dane z magazynu tabel do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W każdym przypadku ten łącznik tabeli platformy Azure obsługuje kopiowanie danych przy użyciu uwierzytelniania sygnatury dostępu współdzielonego z użyciem klucza konta i usługi.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla magazynu tabel.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

### <a name="use-an-account-key"></a>Użyj klucza konta

Połączoną usługę Azure Storage można utworzyć przy użyciu klucza konta. Zapewnia fabrykę danych z dostępem globalnym do magazynu. Obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **AzureTableStorage**. |Tak |
| Parametry połączenia | Określ informacje, które są konieczne do nawiązania połączenia z magazynem dla właściwości connectionString. <br/>Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć `accountKey` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używasz połączonej usługi typu "AzureStorage", jest ona nadal obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego typu połączonej usługi "AzureTableStorage" do przodu.

**Przykład:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
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

### <a name="use-shared-access-signature-authentication"></a>Korzystanie z uwierzytelniania sygnatury dostępu współdzielonego

Możesz również utworzyć połączoną usługę Storage przy użyciu sygnatury dostępu współdzielonego. Zapewnia ona fabrykę danych z ograniczonym dostępnym przez czas dostępem do wszystkich/określonych zasobów w magazynie.

Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Można jej użyć do udzielenia klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas i z określonym zestawem uprawnień. Nie musisz udostępniać kluczy dostępu do konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje parametry zapytania wszystkie informacje niezbędne do uzyskania dostępu uwierzytelnionego do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu za pomocą sygnatury dostępu współdzielonego, klient musi przekazać sygnaturę dostępu współdzielonego tylko do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatury dostępu współdzielonego: Opis modelu sygnatury dostępu współdzielonego](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Data Factory obsługuje teraz zarówno **sygnatury dostępu współdzielonego usługi** , jak i **sygnatury dostępu współdzielonego konta**. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md). 

> [!TIP]
> Aby wygenerować sygnaturę dostępu współdzielonego usługi dla konta magazynu, można wykonać następujące polecenia programu PowerShell. Zastąp symbole zastępcze i Udziel wymaganych uprawnień.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Aby można było korzystać z uwierzytelniania sygnatury dostępu współdzielonego, obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **AzureTableStorage**. |Tak |
| sasUri | Określ identyfikator URI SAS sygnatury dostępu współdzielonego do tabeli. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Token sygnatury dostępu współdzielonego można również umieścić w Azure Key Vault, aby wykorzystać funkcję autorotacji i usunąć część tokenu. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używasz połączonej usługi typu "AzureStorage", jest ona nadal obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego typu połączonej usługi "AzureTableStorage" do przodu.

**Przykład:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
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
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
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

Podczas tworzenia identyfikatora URI sygnatury dostępu współdzielonego należy wziąć pod uwagę następujące kwestie:

- Ustaw odpowiednie uprawnienia do odczytu/zapisu dla obiektów na podstawie sposobu używania połączonej usługi (odczyt, zapis, odczyt/zapis) w fabryce danych.
- Ustaw odpowiednio **czas wygaśnięcia** . Upewnij się, że dostęp do obiektów magazynu nie wygasa w aktywnym okresie potoku.
- Identyfikator URI należy utworzyć na poziomie właściwej tabeli w zależności od potrzeb.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli platformy Azure.

Aby skopiować dane do i z tabeli platformy Azure, ustaw właściwość Type zestawu danych na **Azure**. Obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość **Azure**. |Tak |
| tableName |Nazwa tabeli w wystąpieniu bazy danych magazynu tabel, do której odwołuje się połączona usługa. |Tak |

**Przykład:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schemat według Data Factory

W przypadku magazynów danych bez schematu, takich jak Azure Table, Data Factory wnioskuje schemat w jeden z następujących sposobów:

* Jeśli określisz Mapowanie kolumn w działaniu kopiowania, Data Factory pobiera dane przy użyciu listy kolumn po stronie źródłowej. W takim przypadku, jeśli wiersz nie zawiera wartości dla kolumny, zostanie dla niego podana wartość null.
* Jeśli nie określisz mapowania kolumn w działaniu kopiowania, Data Factory wnioskuje schemat przy użyciu pierwszego wiersza w danych. W tym przypadku, jeśli pierwszy wiersz nie zawiera pełnego schematu (np. niektóre kolumny mają wartość null), niektóre kolumny są pominięte w wyniku operacji kopiowania.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i obiekt ujścia tabeli platformy Azure.

### <a name="azure-table-as-a-source-type"></a>Tabela platformy Azure jako typ źródła

Aby skopiować dane z tabeli platformy Azure, ustaw typ źródła w działaniu Copy na **AzureTableSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość **AzureTableSource**. |Tak |
| azureTableSourceQuery |Użyj zapytania magazynu tabel niestandardowych do odczytu danych.<br/>Zapytanie źródłowe to bezpośrednia mapa z `$filter` opcji zapytania obsługiwanej przez usługę Azure Table Storage, Dowiedz się więcej o składni z [tego dokumentu](/rest/api/storageservices/querying-tables-and-entities#supported-query-options)i Zobacz przykłady w poniższej [sekcji przykładów azureTableSourceQuery](#azuretablesourcequery-examples). |Nie |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy zezwolić na wyjątek nieistniejącej tabeli.<br/>Dozwolone wartości to **true** i **false** (wartość domyślna). |Nie |

### <a name="azuretablesourcequery-examples"></a>Przykłady azureTableSourceQuery

>[!NOTE]
>Operacja zapytania tabeli platformy Azure zostanie przeprowadzona w ciągu 30 sekund jako [wymuszonego przez Table Service platformy Azure](/rest/api/storageservices/setting-timeouts-for-table-service-operations). Dowiedz się, jak zoptymalizować zapytanie z [projektu w celu zbadania](../storage/tables/table-storage-design-for-query.md) artykułu.

W Azure Data Factory, jeśli chcesz filtrować dane przy użyciu kolumny typu DateTime, zapoznaj się z tym przykładem:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Jeśli chcesz filtrować dane przy użyciu kolumny typu String, zapoznaj się z tym przykładem:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

W przypadku użycia parametru potoku należy rzutować wartość DateTime na odpowiedni format zgodnie z poprzednimi przykładami.

### <a name="azure-table-as-a-sink-type"></a>Tabela platformy Azure jako typ ujścia

Aby skopiować dane do tabeli platformy Azure, ustaw typ ujścia w działaniu Copy na **AzureTableSink**. W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **AzureTableSink**. |Tak |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, która może być używana przez ujścia. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, której wartości są używane jako klucze partycji. Jeśli nie zostanie określony, jako klucz partycji zostanie użyta wartość "AzureTableDefaultPartitionKeyValue". |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, której wartości kolumn są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nie |
| azureTableInsertType |Tryb wstawiania danych do tabeli platformy Azure. Ta właściwość określa, czy istniejące wiersze w tabeli wyjściowej ze zgodnymi partycjami i kluczami wierszy mają zamienione lub scalone wartości. <br/><br/>Dozwolone wartości to **merge** (wartość domyślna) i **Zamień**. <br/><br> To ustawienie jest stosowane na poziomie wiersza, a nie na poziomie tabeli. Żadna z tych opcji nie powoduje usunięcia wierszy w tabeli wyjściowej, które nie istnieją w danych wejściowych. Aby dowiedzieć się, jak działają ustawienia scalania i zamieniania, zobacz [Wstawianie lub scalanie](/rest/api/storageservices/Insert-Or-Merge-Entity) jednostki oraz [Wstawianie lub zamienianie jednostki](/rest/api/storageservices/Insert-Or-Replace-Entity). |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy trafi writeBatchSize lub writeBatchTimeout.<br/>Dozwolone wartości to liczba całkowita (liczba wierszy). |Nie (domyślnie 10 000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, gdy trafi writeBatchSize lub writeBatchTimeout.<br/>Dozwolone wartości to TimeSpan. Przykładem jest "00:20:00" (20 minut). |Nie (wartość domyślna to 90 sekund, domyślny limit czasu klienta magazynu) |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Mapowanie kolumny źródłowej do kolumny docelowej przy użyciu właściwości **"Translator"** , aby można było użyć kolumny docelowej jako azureTablePartitionKeyName.

W poniższym przykładzie kolumna źródłowa DivisionID jest mapowana na kolumnę docelową DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

Jako klucz partycji określono wartość "DivisionID".

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapowanie typu danych dla tabeli platformy Azure

Podczas kopiowania danych z i do tabeli platformy Azure następujące mapowania są używane w ramach typów danych tabeli platformy Azure do Data Factory danych pośrednich. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

Po przeniesieniu danych do i z tabeli platformy Azure następujące [mapowania zdefiniowane przez usługę Azure](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) Table są używane w ramach typów OData w tabeli platformy Azure do typu .NET i na odwrót.

| Typ danych tabeli platformy Azure | Data Factory typ danych pośrednich | Szczegóły |
|:--- |:--- |:--- |
| EDM. Binary |Byte [] |Tablica bajtów do 64 KB. |
| Edm.Boolean |bool |Wartość logiczna. |
| EDM. DateTime |DateTime |Wartość 64-bitowa wyrażona jako uniwersalny czas koordynowany (UTC). Obsługiwany zakres DateTime rozpoczyna się od północy, 1 stycznia, 1601 N.E. (0001), UTC. Zakres zostaje zakończony 31 grudnia 9999. |
| Edm.Double |double |64-bitowa wartość zmiennoprzecinkowa. |
| EDM. GUID |Guid (identyfikator GUID) |128-bitowy unikatowy identyfikator globalny. |
| Edm.Int32 |Int32 |32-bitowa liczba całkowita. |
| Edm.Int64 |Int64 |64-bitowa liczba całkowita. |
| Edm.String |Ciąg |Wartość zakodowana w formacie UTF-16. Wartości ciągu mogą należeć do 64 KB. |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).