---
title: Kopiowanie danych z servicenow
description: Dowiedz się, jak skopiować dane z usługi ServiceNow do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: bc48f651a1adb099017e8f47d9fa6bcfa8078fa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415348"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopiowanie danych z usługi ServiceNow przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z usługi ServiceNow. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik ServiceNow jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z ServiceNow do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

Usługa Azure Data Factory udostępnia wbudowany sterownik, aby włączyć łączność, w związku z tym nie trzeba ręcznie zainstalować żadnego sterownika przy użyciu tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach podano szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika ServiceNow.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej ServiceNow:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **ServiceNow** | Tak |
| endpoint | Punkt końcowy serwera ServiceNow`http://<instance>.service-now.com`( ).  | Tak |
| authenticationType | Typ uwierzytelniania do użycia. <br/>Dozwolone wartości to: **Basic**, **OAuth2** | Tak |
| nazwa użytkownika | Nazwa użytkownika używana do łączenia się z serwerem ServiceNow dla uwierzytelniania Basic i OAuth2.  | Tak |
| hasło | Hasło odpowiadające nazwie użytkownika uwierzytelniania Basic i OAuth2. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| clientId | Identyfikator klienta dla uwierzytelniania OAuth2.  | Nie |
| clientSecret | Klucz tajny klienta dla uwierzytelniania OAuth2. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Nie |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartością domyślną jest true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta w certyfikacie serwera ma być zgodna z nazwą hosta serwera podczas łączenia się za korzystając z protokołu TLS. Wartością domyślną jest true.  | Nie |
| usePeerVerification | Określa, czy podczas nawiązywania połączenia zawersyfikowany przez TLS ma być weryfikowany przez tożsamość serwera. Wartością domyślną jest true.  | Nie |

**Przykład:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych ServiceNow.

Aby skopiować dane z ServiceNow, ustaw właściwość typu zestawu danych na **ServiceNowObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **ServiceNowObject** | Tak |
| tableName | Nazwa tabeli. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow jako źródło

Aby skopiować dane z servicenow, ustaw typ źródła w działaniu kopiowania na **ServiceNowSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **ServiceNowSource** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"SELECT * FROM Actual.alm_asset"`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

Zwróć uwagę na następujące kwestie podczas określania schematu i kolumny servicenow w kwerendzie i **zapoznaj się [z poradami dotyczącymi wydajności](#performance-tips) dotyczącymi wpływu na wydajność kopiowania**.

- **Schemat:** określ schemat `Actual` jako `Display` lub w zapytaniu ServiceNow, który można `sysparm_display_value` spojrzeć na niego jako parametr jako true lub false podczas wywoływania [ServiceNow spokojny interfejsów API](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolumna:** nazwa kolumny dla `Actual` rzeczywistej `[column name]_value`wartości w schemacie jest , podczas gdy dla wartości wyświetlania w schemacie `Display` jest `[column name]_display_value`. Zwróć uwagę, że nazwa kolumny wymaga mapy do schematu używanego w kwerendzie.

**Przykładowe zapytanie:** 
 `SELECT col_value FROM Actual.alm_asset` OR 
`SELECT col_display_value FROM Display.alm_asset`

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Porady dotyczące wydajności

### <a name="schema-to-use"></a>Schemat do użycia

ServiceNow ma 2 różne schematy, jeden jest **"Rzeczywiste",** który zwraca rzeczywiste dane, drugi jest **"Display",** który zwraca wartości wyświetlania danych. 

Jeśli masz filtr w kwerendzie, użyj schematu "Rzeczywiste", który ma lepszą wydajność kopiowania. Podczas wykonywania zapytań dotyczących schematu "Rzeczywiste", ServiceNow natywnie obsługuje filtr podczas pobierania danych, aby zwracać tylko filtrowany zestaw wyników, podczas gdy podczas wykonywania zapytań o schemat "Display" usługa ADF pobiera wszystkie dane i stosuje filtr wewnętrznie.

### <a name="index"></a>Indeks

ServiceNow indeks tabeli może pomóc poprawić wydajność kwerendy, patrz [Tworzenie indeksu tabeli](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
