---
title: Kopiuj dane z usługi Google AdWords
description: Dowiedz się, jak skopiować dane z usługi Google AdWords do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: 8b3036f09e41b20bc3c190f06842acd817fcece6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380954"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Skopiuj dane z usługi Google AdWords przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z usługi Google AdWords. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Google AdWords jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)


Dane z usługi Google AdWords można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory udostępnia wbudowany sterownik umożliwiający połączenie, dlatego nie trzeba ręcznie instalować żadnego sterownika przy użyciu tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika usługi Google AdWords.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Google AdWords są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **GoogleAdWords** | Tak |
| clientCustomerID | Identyfikator klienta klienta konta AdWords, dla którego mają zostać pobrane dane raportu.  | Tak |
| developerToken | Token dewelopera skojarzony z kontem menedżera, którego używasz do udzielania dostępu do interfejsu API AdWords.  Możesz oznaczyć to pole jako element SecureString, aby bezpiecznie przechowywać go w podajniku APD, lub przechowywać hasło w Azure Key Vault i wypróbować działanie Copy APD z tego miejsca podczas kopiowania danych — Dowiedz się więcej z [poświadczeń sklepu w Key Vault](store-credentials-in-key-vault.md). | Tak |
| authenticationType | Mechanizm uwierzytelniania OAuth 2,0 używany do uwierzytelniania. Funkcji serviceauthentication można używać tylko w przypadku samodzielnego środowiska IR. <br/>Dozwolone wartości to: **serviceauthentication**, **UserAuthentication** | Tak |
| refreshToken | Token odświeżania uzyskany od firmy Google do autoryzowania dostępu do usługi AdWords dla UserAuthentication. Możesz oznaczyć to pole jako element SecureString, aby bezpiecznie przechowywać go w podajniku APD, lub przechowywać hasło w Azure Key Vault i wypróbować działanie Copy APD z tego miejsca podczas kopiowania danych — Dowiedz się więcej z [poświadczeń sklepu w Key Vault](store-credentials-in-key-vault.md). | Nie |
| clientId | Identyfikator klienta aplikacji Google używany do uzyskiwania tokenu odświeżania. Możesz oznaczyć to pole jako element SecureString, aby bezpiecznie przechowywać go w podajniku APD, lub przechowywać hasło w Azure Key Vault i wypróbować działanie Copy APD z tego miejsca podczas kopiowania danych — Dowiedz się więcej z [poświadczeń sklepu w Key Vault](store-credentials-in-key-vault.md). | Nie |
| clientSecret | Wpis tajny klienta aplikacji Google używany do uzyskiwania tokenu odświeżania. Możesz oznaczyć to pole jako element SecureString, aby bezpiecznie przechowywać go w podajniku APD, lub przechowywać hasło w Azure Key Vault i wypróbować działanie Copy APD z tego miejsca podczas kopiowania danych — Dowiedz się więcej z [poświadczeń sklepu w Key Vault](store-credentials-in-key-vault.md). | Nie |
| poczta e-mail | Identyfikator poczty e-mail konta usługi używany na potrzeby usługi serviceauthentication i może być używany tylko w przypadku samodzielnego środowiska IR.  | Nie |
| Atrybut keyfilepath | Pełna ścieżka do pliku klucza. P12, który jest używany do uwierzytelniania adresu e-mail konta usługi i może być używana tylko w przypadku samodzielnego środowiska IR.  | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM zawierającego certyfikaty zaufanych urzędów certyfikacji w celu zweryfikowania serwera podczas łączenia za pośrednictwem protokołu TLS. Tę właściwość można ustawić tylko w przypadku korzystania z protokołu TLS na samoobsługowym środowisku IR. Wartość domyślna to plik cacerts. pem instalowany z programem IR.  | Nie |
| useSystemTrustStore | Określa, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu czy z określonego pliku PEM. Wartość domyślna to false.  | Nie |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Google AdWords.

Aby skopiować dane z usługi Google AdWords, ustaw właściwość Type zestawu danych na **GoogleAdWordsObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **GoogleAdWordsObject** | Tak |
| tableName | Nazwa tabeli. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords jako źródło

Aby skopiować dane z usługi Google AdWords, ustaw typ źródła w działaniu Copy na **GoogleAdWordsSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **GoogleAdWordsSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

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

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
