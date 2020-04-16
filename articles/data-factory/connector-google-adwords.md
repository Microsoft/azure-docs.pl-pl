---
title: Kopiowanie danych z Google AdWords
description: Dowiedz się, jak kopiować dane z Google AdWords do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: 79e277cb3d512ee17da866a61e9d6d66a50cd902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415023"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Kopiowanie danych z Google AdWords przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


W tym artykule opisano, jak kopiować dane z Google AdWords za pomocą funkcji Kopiowanie w usłudze Azure Data Factory. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Google AdWords jest obsługiwany w następujących działaniach:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)


Możesz kopiować dane z Google AdWords do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

Usługa Azure Data Factory udostępnia wbudowany sterownik, aby włączyć łączność, w związku z tym nie trzeba ręcznie zainstalować żadnego sterownika przy użyciu tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach używanych do definiowania jednostek fabryki danych specyficznych dla łącznika Google AdWords.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

W usłudze połączonej z Google AdWords obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **GoogleAdWords** | Tak |
| identyfikator klienta | Identyfikator klienta konta AdWords, dla którego chcesz pobrać dane raportu.  | Tak |
| developerToken | Token dewelopera skojarzony z kontem menedżera, którego używasz do udzielania dostępu do interfejsu API AdWords.  Możesz zaznaczyć to pole jako SecureString, aby bezpiecznie przechowywać je w usłudze ADF lub przechowywać hasło w usłudze Azure Key Vault i pozwolić aktywność kopiowania ADF pobierać stamtąd podczas wykonywania kopiowania danych — dowiedz się więcej z [poświadczenia sklepu w magazynie kluczy](store-credentials-in-key-vault.md). | Tak |
| authenticationType | Mechanizm uwierzytelniania OAuth 2.0 używany do uwierzytelniania. ServiceAuthentication można używać tylko na własny hostowane IR. <br/>Dozwolone wartości to: **ServiceAuthentication**, **UserAuthentication** | Tak |
| odświeżanieDoken | Token odświeżania uzyskany od Google w celu autoryzowania dostępu do AdWords for UserAuthentication. Możesz zaznaczyć to pole jako SecureString, aby bezpiecznie przechowywać je w usłudze ADF lub przechowywać hasło w usłudze Azure Key Vault i pozwolić aktywność kopiowania ADF pobierać stamtąd podczas wykonywania kopiowania danych — dowiedz się więcej z [poświadczenia sklepu w magazynie kluczy](store-credentials-in-key-vault.md). | Nie |
| clientId | Identyfikator klienta aplikacji Google używany do uzyskania tokenu odświeżania. Możesz zaznaczyć to pole jako SecureString, aby bezpiecznie przechowywać je w usłudze ADF lub przechowywać hasło w usłudze Azure Key Vault i pozwolić aktywność kopiowania ADF pobierać stamtąd podczas wykonywania kopiowania danych — dowiedz się więcej z [poświadczenia sklepu w magazynie kluczy](store-credentials-in-key-vault.md). | Nie |
| clientSecret | Tajemnica klienta aplikacji google użyta do uzyskania tokenu odświeżania. Możesz zaznaczyć to pole jako SecureString, aby bezpiecznie przechowywać je w usłudze ADF lub przechowywać hasło w usłudze Azure Key Vault i pozwolić aktywność kopiowania ADF pobierać stamtąd podczas wykonywania kopiowania danych — dowiedz się więcej z [poświadczenia sklepu w magazynie kluczy](store-credentials-in-key-vault.md). | Nie |
| email | Identyfikator wiadomości e-mail konta usługi, który jest używany do serviceAuthentication i może być używany tylko na własny hostowany iR.  | Nie |
| keyFilePath | Pełna ścieżka do pliku klucza .p12, który jest używany do uwierzytelniania adresu e-mail konta usługi i może być używany tylko na samodzielnym podczerwem.  | Nie |
| trustedCertPath | Pełna ścieżka pliku pem zawierającego zaufane certyfikaty urzędu certyfikacji do sprawdzania serwera podczas łączenia się za ok. Tę właściwość można ustawić tylko podczas korzystania z protokołu TLS na samodzielnym podczerwaniu. Wartością domyślną jest plik cacerts.pem zainstalowany z podczerwień.  | Nie |
| useSystemTrustStore | Określa, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu, czy z określonego pliku PEM. Wartość domyślna to false.  | Nie |

**Przykład:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Google AdWords.

Aby skopiować dane z Google AdWords, ustaw właściwość typu zestawu danych na **GoogleAdWordsObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **GoogleAdWordsObject** | Tak |
| tableName | Nazwa tabeli. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę usług obsługiwanych przez źródło Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords jako źródło

Aby skopiować dane z Google AdWords, ustaw typ źródła w aktywności kopiowania na **GoogleAdWordsSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **GoogleAdWordsSource** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
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
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
