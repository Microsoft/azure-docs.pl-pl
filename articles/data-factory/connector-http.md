---
title: Kopiowanie danych ze źródła HTTP przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z chmury lub lokalnego źródła HTTP do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: f3184602bad8aabf654c8fa94d33372d08c11a66
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573204"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego HTTP przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-http-connector.md)
> * [Bieżąca wersja](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z punktu końcowego HTTP. Artykuł jest oparty na [działaniu kopiowania w Azure Data Factory](copy-activity-overview.md), co przedstawia ogólne omówienie działania kopiowania.

Różnica między tym łącznikiem HTTP, [łącznika REST](connector-rest.md) i [łącznikiem tabeli sieci Web](connector-web-table.md) :

- **Łącznik REST** obsługujący kopiowanie danych z interfejsów API RESTful; 
- **Łącznik http** jest ogólny do pobierania danych z dowolnego punktu końcowego http, np. do pobrania pliku. Przed udostępnieniem łącznika REST może wystąpić potrzeba użycia łącznika HTTP do kopiowania danych z interfejsu API RESTful, który jest obsługiwany, ale mniej funkcjonalny porównanie z łącznikiem REST.
- **Łącznik tabeli sieci Web** wyodrębnia zawartość tabeli z strony html.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik HTTP jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Możesz skopiować dane ze źródła HTTP do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).

Tego łącznika protokołu HTTP można użyć do:

- Pobieranie danych z punktu końcowego HTTP/S przy użyciu metod **Get** lub **post** protokołu HTTP.
- Pobierz dane przy użyciu jednego z następujących uwierzytelnień: **anonimowe**, **podstawowe**, **szyfrowane**, **Windows** lub **ClientCertificate**.
- Należy skopiować odpowiedź HTTP jako-is lub przeanalizować ją przy użyciu [obsługiwanych formatów plików i koderów](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Aby przetestować żądanie HTTP na potrzeby pobierania danych przed skonfigurowaniem łącznika HTTP w Data Factory, Dowiedz się więcej na temat specyfikacji interfejsu API dla wymagań dotyczących nagłówka i treści. Aby sprawdzić poprawność, można użyć narzędzi, takich jak program Poster lub przeglądarka sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania jednostek Data Factory, które są specyficzne dla łącznika HTTP.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi HTTP są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **HttpServer**. | Tak |
| url | Podstawowy adres URL serwera sieci Web. | Tak |
| enableServerCertificateValidation | Określ, czy włączyć weryfikację certyfikatu protokołu TLS/SSL serwera podczas łączenia się z punktem końcowym HTTP. Jeśli serwer HTTPS używa certyfikatu z podpisem własnym, ustaw dla tej właściwości **wartość false**. | Nie<br /> (wartość domyślna to **true**) |
| authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to **anonimowe**, **podstawowe**, **szyfrowane**, **Windows** i **ClientCertificate**. <br><br> Zapoznaj się z sekcjami w poniższej tabeli, aby uzyskać więcej właściwości i przykładów JSON dla tych typów uwierzytelniania. | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

### <a name="using-basic-digest-or-windows-authentication"></a>Korzystanie z uwierzytelniania podstawowego, szyfrowanego lub systemu Windows

Ustaw właściwość **AuthenticationType** na wartość **Basic**, **Digest** lub **Windows**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| userName | Nazwa użytkownika, która ma być używana do uzyskiwania dostępu do punktu końcowego HTTP. | Tak |
| hasło | Hasło użytkownika (wartość **username** ). Oznacz to pole jako typ **SecureString** , aby bezpiecznie przechowywać go w Data Factory. Możesz również [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |

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

Aby użyć uwierzytelniania ClientCertificate, należy ustawić właściwość **AuthenticationType** na **Kolekcja ClientCertificate**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| embeddedCertData | Dane certyfikatu zakodowane algorytmem Base64. | Określ wartość **embeddedCertData** lub **certThumbprint**. |
| certThumbprint | Odcisk palca certyfikatu, który został zainstalowany na własnym magazynie certyfikatów maszyny Integration Runtime. Ma zastosowanie tylko wtedy, gdy w właściwości **właściwością connectvia** jest określony typ samodzielny Integration Runtime. | Określ wartość **embeddedCertData** lub **certThumbprint**. |
| hasło | Hasło skojarzone z certyfikatem. Oznacz to pole jako typ **SecureString** , aby bezpiecznie przechowywać go w Data Factory. Możesz również [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |

Jeśli używasz **certThumbprint** do uwierzytelniania, a certyfikat jest instalowany w magazynie osobistym komputera lokalnego, Udziel uprawnień do odczytu Integration Runtime samoobsługowego:

1. Otwórz program Microsoft Management Console (MMC). Dodaj przystawkę **Certyfikaty** , która jest przeznaczona dla **komputera lokalnego**.
2. Rozwiń węzeł **Certyfikaty**  >  **osobiste**, a następnie wybierz pozycję **Certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat ze sklepu osobistego, a następnie wybierz pozycję **wszystkie zadania**  >  **Zarządzaj kluczami prywatnymi**.
3. Na karcie **zabezpieczenia** Dodaj konto użytkownika, w ramach którego jest uruchomiona usługa hosta Integration Runtime (DIAHostService) z dostępem do odczytu do certyfikatu.

**Przykład 1: korzystanie z certThumbprint**

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

**Przykład 2: używanie embeddedCertData**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku protokołu HTTP w obszarze `location` Ustawienia w zestawie danych opartym na formacie:

| Właściwość    | Opis                                                  | Wymagane |
| ----------- | ------------------------------------------------------------ | -------- |
| typ        | Właściwość Type w `location` elemencie DataSet musi być ustawiona na wartość **HttpServerLocation**. | Tak      |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Łącznik HTTP kopiuje dane ze połączonego adresu URL: `[URL specified in linked service][relative URL specified in dataset]` .   | Nie       |

> [!NOTE]
> Obsługiwany rozmiar ładunku żądania HTTP to około 500 KB. Jeśli rozmiar ładunku, który ma zostać przekazany do punktu końcowego sieci Web, jest większy niż 500 KB, należy rozważyć przetwarzanie wsadowe w mniejszych fragmentach.

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

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło HTTP.

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku protokołu HTTP w obszarze `storeSettings` Ustawienia źródła kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **HttpReadSettings**. | Tak      |
| requestMethod            | Metoda HTTP. <br>Dozwolone wartości to **Get** (default) i **post**. | Nie       |
| additionalHeaders         | Dodatkowe nagłówki żądań HTTP.                             | Nie       |
| Elemencie requestbody              | Treść żądania HTTP.                               | Nie       |
| httpRequestTimeout           | Limit czasu (wartość **TimeSpan** ) żądania HTTP w celu uzyskania odpowiedzi. Ta wartość jest przekroczeniem limitu czasu w celu uzyskania odpowiedzi, a nie limitu czasu odczytu danych odpowiedzi. Wartość domyślna to **00:01:40**. | Nie       |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie       |

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

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Użytkownik chce użyć nowego modelu wymienionego w powyższych sekcjach, przechodząc do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** zestawu danych musi być ustawiona na wartość **HttpFile**. | Tak |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ta właściwość nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. | Nie |
| requestMethod | Metoda HTTP. Dozwolone wartości to **Get** (default) i **post**. | Nie |
| additionalHeaders | Dodatkowe nagłówki żądań HTTP. | Nie |
| Elemencie requestbody | Treść żądania HTTP. | Nie |
| format | Jeśli chcesz pobrać dane z punktu końcowego HTTP jako-is bez analizy, a następnie skopiować dane do magazynu opartego na plikach, Pomiń sekcję **Format** w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować zawartość odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat** i **ParquetFormat**. W obszarze **Format** ustaw właściwość **Type** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format tekstu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Format Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Nie |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Obsługiwane typy: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**.<br/>Obsługiwane poziomy:  **optymalne** i **najszybszy**. |Nie |

> [!NOTE]
> Obsługiwany rozmiar ładunku żądania HTTP to około 500 KB. Jeśli rozmiar ładunku, który ma zostać przekazany do punktu końcowego sieci Web, jest większy niż 500 KB, należy rozważyć przetwarzanie wsadowe w mniejszych fragmentach.

**Przykład 1: użycie metody get (domyślnie)**

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

**Przykład 2: użycie metody post**

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

### <a name="legacy-copy-activity-source-model"></a>Model źródłowy starszego działania kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **HttpSource**. | Tak |
| httpRequestTimeout | Limit czasu (wartość **TimeSpan** ) żądania HTTP w celu uzyskania odpowiedzi. Ta wartość jest przekroczeniem limitu czasu w celu uzyskania odpowiedzi, a nie limitu czasu odczytu danych odpowiedzi. Wartość domyślna to **00:01:40**.  | Nie |

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

Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia w Azure Data Factory, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).
