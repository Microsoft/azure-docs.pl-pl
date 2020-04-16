---
title: Kopiowanie danych z sap BW
description: Dowiedz się, jak kopiować dane z magazynu SAP Business Do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
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
ms.openlocfilehash: 2f8406038be10ba3bdc207bf447fecb86a376fe8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418069"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopiowanie danych z magazynu SAP Business przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktualna wersja](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z magazynu biznesowego SAP (BW). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

>[!TIP]
>Aby dowiedzieć się, że usługa ADF jest obsługiwana w scenariuszu integracji danych SAP, zobacz [integracja danych SAP przy użyciu oficjalnych dokumentów usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ze szczegółowym wprowadzeniem, porównaniem i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik sap business warehouse jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z magazynu SAP Business do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze SAP Business Warehouse obsługuje:

- SAP Business Warehouse **w wersji 7.x**.
- Kopiowanie danych z **InfoCubes i QueryCubes** (w tym zapytań BEx) przy użyciu zapytań MDX.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP Business Warehouse, należy:

- Konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie. Zobacz [self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.
- Zainstaluj **bibliotekę SAP NetWeaver** na komputerze integration runtime. Bibliotekę SAP Netweaver można pobrać od administratora SAP lub bezpośrednio z [centrum pobierania oprogramowania SAP.](https://support.sap.com/swdc) Wyszukaj **#1025361 SAP Note,** aby uzyskać lokalizację pobierania najnowszej wersji. Upewnij się, że wybierzesz **64-bitową** bibliotekę SAP NetWeaver, która pasuje do instalacji środowiska wykonawczego integracji. Następnie zainstaluj wszystkie pliki zawarte w SAP NetWeaver RFC SDK zgodnie z SAP Note. Biblioteka SAP NetWeaver jest również uwzględniona w instalacji narzędzi klienckich SAP.

>[!TIP]
>Aby rozwiązać problem z łącznością z sap BW, upewnij się, że:
>- Wszystkie biblioteki zależności wyodrębnione z SDK NetWeaver RFC są na miejscu w folderze %windir%\system32. Zwykle ma icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Potrzebne porty używane do łączenia się z sap server są włączone na komputerze z własnym hostem podczerwony, które zwykle są portami 3300 i 3201.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach podano szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika sap business warehouse.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej SAP Business Warehouse (BW):

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **SapBw** | Tak |
| serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP BW. | Tak |
| systemNumer | Numer systemu SAP BW.<br/>Dozwolona wartość: dwucyfrowy numer dziesiętny reprezentowany jako ciąg. | Tak |
| clientId | Identyfikator klienta klienta w systemie SAP W.<br/>Dozwolona wartość: trzycyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak |
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Tak |
| hasło | Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Środowisko wykonawcze integracji hostowane samodzielnie jest wymagane, jak wspomniano w [wymaganiach wstępnych.](#prerequisites) |Tak |

**Przykład:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP BW.

Aby skopiować dane z sap BW, ustaw właściwość typu zestawu danych na **SapBwCube**. Chociaż nie istnieją żadne właściwości specyficzne dla typu obsługiwane dla zestawu danych SAP BW typu RelationalTable.

**Przykład:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz `RelationalTable` wpisanego zestawu danych, jest on nadal obsługiwany w stanie gotowym do użycia, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW jako źródło

Aby skopiować dane z sap BW, w sekcji **źródła** działania kopiowania obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **SapBwSource** | Tak |
| query | Określa kwerendę MDX do odczytu danych z wystąpienia SAP BW. | Tak |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz `RelationalSource` wpisanego źródła, jest ono nadal obsługiwane w stanie as-is, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="data-type-mapping-for-sap-bw"></a>Mapowanie typu danych dla SAP BW

Podczas kopiowania danych z SAP BW, następujące mapowania są używane z typów danych SAP BW do usługi Azure Data Factory tymczasowych typów danych. Zobacz [Mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych SAP BW | Tymczasowy typ danych fabryki danych |
|:--- |:--- |
| ACCP (włask. | int |
| Char | Ciąg |
| Clnt | Ciąg |
| Curr | Wartość dziesiętna |
| CUKY (CUKY) | Ciąg |
| Grudnia | Wartość dziesiętna |
| protokół FLTP | Double |
| Int1 (wład. | Byte |
| Int2 (włas ie) | Int16 |
| Int4 (włas ie) | int |
| Lang | Ciąg |
| LCHR (LCHR) | Ciąg |
| LRAW (LRAW) | Bajt[] |
| Prec (prec) | Int16 |
| Quan | Wartość dziesiętna |
| Raw | Bajt[] |
| RAWSTROWANIE | Bajt[] |
| Ciąg | Ciąg |
| Jednostki | Ciąg |
| Dats | Ciąg |
| NuMC (numc) | Ciąg |
| TIMS (TIMS) | Ciąg |


## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
