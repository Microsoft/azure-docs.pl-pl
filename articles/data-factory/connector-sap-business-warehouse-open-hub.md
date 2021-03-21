---
title: Kopiuj dane z programu SAP Business Warehouse za pośrednictwem usługi Open Hub
description: Informacje o kopiowaniu danych z programu SAP Business Warehouse (BW) za pośrednictwem usługi Open Hub do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2020
ms.openlocfilehash: b766ce248a3543ef3323e026d760e550a0e3dd75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386683"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopiowanie danych z programu SAP Business Warehouse za pośrednictwem usługi Open Hub przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z programu SAP Business Warehouse (BW) za pośrednictwem usługi Open Hub. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

>[!TIP]
>Aby poznać ogólną pomoc techniczną w scenariuszu integracji danych SAP, zobacz [integracja danych SAP przy użyciu Azure Data Factory oficjalny dokument](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ze szczegółowymi informacjami na temat poszczególnych łączników SAP, comparsion i wskazówek.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten magazyn SAP Business Warehouse za pośrednictwem łącznika Open Hub jest obsługiwany przez następujące działania:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z programu SAP Business Warehouse można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W ramach tego łącznika centrum danych programu SAP Business Warehouse obsługuje następujące rozwiązania:

- SAP Business Warehouse **w wersji 7,01 lub nowszej (w ostatnim stosie pakietów pomocy technicznej SAP wydanej po roku 2015)**. Ten łącznik nie obsługuje SAP BW/4HANA.
- Kopiowanie danych za pośrednictwem lokalnej tabeli docelowej centrum, która znajduje się poniżej, może być DSO, InfoCube, wieloelementowy, DataSource itd.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.
- Nawiązywanie połączenia z serwerem aplikacji SAP lub serwerem komunikatów SAP.
- Pobieranie danych za pomocą RFC.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Otwórz integrację z centrum 

[SAP BW otwieranie usługi centrum](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) jest wydajnym sposobem wyodrębnienia danych z SAP BW. Na poniższym diagramie przedstawiono jeden z typowych przepływów, które znajdują się w systemie SAP, w tym przypadku dane są przesyłane z modułu SAP ECC — > PSA-> module DSO->.

SAP BW Otwórz lokalizację docelową (OHD) określa miejsce docelowe, do którego są przekazywane dane SAP. Wszystkie obiekty obsługiwane przez proces Transfer danych SAP (DTP) mogą być używane jako otwarte źródła danych, na przykład DSO, InfoCube, DataSource itp. Typ miejsca docelowego typu Open Hub — miejsce przechowywania danych przekazywanych — może to być tabela bazy danych (lokalna lub zdalna) i pliki proste. Ten SAP BW otworzyć łącznik centrum obsługi kopiowania danych z lokalnej tabeli OHD w BW. W przypadku korzystania z innych typów można połączyć się bezpośrednio z bazą danych lub systemem plików przy użyciu innych łączników.

![SAP BW otworzyć Centrum](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Przepływ ekstrakcji różnicowej

Moduł ADF SAP BW otwarty łącznik centrum udostępnia dwie opcjonalne właściwości: `excludeLastRequest` i `baseRequestId` , które mogą być używane do obsługi obciążenia różnicowego z otwartego centrum. 

- **excludeLastRequestId**: określa, czy mają zostać wykluczone rekordy ostatniego żądania. Wartość domyślna to True. 
- **baseRequestId**: Identyfikator żądania dla ładowania różnicowego. Po jego ustawieniu zostaną pobrane tylko dane z identyfikatorem żądania większym niż wartość tej właściwości. 

Ogólnie, wyodrębnianie z oprogramowania SAP InfoProviders do Azure Data Factory (ADF) składa się z dwóch kroków: 

1. **Proces Transfer danych SAP BW (DTP)** Ten krok powoduje skopiowanie danych z SAP BW InfoProvider do SAP BW otwartej tabeli usługi Hub 

1. **Kopiowanie danych ADF** W tym kroku zostanie odczytana tabela "Otwórz Centrum" w ramach łącznika ADF 

![Przepływ ekstrakcji różnicowej](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

W pierwszym kroku jest wykonywane DTP. Każde wykonanie powoduje utworzenie nowego identyfikatora żądania SAP. Identyfikator żądania jest przechowywany w tabeli Open Hub, a następnie używany przez łącznik ADF do identyfikowania różnic. Dwa kroki są wykonywane asynchronicznie: DTP jest wyzwalany przez SAP, a kopiowanie danych ADF jest wyzwalane za pomocą ADF. 

Domyślnie moduł ADF nie odczytuje najnowszych różnic z otwartej tabeli centrum (opcja "Wyklucz ostatnie żądanie" ma wartość true). W takim przypadku dane w podajniku APD nie są aktualne na 100% z danymi w otwartej tabeli centrum (brakuje ostatniej delty). W przypadku powrotu ta procedura gwarantuje, że żadne wiersze nie zostaną utracone z powodu asynchronicznego wyodrębniania. Działa dobrze nawet wtedy, gdy moduł ADF odczytuje otwartą tabelę centrów, podczas gdy DTP nadal zapisuje w tej samej tabeli. 

Zwykle przechowujesz maksymalny identyfikator kopiowanego żądania w ostatnim przebiegu przez ADF w tymczasowym magazynie danych (takim jak obiekt blob platformy Azure na wyższym diagramie). W związku z tym to samo żądanie nie jest odczytywane po raz drugi przez ADF w kolejnym przebiegu. Należy zauważyć, że dane nie są automatycznie usuwane z otwartej tabeli centrum.

W przypadku prawidłowej obsługi różnic nie jest dozwolone posiadanie identyfikatorów żądań z różnych DTPs w tej samej otwartej tabeli. W związku z tym nie należy tworzyć więcej niż jednego DTP dla każdego otwartego miejsca docelowego (OHD). Gdy wymagana jest Ekstrakcja pełna i różnicowa z tego samego InfoProvider, należy utworzyć dwa OHDs dla tego samego InfoProvider. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika centrum danych SAP Business Warehouse, należy wykonać następujące czynności:

- Skonfiguruj własne Integration Runtime w wersji 3,13 lub nowszej. Aby uzyskać szczegółowe informacje, zobacz artykuł [Integration Runtime samodzielny](create-self-hosted-integration-runtime.md) .

- Pobierz **64-bitowy [Łącznik SAP .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html)** z witryny sieci Web SAP i zainstaluj go na samoobsługowej maszynie IR. W przypadku instalowania programu w oknie opcjonalne kroki instalacji upewnij się, że wybrano opcję **Zainstaluj zestawy do GAC** , jak pokazano na poniższej ilustracji. 

    ![Instalowanie łącznika SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Użytkownik SAP używany w łączniku Data Factory BW musi mieć następujące uprawnienia: 

    - Autoryzacja dla RFC i SAP BW. 
    - Uprawnienia do działania "Execute" obiektu autoryzacji "S_SDSAUTH".

- Utwórz typ docelowy typu "Open Hub" SAP jako **tabelę bazy danych** z zaznaczoną opcją "klucz techniczny".  Zaleca się również pozostawienie usunięcia danych z tabeli jako niezaznaczone, chociaż nie jest to wymagane. Użyj DTP (bezpośrednio wykonaj lub Zintegruj do istniejącego łańcucha procesów), aby wystawić dane z obiektu źródłowego (na przykład modułu), które zostały wybrane do tabeli docelowej centrum.

## <a name="getting-started"></a>Wprowadzenie

> [!TIP]
>
> Aby zapoznać się ze wskazówkami dotyczącymi używania łącznika SAP BW Open Hub, zobacz temat [ładowanie danych z oprogramowania SAP Business Warehouse (BW) przy użyciu Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla programu SAP Business Warehouse Open Hub Connector.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane w przypadku usługi SAP Business Warehouse Open Hub połączonej:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **SapOpenHub** | Tak |
| serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP BW. | Tak |
| systemNumber | Numer systemu SAP BW.<br/>Dozwolona wartość: dwucyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak |
| messageServer | Nazwa hosta serwera komunikatów SAP.<br/>Służy do nawiązywania połączenia z serwerem komunikatów SAP. | Nie |
| messageServerService | Nazwa usługi lub numer portu serwera komunikatów.<br/>Służy do nawiązywania połączenia z serwerem komunikatów SAP. | Nie |
| systemId | Identyfikator systemu SAP, w którym znajduje się tabela.<br/>Służy do nawiązywania połączenia z serwerem komunikatów SAP. | Nie |
| Logowanie | Grupa logowania dla systemu SAP.<br/>Służy do nawiązywania połączenia z serwerem komunikatów SAP. | Nie |
| clientId | Identyfikator klienta klienta w systemie SAP w.<br/>Dozwolona wartość: 3-cyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak |
| language | Język, którego używa System SAP. | Nie (wartość domyślna to **EN**)|
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Tak |
| hasło | Hasło użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano w [wymaganiach wstępnych](#prerequisites). |Tak |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez SAP BW otwartego centrum danych.

Aby skopiować dane z i do SAP BW Otwórz Centrum, ustaw właściwość Type zestawu danych na **SapOpenHubTable**. Obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **SapOpenHubTable**.  | Tak |
| openHubDestinationName | Nazwa miejsca docelowego typu Open Hub, z którego mają zostać skopiowane dane. | Tak |

Jeśli ustawienia `excludeLastRequest` i `baseRequestId` w zestawie danych, nadal są obsługiwane w stanie takim, w jakim jest zalecane użycie nowego modelu w źródle aktywności.

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez SAP BW Otwórz źródło centrum.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW otworzyć Centrum jako źródło

Aby skopiować dane z SAP BW Otwórz Centrum, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **SapOpenHubSource**. | Tak |
| excludeLastRequest | Określa, czy mają zostać wykluczone rekordy ostatniego żądania. | Nie (wartość domyślna to **true**) |
| baseRequestId | Identyfikator żądania dla ładowania różnicowego. Po jego ustawieniu zostaną pobrane tylko dane z identyfikatorem żądania **większym niż** wartość tej właściwości.  | Nie |

>[!TIP]
>Jeśli otwarta tabela centrów zawiera tylko dane wygenerowane przez pojedynczy identyfikator żądania, na przykład zawsze należy wykonać pełne ładowanie i zastąpić istniejące dane w tabeli, lub uruchomić DTP tylko raz dla testu, pamiętaj o usunięciu zaznaczenia opcji "excludeLastRequest" w celu skopiowania danych.

Aby przyspieszyć ładowanie danych, można ustawić [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) działanie kopiowania w celu załadowania danych z SAP BW otwartego centrum. Jeśli na przykład ustawisz `parallelCopies` cztery, Data Factory współbieżnie wykonuje cztery wywołania RFC, a każde wywołanie RFC pobierze część danych z SAP BW otwartej tabeli z podziałem na partycje według identyfikatora żądania DTP i identyfikatora pakietu. Ma to zastosowanie, gdy liczba unikatowych identyfikatorów żądań DTP i identyfikator pakietu jest większa niż wartość `parallelCopies` . Podczas kopiowania danych do magazynu danych opartego na plikach, jest również ponownie wykonywane polecenie zapisu do folderu jako wiele plików (Określ nazwę folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

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

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapowanie typu danych dla SAP BW Open Hub

Podczas kopiowania danych z SAP BW otwartego centrum następujące mapowania są używane z poziomu SAP BW danych do Azure Data Factory pośrednich typów danych. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ SAP ABAP | Typ danych pośrednich fabryki danych |
|:--- |:--- |
| C (ciąg) | Ciąg |
| I (liczba całkowita) | Int32 |
| F (float) | Double |
| D (Data) | Ciąg |
| T (godzina) | Ciąg |
| P (spakowane, walutowe, dziesiętne, ilość) | Liczba dziesiętna |
| N (NUMC) | Ciąg |
| X (binarny i nieprzetworzony) | Ciąg |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

**Objawy:** W przypadku korzystania z programu SAP BW na platformie HANA i obserwowania tylko podzbioru danych jest kopiowanych za pośrednictwem działania kopiowania APD (1 000 000 wierszy), możliwe przyczyny jest włączenie opcji "SAP HANA Execution" w DTP, w tym przypadku funkcja ADF może pobrać tylko pierwszą partię danych.

**Rozwiązanie:** Wyłącz opcję "SAP HANA Execution" w DTP, Przetwórz ponownie dane, a następnie ponów próbę wykonania działania kopiowania.

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
