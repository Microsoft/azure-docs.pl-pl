---
title: Kopiowanie danych z magazynu sap business za pośrednictwem Open Hub
description: Dowiedz się, jak kopiować dane z magazynu SAP Business Warehouse (BW) za pośrednictwem usługi Open Hub do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
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
ms.date: 03/24/2020
ms.openlocfilehash: ff3b4799f42e85ad3df62ef18469a26120ae3021
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418086"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopiowanie danych z magazynu SAP Business przez Open Hub przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z magazynu biznesowego SAP (BW) za pośrednictwem usługi Open Hub. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

>[!TIP]
>Aby dowiedzieć się, że usługa ADF jest obsługiwana w scenariuszu integracji danych SAP, zobacz [integracja danych SAP przy użyciu oficjalnych dokumentów usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ze szczegółowym wprowadzeniem, porównaniem i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten magazyn biznesowy SAP za pośrednictwem łącznika Open Hub jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane z magazynu SAP Business store można kopiować za pośrednictwem usługi Open Hub do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze SAP Business Warehouse Open Hub obsługuje:

- SAP Business Warehouse **w wersji 7.01 lub nowszej (w niedawnym stosie pakietów pomocy technicznej SAP wydanym po roku 2015).**
- Kopiowanie danych za pośrednictwem tabeli lokalnej Open Hub Destination, która pod spodem może być DSO, InfoCube, MultiProvider, DataSource itp.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.
- Łączenie się z serwerem aplikacji.

## <a name="sap-bw-open-hub-integration"></a>Integracja z otwartym koncentratorem SAP BW 

[Usługa SAP BW Open Hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) to skuteczny sposób wyodrębniania danych z SAP BW. Na poniższym diagramie przedstawiono jeden z typowych przepływów, które klienci mają w swoim systemie SAP, w którym to przypadku przepływy danych z SAP ECC -> PSA -> DSO -> Cube.

SAP BW Open Hub Destination (OHD) definiuje obiekt docelowy, do którego są przekazywane dane SAP. Wszystkie obiekty obsługiwane przez proces transferu danych SAP (DTP) mogą być używane jako źródła danych open hub, na przykład DSO, InfoCube, DataSource itp. Open Hub Destination type — gdzie są przechowywane przekazywane dane — mogą być tabelami bazy danych (lokalnymi lub zdalnymi) i plikami płaskimi. Ten łącznik SAP BW Open Hub obsługuje kopiowanie danych z lokalnej tabeli OHD w BW. W przypadku, gdy używasz innych typów, można bezpośrednio połączyć się z bazą danych lub systemem plików przy użyciu innych łączników.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Przepływ ekstrakcji delta

Łącznik ADF SAP BW Open Hub `excludeLastRequest` oferuje `baseRequestId` dwie opcjonalne właściwości: i które mogą być używane do obsługi obciążenia delta z Open Hub. 

- **excludeLastRequestId**: Czy wykluczyć rekordy ostatniego żądania. Wartość domyślna jest true. 
- **baseRequestId:** Identyfikator żądania obciążenia różnicowego. Po ustawieniu zostaną pobrane tylko dane z requestId większym niż wartość tej właściwości. 

Ogólnie rzecz biorąc wyodrębnianie z sap InfoProviders do usługi Azure Data Factory (ADF) składa się z 2 kroków: 

1. **Proces transferu danych SAP BW (DTP)** Ten krok kopiuje dane z programu SAP BW InfoProvider do tabeli SAP BW Open Hub 

1. **Kopiowanie danych usługi ADF** W tym kroku tabela Otwórz koncentrator jest odczytywana przez złącze ADF 

![Przepływ ekstrakcji delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

W pierwszym kroku jest wykonywany protokół DTP. Każde wykonanie tworzy nowy identyfikator żądania SAP. Identyfikator żądania jest przechowywany w tabeli Otwórz koncentrator, a następnie używany przez łącznik ADF do identyfikacji delta. Dwa kroki są uruchamiane asynchronicznie: protokół DTP jest wyzwalany przez sap, a kopia danych usługi ADF jest wyzwalana za pośrednictwem podajnika ADF. 

Domyślnie podajnik ADF nie odczytuje najnowszej różnicy z tabeli Otwórz centrum (opcja "wyklucz ostatnie żądanie" jest spełniona). W tym przypadku dane w podajniku ADF nie są w 100% aktualne z danymi w tabeli Otwórz koncentrator (brakuje ostatniej różnicy). W zamian ta procedura zapewnia, że żadne wiersze nie gubią się spowodowane przez ekstrakcję asynchroniza. Działa dobrze nawet wtedy, gdy podajnik ADF odczytuje tabelę Otwórz centrum, podczas gdy protokół DTP nadal zapisuje się w tej samej tabeli. 

Zazwyczaj przechowujesz maksymalny skopiowany identyfikator żądania w ostatnim uruchomieniu przez podajnikU ADF w magazynie danych przemieszczania (takich jak Azure Blob na powyższym diagramie). W związku z tym to samo żądanie nie jest odczytywane po raz drugi przez podajnik ADF w kolejnym uruchomieniu. Tymczasem należy pamiętać, że dane nie są automatycznie usuwane z tabeli Otwórz centrum.

W przypadku prawidłowej obsługi różnicowej nie można mieć identyfikatorów żądań z różnych DTPs w tej samej tabeli Open Hub. W związku z tym nie należy tworzyć więcej niż jeden DTP dla każdego open hub przeznaczenia (OHD). Gdy potrzebujesz pełne i Delta wyodrębniania z tego samego InfoProvider, należy utworzyć dwa OHDs dla tego samego InfoProvider. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP Business Warehouse Open Hub, należy:

- Skonfiguruj własny środowisko wykonawcze integracji w wersji 3.13 lub wyższej. Zobacz [self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.

- Pobierz **64-bitowy [program SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html) ** ze strony internetowej firmy SAP i zainstaluj go na komputerze podczerwony z własnym hostem. Podczas instalacji w oknie kroki konfiguracji opcjonalne upewnij się, że wybrano opcję **Zainstaluj zestawy do plików GAC,** jak pokazano na poniższej ilustracji. 

    ![Instalowanie łącznika SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Użytkownik SAP używany w łączniku BW fabryki danych musi mieć następujące uprawnienia: 

    - Autoryzacja dla RFC i SAP BW. 
    - Uprawnienia do "Execute" Działania obiektu autoryzacji "S_SDSAUTH".

- Utwórz typ miejsca docelowego SAP Open Hub jako **tabela bazy danych** z zaznaczoną opcją "Klucz techniczny".  Zaleca się również pozostawienie usuwania danych z tabeli jako niezaznaczone, chociaż nie jest to wymagane. Wykorzystaj DTP (bezpośrednio wykonać lub zintegrować z istniejącym łańcuchem procesów) do wyładowywania danych z obiektu źródłowego (takiego jak moduł), który został wybrany do otwartej tabeli docelowej koncentratora.

## <a name="getting-started"></a>Wprowadzenie

> [!TIP]
>
> Aby zapoznać się z instruktażem dotyczący korzystania z łącznika SAP BW Open Hub, zobacz [Ładowanie danych z magazynu sap business warehouse (BW) przy użyciu usługi Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach podano szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej SAP Business Warehouse Open Hub:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **SapOpenHub** | Tak |
| serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP BW. | Tak |
| systemNumer | Numer systemu SAP BW.<br/>Dozwolona wartość: dwucyfrowy numer dziesiętny reprezentowany jako ciąg. | Tak |
| clientId | Identyfikator klienta klienta w systemie SAP W.<br/>Dozwolona wartość: trzycyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak |
| language | Język używany przez system SAP. | Nie (domyślna wartość to **PL)**|
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Tak |
| hasło | Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Środowisko wykonawcze integracji hostowane samodzielnie jest wymagane, jak wspomniano w [wymaganiach wstępnych.](#prerequisites) |Tak |

**Przykład:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP BW Open Hub.

Aby skopiować dane z i do usługi SAP BW Open Hub, ustaw właściwość typu zestawu danych na **SapOpenHubTable**. Obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **SapOpenHubTable**.  | Tak |
| openHubDestinationName | Nazwa miejsca docelowego otwarte centrum do skopiowania danych. | Tak |

Jeśli ustawienia `excludeLastRequest` i `baseRequestId` w zestawie danych, nadal jest obsługiwany jako — jest, podczas gdy zaleca się użycie nowego modelu w źródle aktywności w przyszłości.

**Przykład:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub jako źródło

Aby skopiować dane z usługi SAP BW Open Hub, w sekcji **źródła** działania kopiowania obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **SapOpenHubSource**. | Tak |
| wykluczEńLastRequest | Czy wykluczyć rekordy ostatniego żądania. | Nie (wartość domyślna jest **prawdziwa)** |
| podstawowychRequestId | Identyfikator żądania ładowania różnicowego. Po ustawieniu zostaną pobrane tylko dane z requestId **większym niż** wartość tej właściwości.  | Nie |

>[!TIP]
>Jeśli tabela Open Hub zawiera tylko dane generowane przez identyfikator pojedynczego żądania, na przykład zawsze należy wykonać pełne obciążenie i zastąpić istniejące dane w tabeli lub uruchomić DTP tylko raz do testu, pamiętaj, aby odznaczyć opcję "excludeLastRequest" w celu skopiowania danych.

Aby przyspieszyć ładowanie danych, można [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawić na działania kopiowania, aby załadować dane z SAP BW Open Hub równolegle. Na przykład jeśli `parallelCopies` ustawisz cztery, data factory jednocześnie wykonuje cztery wywołania RFC, a każde wywołanie RFC pobiera część danych z tabeli SAP BW Open Hub na partycje według identyfikatora żądania DTP i identyfikatora pakietu. Ma to zastosowanie, gdy liczba unikatowych identyfikatorów żądania DTP + `parallelCopies`identyfikatora pakietu jest większa niż wartość . Podczas kopiowania danych do magazynu danych opartych na plikach zaleca się również zapisywanie w folderze jako wielu plików (określanie tylko nazwy folderu), w którym to przypadku wydajność jest lepsza niż zapisywanie do jednego pliku.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapowanie typu danych dla sap BW Open Hub

Podczas kopiowania danych z usługi SAP BW Open Hub używane są następujące mapowania z typów danych SAP BW do tymczasowych typów danych usługi Azure Data Factory. Zobacz [Mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ SAP ABAP | Tymczasowy typ danych fabryki danych |
|:--- |:--- |
| C (ciąg) | Ciąg |
| I (całkowita) | Int32 |
| F (Spławik) | Double |
| D (Data) | Ciąg |
| T (Czas) | Ciąg |
| P (BCD Pakowane, Waluta, Dziesiętne, Ilość) | Wartość dziesiętna |
| N (Numc) | Ciąg |
| X (binarny i surowy) | Ciąg |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

**Objawy:** Jeśli korzystasz z sap BW na HANA i obserwować tylko podzbiór danych jest kopiowany za pomocą działania kopiowania ADF (1 milion wierszy), możliwa przyczyna jest, aby włączyć opcję "SAP HANA Execution" w DTP, w którym to przypadku usługa ADF może pobrać tylko pierwszą partię danych.

**Rozdzielczość:** Wyłącz opcję "SAP HANA Execution" w DTP, ponownie przetwarzaj dane, a następnie spróbuj ponownie wykonać działanie kopiowania.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
