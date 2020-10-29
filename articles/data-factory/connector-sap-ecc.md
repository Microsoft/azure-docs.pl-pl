---
title: Kopiowanie danych z systemu SAP ECC
description: Informacje o kopiowaniu danych z systemu SAP ECC do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: 1f3ab61c6030c2871356f494db228711305e5466
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901585"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopiowanie danych z systemu SAP ECC przy użyciu Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z oprogramowania SAP Enterprise Central Component (ECC). Aby uzyskać więcej informacji, zobacz [Omówienie działania kopiowania](copy-activity-overview.md).

>[!TIP]
>Aby poznać ogólną pomoc techniczną w scenariuszu integracji danych SAP, zobacz [integracja danych SAP przy użyciu Azure Data Factory oficjalny dokument](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ze szczegółowymi informacjami na temat poszczególnych łączników SAP, comparsion i wskazówek.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik SAP ECC jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z protokołu SAP ECC można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W przypadku tego łącznika SAP ECC obsługiwane są następujące rozwiązania:

- Kopiowanie danych z systemu SAP ECC w oprogramowaniu SAP NetWeaver w wersji 7,0 lub nowszej.
- Kopiowanie danych z dowolnych obiektów udostępnianych przez usługi SAP ECC OData, takie jak:

  - Tabele lub widoki SAP.
  - Obiekty interfejsu programowania aplikacji biznesowej [BAPI].
  - Wyodrębnianie danych.
  - Dane lub dokumenty pośrednie (IDOCs) wysyłane do integracji procesów SAP (PI), które można odbierać jako OData za pośrednictwem kart względnych.

- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

Wersja 7,0 lub nowsza odnosi się do wersji SAP NetWeaver zamiast wersji SAP ECC. Na przykład system SAP ECC 6,0 EHP 7 ogólnie ma wersję NetWeaver >= 7,4. Jeśli nie masz pewności o Twoim środowisku, poniżej przedstawiono kroki umożliwiające potwierdzenie wersji z systemu SAP:

1. Połącz się z systemem SAP przy użyciu graficznego interfejsu użytkownika SAP. 
2. Przejdź do **System** pozycji  ->  **stan** systemu. 
3. Sprawdź wersję SAP_BASIS, upewnij się, że jest równa lub większa niż 701.  
      ![Sprawdź SAP_BASIS](./media/connector-sap-table/sap-basis.png)

>[!TIP]
>Aby skopiować dane z systemu SAP ECC za pośrednictwem tabeli lub widoku SAP, użyj łącznika [tabeli SAP](connector-sap-table.md) , który jest szybszy i bardziej skalowalny.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP ECC, należy uwidocznić jednostki SAP ECC za pośrednictwem usług OData za pośrednictwem bramy SAP. Więcej szczegółów:

- **Skonfiguruj bramę SAP Gateway** . W przypadku serwerów z systemem SAP NetWeaver w wersji nowszej niż 7,4 Brama SAP jest już zainstalowana. W przypadku wcześniejszych wersji należy zainstalować osadzoną bramę SAP lub system centrów SAP Gateway przed udostępnieniem danych SAP ECC za pomocą usług OData. Aby skonfigurować bramę SAP, zobacz [Przewodnik instalacji](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktywuj i skonfiguruj usługę SAP OData** . Usługę OData można aktywować za pomocą TCODE SICF w ciągu kilku sekund. Można również skonfigurować, które obiekty muszą być uwidocznione. Aby uzyskać więcej informacji, zobacz [wskazówki krok po kroku](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika SAP ECC.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi SAP ECC są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| `type` | `type`Właściwość musi być ustawiona na wartość `SapEcc` . | Tak |
| `url` | Adres URL usługi SAP ECC OData. | Tak |
| `username` | Nazwa użytkownika używana do nawiązywania połączenia z funkcją SAP ECC. | Nie |
| `password` | Hasło w postaci zwykłego tekstu używane do nawiązywania połączenia z funkcją SAP ECC. | Nie |
| `connectVia` | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie określisz środowiska uruchomieniowego, zostanie użyta domyślna usługa Azure Integration Runtime. | Nie |

### <a name="example"></a>Przykład

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z pełną listą sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [zestawy danych](concepts-datasets-linked-services.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP ECC.

Aby skopiować dane z protokołu SAP ECC, ustaw `type` Właściwość zestawu danych na `SapEccResource` .

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| `path` | Ścieżka jednostki usługi SAP ECC OData. | Tak |

### <a name="example"></a>Przykład

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby zapoznać się z pełną listą sekcji i właściwości dostępnych do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez źródło SAP ECC.

### <a name="sap-ecc-as-a-source"></a>System SAP ECC jako źródło

Aby skopiować dane z protokołu SAP ECC, należy ustawić `type` Właściwość w `source` sekcji działania kopiowania na `SapEccSource` .

W sekcji działania kopiowania są obsługiwane następujące właściwości `source` :

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| `type` | `type`Właściwość sekcji działania kopiowania `source` musi być ustawiona na wartość `SapEccSource` . | Tak |
| `query` | Opcje zapytania OData służące do filtrowania danych. Na przykład:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Łącznik SAP ECC kopiuje dane ze połączonego adresu URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Aby uzyskać więcej informacji, zobacz [składniki URL usługi OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |
| `sapDataColumnDelimiter` | Pojedynczy znak, który jest używany jako ogranicznik przeszedł do protokołu SAP RFC w celu podzielenia danych wyjściowych. | Nie |
| `httpRequestTimeout` | Limit czasu (wartość **TimeSpan** ) żądania HTTP w celu uzyskania odpowiedzi. Ta wartość jest przekroczeniem limitu czasu w celu uzyskania odpowiedzi, a nie limitu czasu odczytu danych odpowiedzi. Jeśli nie zostanie określony, wartość domyślna to **00:30:00** (30 minut). | Nie |

### <a name="example"></a>Przykład

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mapowania typu danych dla SAP ECC

Podczas kopiowania danych z systemu SAP ECC następujące mapowania są używane z typów danych OData dla danych SAP ECC do Azure Data Factory pośrednich typów danych. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych OData | Data Factory typ danych pośrednich |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Złożone typy danych nie są obecnie obsługiwane.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
