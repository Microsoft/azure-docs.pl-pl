---
title: Funkcje optymalizacji wydajności działania kopiowania
description: Zapoznaj się z kluczowymi funkcjami, które ułatwiają optymalizację wydajności działania kopiowania w programie Azure Data Factory.
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
ms.date: 03/09/2020
ms.openlocfilehash: fd7844340553809e1429097a9dda70f6bdb3e075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414192"
---
# <a name="copy-activity-performance-optimization-features"></a>Funkcje optymalizacji wydajności działania kopiowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano funkcje optymalizacji wydajności działania kopiowania, których można użyć w Azure Data Factory.

## <a name="data-integration-units"></a>Jednostki integracji danych

Jednostka integracji danych to miara, która reprezentuje moc (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w Azure Data Factory. Jednostka integracji danych ma zastosowanie tylko do [środowiska Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), ale nie do [własnego środowiska Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

Dozwolony DIUs do upoważnienia do uruchomienia działania kopiowania jest z **przedziału od 2 do 256**. Jeśli nie zostanie określony, lub wybierzesz opcję "automatycznie" w interfejsie użytkownika, Data Factory dynamicznie zastosować optymalne ustawienie DIU na podstawie pary Source-sink i wzorca danych. W poniższej tabeli wymieniono obsługiwane zakresy DIU i domyślne zachowanie w różnych scenariuszach kopiowania:

| Kopiuj scenariusz | Obsługiwany zakres DIU | Domyślna DIUs określona przez usługę |
|:--- |:--- |---- |
| Między magazynami plików |- **Kopiuj z lub do pojedynczego pliku**: 2-4 <br>- **Kopiuj z i do wielu plików**: 2-256, w zależności od liczby i rozmiaru plików <br><br>Na przykład, jeśli skopiujesz dane z folderu zawierającego 4 duże pliki i wybierzesz zachowanie hierarchii, maksymalna obowiązująca DIU wynosi 16; w przypadku wybrania opcji scalenia pliku maksymalna obowiązująca DIU to 4. |Od 4 do 32 w zależności od liczby i rozmiaru plików |
| Ze sklepu plików do magazynu innego niż plik |- **Kopiuj z pojedynczego pliku**: 2-4 <br/>- **Kopiuj z wielu plików**: 2-256 w zależności od liczby i rozmiaru plików <br/><br/>Na przykład w przypadku kopiowania danych z folderu zawierającego 4 duże pliki maksymalna obowiązująca DIU to 16. |- **Kopiuj do Azure SQL Database lub Azure Cosmos DB**: od 4 do 16 w zależności od warstwy ujścia (DTU/jednostek ru) i wzorca pliku źródłowego<br>- **Kopiuj do usługi Azure Synapse Analytics** przy użyciu instrukcji Base lub Copy: 2<br>— Inny scenariusz: 4 |
| Z magazynu innego niż plik do magazynu plików |- **Kopiuj z magazynów danych z włączoną obsługą partycji (w tym programu** [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 podczas zapisywania do folderu i 2-4 podczas zapisywania do jednego pojedynczego pliku. Uwaga na partycję danych źródłowych można użyć maksymalnie 4 DIUs.<br>- **Inne scenariusze**: 2-4 |- **Kopiuj z REST lub http**: 1<br/>- **Kopiowanie z usługi Amazon RedShift** przy użyciu usługi Unload: 2<br>- **Inny scenariusz**: 4 |
| Między magazynami nienależącymi do plików |- **Kopiuj z magazynów danych z włączoną obsługą partycji (w tym programu** [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 podczas zapisywania do folderu i 2-4 podczas zapisywania do jednego pojedynczego pliku. Uwaga na partycję danych źródłowych można użyć maksymalnie 4 DIUs.<br/>- **Inne scenariusze**: 2-4 |- **Kopiuj z REST lub http**: 1<br>- **Inny scenariusz**: 4 |

Można zobaczyć DIUs używany dla każdego przebiegu kopiowania w widoku monitorowania działania kopiowania lub w danych wyjściowych działania. Aby uzyskać więcej informacji, zobacz [monitorowanie aktywności kopiowania](copy-activity-monitoring.md). Aby zastąpić to ustawienie domyślne, określ wartość `dataIntegrationUnits` właściwości w następujący sposób. *Rzeczywista liczba DIUs* , których operacja kopiowania używa w czasie wykonywania, jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca danych.

Zostanie naliczona opłata za **użycie jednostki czasu \* \* trwania kopiowania DIUs (cena jednostkowa)/DIU godzin**. Zobacz bieżące ceny [tutaj](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Waluta lokalna i oddzielne opłaty mogą dotyczyć poszczególnych typów subskrypcji.

**Przykład:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Samoobsługowa skalowalność środowiska Integration Runtime

Jeśli chcesz osiągnąć wyższą przepływność, możesz skalować w górę lub w poziomie do własnego środowiska IR:

- Jeśli użycie procesora CPU i dostępnej pamięci w węźle podczerwieni nie jest w pełni wykorzystane, ale wykonanie współbieżnych zadań zbliża się do limitu, należy skalować w górę, zwiększając liczbę współbieżnych zadań, które mogą być uruchamiane w węźle.  Aby uzyskać instrukcje, zobacz [tutaj](create-self-hosted-integration-runtime.md#scale-up) .
- Jeśli z drugiej strony procesor CPU jest wysoki w węźle samodzielnego środowiska IR lub dostępna pamięć jest niska, można dodać nowy węzeł, aby ułatwić skalowanie obciążenia w wielu węzłach.  Aby uzyskać instrukcje, zobacz [tutaj](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

Należy pamiętać, że w następujących scenariuszach wykonywanie pojedynczego działania kopiowania może korzystać z wielu samodzielnych węzłów podczerwieni:

- Kopiowanie danych z magazynów opartych na plikach, w zależności od liczby i rozmiaru plików.
- Skopiuj dane z magazynu danych z włączoną obsługą partycji (w tym z [rozwiązań firmy Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)i [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), w zależności od liczby partycji danych.

## <a name="parallel-copy"></a>Kopiowanie równoległe

Można ustawić kopiowanie równoległe (`parallelCopies` Właściwość) dla działania kopiowania, aby wskazać równoległość, która ma być używana przez działanie kopiowania. Tę właściwość można traktować jako maksymalną liczbę wątków w działaniu kopiowania odczytywanych ze źródła lub zapisywać do magazynów danych ujścia równolegle.

Równoległa kopia jest prostopadła do [jednostek integracji danych](#data-integration-units) lub [samodzielnych węzłów podczerwieni](#self-hosted-integration-runtime-scalability). Jest on liczony dla wszystkich DIUs lub samodzielnych węzłów IR.

Dla każdego przebiegu działania kopiowania domyślnie Azure Data Factory dynamicznie stosować optymalne ustawienia kopiowania równoległego na podstawie pary i wzorca danych źródła. 

> [!TIP]
> Domyślne zachowanie funkcji kopiowania równoległego zwykle zapewnia najlepszą przepływność, która jest automatycznie określana przez funkcję ADF na podstawie pary Source-ujścia, wzorca danych i liczby DIUs lub obsługiwanego przez siebie procesora CPU/pamięci/węzłów środowiska IR. Zapoznaj się z tematem [Rozwiązywanie problemów z wydajnością operacji kopiowania](copy-activity-performance-troubleshooting.md) przy dostrojeniu kopii równoległej.

W poniższej tabeli przedstawiono zachowanie kopiowania równoległego:

| Kopiuj scenariusz | Zachowanie kopiowania równoległego |
| --- | --- |
| Między magazynami plików | `parallelCopies`Określa równoległość **na poziomie pliku**. Fragmenty poszczególnych plików odbywają się automatycznie i w sposób przezroczysty. Zaprojektowano w celu użycia najlepszego odpowiedniego rozmiaru fragmentu dla danego typu magazynu danych w celu równoległego ładowania danych. <br/><br/>Rzeczywista liczba kopii równoległych działania kopiowania w czasie wykonywania nie jest większa niż liczba plików, które posiadasz. Jeśli zachowanie kopiowania jest **mergeFile** do ujścia plików, działanie kopiowania nie może korzystać z równoległości na poziomie plików. |
| Ze sklepu plików do magazynu innego niż plik | — Podczas kopiowania danych do Azure SQL Database lub Azure Cosmos DB, domyślna kopia równoległa zależy również od warstwy ujścia (liczba DTU/jednostek ru).<br>— Podczas kopiowania danych do tabeli platformy Azure domyślnie jest to 4. |
| Z magazynu innego niż plik do magazynu plików | — Podczas kopiowania danych z magazynu danych z włączoną obsługą partycji (w tym z [rozwiązań firmy Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)i [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) domyślna kopia równoległa to 4. Rzeczywista liczba kopii równoległych działania kopiowania w czasie wykonywania nie przekracza liczby posiadanych partycji danych. W przypadku używania samodzielnych Integration Runtime i kopiowania do obiektów BLOB/ADLS Gen2 platformy Azure Zwróć uwagę na to, że maksymalna efektywna kopia jest równa 4 lub 5 na węzeł IR.<br>— W przypadku innych scenariuszy kopiowanie równoległe nie zacznie obowiązywać. Nawet jeśli jest określona równoległość, nie jest stosowana. |
| Między magazynami nienależącymi do plików | — Podczas kopiowania danych do Azure SQL Database lub Azure Cosmos DB, domyślna kopia równoległa zależy również od warstwy ujścia (liczba DTU/jednostek ru).<br/>— Podczas kopiowania danych z magazynu danych z włączoną obsługą partycji (w tym z [rozwiązań firmy Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)i [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) domyślna kopia równoległa to 4.<br>— Podczas kopiowania danych do tabeli platformy Azure domyślnie jest to 4. |

Aby kontrolować obciążenie maszyn, które obsługują magazyny danych, lub dostosować wydajność kopiowania, można zastąpić wartość domyślną i określić wartość `parallelCopies` właściwości. Wartość musi być liczbą całkowitą większą lub równą 1. W czasie wykonywania w celu uzyskania najlepszej wydajności działanie kopiowania używa wartości, która jest mniejsza lub równa ustawionej wartości.

Po określeniu wartości `parallelCopies` właściwości należy zwiększyć obciążenie dla magazynu danych źródłowych i ujścia. Należy również rozważyć zwiększenie obciążenia do własnego środowiska Integration Runtime, jeśli działanie kopiowania jest przez niego uprawnione. Ten wzrost obciążenia występuje szczególnie w przypadku wielu działań lub współbieżnych uruchomień tych samych działań, które działają w tym samym magazynie danych. Jeśli zauważysz, że magazyn danych lub własne środowisko Integration Runtime jest przeciążone obciążeniem, Zmniejsz `parallelCopies` wartość, aby zwolnić obciążenie.

**Przykład:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Kopia przygotowana

W przypadku kopiowania danych ze źródłowego magazynu danych do magazynu danych ujścia można użyć usługi BLOB Storage jako tymczasowego magazynu przemieszczania. Przygotowanie jest szczególnie przydatne w następujących przypadkach:

- **Chcesz pozyskać dane z różnych magazynów danych do SQL Data Warehouse za pośrednictwem bazy.** SQL Data Warehouse korzysta z bazy danych wbudowanych jako mechanizmu wysokiej przepływności w celu załadowania dużej ilości dane do SQL Data Warehouse. Dane źródłowe muszą znajdować się w magazynie obiektów blob lub Azure Data Lake Store i muszą spełniać dodatkowe kryteria. Podczas ładowania danych z magazynu danych innego niż magazyn obiektów blob lub Azure Data Lake Store można aktywować kopiowanie danych za pośrednictwem tymczasowego tymczasowego magazynu obiektów BLOB. W takim przypadku Azure Data Factory wykonuje wymagane przekształcenia danych, aby upewnić się, że spełnia on wymagania bazy. Następnie używa metody Base, aby załadować dane do SQL Data Warehouse wydajnie. Aby uzyskać więcej informacji, zobacz [Korzystanie z bazy danych w celu ładowania do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Czasami trwa przeprowadzenie hybrydowego przenoszenia danych (czyli kopiowania z lokalnego magazynu danych do magazynu danych w chmurze) przez wolne połączenie sieciowe.** Aby zwiększyć wydajność, można użyć kopii przygotowanej do skompresowania danych w środowisku lokalnym, co pozwala na przenoszenie danych do tymczasowego magazynu danych w chmurze. Następnie można zdekompresować dane w magazynie przemieszczania przed załadowaniem do docelowego magazynu danych.
- **Nie chcesz otwierać portów innych niż port 80 i port 443 w zaporze ze względu na firmowe zasady IT.** Na przykład podczas kopiowania danych z lokalnego magazynu danych do ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse, należy aktywować wychodzącą komunikację TCP na porcie 1433 dla zapory systemu Windows i zapory firmowej. W tym scenariuszu kopia przygotowana może korzystać z własnego środowiska Integration Runtime, aby najpierw skopiować dane do wystąpienia tymczasowego magazynu obiektów BLOB za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie może załadować dane do SQL Database lub SQL Data Warehouse z przemieszczania magazynu obiektów BLOB. W tym przepływie nie trzeba włączać portu 1433.

### <a name="how-staged-copy-works"></a>Jak działa kopia przygotowana

Gdy uaktywniasz funkcję przemieszczania, najpierw dane są kopiowane ze źródłowego magazynu danych do tymczasowego magazynu obiektów BLOB (należy je przenieść). Następnie dane zostaną skopiowane z tymczasowego magazynu danych do magazynu danych ujścia. Azure Data Factory automatycznie zarządza przepływem dwuetapowym. Azure Data Factory również czyści dane tymczasowe z magazynu tymczasowego po zakończeniu przenoszenia danych.

![Kopia przygotowana](media/copy-activity-performance/staged-copy.png)

W przypadku aktywowania przenoszenia danych przy użyciu magazynu przemieszczania można określić, czy dane mają być kompresowane przed przeniesieniem danych ze źródłowego magazynu danych do tymczasowego lub przejściowego magazynu danych, a następnie zdekompresować przed przeniesieniem danych z tymczasowego lub przejściowego magazynu danych do magazynu danych ujścia.

Obecnie nie można kopiować danych między dwoma magazynami danych, które są połączone za pośrednictwem różnych urzędów certyfikacji samodzielnych, ani z kopią etapową lub bez niej. W tym scenariuszu można skonfigurować dwa jawne działanie kopiowania w łańcuchu w celu skopiowania danych ze źródła do przemieszczania z miejsca przejściowego do ujścia.

### <a name="configuration"></a>Konfiguracja

Skonfiguruj ustawienie **enableStaging** w działaniu kopiowania, aby określić, czy dane mają zostać przygotowane w magazynie obiektów BLOB przed załadowaniem ich do docelowego magazynu danych. Po ustawieniu **enableStaging** na `TRUE`, określ dodatkowe właściwości wymienione w poniższej tabeli. Należy również utworzyć usługę Azure Storage lub usługi połączonej sygnatury dostępu współdzielonego na potrzeby przemieszczania, jeśli nie istnieje.

| Właściwość | Opis | Wartość domyślna | Wymagany |
| --- | --- | --- | --- |
| enableStaging |Określ, czy chcesz kopiować dane za pośrednictwem tymczasowego magazynu przemieszczania. |Fałsz |Nie |
| linkedServiceName |Określ nazwę połączonej usługi [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) , która odwołuje się do wystąpienia magazynu, którego używasz jako tymczasowego magazynu przemieszczania. <br/><br/> Nie można użyć magazynu z sygnaturą dostępu współdzielonego w celu załadowania danych do SQL Data Warehouse za pośrednictwem bazy. Można go używać we wszystkich innych scenariuszach. |Nie dotyczy |Tak, gdy **enableStaging** jest ustawiona na wartość true |
| ścieżka |Określ ścieżkę magazynu obiektów blob, która ma zawierać dane przemieszczane. Jeśli nie podano ścieżki, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę tylko wtedy, gdy używasz magazynu z sygnaturą dostępu współdzielonego lub potrzebujesz danych tymczasowych, aby znajdować się w określonej lokalizacji. |Nie dotyczy |Nie |
| Ustawieniem EnableCompression |Określa, czy dane mają być kompresowane przed skopiowaniem do lokalizacji docelowej. To ustawienie zmniejsza ilość przesyłanych danych. |Fałsz |Nie |

>[!NOTE]
> W przypadku użycia kopiowania etapowego z włączoną kompresją usługa główna lub uwierzytelnianie MSI dla połączonej usługi obiektów BLOB są nieobsługiwane.

Oto przykładowa definicja działania kopiowania z właściwościami, które są opisane w powyższej tabeli:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Wpływ rozliczania przygotowanych kopii

Opłata jest naliczana na podstawie dwóch kroków: Kopiuj czas trwania i typ kopiowania.

* W przypadku korzystania z przemieszczania podczas kopiowania w chmurze, który kopiuje dane z magazynu danych w chmurze do innego magazynu danych w chmurze, to oba etapy, które są uprawnione przez środowisko uruchomieniowe Azure Integration Runtime, są obciążani [łączny czas trwania kopiowania dla kroku 1 i 2] x [cena jednostkowa kopiowania w chmurze].
* W przypadku korzystania z przemieszczania w ramach kopii hybrydowej, która kopiuje dane z lokalnego magazynu danych do magazynu danych w chmurze, jednym etapem, który jest obsługiwany przez środowisko Integration Runtime, jest naliczana opłata za [okres kopiowania hybrydowego] x [cena jednostki kopiowania hybrydowego] + [czas trwania kopiowania w chmurze] x [cena jednostkowa kopiowania w chmurze].

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Przewodnik dotyczący wydajności i skalowalności działania kopiowania](copy-activity-performance.md)
- [Rozwiązywanie problemów z wydajnością działania kopiowania](copy-activity-performance-troubleshooting.md)
- [Używanie Azure Data Factory do migrowania danych z usługi Data Lake lub magazynu danych na platformę Azure](data-migration-guidance-overview.md)
- [Migrowanie danych z usługi Amazon S3 do usługi Azure Storage](data-migration-guidance-s3-azure-storage.md)