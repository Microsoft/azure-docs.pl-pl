---
title: Kopiowanie danych ze źródła HTTP przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak kopiować dane z chmury lub lokalnego źródła HTTP do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 730efb552ef218cc5a5ce6a984d20b4e23b364ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416943"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego HTTP przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-http-connector.md)
> * [Bieżąca wersja](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z punktu końcowego HTTP. Artykuł opiera się na [copy activity w usłudze Azure Data Factory](copy-activity-overview.md), który przedstawia ogólne omówienie działania kopiowania.

Różnica między tym łącznikiem HTTP, [łącznikiem REST](connector-rest.md) i [łącznikiem tabeli sieci Web](connector-web-table.md) to:

- **Złącze REST** obsługuje w szczególności kopiowanie danych z interfejsów API RESTful; 
- **Łącznik HTTP** jest ogólny, aby pobrać dane z dowolnego punktu końcowego HTTP, na przykład do pobrania pliku. Zanim łącznik REST stanie się dostępny, może się zdarzyć użycie łącznika HTTP do kopiowania danych z interfejsu API RESTful, który jest obsługiwany, ale mniej funkcjonalny w porównaniu do łącznika REST.
- **Łącznik tabeli sieci Web** wyodrębnia zawartość tabeli ze strony html.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik HTTP jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane ze źródła HTTP można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i pochłaniacze, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).

Za pomocą tego łącznika HTTP można:

- Pobieranie danych z punktu końcowego HTTP/S przy użyciu metod HTTP **GET** lub **POST.**
- Pobieranie danych przy użyciu jednego z następujących uwierzytelniania: **Anonimowy**, **Podstawowy**, **Skrót**, **Windows**lub **ClientCertificate**.
- Skopiuj odpowiedź HTTP w stanie, w jakim jest, lub przesiemaj ją przy użyciu [obsługiwanych formatów plików i kodeków kompresji](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Aby przetestować żądanie HTTP do pobierania danych przed skonfigurowaniem łącznika HTTP w fabryce danych, zapoznaj się ze specyfikacją interfejsu API dla wymagań nagłówka i treści. Do sprawdzania poprawności można użyć narzędzi, takich jak Listonosz lub przeglądarka internetowa.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do zdefiniowania jednostek fabryki danych, które są specyficzne dla łącznika HTTP.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Dla połączonej usługi HTTP są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** musi być ustawiona na **HttpServer**. | Tak |
| url | Podstawowy adres URL serwera sieci web. | Tak |
| enableServerCertificateWwadacja | Określ, czy podczas łączenia się z punktem końcowym HTTP ma być włączany sprawdzanie poprawności certyfikatu protokołu TLS/SSL serwera. Jeśli serwer HTTPS używa certyfikatu z podpisem własnym, ustaw tę właściwość na **false**. | Nie<br /> (wartość domyślna jest **prawdziwa)** |
| authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to **Anonimowe,** **Podstawowe,** **Skrótowe,** **Windows**i **ClientCertificate**. <br><br> Zobacz sekcje, które należy wykonać w tej tabeli, aby uzyskać więcej właściwości i przykłady JSON dla tych typów uwierzytelniania. | Tak |
| connectVia | [Środowisko wykonawcze integracji](concepts-integration-runtime.md) do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

### <a name="using-basic-digest-or-windows-authentication"></a>Korzystanie z uwierzytelniania podstawowego, skrótowego lub uwierzytelniania systemu Windows

Ustaw właściwość **authenticationType** na **Basic**, **Digest**lub **Windows**. Oprócz właściwości ogólnych, które są opisane w poprzedniej sekcji, należy określić następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| userName | Nazwa użytkownika, która ma być używana do uzyskiwania dostępu do punktu końcowego HTTP. | Tak |
| hasło | Hasło użytkownika (wartość **userName).** Oznacz to pole jako typ **SecureString,** aby bezpiecznie przechowywać go w fabryce danych. Można również [odwoływać się do klucza tajnego przechowywanego w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |

**Przykład**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
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

### <a name="using-clientcertificate-authentication"></a>Korzystanie z uwierzytelniania ClientCertificate

Aby użyć uwierzytelniania ClientCertificate, należy ustawić właściwość **authenticationType** na **ClientCertificate**. Oprócz właściwości ogólnych, które są opisane w poprzedniej sekcji, należy określić następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| embeddedCertData | Dane certyfikatów zakodowanych w bazie Base64. | Określ **osadzoneCertData** lub **certThumbprint**. |
| certThumbprint | Odcisk palca certyfikatu, który jest zainstalowany w magazynie certyfikatów komputera integracji hostowanego przez środowisko wykonawcze. Ma zastosowanie tylko wtedy, gdy samodzielnie hostowany typ środowiska wykonawczego integracji jest określony we właściwości **connectVia.** | Określ **osadzoneCertData** lub **certThumbprint**. |
| hasło | Hasło skojarzone z certyfikatem. Oznacz to pole jako typ **SecureString,** aby bezpiecznie przechowywać go w fabryce danych. Można również [odwoływać się do klucza tajnego przechowywanego w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |

Jeśli do uwierzytelniania jest używany **certThumbprint,** a certyfikat jest zainstalowany w magazynie osobistym komputera lokalnego, udziel uprawnień do odczytu do samodzielnego środowiska wykonawczego integracji:

1. Otwórz program Microsoft Management Console (MMC). Dodaj przystawkę **Certyfikaty,** która jest przeznaczona dla **komputera lokalnego**.
2. Rozwiń pozycję **Certyfikaty** > **osobiste**, a następnie wybierz pozycję **Certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat z magazynu osobistego, a następnie wybierz pozycję **Wszystkie zadania** > **Zarządzaj kluczami prywatnymi**.
3. Na karcie **Zabezpieczenia** dodaj konto użytkownika, na którym jest uruchomiona usługa hosta środowiska wykonawczego integracji (DIAHostService), z dostępem do odczytu do certyfikatu.

**Przykład 1: Korzystanie z certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: Korzystanie z embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla `location` protokołu HTTP w ustawieniach w zestawie danych opartym na formacie:

| Właściwość    | Opis                                                  | Wymagany |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | Właściwość typu `location` w obszarze w zestawie danych musi być ustawiona na **HttpServerLocation**. | Tak      |
| względnyUrl | Względny adres URL do zasobu, który zawiera dane. Łącznik HTTP kopiuje dane z `[URL specified in linked service][relative URL specified in dataset]`połączonego adresu URL: .   | Nie       |

> [!NOTE]
> Obsługiwany rozmiar ładunku żądania HTTP wynosi około 500 KB. Jeśli rozmiar ładunku, który chcesz przekazać do punktu końcowego sieci web jest większy niż 500 KB, należy wziąć pod uwagę przetwarzanie ładunku w mniejszych fragmentów.

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Ta sekcja zawiera listę właściwości, które obsługuje źródło HTTP.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla `storeSettings` protokołu HTTP w ustawieniach w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagany |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **HttpReadSettings**. | Tak      |
| wniosekMetoda            | Metoda HTTP. <br>Dozwolone wartości **to Get** (default) i **Post**. | Nie       |
| addtionalHeaders (dodawanie nagłówków)         | Dodatkowe nagłówki żądań HTTP.                             | Nie       |
| requestBody (Ciało)              | Treść żądania HTTP.                               | Nie       |
| httpRequestTimeout           | Limit czasu (wartość **TimeSpan)** dla żądania HTTP, aby uzyskać odpowiedź. Ta wartość jest limit czasu, aby uzyskać odpowiedź, a nie limit czasu do odczytu danych odpowiedzi. Wartość domyślna **to 00:01:40**. | Nie       |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie       |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane jako — dla zgodności z powrotem. Zaleca się użycie nowego modelu wymienionego w powyższych sekcjach w przyszłości, a interfejs użytkownika tworzenia podajnikiem ADF przełączył się na generowanie nowego modelu.

### <a name="legacy-dataset-model"></a>Starszy model zestawu danych

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** zestawu danych musi być ustawiona na **HttpFile**. | Tak |
| względnyUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ta właściwość nie jest określona, używany jest tylko adres URL określony w definicji połączonej usługi. | Nie |
| wniosekMetoda | Metoda HTTP. Dozwolone wartości **to Get** (default) i **Post**. | Nie |
| dodatkowePozyty | Dodatkowe nagłówki żądań HTTP. | Nie |
| requestBody (Ciało) | Treść żądania HTTP. | Nie |
| format | Jeśli chcesz pobrać dane z punktu końcowego HTTP w stanie, w jakim jest bez analizowania, a następnie skopiować dane do magazynu opartego na plikach, pomiń sekcję **formatu** w definicjach wejściowych i wyjściowych zestawów danych.<br/><br/>Jeśli chcesz przeanalizować zawartość odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. W obszarze **format**ustaw właściwość **typu** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format tekstu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Ork](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Format parkietu](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Nie |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [Obsługiwane formaty plików i kodeki kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Obsługiwane typy: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy: **Optymalne** i **najszybsze**. |Nie |

> [!NOTE]
> Obsługiwany rozmiar ładunku żądania HTTP wynosi około 500 KB. Jeśli rozmiar ładunku, który chcesz przekazać do punktu końcowego sieci web jest większy niż 500 KB, należy wziąć pod uwagę przetwarzanie ładunku w mniejszych fragmentów.

**Przykład 1: Użycie metody Pobierz (domyślnie)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Przykład 2: Używanie metody Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Starszy model źródła działania kopiowania

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **HttpSource**. | Tak |
| httpRequestTimeout | Limit czasu (wartość **TimeSpan)** dla żądania HTTP, aby uzyskać odpowiedź. Ta wartość jest limit czasu, aby uzyskać odpowiedź, a nie limit czasu do odczytu danych odpowiedzi. Wartość domyślna **to 00:01:40**.  | Nie |

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działanie kopiowania obsługuje jako źródła i pochłaniacze w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).
