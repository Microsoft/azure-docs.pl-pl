---
title: Kopiowanie danych z Magento za pomocą Azure Data Factory (wersja zapoznawcza)
description: Informacje o kopiowaniu danych z programu Magento do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 2e27c54b6d5a95de92f77e61f5a1e11e1fd53ee5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416915"
---
# <a name="copy-data-from-magento-using-azure-data-factory-preview"></a>Kopiowanie danych z Magento za pomocą Azure Data Factory (wersja zapoznawcza)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z Magento. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w wersji zapoznawczej. Możesz ją wypróbować i przekazać nam swoją opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Magento jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z Magento można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory udostępnia wbudowany sterownik umożliwiający połączenie, dlatego nie trzeba ręcznie instalować żadnego sterownika przy użyciu tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Magento.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Magento są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **Magento** | Tak |
| host | Adres URL wystąpienia Magento. (to jest, 192.168.222.110/magento3)  | Tak |
| accessToken | Token dostępu z Magento. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartością domyślną jest true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta ma być wymagana w certyfikacie serwera, aby odpowiadała nazwie hosta serwera podczas łączenia się za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |
| usePeerVerification | Określa, czy należy zweryfikować tożsamość serwera podczas łączenia za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |

**Przykład:**

```json
{
    "name": "MagentoLinkedService",
    "properties": {
        "type": "Magento",
        "typeProperties": {
            "host" : "192.168.222.110/magento3",
            "accessToken": {
                "type": "SecureString",
                "value": "<accessToken>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Magento.

Aby skopiować dane z Magento, ustaw właściwość Type zestawu danych na **MagentoObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **MagentoObject** | Tak |
| tableName | Nazwa tabeli. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przykład**

```json
{
    "name": "MagentoDataset",
    "properties": {
        "type": "MagentoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Magento linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Magento.

### <a name="magento-as-source"></a>Magento jako źródło

Aby skopiować dane z Magento, ustaw typ źródła w działaniu Copy na **MagentoSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **MagentoSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Przykład: `"SELECT * FROM Customers"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMagento",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Magento input dataset name>",
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
                "type": "MagentoSource",
                "query": "SELECT * FROM Customers where Id > XXX"
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
