---
title: Przenoszenie danych ze źródła HTTP — Azure
description: Dowiedz się, jak przenieść dane z lokalnego lub w chmurze źródła HTTP przy użyciu Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/22/2018
robots: noindex
ms.openlocfilehash: ce29b5a112d70575a721b0b527947fd95868da80
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382926"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Przenoszenie danych ze źródła HTTP przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-http-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-http.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Azure Data Factory, zobacz [łącznik protokołu HTTP w wersji 2](../connector-http.md).

W tym artykule opisano sposób używania działania kopiowania w programie Azure Data Factory do przenoszenia danych z lokalnego lub punktu końcowego HTTP w chmurze do obsługiwanego magazynu danych ujścia. Ten artykuł dotyczy [przenoszenia danych za pomocą działania kopiowania](data-factory-data-movement-activities.md), które przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania. Ten artykuł zawiera również listę magazynów danych, których działanie kopiowania obsługuje jako źródła i ujścia.

Data Factory obecnie obsługuje tylko przeniesienie danych ze źródła HTTP do innych magazynów danych. Nie obsługuje on przemieszczania danych z innych magazynów danych do miejsca docelowego HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Obsługiwane scenariusze i typy uwierzytelniania

Za pomocą tego łącznika protokołu HTTP można pobrać dane z *chmury i lokalnego punktu końcowego HTTP/S* przy użyciu metod **Get** lub **post** protokołu HTTP. Obsługiwane są następujące typy uwierzytelniania: **anonimowe**, **podstawowe**, **szyfrowane**, **Windows** i **ClientCertificate**. Zwróć uwagę na różnicę między tym łącznikiem i [łącznikiem tabeli sieci Web](data-factory-web-table-connector.md). Łącznik tabeli sieci Web wyodrębnia zawartość tabeli z strony HTML.

Podczas kopiowania danych z lokalnego punktu końcowego HTTP należy zainstalować bramę Zarządzanie danymi w środowisku lokalnym lub na maszynie wirtualnej platformy Azure. Aby dowiedzieć się więcej na temat bramy Zarządzanie danymi i uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy, zobacz temat [przeniesienie danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Rozpoczęcie pracy

Można utworzyć potok z działaniem kopiowania, aby przenieść dane ze źródła HTTP przy użyciu różnych narzędzi lub interfejsów API:

- Najprostszym sposobem tworzenia potoku jest użycie Kreatora Kopiowanie danych. Aby zapoznać się z krótkim przewodnikiem tworzenia potoku przy użyciu kreatora Kopiowanie danych, zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

- Możesz również użyć następujących narzędzi do tworzenia potoku: programu **Visual Studio**, **Azure PowerShell**, **szablonu Azure Resource Manager**, **interfejsu API platformy .NET** lub **interfejsu API REST**. Instrukcje krok po kroku dotyczące tworzenia potoku z działaniem kopiowania można znaleźć w [samouczku działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Przykłady JSON kopiujące dane ze źródła HTTP do magazynu obiektów blob platformy Azure można znaleźć w [przykładach JSON](#json-examples).

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

W poniższej tabeli opisano elementy JSON, które są specyficzne dla połączonej usługi HTTP:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ | Właściwość **Type** musi być ustawiona na wartość **http**. | Tak |
| url | Podstawowy adres URL serwera sieci Web. | Tak |
| authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to **anonimowe**, **podstawowe**, **szyfrowane**, **Windows** i **ClientCertificate**. <br><br> Zapoznaj się z nowszymi sekcjami w tym artykule, aby uzyskać więcej właściwości i próbek JSON dla tych typów uwierzytelniania. | Tak |
| enableServerCertificateValidation | Określa, czy włączyć weryfikację certyfikatu protokołu TLS/SSL serwera, jeśli źródłem jest serwer sieci Web HTTPS. Gdy serwer HTTPS używa certyfikatu z podpisem własnym, ustaw tę wartość na **false**. | Nie<br /> (wartość domyślna to **true**) |
| gatewayName | Nazwa wystąpienia bramy Zarządzanie danymi, która ma być używana do nawiązywania połączenia z lokalnym źródłem HTTP. | Tak, w przypadku kopiowania danych z lokalnego źródła HTTP |
| encryptedCredential | Zaszyfrowane poświadczenia do uzyskiwania dostępu do punktu końcowego HTTP. Wartość jest generowana automatycznie podczas konfigurowania informacji o uwierzytelnianiu w Kreatorze kopiowania lub przy użyciu okna dialogowego **ClickOnce** . | Nie<br /> (stosuje się tylko w przypadku kopiowania danych z lokalnego serwera HTTP) |

Aby uzyskać szczegółowe informacje na temat ustawiania poświadczeń dla lokalnego źródła danych łącznika HTTP, zobacz [przenoszenie danych między źródłami lokalnymi i chmurą przy użyciu bramy zarządzanie danymi](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Korzystanie z uwierzytelniania podstawowego, szyfrowanego lub systemu Windows

Ustaw wartość **AuthenticationType** na **Basic**, **Digest** lub **Windows**. Poza ogólnymi właściwościami łącznika HTTP opisanymi w poprzednich sekcjach ustaw następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| userName | Nazwa użytkownika, która ma być używana do uzyskiwania dostępu do punktu końcowego HTTP. | Tak |
| hasło | Hasło użytkownika (**username**). | Tak |

**Przykład: używanie uwierzytelniania podstawowego, szyfrowanego lub systemu Windows**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Korzystanie z uwierzytelniania ClientCertificate

Aby użyć uwierzytelniania podstawowego, ustaw wartość **AuthenticationType** na **ClientCertificate**. Poza ogólnymi właściwościami łącznika HTTP opisanymi w poprzednich sekcjach ustaw następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| embeddedCertData | Zakodowana w formacie base64 zawartość danych binarnych pliku PFX. | Określ wartość **embeddedCertData** lub **certThumbprint** |
| certThumbprint | Odcisk palca certyfikatu, który został zainstalowany w magazynie certyfikatów maszyny bramy. Stosuje się tylko w przypadku kopiowania danych z lokalnego źródła HTTP. | Określ wartość **embeddedCertData** lub **certThumbprint** |
| hasło | Hasło skojarzone z certyfikatem. | Nie |

Jeśli używasz **certThumbprint** do uwierzytelniania, a certyfikat jest instalowany w magazynie osobistym komputera lokalnego, Udziel uprawnień do odczytu do usługi bramy:

1. Otwórz program Microsoft Management Console (MMC). Dodaj przystawkę **Certyfikaty** , która jest przeznaczona dla **komputera lokalnego**.
2. Rozwiń węzeł **Certyfikaty**  >  **osobiste**, a następnie wybierz pozycję **Certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat ze sklepu osobistego, a następnie wybierz pozycję **wszystkie zadania**  > **Zarządzaj kluczami prywatnymi**.
3. Na karcie **zabezpieczenia** Dodaj konto użytkownika, pod którym działa usługa hosta bramy zarządzanie danymi, z dostępem do odczytu do certyfikatu.  

**Przykład: używanie certyfikatu klienta**

Ta połączona usługa łączy fabrykę danych z lokalnym serwerem sieci Web HTTP. Używa certyfikatu klienta zainstalowanego na komputerze, na którym zainstalowano bramę Zarządzanie danymi.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

**Przykład: używanie certyfikatu klienta w pliku**

Ta połączona usługa łączy fabrykę danych z lokalnym serwerem sieci Web HTTP. Używa on pliku certyfikatu klienta na komputerze, na którym zainstalowano bramę Zarządzanie danymi.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Niektóre sekcje pliku JSON zestawu danych, takie jak struktura, dostępność i zasady, są podobne dla wszystkich typów zestawu danych (Azure SQL Database, Azure Blob Storage, Azure Table Storage).

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych. Sekcja **typeProperties** zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** zestawu danych typu **http** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | **Typ** zestawu danych musi być ustawiony na wartość **http**. | Tak |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ścieżka nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. <br><br> Aby utworzyć dynamiczny adres URL, można użyć [funkcji Data Factory i zmiennych systemowych](data-factory-functions-variables.md). Przykład: **relativeUrl**: **$ $Text. Format ("/My/Report? miesiąc = {0: rrrr}-{0: mm} &FMT = CSV", parametru slicestart)**. | Nie |
| requestMethod | Metoda HTTP. Dozwolone wartości to **Get** i **post**. | Nie <br />(wartość domyślna to **Get**) |
| additionalHeaders | Dodatkowe nagłówki żądań HTTP. | Nie |
| Elemencie requestbody | Treść żądania HTTP. | Nie |
| format | Jeśli chcesz *pobrać dane z punktu końcowego http, tak jakby się* nie przeanalizować, Pomiń ustawienie **formatu** . <br><br> Jeśli chcesz przeanalizować zawartość odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat** i **ParquetFormat**. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

**Przykład: użycie metody GET (default)**

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
          "relativeUrl": "XXX/test.xml",
          "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Przykład: użycie metody POST**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
       "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, a zasady są dostępne dla wszystkich typów działań.

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md). 

Właściwości, które są dostępne w sekcji **typeProperties** działania, różnią się w zależności od typu działania. W przypadku działania kopiowania właściwości różnią się w zależności od typów źródeł i ujścia.

Obecnie, gdy źródło w działaniu kopiowania ma typ **HttpSource** , obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| httpRequestTimeout | Limit czasu (wartość **TimeSpan** ) żądania HTTP w celu uzyskania odpowiedzi. Jest to limit czasu, aby uzyskać odpowiedź, a nie limit czasu odczytu danych odpowiedzi. | Nie<br />(wartość domyślna: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji

Aby uzyskać więcej informacji [, zobacz formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-examples"></a>Przykłady JSON

W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przykłady pokazują, jak skopiować dane ze źródła HTTP do usługi Azure Blob Storage. Dane można jednak skopiować *bezpośrednio* z dowolnego źródła do dowolnego ujścia [, które są obsługiwane](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

**Przykład: kopiowanie danych ze źródła HTTP do usługi Azure Blob Storage**

Data Factory rozwiązanie dla tego przykładu zawiera następujące jednostki Data Factory:

*   Połączona usługa typu [http](#linked-service-properties).
*   Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [http](#dataset-properties).
*   Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [HttpSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane ze źródła HTTP do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach, które są zgodne z przykładami.

### <a name="http-linked-service"></a>Połączona usługa HTTP

Ten przykład używa połączonej usługi HTTP z uwierzytelnianiem anonimowym. Zobacz [połączoną usługę http](#linked-service-properties) dla różnych typów uwierzytelniania, których można użyć.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Wejściowy zestaw danych HTTP

Ustawienie od **zewnątrz** do **true** informuje usługę Data Factory, że zestaw danych znajduje się poza fabryką danych i nie jest wytwarzany przez działanie w fabryce danych.

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
        "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
    },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure

Dane są zapisywane w nowym obiekcie blob co godzinę (**częstotliwość**: **godzina**, **Interwał**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Potok używający działania kopiowania

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **HttpSource** , a typ **ujścia** to **wartość blobsink**.

Aby uzyskać listę właściwości, które obsługuje **HttpSource** , zobacz [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Aby zmapować kolumny ze źródłowego zestawu danych do kolumn z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Aby dowiedzieć się więcej na temat kluczowych czynników wpływających na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji, zobacz [Przewodnik dotyczący wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md).
