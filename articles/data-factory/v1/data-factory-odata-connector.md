---
title: Przenoszenie danych ze źródeł OData
description: Dowiedz się więcej na temat przenoszenia danych ze źródeł OData przy użyciu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fae78459a752d78fe47f189bca67667e917ba561
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380070"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Przenoszenie danych ze źródła strumieniowego OData przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-odata-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-odata.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik OData w wersji 2](../connector-odata.md).


W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane ze źródła strumieniowego OData. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Możesz skopiować dane ze źródła danych OData do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Usługa Data Factory obsługuje obecnie tylko przeniesienie danych ze źródła OData do innych magazynów danych, ale nie do przeniesienia danych z innych magazynów danych do źródła strumieniowego OData.

## <a name="supported-versions-and-authentication-types"></a>Obsługiwane wersje i typy uwierzytelniania
Ten łącznik OData obsługuje protokół OData w wersji 3,0 i 4,0, a dane można kopiować zarówno z usługi OData w chmurze, jak i lokalnych źródeł danych OData. Dla tych ostatnich należy zainstalować bramę Zarządzanie danymi. Szczegółowe informacje na temat bramy Zarządzanie danymi można znaleźć w artykule [przenoszenie danych między środowiskiem lokalnym i chmurą](data-factory-move-data-between-onprem-and-cloud.md) .

Obsługiwane są następujące typy uwierzytelniania:

* Aby uzyskać dostęp do źródła danych OData w **chmurze** , można użyć anonimowego, podstawowego (nazwy użytkownika i hasła) lub Azure Active Directory uwierzytelniania OAuth.
* Aby uzyskać dostęp do **lokalnego** źródła danych OData, można użyć anonimowych, podstawowych (nazwa użytkownika i hasło) lub uwierzytelniania systemu Windows.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które przenosi dane ze źródła OData przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Aby zapoznać się z przykładową definicją JSON dla Data Factory jednostek, które są używane do kopiowania danych ze źródła OData, zobacz [przykład JSON: Kopiuj dane ze źródła danych OData do usługi Azure BLOB w](#json-example-copy-data-from-odata-source-to-azure-blob) tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla źródła danych OData:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi OData.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi być ustawiona na wartość: **OData** |Tak |
| url |Adres URL usługi OData. |Tak |
| authenticationType |Typ uwierzytelniania używany do nawiązywania połączenia ze źródłem danych OData. <br/><br/> W przypadku protokołu OData w chmurze możliwe wartości to Anonymous, Basic i OAuth (Uwaga Azure Data Factory obecnie obsługuje tylko Azure Active Directory uwierzytelniania OAuth). <br/><br/> W przypadku lokalnego protokołu OData możliwe wartości to anonimowe, podstawowe i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika w przypadku korzystania z uwierzytelniania podstawowego. |Tak (tylko w przypadku korzystania z uwierzytelniania podstawowego) |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Tak (tylko w przypadku korzystania z uwierzytelniania podstawowego) |
| authorizedCredential |Jeśli używasz uwierzytelniania OAuth, kliknij przycisk **Autoryzuj** w kreatorze kopiowania Data Factory lub edytorze i wprowadź poświadczenia, a następnie wartość tej właściwości zostanie wygenerowana automatycznie. |Tak (tylko w przypadku korzystania z uwierzytelniania OAuth) |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną usługą OData. Określ tylko w przypadku kopiowania danych z lokalnego źródła OData. |Nie |

### <a name="using-basic-authentication"></a>Korzystanie z uwierzytelniania podstawowego
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Korzystanie z uwierzytelniania anonimowego
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Korzystanie z uwierzytelniania systemu Windows do lokalnego źródła danych OData
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Używanie uwierzytelniania OAuth do uzyskiwania dostępu do źródła danych OData w chmurze
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja typeProperties zestawu danych typu **ODataResource** (który zawiera zestaw danych OData) ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| path |Ścieżka do zasobu OData |Nie |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji typeProperties działania z drugiej strony różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

Jeśli źródło jest typu **RelationalSource** (w tym OData), w sekcji typeProperties są dostępne następujące właściwości:

| Właściwość | Opis | Przykład | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |"? $select = nazwa, opis&$top = 5" |Nie |

## <a name="type-mapping-for-odata"></a>Mapowanie typu dla usługi OData
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych na typy obiektów ujścia z poniższym podejściem dwuetapowym.

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przemieszczania danych z usługi OData następujące mapowania są używane z typów OData do typu .NET.

| Typ danych protokołu OData | Typ .NET |
| --- | --- |
| EDM. Binary |Byte [] |
| Edm.Boolean |Wartość logiczna |
| EDM. Byte |Byte [] |
| EDM. DateTime |DateTime |
| EDM. Decimal |Liczba dziesiętna |
| Edm.Double |Double |
| EDM. Single |Pojedynczy |
| EDM. GUID |Guid (identyfikator GUID) |
| EDM. Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| EDM. |Int16 |
| Edm.String |Ciąg |
| EDM. Time |przedział_czasu |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> Złożone typy danych OData, np. Object, nie są obsługiwane.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Przykład JSON: kopiowanie danych ze źródła OData do obiektu blob platformy Azure
Ten przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one, jak skopiować dane ze źródła danych OData do Blob Storage platformy Azure. Dane można jednak kopiować do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory. Przykład zawiera następujące jednostki Data Factory:

1. Połączona usługa typu [OData](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [ODataResource](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [RelationalSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z zapytania względem źródła danych OData do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa OData:** W tym przykładzie jest stosowane uwierzytelnianie anonimowe. Zobacz sekcję [połączonej usługi OData](#linked-service-properties) dla różnych typów uwierzytelniania, których można użyć.

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Połączona usługa Azure Storage:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Wejściowy zestaw danych OData:**

Ustawienie "External": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Określanie **ścieżki** w definicji zestawu danych jest opcjonalne.

**Wyjściowy zestaw danych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Działanie kopiowania w potoku ze źródłem danych OData i obiektem BLOB:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **RelationalSource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **zapytania** wybiera najnowsze (najnowsze) dane ze źródła OData.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

Określanie **zapytania** w definicji potoku jest opcjonalne. **Adres URL** , którego usługa Data Factory używa do pobierania danych: adres URL określony w połączonej usłudze (wymagane) + ścieżka określona w zestawie danych (opcjonalnie) + zapytanie w potoku (opcjonalne).

### <a name="type-mapping-for-odata"></a>Mapowanie typu dla usługi OData
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych na typy obiektów ujścia o następujących dwuetapowych podejściach:

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przesuwania danych z magazynów danych OData typy danych OData są mapowane na typy .NET.

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej na temat mapowania kolumn w źródłowym zestawie danych na kolumny w datadataset, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzanie odczytu ze źródeł relacyjnych
Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek był uruchamiany ponownie w przypadku wystąpienia błędu. Gdy wycinek jest uruchamiany ponownie w dowolny sposób, należy się upewnić, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek. Zobacz [powtarzanie odczytu ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.
