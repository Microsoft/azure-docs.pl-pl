---
title: Kopiowanie danych z Oracle Eloqua (Wersja zapoznawcza)
description: Dowiedz się, jak kopiować dane z oracle Eloqua do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
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
ms.openlocfilehash: 3ea9d7baf427e70df349c926a0b6b8b72ba82293
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416851"
---
# <a name="copy-data-from-oracle-eloqua-using-azure-data-factory-preview"></a>Kopiowanie danych z oracle eloqua przy użyciu usługi Azure Data Factory (wersja zapoznawcza)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z oracle Eloqua. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w wersji zapoznawczej. Możesz go wypróbować i przekazać opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Oracle Eloqua jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane z oracle Eloqua można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

Usługa Azure Data Factory udostępnia wbudowany sterownik, aby włączyć łączność, w związku z tym nie trzeba ręcznie zainstalować żadnego sterownika przy użyciu tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Data Factory specyficznych dla łącznika Oracle Eloqua.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej Oracle Eloqua:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **Eloqua** | Tak |
| endpoint | Punkt końcowy serwera Eloqua. Eloqua obsługuje wiele centrów danych, aby https://login.eloqua.com określić punkt końcowy, zaloguj się za pomocą poświadczeń, a następnie skopiuj **podstawową** część adresu URL z przekierowanego adresu URL ze wzorcem `xxx.xxx.eloqua.com`. | Tak |
| nazwa użytkownika | Nazwa witryny i nazwa użytkownika twojego konta Eloqua w formie: `SiteName\Username` np. `Eloqua\Alice`  | Tak |
| hasło | Hasło odpowiadające nazwie użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartością domyślną jest true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta w certyfikacie serwera ma być zgodna z nazwą hosta serwera podczas łączenia się za korzystając z protokołu TLS. Wartością domyślną jest true.  | Nie |
| usePeerVerification | Określa, czy podczas nawiązywania połączenia zawersyfikowany przez TLS ma być weryfikowany przez tożsamość serwera. Wartością domyślną jest true.  | Nie |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Oracle Eloqua.

Aby skopiować dane z oracle Eloqua, ustaw właściwość typu zestawu danych na **EloquaObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **EloquaObject** | Tak |
| tableName | Nazwa tabeli. | Nie (jeśli określono "zapytanie" w źródle działania) |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Oracle Eloqua.

### <a name="eloqua-as-source"></a>Eloqua jako źródło

Aby skopiować dane z oracle Eloqua, ustaw typ źródła w działaniu kopiowania na **EloquaSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **EloquaSource** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"SELECT * FROM Accounts"`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

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

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę obsługiwanych danych przechowywanych przez usługę Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
