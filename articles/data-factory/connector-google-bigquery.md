---
title: Kopiowanie danych z Google BigQuery przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak kopiować dane z Google BigQuery do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku fabryki danych.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 6751f64706444176f0df8f8fc0c6132e76b39b2d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417319"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopiowanie danych z Google BigQuery przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak kopiować dane z usługi Google BigQuery za pomocą funkcji Kopiowanie aktywności w usłudze Azure Data Factory. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Google BigQuery jest obsługiwany w następujących działaniach:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Możesz skopiować dane z Google BigQuery do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła lub pochłaniacze przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Usługa Data Factory udostępnia wbudowany sterownik umożliwiający łączność. W związku z tym nie trzeba ręcznie zainstalować sterownik, aby użyć tego łącznika.

>[!NOTE]
>To złącze Google BigQuery jest zbudowany na szczycie Interfejsów API BigQuery. Należy pamiętać, że BigQuery ogranicza maksymalną szybkość przychodzących żądań i wymusza odpowiednie przydziały na podstawie projektu, odnoszą się do [limitów & przydziałów — żądania interfejsu API](https://cloud.google.com/bigquery/quotas#api_requests). Upewnij się, że nie wyzwolić zbyt wiele równoczesnych żądań do konta.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika Google BigQuery.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej Google BigQuery.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **GoogleBigQuery**. | Tak |
| projekt | Identyfikator projektu domyślnego projektu BigQuery do kwerendy.  | Tak |
| dodatkoweProjekty | Oddzielona przecinkami lista identyfikatorów projektów publicznych projektów BigQuery, do do które mają być dostępne.  | Nie |
| requestGoogleDriveScope | Czy chcesz uzyskać dostęp do Dysku Google. Zezwolenie na dostęp do Dysku Google umożliwia obsługę tabel federacyjnych, które łączą dane BigQuery z danymi z Dysku Google. Wartość domyślna to **fałsz**.  | Nie |
| authenticationType | Mechanizm uwierzytelniania OAuth 2.0 używany do uwierzytelniania. ServiceAuthentication może być używany tylko w czasie wykonywania integracji hostowane samodzielnie. <br/>Dozwolone wartości to **UserAuthentication** i **ServiceAuthentication**. Zapoznaj się z sekcjami poniżej tej tabeli na więcej właściwości i przykłady JSON dla tych typów uwierzytelniania odpowiednio. | Tak |

### <a name="using-user-authentication"></a>Korzystanie z uwierzytelniania użytkowników

Ustaw właściwość "authenticationType" na **UserAuthentication**i określ następujące właściwości wraz z właściwościami ogólnymi opisanymi w poprzedniej sekcji:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| clientId | Identyfikator aplikacji używanej do generowania tokenu odświeżania. | Nie |
| clientSecret | Klucz tajny aplikacji używanej do generowania tokenu odświeżania. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Nie |
| odświeżanieDoken | Token odświeżania uzyskany od Google służy do autoryzacji dostępu do BigQuery. Dowiedz się, jak uzyskać jeden z [Uzyskiwanie OAuth 2.0 tokeny dostępu](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) i [tego bloga społeczności](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Nie |

**Przykład:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Korzystanie z uwierzytelniania usługi

Ustaw właściwość "authenticationType" na **ServiceAuthentication**i określ następujące właściwości wraz z właściwościami ogólnymi opisanymi w poprzedniej sekcji. Tego typu uwierzytelniania można używać tylko w samodzielnym czasie działania integracji.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| email | Identyfikator wiadomości e-mail konta usługi, który jest używany do ServiceAuthentication. Może być używany tylko w własnym czasie działania integracji.  | Nie |
| keyFilePath | Pełna ścieżka do pliku klucza .p12, który jest używany do uwierzytelniania adresu e-mail konta usługi. | Nie |
| trustedCertPath | Pełna ścieżka pliku pem zawierającego zaufane certyfikaty urzędu certyfikacji używane do weryfikacji serwera podczas łączenia się za pomocą protokołu TLS. Tę właściwość można ustawić tylko wtedy, gdy używasz protokołu TLS w samodzielnym czasie działania integracji. Wartością domyślną jest plik cacerts.pem zainstalowany wraz ze środowiska wykonawczego integracji.  | Nie |
| useSystemTrustStore | Określa, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu, czy z określonego pliku pem. Wartość domyślna to **fałsz**.  | Nie |

**Przykład:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Google BigQuery.

Aby skopiować dane z Google BigQuery, ustaw właściwość typu zestawu danych na **GoogleBigQueryObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **GoogleBigQueryObject** | Tak |
| Dataset | Nazwa zestawu danych Google BigQuery. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tabela | Nazwa tabeli. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tableName | Nazwa tabeli. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. W przypadku nowego `dataset` `table`obciążenia, użycia i programu . | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez typ źródła Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource jako typ źródła

Aby skopiować dane z Google BigQuery, ustaw typ źródła w aktywności kopiowania na **GoogleBigQuerySource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła aktywności kopiowania musi być ustawiona na **GoogleBigQuerySource**. | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Może to być na przykład `"SELECT * FROM MyTable"`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
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

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w fabryce danych, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
