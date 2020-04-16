---
title: Kopiowanie danych z tabeli SAP
description: Dowiedz się, jak skopiować dane z tabeli SAP do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: d96b2b1f8465132549c59ac5555adf99e7758a3b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415233"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopiowanie danych z tabeli SAP przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z tabeli SAP. Aby uzyskać więcej informacji, zobacz [Omówienie działania kopiowania](copy-activity-overview.md).

>[!TIP]
>Aby dowiedzieć się, że usługa ADF jest obsługiwana w scenariuszu integracji danych SAP, zobacz [integracja danych SAP przy użyciu oficjalnych dokumentów usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ze szczegółowym wprowadzeniem, porównaniem i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik tabeli SAP jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z tabeli SAP do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła lub pochłaniacze przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik tabeli SAP obsługuje:

- Kopiowanie danych z tabeli SAP w:

  - Sap ERP Central Component (SAP ECC) w wersji 7.01 lub nowszej (w niedawnym stosie pakietów pomocy technicznej SAP wydany po 2015).
  - SAP Business Warehouse (SAP BW) w wersji 7.01 lub nowszej (w niedawnym stosie pakietów pomocy technicznej SAP wydanym po 2015 r.).
  - SAP S/4HANA.
  - Inne produkty w pakiecie SAP Business Suite w wersji 7.01 lub nowszej (w niedawnym stosie pakietów pomocy technicznej SAP wydanym po 2015 r.).

- Kopiowanie danych zarówno z przezroczystej tabeli SAP, tabeli puli, tabeli klastrowej i widoku.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego lub bezpiecznej komunikacji sieciowej (SNC), jeśli skonfigurowano snc.
- Łączenie się z serwerem aplikacji SAP lub serwerem komunikatów SAP.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika tabeli SAP, należy:

- Konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie (wersja 3.17 lub nowsza). Aby uzyskać więcej informacji, zobacz [Tworzenie i konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie](create-self-hosted-integration-runtime.md).

- Pobierz 64-bitowy [łącznik SAP dla firmy Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) ze strony internetowej firmy SAP i zainstaluj go na komputerze uruchamianym integracji hostowanego przez użytkownika. Podczas instalacji upewnij się, że w oknie **Kroki konfiguracji opcjonalnej** zostały wybrane opcja **Zainstaluj zestawy do gac.**

  ![Instalowanie łącznika SAP dla platformy .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Użytkownik SAP, który jest używany w łączniku tabeli SAP fabryki danych musi mieć następujące uprawnienia:

  - Autoryzacja używania miejsc docelowych wywołania funkcji zdalnych (RFC).
  - Uprawnienia do wykonywania działania obiektu autoryzacji S_SDSAUTH.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika tabeli SAP.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej SAP BW Open Hub:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `type` | Właściwość `type` musi być `SapTable`ustawiona na . | Tak |
| `server` | Nazwa serwera, na którym znajduje się wystąpienie SAP.<br/>Służy do łączenia się z serwerem aplikacji SAP. | Nie |
| `systemNumber` | Numer systemowy systemu SAP.<br/>Służy do łączenia się z serwerem aplikacji SAP.<br/>Dozwolona wartość: Dwucyfrowy numer dziesiętny reprezentowany jako ciąg. | Nie |
| `messageServer` | Nazwa hosta serwera komunikatów SAP.<br/>Służy do łączenia się z serwerem komunikatów SAP. | Nie |
| `messageServerService` | Nazwa usługi lub numer portu serwera wiadomości.<br/>Służy do łączenia się z serwerem komunikatów SAP. | Nie |
| `systemId` | Identyfikator systemu SAP, w którym znajduje się tabela.<br/>Służy do łączenia się z serwerem komunikatów SAP. | Nie |
| `logonGroup` | Grupa logowania dla systemu SAP.<br/>Służy do łączenia się z serwerem komunikatów SAP. | Nie |
| `clientId` | Identyfikator klienta w systemie SAP.<br/>Dozwolona wartość: Trzycyfrowy numer dziesiętny reprezentowany jako ciąg. | Tak |
| `language` | Język używany przez system SAP.<br/>Wartością `EN`domyślną jest .| Nie |
| `userName` | Nazwa użytkownika, który ma dostęp do serwera SAP. | Tak |
| `password` | Hasło dla użytkownika. Oznacz to pole `SecureString` typem, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| `sncMode` | Wskaźnik aktywacji SNC, aby uzyskać dostęp do serwera SAP, na którym znajduje się tabela.<br/>Użyj, jeśli chcesz użyć SNC, aby połączyć się z serwerem SAP.<br/>Dozwolone wartości `0` to (wyłączone, domyślne) lub `1` (włączone). | Nie |
| `sncMyName` | Nazwa SNC inicjatora, aby uzyskać dostęp do serwera SAP, na którym znajduje się tabela.<br/>Ma zastosowanie, gdy `sncMode` jest włączony. | Nie |
| `sncPartnerName` | Nazwa SNC partnera komunikacyjnego, aby uzyskać dostęp do serwera SAP, na którym znajduje się tabela.<br/>Ma zastosowanie, gdy `sncMode` jest włączony. | Nie |
| `sncLibraryPath` | Biblioteka zewnętrznego produktu zabezpieczeń, aby uzyskać dostęp do serwera SAP, na którym znajduje się tabela.<br/>Ma zastosowanie, gdy `sncMode` jest włączony. | Nie |
| `sncQop` | Poziom jakości ochrony SNC do zastosowania.<br/>Ma zastosowanie, gdy `sncMode` jest włączone. <br/>Dozwolone wartości `1` to `2` (uwierzytelnianie), `3` (integralność), (prywatność), `8` (domyślne), `9` (maksymalna). | Nie |
| `connectVia` | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Wymagane jest środowisko uruchomieniowe integracji hostowanego samodzielnie, jak wspomniano wcześniej w [wymaganiach wstępnych.](#prerequisites) |Tak |

**Przykład 1: Łączenie się z serwerem aplikacji SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
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

### <a name="example-2-connect-to-an-sap-message-server"></a>Przykład 2: Łączenie się z serwerem komunikatów SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Przykład 3: Połącz za pomocą SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości do definiowania zestawów danych, zobacz [Zestawy danych](concepts-datasets-linked-services.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli SAP.

Aby skopiować dane z i do połączonej usługi SAP BW Open Hub, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `type` | Właściwość `type` musi być `SapTableResource`ustawiona na . | Tak |
| `tableName` | Nazwa tabeli SAP do kopiowania danych. | Tak |

### <a name="example"></a>Przykład

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez źródło tabeli SAP.

### <a name="sap-table-as-source"></a>Tabela SAP jako źródło

Aby skopiować dane z tabeli SAP, obsługiwane są następujące właściwości:

| Właściwość                         | Opis                                                  | Wymagany |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Właściwość `type` musi być `SapTableSource`ustawiona na .         | Tak      |
| `rowCount`                         | Liczba wierszy do pobrania.                              | Nie       |
| `rfcTableFields`                   | Pola (kolumny) do skopiowania z tabeli SAP. Na przykład `column0, column1`. | Nie       |
| `rfcTableOptions`                  | Opcje filtrowania wierszy w tabeli SAP. Na przykład `COLUMN0 EQ 'SOMEVALUE'`. Zobacz też tabelę operatora zapytań SAP w dalszej części tego artykułu. | Nie       |
| `customRfcReadTableFunctionModule` | Niestandardowy moduł funkcji RFC, który może służyć do odczytywania danych z tabeli SAP.<br>Niestandardowy moduł funkcji RFC służy do definiowania sposobu pobierania danych z systemu SAP i zwracania do fabryki danych. Moduł funkcji niestandardowej musi mieć zaimplementowany interfejs (import, `/SAPDS/RFC_READ_TABLE2`eksport, tabele), który jest podobny do , który jest domyślnym interfejsem używanym przez fabrykę danych. | Nie       |
| `partitionOption`                  | Mechanizm partycji do odczytu z tabeli SAP. Obsługiwane opcje obejmują: <ul><li>`None`</li><li>`PartitionOnInt`(normalne wartości całkowite lub całkowite z zerowym dopełnieniem `0000012345`po lewej stronie, takie jak)</li><li>`PartitionOnCalendarYear`(4 cyfry w formacie "YYYY")</li><li>`PartitionOnCalendarMonth`(6 cyfr w formacie "RRRRM")</li><li>`PartitionOnCalendarDate`(8 cyfr w formacie "RRRRMDD")</li></ul> | Nie       |
| `partitionColumnName`              | Nazwa kolumny używanej do partycjonowania danych.                | Nie       |
| `partitionUpperBound`              | Maksymalna wartość kolumny określonej w `partitionColumnName` tym będzie używana do kontynuowania partycjonowania. | Nie       |
| `partitionLowerBound`              | Minimalna wartość kolumny określonej `partitionColumnName` w tym będzie używana do kontynuowania partycjonowania. (Uwaga: `partitionLowerBound` nie może być "0", gdy opcja partycji jest) `PartitionOnInt` | Nie       |
| `maxPartitionsNumber`              | Maksymalna liczba partycji do podzielenia danych.     | Nie       |

>[!TIP]
>Jeśli tabela SAP ma dużą ilość danych, takich jak `partitionOption` `partitionSetting` kilka miliardów wierszy, użyj i podzielić dane na mniejsze partycje. W takim przypadku dane są odczytywane na partycję, a każda partycja danych jest pobierana z serwera SAP za pośrednictwem jednego wywołania RFC.<br/>
<br/>
>`partitionOnInt` Jako `partitionOption` przykład obliczana jest liczba wierszy w każdej partycji za pomocą następującego `partitionLowerBound`wzoru: (suma wierszy przypadających między`maxPartitionsNumber` `partitionUpperBound` i )/ .<br/>
<br/>
>Aby załadować partycje danych równolegle, aby przyspieszyć kopiowanie, stopień równoległy jest kontrolowany [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) przez ustawienie działania kopiowania. Na przykład jeśli `parallelCopies` ustawisz cztery, data factory jednocześnie generuje i uruchamia cztery kwerendy na podstawie określonej opcji partycji i ustawień, a każda kwerenda pobiera część danych z tabeli SAP. Zdecydowanie zaleca `maxPartitionsNumber` się dokonanie wielokrotności `parallelCopies` wartości właściwości. Podczas kopiowania danych do magazynu danych opartych na plikach zaleca się również zapisywanie w folderze jako wielu plików (określanie tylko nazwy folderu), w którym to przypadku wydajność jest lepsza niż zapisywanie do jednego pliku.

W `rfcTableOptions`programie można użyć następujących typowych operatorów zapytań SAP do filtrowania wierszy:

| Operator | Opis |
| :------- | :------- |
| `EQ` | Równe |
| `NE` | Różne od |
| `LT` | Mniejsze niż |
| `LE` | Mniejsze niż lub równe |
| `GT` | Większe niż |
| `GE` | Większe niż lub równe |
| `IN` | Podobnie jak w`TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Podobnie jak w`LIKE 'Emma%'` |

### <a name="example"></a>Przykład

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapowania typów danych dla tabeli SAP

Podczas kopiowania danych z tabeli SAP, następujące mapowania są używane z typów danych tabeli SAP do tymczasowego typu danych usługi Azure Data Factory. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

| Typ SAP ABAP | Tymczasowy typ danych data factory |
|:--- |:--- |
| `C`(Ciąg) | `String` |
| `I`(Całkowita( 100) | `Int32` |
| `F`(Pływak) | `Double` |
| `D`(Data) | `String` |
| `T`(Czas) | `String` |
| `P`(BcD Pakowane, Waluta, Dziesiętne, Ilość) | `Decimal` |
| `N`(Numeryczne) | `String` |
| `X`(Binarne i surowe) | `String` |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
