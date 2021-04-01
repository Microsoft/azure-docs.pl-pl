---
title: Kopiuj dane z usługi ServiceNow
description: Informacje o kopiowaniu danych z programu usługi ServiceNow do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 4e7ebc422a9fd8503c5a3b004e1d06cb5ebfb987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378455"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopiowanie danych z usługi ServiceNow za pomocą Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z usługi ServiceNow. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi ServiceNow jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z usługi ServiceNow można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory udostępnia wbudowany sterownik umożliwiający połączenie, dlatego nie trzeba ręcznie instalować żadnego sterownika przy użyciu tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika usługi ServiceNow.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi usługi ServiceNow są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **usługi ServiceNow** | Tak |
| endpoint | Punkt końcowy serwera usługi ServiceNow ( `http://<instance>.service-now.com` ).  | Tak |
| authenticationType | Typ uwierzytelniania do użycia. <br/>Dozwolone wartości to: **Basic**, **OAuth2** | Tak |
| nazwa użytkownika | Nazwa użytkownika używana do nawiązywania połączenia z serwerem usługi ServiceNow na potrzeby uwierzytelniania podstawowego i OAuth2.  | Tak |
| hasło | Hasło odpowiadające nazwie użytkownika uwierzytelniania podstawowego i OAuth2. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| clientId | Identyfikator klienta na potrzeby uwierzytelniania OAuth2.  | Nie |
| clientSecret | Wpis tajny klienta na potrzeby uwierzytelniania OAuth2. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartością domyślną jest true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta ma być wymagana w certyfikacie serwera, aby odpowiadała nazwie hosta serwera podczas łączenia się za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |
| usePeerVerification | Określa, czy należy zweryfikować tożsamość serwera podczas łączenia za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi ServiceNow.

Aby skopiować dane z usługi ServiceNow, ustaw właściwość Type zestawu danych na **ServiceNowObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **ServiceNowObject** | Tak |
| tableName | Nazwa tabeli. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło usługi ServiceNow.

### <a name="servicenow-as-source"></a>Usługi ServiceNow jako źródło

Aby skopiować dane z usługi ServiceNow, ustaw typ źródła w działaniu Copy na **ServiceNowSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **ServiceNowSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM Actual.alm_asset"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

Należy pamiętać o następujących kwestiach podczas określania schematu i kolumny dla usługi ServiceNow w kwerendzie i **zapoznaj się z [poradami dotyczącymi wydajności](#performance-tips) kopiowania**.

- **Schemat:** Określ schemat jako `Actual` lub `Display` w zapytaniu usługi ServiceNow, który można sprawdzić jako parametr `sysparm_display_value` jako wartość true lub false podczas wywoływania [interfejsów API usługi ServiceNow RESTful](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolumna:** nazwa kolumny dla wartości rzeczywistej w obszarze `Actual` schemat to `[column name]_value` , a dla wartości wyświetlanej w obszarze `Display` schemat znajduje się wartość `[column name]_display_value` . Zwróć uwagę na to, że nazwa kolumny musi być mapowana na schemat używany w zapytaniu.

**Przykładowe zapytanie:** 
 `SELECT col_value FROM Actual.alm_asset` ORAZ`SELECT col_display_value FROM Display.alm_asset`

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

Usługi ServiceNow ma 2 różne schematy, jedna to **"rzeczywista"** , która zwraca rzeczywiste dane, druga to **"Display"** , która zwraca wartości wyświetlania danych. 

Jeśli w zapytaniu znajduje się filtr, użyj "rzeczywistego" schematu, który zapewnia lepszą wydajność kopiowania. Podczas wykonywania zapytania względem schematu "rzeczywisty" usługi ServiceNow natywnie obsługuje filtr podczas pobierania danych, aby zwracał tylko przefiltrowany zestaw wyników, podczas gdy zapytanie o schemat "Display", ADF pobiera wszystkie dane i stosuje filtr wewnętrznie.

### <a name="index"></a>Indeks

Indeks tabeli usługi ServiceNow może pomóc w zwiększeniu wydajności zapytania, zapoznaj się z tematem [Tworzenie indeksu tabeli](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
