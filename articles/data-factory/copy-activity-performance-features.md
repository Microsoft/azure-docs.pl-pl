---
title: Kopiowanie funkcji optymalizacji wydajności działania
description: Dowiedz się więcej o kluczowych funkcjach, które pomagają zoptymalizować wydajność działania kopiowania w usłudze Azure Data Factory
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414192"
---
# <a name="copy-activity-performance-optimization-features"></a>Kopiowanie funkcji optymalizacji wydajności działania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono funkcje optymalizacji wydajności działania kopiowania, które można wykorzystać w usłudze Azure Data Factory.

## <a name="data-integration-units"></a>Jednostki integracji danych

Jednostka integracji danych to miara reprezentująca moc (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w usłudze Azure Data Factory. Jednostka integracji danych ma zastosowanie tylko do [środowiska wykonawczego integracji platformy Azure,](concepts-integration-runtime.md#azure-integration-runtime)ale nie [do samodzielnego hostowanego środowiska wykonawczego integracji.](concepts-integration-runtime.md#self-hosted-integration-runtime)

Dozwolone DIUs upoważnić przebieg działania kopiowania jest **między 2 i 256**. Jeśli nie określono lub wybierzesz "Auto" w interfejsie użytkownika, usługa Data Factory dynamicznie zastosuj optymalne ustawienie DIU na podstawie pary źródła ujścia i wzorca danych. W poniższej tabeli wymieniono obsługiwane zakresy diu i domyślne zachowanie w różnych scenariuszach kopiowania:

| Scenariusz kopiowania | Obsługiwane oferty DIU | Domyślne diu określone przez usługę |
|:--- |:--- |---- |
| Między magazynami plików |- **Kopiowanie z lub do pojedynczego pliku:** 2-4 <br>- **Kopiowanie z i do wielu plików:** 2-256 w zależności od liczby i rozmiaru plików <br><br>Na przykład, jeśli kopiujesz dane z folderu z 4 dużymi plikami i wybierasz zachować hierarchię, maksymalna efektywna diu wynosi 16; po wybraniu scalić pliku, max skuteczne DIU jest 4. |Od 4 do 32 w zależności od liczby i rozmiaru plików |
| Od magazynu plików do magazynu bez plików |- **Kopiowanie z pojedynczego pliku**: 2-4 <br/>- **Kopiowanie z wielu plików:** 2-256 w zależności od liczby i rozmiaru plików <br/><br/>Na przykład, jeśli kopiujesz dane z folderu z 4 dużymi plikami, maksymalna skuteczna diu wynosi 16. |- **Kopiowanie do bazy danych SQL azure lub usługi Azure Cosmos DB:** od 4 do 16 w zależności od warstwy ujścia (DTU/RU) i wzorca pliku źródłowego<br>- **Kopiowanie do usługi Azure Synapse Analytics** przy użyciu instrukcji PolyBase lub COPY: 2<br>- Inny scenariusz: 4 |
| Od magazynu bez plików do magazynu plików |- **Kopiowanie ze sklepów danych obsługujących opcje partycji** (w tym [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata):](connector-teradata.md#teradata-as-source)2-256 podczas pisania do folderu i 2-4 podczas zapisywania do jednego pliku. Uwaga na partycję danych źródłowych można użyć do 4 DIUs.<br>- **Inne scenariusze**: 2-4 |- **Kopiowanie z REST lub HTTP**: 1<br/>- **Kopiowanie z Amazon Redshift** za pomocą UNLOAD: 2<br>- **Inny scenariusz**: 4 |
| Między magazynami niebędącymi plikami |- **Kopiowanie ze sklepów danych obsługujących opcje partycji** (w tym [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata):](connector-teradata.md#teradata-as-source)2-256 podczas pisania do folderu i 2-4 podczas zapisywania do jednego pliku. Uwaga na partycję danych źródłowych można użyć do 4 DIUs.<br/>- **Inne scenariusze**: 2-4 |- **Kopiowanie z REST lub HTTP**: 1<br>- **Inny scenariusz**: 4 |

Można zobaczyć DIUs używane dla każdego uruchomienia kopii w widoku monitorowania działania kopiowania lub danych wyjściowych działania. Aby uzyskać więcej informacji, zobacz [Monitorowanie aktywności kopiowania](copy-activity-monitoring.md). Aby zastąpić tę wartość domyślną, `dataIntegrationUnits` należy określić wartość właściwości w następujący sposób. *Rzeczywista liczba diu,* które używa operacji kopiowania w czasie wykonywania jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca danych.

Zostanie naliczona **opłata # \* używanego DIUs czas \* trwania ceny jednostkowej / DIU-hour**. Zobacz aktualne ceny [tutaj](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). W walucie lokalnej i oddzielne rabaty mogą mieć zastosowanie dla typu subskrypcji.

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

## <a name="self-hosted-integration-runtime-scalability"></a>Skalowalność środowiska uruchomieniowego integracji hostowanego samodzielnie

Jeśli chcesz osiągnąć wyższą przepustowość, można skalować w górę lub skalować w poziomie self-hosted IR:

- Jeśli procesor CPU i dostępna pamięć w węźle podczerwony hostowane samodzielnie nie są w pełni wykorzystywane, ale wykonywanie równoczesnych zadań osiąga limit, należy skalować w górę, zwiększając liczbę równoczesnych zadań, które można uruchomić w węźle.  Instrukcje można [znaleźć tutaj.](create-self-hosted-integration-runtime.md#scale-up)
- Jeśli z drugiej strony procesor jest wysoki w węźle WY hostowanego samodzielnie lub dostępna pamięć jest niska, można dodać nowy węzeł, aby ułatwić skalowanie obciążenia w wielu węzłach.  Instrukcje można [znaleźć tutaj.](create-self-hosted-integration-runtime.md#high-availability-and-scalability)

Uwaga w następujących scenariuszach wykonanie działania pojedynczej kopii może korzystać z wielu węzłów podczerwonych hostowanych samodzielnie:

- Kopiowanie danych ze sklepów opartych na plikach, w zależności od liczby i rozmiaru plików.
- Kopiowanie danych z magazynu danych z opcją partycji (w tym [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)i SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), w zależności od liczby partycji danych.

## <a name="parallel-copy"></a>Kopia równoległa

Można ustawić kopię`parallelCopies` równoległą (właściwość) na działania kopiowania, aby wskazać równoległość, który ma być używany przez działanie kopiowania. Tę właściwość można interpretować jako maksymalną liczbę wątków w ramach działania kopiowania, które odczytują ze źródła lub zapisują do magazynów danych ujścia równolegle.

Kopia równoległa jest ortogonalna do [jednostek integracji danych](#data-integration-units) lub [samodzielnie hostowanych węzłów podczerwonych.](#self-hosted-integration-runtime-scalability) Jest liczony we wszystkich diu lub self-hosted węzłów podczerwony.

Dla każdego uruchomienia działania kopiowania domyślnie usługa Azure Data Factory dynamicznie zastosuj optymalne ustawienie kopiowania równoległego na podstawie pary źródła ujścia i wzorca danych. 

> [!TIP]
> Domyślne zachowanie kopii równoległej zwykle zapewnia najlepszą przepływność, która jest automatycznie określana przez adf na podstawie pary źródła ujścia, wzorca danych i liczby diu lub self-hosted IR CPU/memory/node count. Zapoznaj się [z rozwiązywania problemów z wydajnością działania kopiowania,](copy-activity-performance-troubleshooting.md) kiedy dostroić kopię równoległą.

W poniższej tabeli wymieniono zachowanie kopiowania równoległego:

| Scenariusz kopiowania | Zachowanie kopiowania równoległego |
| --- | --- |
| Między magazynami plików | `parallelCopies`określa równoległość **na poziomie pliku**. Fragmentowanie w każdym pliku odbywa się pod spodem automatycznie i w sposób przezroczysty. Jest przeznaczony do używania najlepszego odpowiedniego rozmiaru fragmentu dla danego typu magazynu danych, aby ładować dane równolegle. <br/><br/>Rzeczywista liczba równoległych kopii działania kopiowania używa w czasie wykonywania jest nie więcej niż liczba plików, które masz. Jeśli zachowanie kopiowania jest **mergeFile** do ujścia pliku, działanie kopiowania nie może korzystać z równoległości na poziomie pliku. |
| Od magazynu plików do magazynu bez plików | - Podczas kopiowania danych do bazy danych SQL azure lub usługi Azure Cosmos DB domyślna kopia równoległa zależy również od warstwy ujścia (liczba procesorów DTU/RU).<br>- Podczas kopiowania danych do tabeli platformy Azure domyślna kopia równoległa to 4. |
| Od magazynu bez plików do magazynu plików | - Podczas kopiowania danych z magazynu danych z opcją partycji (w tym [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)i SAP [Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)domyślna kopia równoległa wynosi 4. Rzeczywista liczba równoległych kopii działania kopiowania używa w czasie wykonywania jest nie więcej niż liczba partycji danych masz. W przypadku korzystania z self-hosted Integration Runtime i skopiować do usługi Azure Blob/ADLS Gen2, należy pamiętać, że maksymalna efektywna kopia równoległa wynosi 4 lub 5 na węzeł podczerwony.<br>- W przypadku innych scenariuszy kopiowanie równoległe nie jest skuteczne. Nawet jeśli określono równoległość, nie jest stosowany. |
| Między magazynami niebędącymi plikami | - Podczas kopiowania danych do bazy danych SQL azure lub usługi Azure Cosmos DB domyślna kopia równoległa zależy również od warstwy ujścia (liczba procesorów DTU/RU).<br/>- Podczas kopiowania danych z magazynu danych z opcją partycji (w tym [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)i SAP [Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)domyślna kopia równoległa wynosi 4.<br>- Podczas kopiowania danych do tabeli platformy Azure domyślna kopia równoległa to 4. |

Aby kontrolować obciążenie na komputerach, na których znajdują się magazyny danych lub dostroić `parallelCopies` wydajność kopiowania, można zastąpić wartość domyślną i określić wartość właściwości. Wartość musi być całkowitej większej lub równej 1. W czasie wykonywania dla najlepszej wydajności działanie kopiowania używa wartości, która jest mniejsza lub równa ustawionej wartości.

Po określeniu wartości `parallelCopies` właściwości, należy wziąć pod uwagę wzrost obciążenia na źródła i magazyny danych ujścia pod uwagę. Należy również wziąć pod uwagę wzrost obciążenia do środowiska uruchomieniowego integracji hostowanej samodzielnie, jeśli działanie kopiowania jest przez nią wzmocnione. Ten wzrost obciążenia występuje szczególnie, gdy masz wiele działań lub równoczesnych przebiegów tych samych działań, które są uruchamiane dla tego samego magazynu danych. Jeśli zauważysz, że magazyn danych lub środowisko uruchomieniowe integracji hostowanego `parallelCopies` samodzielnie jest przeciążone obciążeniem, zmniejsz wartość, aby zmniejszyć obciążenie.

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

## <a name="staged-copy"></a>Kopia etapowa

Podczas kopiowania danych z magazynu danych źródłowych do magazynu danych ujścia, można użyć magazynu obiektów Blob jako tymczasowego magazynu przemieszczania. Inscenizacja jest szczególnie przydatna w następujących przypadkach:

- **Chcesz pozyskiwania danych z różnych magazynów danych do magazynu danych SQL za pośrednictwem PolyBase.** Usługa SQL Data Warehouse używa polybase jako mechanizmu o wysokiej przepływności do ładowania dużej ilości danych do magazynu danych SQL. Dane źródłowe muszą znajdować się w magazynie obiektów Blob lub usłudze Azure Data Lake Store i muszą spełniać dodatkowe kryteria. Podczas ładowania danych z magazynu danych innych niż magazyn obiektów Blob lub usługi Azure Data Lake Store, można aktywować kopiowanie danych za pośrednictwem tymczasowego tymczasowego magazynu obiektów blob. W takim przypadku usługa Azure Data Factory wykonuje wymagane przekształcenia danych, aby upewnić się, że spełnia wymagania PolyBase. Następnie używa PolyBase do efektywnego ładowania danych do usługi SQL Data Warehouse. Aby uzyskać więcej informacji, zobacz [PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Czasami zajmuje trochę czasu, aby wykonać hybrydowy przenoszenie danych (to znaczy, aby skopiować z lokalnego magazynu danych do magazynu danych w chmurze) za pośrednictwem wolnego połączenia sieciowego.** Aby zwiększyć wydajność, można użyć kopii etapowej do kompresji danych lokalnie, dzięki czemu trwa mniej czasu, aby przenieść dane do magazynu danych przemieszczania w chmurze. Następnie można rozpakować dane w magazynie przemieszczania przed załadowaniem do docelowego magazynu danych.
- **Nie chcesz otwierać portów innych niż port 80 i port 443 w zaporze z powodu firmowych zasad IT.** Na przykład podczas kopiowania danych z lokalnego magazynu danych do ujścia bazy danych SQL platformy Azure lub ujścia usługi Azure SQL Data Warehouse, należy aktywować wychodzącą komunikację TCP na porcie 1433 zarówno dla zapory systemu Windows, jak i zapory firmowej. W tym scenariuszu kopia etapowa może korzystać z samodzielnego środowiska wykonawczego integracji, aby najpierw skopiować dane do wystąpienia przemieszczania magazynu obiektów Blob za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie można załadować dane do bazy danych SQL lub magazynu danych SQL z magazynu obiektów blob przemieszczania. W tym przepływie nie trzeba włączać portu 1433.

### <a name="how-staged-copy-works"></a>Jak działa kopia etapowa

Po uaktywnieniu funkcji przemieszczania, najpierw dane są kopiowane z magazynu danych źródłowych do magazynu przemieszczania obiektów Blob (przynieść własne). Następnie dane są kopiowane z magazynu danych przemieszczania do magazynu danych ujścia. Usługa Azure Data Factory automatycznie zarządza przepływem dwuetapowym. Usługa Azure Data Factory również czyści dane tymczasowe z magazynu przemieszczania po zakończeniu przenoszenia danych.

![Kopia etapowa](media/copy-activity-performance/staged-copy.png)

Po uaktywnieniu przenoszenia danych przy użyciu magazynu przemieszczania można określić, czy dane mają być kompresowane przed przeniesieniem danych z magazynu danych źródłowych do tymczasowego lub przemieszczania magazynu danych, a następnie zdekompresować przed przeniesieniem danych z tymczasowego lub przemieszczania magazynu danych do magazynu danych ujścia.

Obecnie nie można kopiować danych między dwoma magazynami danych, które są połączone za pośrednictwem różnych samodzielnie hostowanych adresów IRs, ani z kopią etapową, ani bez niej. W takim scenariuszu można skonfigurować dwa jawnie połączone działania kopiowania do kopiowania ze źródła do przemieszczania następnie z przemieszczania do ujścia.

### <a name="configuration"></a>Konfigurowanie

Skonfiguruj ustawienie **enableStaging** w działaniu kopiowania, aby określić, czy dane mają być przemieszczane w magazynie obiektów Blob przed załadowaniem ich do docelowego magazynu danych. Po ustawieniu **enableStaging** do `TRUE`, określ dodatkowe właściwości wymienione w poniższej tabeli. Należy również utworzyć usługę Azure Storage lub usługi połączone z dostępem współdzielonym w usłudze przemieszczania, jeśli jej nie masz.

| Właściwość | Opis | Wartość domyślna | Wymagany |
| --- | --- | --- | --- |
| enableStaging (włączaniestaging) |Określ, czy dane mają być kopiowane za pośrednictwem tymczasowego magazynu przemieszczania. |False |Nie |
| linkedServiceName |Określ nazwę usługi połączonej [Usługi AzureStorage,](connector-azure-blob-storage.md#linked-service-properties) która odwołuje się do wystąpienia magazynu, którego używasz jako tymczasowego magazynu przemieszczania. <br/><br/> Nie można użyć magazynu z podpisem dostępu współdzielonego do ładowania danych do magazynu danych SQL za pośrednictwem polybase. Można go używać we wszystkich innych scenariuszach. |Nie dotyczy |Tak, gdy **opcja enableStaging** jest ustawiona na WARTOŚĆ TRUE |
| ścieżka |Określ ścieżkę magazynu obiektów Blob, która ma zawierać dane etapowe. Jeśli nie podasz ścieżkę, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę tylko wtedy, gdy używasz magazynu z podpisem dostępu współdzielonego lub potrzebujesz danych tymczasowych, aby znajdować się w określonej lokalizacji. |Nie dotyczy |Nie |
| enableKompresja |Określa, czy dane mają być kompresowane przed skopiowaniem do miejsca docelowego. To ustawienie zmniejsza ilość przesyłanych danych. |False |Nie |

>[!NOTE]
> Jeśli używasz kopii etapowej z włączoną kompresją, jednostki usługi lub msi uwierzytelniania dla usługi połączonej obiektu blob przemieszczania nie jest obsługiwany.

Oto przykładowa definicja działania kopiowania z właściwościami opisanymi w powyższej tabeli:

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

### <a name="staged-copy-billing-impact"></a>Wpływ rozliczeń za pomocą kopii etapowej

Opłata jest naliczana na podstawie dwóch kroków: czasu trwania kopiowania i typu kopiowania.

* Podczas tworzenia przemieszczania podczas kopiowania w chmurze, czyli kopiowania danych z magazynu danych w chmurze do innego magazynu danych w chmurze, oba etapy są obsługiwane przez środowisko wykonawcze integracji platformy Azure, zostanie naliczona opłata [suma czasu trwania kopiowania dla kroków 1 i kroku 2] x [cena jednostkowa kopiowania w chmurze].
* Podczas tworzenia przemieszczania podczas kopiowania hybrydowego, czyli kopiowania danych z lokalnego magazynu danych do magazynu danych w chmurze, jeden etap jest umocowany przez środowisko uruchomieniowe integracji hostowanej samodzielnie, naliczana jest opłata za [czas trwania kopii hybrydowej] x [cena jednostkowa kopii hybrydowej] + [czas trwania kopiowania w chmurze] x [cena jednostkowa kopiowania w chmurze].

## <a name="next-steps"></a>Następne kroki
Zobacz inne artykuły dotyczące aktywności kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Przewodnik po wydajności i skalowalności działania kopiowania](copy-activity-performance.md)
- [Rozwiązywanie problemów z wydajnością działania kopiowania](copy-activity-performance-troubleshooting.md)
- [Użyj usługi Azure Data Factory do migracji danych z usługi Data Lake lub magazynu danych na platformę Azure](data-migration-guidance-overview.md)
- [Migrowanie danych z usługi Amazon S3 do usługi Azure Storage](data-migration-guidance-s3-azure-storage.md)