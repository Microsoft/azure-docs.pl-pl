---
title: Kopiowanie danych z programu Oracle Eloqua (wersja zapoznawcza)
description: Informacje o kopiowaniu danych z programu Oracle Eloqua do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: e4bb392b05fb54f22720202b83f1af707e381251
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372233"
---
# <a name="copy-data-from-oracle-eloqua-using-azure-data-factory-preview"></a>Kopiowanie danych z programu Oracle Eloqua przy użyciu Azure Data Factory (wersja zapoznawcza)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z programu Oracle Eloqua. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w wersji zapoznawczej. Możesz wypróbować tę usługę i przekazać opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik programu Oracle Eloqua jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z programu Oracle Eloqua można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory udostępnia wbudowany sterownik umożliwiający połączenie, dlatego nie trzeba ręcznie instalować żadnego sterownika przy użyciu tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Oracle Eloqua.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Oracle Eloqua są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **Eloqua** | Tak |
| endpoint | Punkt końcowy serwera Eloqua. Usługa Eloqua obsługuje wiele centrów danych, aby określić punkt końcowy, zalogować się do https://login.eloqua.com niego przy użyciu poświadczeń, a następnie skopiować część **podstawowego adresu URL** z przekierowanego adresu URL ze wzorcem `xxx.xxx.eloqua.com` . | Tak |
| nazwa użytkownika | Nazwa witryny i nazwa użytkownika konta Eloqua w postaci: `SiteName\Username` np. `Eloqua\Alice` .  | Tak |
| hasło | Hasło odpowiadające nazwie użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartością domyślną jest true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta ma być wymagana w certyfikacie serwera, aby odpowiadała nazwie hosta serwera podczas łączenia się za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |
| usePeerVerification | Określa, czy należy zweryfikować tożsamość serwera podczas łączenia za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |

**Przykład:**

```json
{
    "name": "EloquaLinkedService",
    "properties": {
        "type": "Eloqua",
        "typeProperties": {
            "endpoint" : "<base URL e.g. xxx.xxx.eloqua.com>",
            "username" : "<site name>\\<user name e.g. Eloqua\\Alice>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Eloqua firmy Oracle.

Aby skopiować dane z programu Oracle Eloqua, ustaw właściwość Type zestawu danych na **EloquaObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **EloquaObject** | Tak |
| tableName | Nazwa tabeli. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przykład**

```json
{
    "name": "EloquaDataset",
    "properties": {
        "type": "EloquaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Eloqua linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Eloqua firmy Oracle.

### <a name="eloqua-as-source"></a>Eloqua jako źródło

Aby skopiować dane z programu Oracle Eloqua, ustaw typ źródła w działaniu Copy na **EloquaSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **EloquaSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM Accounts"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromEloqua",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Eloqua input dataset name>",
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
                "type": "EloquaSource",
                "query": "SELECT * FROM Accounts"
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
Listę obsługiwanych danych przechowywanych przez Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
