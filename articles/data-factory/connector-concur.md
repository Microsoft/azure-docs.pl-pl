---
title: Kopiowanie danych z concur przy użyciu usługi Azure Data Factory (Wersja zapoznawcza)
description: Dowiedz się, jak skopiować dane z concur do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
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
ms.openlocfilehash: 6699178e514f4d25666305f3251e8eaf9d28e6dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417453"
---
# <a name="copy-data-from-concur-using-azure-data-factory-preview"></a>Kopiowanie danych z concur przy użyciu usługi Azure Data Factory (Wersja zapoznawcza)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z concur. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w wersji zapoznawczej. Możesz go wypróbować i przekazać nam swoją opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik concur jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z Concur do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

Usługa Azure Data Factory udostępnia wbudowany sterownik, aby włączyć łączność, w związku z tym nie trzeba ręcznie zainstalować żadnego sterownika przy użyciu tego łącznika.

> [!NOTE]
> Konto partnera nie jest obecnie obsługiwane.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika Concur.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej Concur:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **Concur** | Tak |
| clientId | Client_id aplikacji dostarczane przez Concur App Management.  | Tak |
| nazwa użytkownika | Nazwa użytkownika używana do uzyskiwania dostępu do usługi Concur.  | Tak |
| hasło | Hasło odpowiadające nazwie użytkownika podanej w polu nazwa_użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartością domyślną jest true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta w certyfikacie serwera ma być zgodna z nazwą hosta serwera podczas łączenia się za korzystając z protokołu TLS. Wartością domyślną jest true.  | Nie |
| usePeerVerification | Określa, czy podczas nawiązywania połączenia zawersyfikowany przez TLS ma być weryfikowany przez tożsamość serwera. Wartością domyślną jest true.  | Nie |

**Przykład:**

```json
{
    "name": "ConcurLinkedService",
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "clientId" : "<clientId>",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Concur.

Aby skopiować dane z concur, ustaw właściwość typu zestawu danych na **ConcurObject**. W tym typie zestawu danych nie ma żadnej dodatkowej właściwości specyficznej dla typu. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **ConcurObject** | Tak |
| tableName | Nazwa tabeli. | Nie (jeśli określono "zapytanie" w źródle działania) |


**Przykład**

```json
{
    "name": "ConcurDataset",
    "properties": {
        "type": "ConcurObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Concur linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Concur.

### <a name="concursource-as-source"></a>ConcurSource jako źródło

Aby skopiować dane z concur, ustaw typ źródła w działaniu kopiowania na **ConcurSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **ConcurSource** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"SELECT * FROM Opportunities where Id = xxx "`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromConcur",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Concur input dataset name>",
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
                "type": "ConcurSource",
                "query": "SELECT * FROM Opportunities where Id = xxx"
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
