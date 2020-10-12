---
title: Funkcje optymalizacji wydajności działania kopiowania
description: Poznaj najważniejsze funkcje, które ułatwiają optymalizację wydajności działania kopiowania w programie Azure Data Factory.
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
ms.date: 09/24/2020
ms.openlocfilehash: 8e46e9b323657b747fd73bad3b25ed66390f3aa9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324335"
---
# <a name="copy-activity-performance-optimization-features"></a>Funkcje optymalizacji wydajności działania kopiowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano funkcje optymalizacji wydajności działania kopiowania, których można użyć w Azure Data Factory.

## <a name="data-integration-units"></a>Jednostki integracji danych

Jednostka integracji danych to miara, która reprezentuje moc (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w Azure Data Factory. Jednostka integracji danych ma zastosowanie tylko do [środowiska Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), ale nie do [własnego środowiska Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

Dozwolony DIUs do upoważnienia do uruchomienia działania kopiowania jest z **przedziału od 2 do 256**. Jeśli nie zostanie określony, lub wybierzesz opcję "automatycznie" w interfejsie użytkownika, Data Factory dynamicznie stosuje optymalne ustawienie DIU na podstawie pary Source-sink i wzorca danych. W poniższej tabeli wymieniono obsługiwane zakresy DIU i domyślne zachowanie w różnych scenariuszach kopiowania:

| Kopiuj scenariusz | Obsługiwany zakres DIU | Domyślna DIUs określona przez usługę |
|:--- |:--- |---- |
| Między magazynami plików |- **Kopiuj z lub do pojedynczego pliku**: 2-4 <br>- **Kopiuj z i do wielu plików**: 2-256, w zależności od liczby i rozmiaru plików <br><br>Na przykład, jeśli skopiujesz dane z folderu zawierającego 4 duże pliki i wybierzesz zachowanie hierarchii, maksymalna obowiązująca DIU wynosi 16; w przypadku wybrania opcji scalenia pliku maksymalna obowiązująca DIU to 4. |Od 4 do 32 w zależności od liczby i rozmiaru plików |
| Ze sklepu plików do magazynu innego niż plik |- **Kopiuj z pojedynczego pliku**: 2-4 <br/>- **Kopiuj z wielu plików**: 2-256 w zależności od liczby i rozmiaru plików <br/><br/>Na przykład w przypadku kopiowania danych z folderu zawierającego 4 duże pliki maksymalna obowiązująca DIU to 16. |- **Kopiuj do Azure SQL Database lub Azure Cosmos DB**: od 4 do 16 w zależności od warstwy ujścia (DTU/jednostek ru) i wzorca pliku źródłowego<br>- **Kopiuj do usługi Azure Synapse Analytics** przy użyciu instrukcji Base lub Copy: 2<br>— Inny scenariusz: 4 |
| Z magazynu innego niż plik do magazynu plików |- **Kopiuj z magazynów danych z włączonymi opcjami partycji (w** tym [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [wystąpienia zarządzanego Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)i [Teradata](connector-teradata.md#teradata-as-source)): 2-256 podczas zapisywania do folderu i 2-4 podczas zapisywania do jednego pojedynczego pliku. Uwaga na partycję danych źródłowych można użyć maksymalnie 4 DIUs.<br>- **Inne scenariusze**: 2-4 |- **Kopiuj z REST lub http**: 1<br/>- **Kopiowanie z usługi Amazon RedShift** przy użyciu usługi Unload: 2<br>- **Inny scenariusz**: 4 |
| Między magazynami nienależącymi do plików |- **Kopiuj z magazynów danych z włączonymi opcjami partycji (w** tym [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [wystąpienia zarządzanego Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)i [Teradata](connector-teradata.md#teradata-as-source)): 2-256 podczas zapisywania do folderu i 2-4 podczas zapisywania do jednego pojedynczego pliku. Uwaga na partycję danych źródłowych można użyć maksymalnie 4 DIUs.<br/>- **Inne scenariusze**: 2-4 |- **Kopiuj z REST lub http**: 1<br>- **Inny scenariusz**: 4 |

Można zobaczyć DIUs używany dla każdego przebiegu kopiowania w widoku monitorowania działania kopiowania lub w danych wyjściowych działania. Aby uzyskać więcej informacji, zobacz [monitorowanie aktywności kopiowania](copy-activity-monitoring.md). Aby zastąpić to ustawienie domyślne, określ wartość `dataIntegrationUnits` właściwości w następujący sposób. *Rzeczywista liczba DIUs* , których operacja kopiowania używa w czasie wykonywania, jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca danych.

Zostanie naliczona opłata za **użycie \* jednostki czasu trwania kopiowania DIUs \* (cena jednostkowa)/DIU godzin**. Zobacz bieżące ceny [tutaj](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Waluta lokalna i oddzielne opłaty mogą dotyczyć poszczególnych typów subskrypcji.

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
- Jeśli z drugiej strony, procesor CPU jest wysoki w nieobsługiwanym przez siebie węźle IR lub dostępna pamięć jest niska, można dodać nowy węzeł, aby ułatwić skalowanie obciążenia w wielu węzłach.  Aby uzyskać instrukcje, zobacz [tutaj](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

Należy pamiętać, że w następujących scenariuszach wykonywanie pojedynczego działania kopiowania może korzystać z wielu samodzielnych węzłów podczerwieni:

- Kopiowanie danych z magazynów opartych na plikach, w zależności od liczby i rozmiaru plików.
- Skopiuj dane z magazynu danych z włączoną obsługą partycji (w tym [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [wystąpienia zarządzanego usługi Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)i [Teradata](connector-teradata.md#teradata-as-source)), w zależności od liczby partycji danych.

## <a name="parallel-copy"></a>Kopiowanie równoległe

Można ustawić kopiowanie równoległe ( `parallelCopies` Właściwość) dla działania kopiowania, aby wskazać równoległość, która ma być używana przez działanie kopiowania. Tę właściwość można traktować jako maksymalną liczbę wątków w działaniu kopiowania odczytywanych ze źródła lub zapisywać do magazynów danych ujścia równolegle.

Równoległa kopia jest prostopadła do [jednostek integracji danych](#data-integration-units) lub [samodzielnych węzłów podczerwieni](#self-hosted-integration-runtime-scalability). Jest on liczony dla wszystkich DIUs lub samodzielnych węzłów IR.

Dla każdego przebiegu działania kopiowania domyślnie Azure Data Factory dynamicznie stosuje optymalne ustawienie kopiowania równoległego na podstawie pary i wzorca danych źródła. 

> [!TIP]
> Domyślne zachowanie funkcji kopiowania równoległego zwykle zapewnia najlepszą przepływność, która jest automatycznie określana przez funkcję ADF na podstawie pary Source-ujścia, wzorca danych i liczby DIUs lub obsługiwanego przez siebie procesora CPU/pamięci/węzłów środowiska IR. Zapoznaj się z tematem [Rozwiązywanie problemów z wydajnością operacji kopiowania](copy-activity-performance-troubleshooting.md) przy dostrojeniu kopii równoległej.

W poniższej tabeli przedstawiono zachowanie kopiowania równoległego:

| Kopiuj scenariusz | Zachowanie kopiowania równoległego |
| --- | --- |
| Między magazynami plików | `parallelCopies` Określa równoległość **na poziomie pliku**. Fragmenty poszczególnych plików odbywają się automatycznie i w sposób przezroczysty. Zaprojektowano w celu użycia najlepszego odpowiedniego rozmiaru fragmentu dla danego typu magazynu danych w celu równoległego ładowania danych. <br/><br/>Rzeczywista liczba kopii równoległych działania kopiowania w czasie wykonywania nie jest większa niż liczba plików, które posiadasz. Jeśli zachowanie kopiowania jest **mergeFile** do ujścia plików, działanie kopiowania nie może korzystać z równoległości na poziomie plików. |
| Ze sklepu plików do magazynu innego niż plik | — Podczas kopiowania danych do Azure SQL Database lub Azure Cosmos DB, domyślna kopia równoległa zależy również od warstwy ujścia (liczba DTU/jednostek ru).<br>— Podczas kopiowania danych do tabeli platformy Azure domyślnie jest to 4. |
| Z magazynu innego niż plik do magazynu plików | — Podczas kopiowania danych z magazynu danych z włączoną obsługą partycji (w tym [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [wystąpienia zarządzanego usługi Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)i [Teradata](connector-teradata.md#teradata-as-source)), domyślna kopia równoległa to 4. Rzeczywista liczba kopii równoległych działania kopiowania w czasie wykonywania nie przekracza liczby posiadanych partycji danych. W przypadku używania samodzielnych Integration Runtime i kopiowania do obiektów BLOB/ADLS Gen2 platformy Azure Zwróć uwagę na to, że maksymalna efektywna kopia jest równa 4 lub 5 na węzeł IR.<br>— W przypadku innych scenariuszy kopiowanie równoległe nie zacznie obowiązywać. Nawet jeśli jest określona równoległość, nie jest stosowana. |
| Między magazynami nienależącymi do plików | — Podczas kopiowania danych do Azure SQL Database lub Azure Cosmos DB, domyślna kopia równoległa zależy również od warstwy ujścia (liczba DTU/jednostek ru).<br/>— Podczas kopiowania danych z magazynu danych z włączoną obsługą partycji (w tym [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [wystąpienia zarządzanego usługi Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)i [Teradata](connector-teradata.md#teradata-as-source)), domyślna kopia równoległa to 4.<br>— Podczas kopiowania danych do tabeli platformy Azure domyślnie jest to 4. |

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

W przypadku kopiowania danych ze źródłowego magazynu danych do magazynu danych ujścia można użyć usługi Azure Blob Storage lub Azure Data Lake Storage Gen2 jako tymczasowego magazynu przemieszczania. Przygotowanie jest szczególnie przydatne w następujących przypadkach:

- **Chcesz pozyskać dane z różnych magazynów danych do usługi Azure Synapse Analytics (dawniej SQL Data Warehouse) za pośrednictwem bazy danych Base, skopiować dane z/do płaty śnieg lub pozyskiwać dane z usług Amazon RedShift/HDFS performantly.** Więcej informacji zawiera temat:
  - [Użyj podstawy, aby załadować dane do usługi Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics).
  - [Łącznik płatka śniegu](connector-snowflake.md)
  - [Łącznik usługi Amazon RedShift](connector-amazon-redshift.md)
  - [Łącznik HDFS](connector-hdfs.md)
- **Nie chcesz otwierać portów innych niż port 80 i port 443 w zaporze ze względu na firmowe zasady IT.** Na przykład podczas kopiowania danych z lokalnego magazynu danych do Azure SQL Database lub analizy usługi Azure Synapse należy aktywować wychodzącą komunikację TCP na porcie 1433 zarówno dla zapory systemu Windows, jak i zapory firmowej. W tym scenariuszu kopia przygotowana może korzystać z własnego środowiska Integration Runtime, aby najpierw skopiować dane do tymczasowego magazynu za pośrednictwem protokołu HTTP lub HTTPS na porcie 443, a następnie załadować dane z przemieszczania do SQL Database lub Azure Synapse Analytics. W tym przepływie nie trzeba włączać portu 1433.
- **Czasami trwa przeprowadzenie hybrydowego przenoszenia danych (czyli kopiowania z lokalnego magazynu danych do magazynu danych w chmurze) przez wolne połączenie sieciowe.** Aby zwiększyć wydajność, można użyć kopii przygotowanej do skompresowania danych w środowisku lokalnym, co pozwala na przenoszenie danych do tymczasowego magazynu danych w chmurze. Następnie można zdekompresować dane w magazynie przemieszczania przed załadowaniem do docelowego magazynu danych.

### <a name="how-staged-copy-works"></a>Jak działa kopia przygotowana

Gdy uaktywniasz funkcję przemieszczania, najpierw dane są kopiowane z magazynu danych źródłowych do magazynu przemieszczania (należy przenieść własny obiekt blob platformy Azure lub Azure Data Lake Storage Gen2). Następnie dane są kopiowane z przemieszczania do magazynu danych ujścia. Działanie kopiowania Azure Data Factory automatycznie zarządza przepływem dwuetapowym, a także czyści dane tymczasowe z magazynu przemieszczania po zakończeniu przenoszenia danych.

![Kopia przygotowana](media/copy-activity-performance/staged-copy.png)

W przypadku aktywowania przenoszenia danych przy użyciu magazynu przemieszczania można określić, czy dane mają być kompresowane przed przeniesieniem danych ze źródłowego magazynu danych do magazynu przemieszczania, a następnie zdekompresować przed przeniesieniem danych z tymczasowego lub przejściowego magazynu danych do magazynu danych ujścia.

Obecnie nie można kopiować danych między dwoma magazynami danych, które są połączone za pośrednictwem różnych urzędów certyfikacji samodzielnych, ani z kopią etapową lub bez niej. W tym scenariuszu można skonfigurować dwa jawne działania kopiowania w łańcuchu w celu skopiowania danych ze źródła do przemieszczania z miejsca przejściowego do ujścia.

### <a name="configuration"></a>Konfiguracja

Skonfiguruj ustawienie **enableStaging** w działaniu kopiowania, aby określić, czy dane mają zostać przygotowane w magazynie przed załadowaniem ich do docelowego magazynu danych. Po ustawieniu **enableStaging** na `TRUE` , określ dodatkowe właściwości wymienione w poniższej tabeli. 

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| enableStaging |Określ, czy chcesz kopiować dane za pośrednictwem tymczasowego magazynu przemieszczania. |Fałsz |Nie |
| linkedServiceName |Określ nazwę usługi [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) połączoną usługę, która odwołuje się do wystąpienia magazynu używanego jako tymczasowy magazyn tymczasowy. |Nie dotyczy |Tak, gdy **enableStaging** jest ustawiona na wartość true |
| path |Określ ścieżkę, która ma zawierać dane przemieszczane. Jeśli nie podano ścieżki, usługa tworzy kontener do przechowywania danych tymczasowych. |Nie dotyczy |Nie |
| Ustawieniem EnableCompression |Określa, czy dane mają być kompresowane przed skopiowaniem do lokalizacji docelowej. To ustawienie zmniejsza ilość przesyłanych danych. |Fałsz |Nie |

>[!NOTE]
> W przypadku użycia kopiowania etapowego z włączoną kompresją usługa główna lub uwierzytelnianie MSI dla połączonej usługi obiektów BLOB są nieobsługiwane.

Oto przykładowa definicja działania kopiowania z właściwościami, które są opisane w powyższej tabeli:

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
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

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Przewodnik dotyczący wydajności i skalowalności działania kopiowania](copy-activity-performance.md)
- [Rozwiązywanie problemów z wydajnością działania kopiowania](copy-activity-performance-troubleshooting.md)
- [Używanie Azure Data Factory do migrowania danych z usługi Data Lake lub magazynu danych na platformę Azure](data-migration-guidance-overview.md)
- [Migrowanie danych z usługi Amazon S3 do usługi Azure Storage](data-migration-guidance-s3-azure-storage.md)