---
title: Kopiowanie danych do i z magazynu tabel platformy Azure
description: Dowiedz się, jak kopiować dane z obsługiwanych magazynów źródłowych do magazynu tabel platformy Azure lub z magazynu tabel do obsługiwanych magazynów ujścia przy użyciu usługi Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: 6edd32f8f3579238d1f08f55ce9fb1528fa5d211
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417492"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopiowanie danych do i z magazynu tabel platformy Azure przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-table-connector.md)
> * [Bieżąca wersja](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z magazynu tabel platformy Azure. Opiera się na [omówienie działania kopiowania,](copy-activity-overview.md) który przedstawia ogólne omówienie działania kopiowania.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Azure Table Storage jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do magazynu tabel. Można również skopiować dane z magazynu tabel do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła lub pochłaniacze przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik tabeli platformy Azure obsługuje kopiowanie danych przy użyciu uwierzytelniania podpisu współdzielonego dostępu do konta i usługi.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla magazynu tabel.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

### <a name="use-an-account-key"></a>Używanie klucza konta

Usługę połączony usługi Azure Storage można utworzyć przy użyciu klucza konta. Zapewnia fabryce danych globalny dostęp do magazynu. Obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureTableStorage**. |Tak |
| Parametry połączenia | Określ informacje potrzebne do nawiązania połączenia z magazynem dla właściwości connectionString. <br/>Można również umieścić klucz konta w usłudze Azure Key Vault i wyciągnąć konfigurację `accountKey` z ciągu połączenia. Więcej informacji można znaleźć w poniższych przykładach i [poświadczeniach sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

>[!NOTE]
>Jeśli używasz usługi połączonej typu "AzureStorage", nadal jest obsługiwana w stanie takim, w jakim jest, podczas gdy zaleca się użycie tego nowego typu usługi połączonej "AzureTableStorage" w przyszłości.

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

**Przykład: klucz konta magazynu w usłudze Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
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

### <a name="use-shared-access-signature-authentication"></a>Korzystanie z uwierzytelniania podpisu dostępu współdzielonego

Można również utworzyć usługę połączoną magazynu przy użyciu podpisu dostępu udostępnionego. Zapewnia fabryce danych ograniczony/czasowy dostęp do wszystkich/określonych zasobów w magazynie.

Podpis dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Można go użyć do przyznania klientowi ograniczone uprawnienia do obiektów na koncie magazynu przez określony czas i z określonym zestawem uprawnień. Nie musisz udostępniać kluczy dostępu do konta. Sygnatura dostępu współdzielonego jest identyfikatorem URI, który obejmuje w swoich parametrach zapytania wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu z podpisem dostępu współdzielonego, klient musi przekazać tylko w podpisie dostępu współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat podpisów dostępu współdzielonego, zobacz [Podpisy dostępu udostępnionego: Zrozumienie modelu podpisu dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Usługa Data Factory obsługuje teraz zarówno **podpisy dostępu współdzielonego usługi,** jak i **podpisy dostępu współdzielonego na koncie.** Aby uzyskać więcej informacji na temat podpisów dostępu współdzielonego, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../storage/common/storage-sas-overview.md) 

> [!TIP]
> Aby wygenerować podpis dostępu współdzielonego usługi dla konta magazynu, można wykonać następujące polecenia programu PowerShell. Wymień symbole zastępcze i przyznaj wymagane uprawnienia.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Aby użyć uwierzytelniania podpisu dostępu współdzielonego, obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureTableStorage**. |Tak |
| sasuri ( sasuri ) | Określ identyfikator URI sygnatury dostępu współdzielonego identyfikatora URI podpisu dostępu udostępnionego do tabeli. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych. Token Sygnatury dostępu Współdzielonego można również umieścić w usłudze Azure Key Vault, aby wykorzystać automatyczną rotację i usunąć część tokenu. Więcej informacji można znaleźć w poniższych przykładach i [poświadczeniach sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

>[!NOTE]
>Jeśli używasz usługi połączonej typu "AzureStorage", nadal jest obsługiwana w stanie takim, w jakim jest, podczas gdy zaleca się użycie tego nowego typu usługi połączonej "AzureTableStorage" w przyszłości.

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

**Przykład: klucz konta magazynu w usłudze Azure Key Vault**

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

Podczas tworzenia identyfikatora URI podpisu dostępu udostępnionego należy wziąć pod uwagę następujące kwestie:

- Ustaw odpowiednie uprawnienia do odczytu/zapisu dla obiektów na podstawie sposobu, w jaki usługa połączona (odczyt, zapis, odczyt/zapis) jest używana w fabryce danych.
- Ustaw czas **wygaśnięcia** odpowiednio. Upewnij się, że dostęp do obiektów magazynu nie wygasa w aktywnym okresie potoku.
- Identyfikator URI powinny być tworzone na poziomie tabeli prawo na podstawie potrzeby.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli platformy Azure.

Aby skopiować dane do i z tabeli platformy Azure, ustaw właściwość typu zestawu danych na **AzureTable**. Obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na **AzureTable**. |Tak |
| tableName |Nazwa tabeli w wystąpieniu bazy danych magazynu tabel, do których odwołuje się usługa połączona. |Tak |

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

### <a name="schema-by-data-factory"></a>Schemat według fabryki danych

W przypadku magazynów danych wolnych od schematu, takich jak tabela Azure, usługa Data Factory wywnioskować schemat w jeden z następujących sposobów:

* Jeśli określisz mapowanie kolumn w działaniu kopiowania, usługa Data Factory użyj listy kolumn po stronie źródłowej do pobierania danych. W takim przypadku jeśli wiersz nie zawiera wartości dla kolumny, wartość null jest dla niego podana.
* Jeśli nie określisz mapowania kolumn w działaniu kopiowania, usługa Data Factory wyjmuje schemat przy użyciu pierwszego wiersza w danych. W takim przypadku jeśli pierwszy wiersz nie zawiera pełnego schematu (np. niektóre kolumny mają wartość null), niektóre kolumny są pomijane w wyniku operacji kopiowania.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło tabeli platformy Azure i ujście.

### <a name="azure-table-as-a-source-type"></a>Tabela platformy Azure jako typ źródłowy

Aby skopiować dane z tabeli platformy Azure, ustaw typ źródła w działaniu kopiowania na **AzureTableSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na **AzureTableSource**. |Tak |
| azureTableSourceQuery |Użyj niestandardowej kwerendy magazynu tabeli do odczytu danych. Zobacz przykłady w poniższej sekcji. |Nie |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy nie istnieje wyjątek tabeli.<br/>Dozwolone wartości to **Prawda** i **Fałsz** (domyślnie). |Nie |

### <a name="azuretablesourcequery-examples"></a>przykłady azureTableSourceQuery

>[!NOTE]
>Upłynie czas realizacji operacji kwerendy tabeli platformy Azure w ciągu 30 [sekund, zgodnie z wymuszanym przez usługę Azure Table service](https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations). Dowiedz się, jak zoptymalizować kwerendę z [projektu do wykonywania zapytań](../storage/tables/table-storage-design-for-query.md) w artykule.

W usłudze Azure Data Factory, jeśli chcesz filtrować dane względem kolumny typu datetime, zapoznaj się z tym przykładem:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Jeśli chcesz filtrować dane względem kolumny typu ciągu, zapoznaj się z tym przykładem:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Jeśli używasz parametru potoku, należy rzutować wartość datetime do prawidłowego formatu zgodnie z poprzednimi próbkami.

### <a name="azure-table-as-a-sink-type"></a>Tabela platformy Azure jako typ ujścia

Aby skopiować dane do tabeli platformy Azure, ustaw typ ujścia w działaniu kopiowania na **AzureTableSink**. Następujące właściwości są obsługiwane w sekcji ujście działania **kopiowania.**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na **AzureTableSink**. |Tak |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, która może być używana przez obiekt sink. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, której wartości są używane jako klucze partycji. Jeśli nie określono, "AzureTableDefaultPartitionKeyValue" jest używany jako klucz partycji. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, której wartości kolumn są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nie |
| azureTableInsertType |Tryb wstawiania danych do tabeli platformy Azure. Ta właściwość określa, czy istniejące wiersze w tabeli danych wyjściowych z pasującymi kluczami partycji i wierszy mają swoje wartości zastąpione lub scalone. <br/><br/>Dozwolone wartości są **scalane** (domyślnie) i **zastępują**. <br/><br> To ustawienie ma zastosowanie na poziomie wiersza, a nie na poziomie tabeli. Żadna z tych opcji nie usuwa wierszy w tabeli danych wyjściowych, które nie istnieją w danych wejściowych. Aby dowiedzieć się, jak działają ustawienia scalania i zamieniania, zobacz [Wstawianie lub scalanie encji](https://msdn.microsoft.com/library/azure/hh452241.aspx) oraz [Wstawianie lub zamienianie encji](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy wpisanieBatchSize lub writeBatchTimeout zostanie trafiony.<br/>Dozwolone wartości to liczba całkowita (liczba wierszy). |Nie (wartość domyślna to 10 000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, gdy wpisanieBatchSize lub writeBatchTimeout zostanie trafiony.<br/>Dozwolone wartości są timespan. Przykładem jest "00:20:00" (20 minut). |Nie (wartość domyślna to 90 sekund, domyślny limit czasu klienta magazynu) |

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

Zamapuj kolumnę źródłową do kolumny docelowej przy użyciu właściwości **"translator",** zanim będzie można użyć kolumny docelowej jako azureTablePartitionKeyName.

W poniższym przykładzie kolumna źródłowska DivisionID jest mapowana na kolumnę docelową DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" jest określony jako klucz partycji.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapowanie typów danych dla tabeli platformy Azure

Podczas kopiowania danych z i do tabeli platformy Azure następujące mapowania są używane z typów danych tabeli azure do danych tymczasowe typy danych fabryki danych. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

Podczas przenoszenia danych do i z tabeli platformy Azure następujące [mapowania zdefiniowane przez tabelę platformy Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) są używane z typów OData tabeli platformy Azure do typu .NET i odwrotnie.

| Typ danych tabeli platformy Azure | Tymczasowy typ danych data factory | Szczegóły |
|:--- |:--- |:--- |
| Edm.Binary (Edm.Binary) |bajt[] |Tablica bajtów o masie do 64 KB. |
| Edm.Boolean |bool |Wartość logiczna. |
| Edm.DateTime |DateTime |Wartość 64-bitowa wyrażona jako skoordynowany czas uniwersalny (UTC). Obsługiwany zakres DateTime rozpoczyna się o północy, 1 stycznia 1601 r. r. (C.E.), UTC. Zakres kończy się 31 grudnia 9999. |
| Edm.Double |double |64-bitowa wartość zmiennoprzecinkowa. |
| Edm.Guid (Edm.Guid) |Guid (identyfikator GUID) |128-bitowy unikatowy identyfikator globalny. |
| Edm.Int32 |Int32 |32-bitowa ć całkowita. |
| Edm.Int64 |Int64 |64-bitowa ć całkowita. |
| Edm.String |Ciąg |Wartość zakodowana w stanie UTF-16. Wartości ciągu mogą mieć maksymalnie 64 KB. |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w fabryce danych, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
