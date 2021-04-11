---
title: Monitorowanie działania kopiowania
description: Dowiedz się więcej na temat monitorowania wykonywania działania kopiowania w Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: jingwang
ms.openlocfilehash: 4aefeea33e61b6ee2541e929dbeb3fc36343cac5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771383"
---
# <a name="monitor-copy-activity"></a>Monitorowanie działania kopiowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób monitorowania wykonywania działania kopiowania w Azure Data Factory. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="monitor-visually"></a>Monitorowanie wizualne

Po utworzeniu i opublikowaniu potoku w Azure Data Factory można skojarzyć go z wyzwalaczem lub ręcznie uruchomić przebieg ad hoc. Wszystkie uruchomienia potoków można monitorować natywnie w środowisku użytkownika Azure Data Factory. Poznaj ogólne informacje o monitorowaniu Azure Data Factory na podstawie [wizualnie monitorowanych Azure Data Factory](monitor-visually.md).

Aby monitorować przebieg działania kopiowania, przejdź do interfejsu użytkownika narzędzia Monitor fabryki danych **&** . Na karcie **monitor** zostanie wyświetlona lista uruchomień potoku, kliknij link **Nazwa potoku** , aby uzyskać dostęp do listy uruchomień działania w ramach uruchomienia potoku.

![Monitorowanie uruchomienia potoku](./media/copy-activity-overview/monitor-pipeline-run.png)

Na tym poziomie można zobaczyć linki do danych wejściowych, danych wyjściowych i błędów działania kopiowania (w przypadku niepowodzenia uruchomienia działania kopiowania), a także statystyki, takie jak czas trwania/stan. Kliknięcie przycisku **szczegóły** (okularów) obok nazwy działania kopiowania spowoduje udostępnienie szczegółowych informacji dotyczących wykonywania działania kopiowania. 

![Monitoruj przebiegi działania kopiowania](./media/copy-activity-overview/monitor-copy-activity-run.png)

W tym widoku graficznym monitorowania Azure Data Factory przedstawia informacje o wykonywaniu działania kopiowania, w tym woluminy do odczytu/zapisu danych, liczba plików/wierszy danych skopiowanych ze źródła do ujścia, przepływność, konfiguracje zastosowane do scenariusza kopiowania, kroki kopiowania odbywają się za pomocą odpowiednich czasów trwania i szczegółów. Zapoznaj się z [tą tabelą](#monitor-programmatically) dla każdej możliwej metryki i jej szczegółowego opisu. 

W niektórych scenariuszach, gdy uruchomisz działanie kopiowania w Data Factory, zobaczysz **"porady dotyczące dostrajania wydajności"**  w górnej części widoku monitorowanie działania kopiowania, jak pokazano w przykładzie. Porady wskazują na wąskie gardła identyfikowane przez ADF dla określonej kopii, a także sugestię dotyczącą tego, co należy zmienić, aby zwiększyć przepływność kopiowania. Dowiedz się więcej na temat [porad dotyczących dostrajania automatycznego](copy-activity-performance-troubleshooting.md#performance-tuning-tips)zwiększania wydajności.

**Szczegóły i czas trwania ostatniego wykonania** opisują kluczowe kroki działania kopiowania, które jest szczególnie przydatne w przypadku rozwiązywania problemów z wydajnością kopiowania. Wąskie gardła przebiegu kopii są takie same, jak najdłuższy czas trwania. Zapoznaj się z informacjami o [rozwiązywaniu problemów z wydajnością operacji kopiowania](copy-activity-performance-troubleshooting.md) na potrzeby poszczególnych etapów i szczegółowe wskazówki dotyczące rozwiązywania problemów.

**Przykład: Kopiuj z usługi Amazon S3 do Azure Data Lake Storage Gen2**

![Monitoruj szczegóły uruchomienia działania kopiowania](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitoruj programowo

Szczegóły wykonania działania kopiowania i charakterystyki wydajności są również zwracane w sekcji   >  **danych wyjściowych** działania Uruchom działanie kopiowania, która jest używana do renderowania widoku monitorowania interfejsu użytkownika. Poniżej znajduje się kompletna lista właściwości, które mogą zostać zwrócone. Zobaczysz tylko właściwości, które mają zastosowanie do Twojego scenariusza kopiowania. Aby uzyskać informacje o tym, jak ogólnie monitorować uruchomienia działań, zobacz programowe [monitorowanie fabryki danych Azure](monitor-programmatically.md).

| Nazwa właściwości  | Opis | Jednostka w danych wyjściowych |
|:--- |:--- |:--- |
| Odczyt DataReady | Rzeczywista ilość danych odczytywanych ze źródła. | Wartość Int64, w bajtach |
| zapisywana | Rzeczywista instalacja danych zapisywana/zatwierdzona do ujścia. Rozmiar może różnić się od `dataRead` rozmiaru, ponieważ odnosi się do tego, jak każdy magazyn danych przechowuje dane. | Wartość Int64, w bajtach |
| filesRead | Liczba plików odczytywanych ze źródła opartego na plikach. | Wartość Int64 (brak jednostki) |
| filesWritten | Liczba plików, które zostały zapełnione/przekazane do ujścia opartego na plikach. | Wartość Int64 (brak jednostki) |
| filesSkipped | Liczba plików pominiętych w źródle opartym na plikach. | Wartość Int64 (brak jednostki) |
| dataConsistencyVerification | Szczegóły weryfikacji spójności danych, gdzie można sprawdzić, czy skopiowane dane zostały zweryfikowane pod kątem spójności między magazynem źródłowym i docelowym. Dowiedz się więcej z [tego artykułu](copy-activity-data-consistency.md#monitoring). | Tablica |
| sourcePeakConnections | Szczytowa liczba jednoczesnych połączeń ustanowionych w źródłowym magazynie danych podczas uruchomienia działania kopiowania. | Wartość Int64 (brak jednostki) |
| sinkPeakConnections | Szczytowa liczba jednoczesnych połączeń ustanowionych do magazynu danych ujścia podczas uruchomienia działania kopiowania. | Wartość Int64 (brak jednostki) |
| rowsRead | Liczba wierszy odczytanych ze źródła. Ta Metryka nie ma zastosowania w przypadku kopiowania plików jako-jest bez analizy, na przykład gdy źródła i ujścia danych są typu binarnego, lub innego typu formatu z identycznymi ustawieniami. | Wartość Int64 (brak jednostki) |
| rowsCopied | Liczba wierszy skopiowanych do ujścia. Ta Metryka nie ma zastosowania w przypadku kopiowania plików jako-jest bez analizy, na przykład gdy źródła i ujścia danych są typu binarnego, lub innego typu formatu z identycznymi ustawieniami.  | Wartość Int64 (brak jednostki) |
| rowsSkipped | Liczba niezgodnych wierszy, które zostały pominięte. Aby można było pominąć niezgodne wiersze, można ustawić `enableSkipIncompatibleRow` wartość true. | Wartość Int64 (brak jednostki) |
| copyDuration | Czas trwania kopiowania. | Wartość Int32 (w sekundach) |
| danych | Współczynnik transferu danych obliczony przez `dataRead` podzieloną przez `copyDuration` . | Liczba zmiennoprzecinkowa w KB/s |
| sourcePeakConnections | Szczytowa liczba jednoczesnych połączeń ustanowionych w źródłowym magazynie danych podczas uruchomienia działania kopiowania. | Wartość Int32 (brak jednostki) |
| sinkPeakConnections| Szczytowa liczba jednoczesnych połączeń ustanowionych do magazynu danych ujścia podczas uruchomienia działania kopiowania.| Wartość Int32 (brak jednostki) |
| sqlDwPolyBase | Niezależnie od tego, czy baza danych jest używana, gdy dane są kopiowane do usługi Azure Synapse Analytics. | Wartość logiczna |
| redshiftUnload | Czy ZWALNIAnie jest używane, gdy dane są kopiowane z RedShift. | Wartość logiczna |
| hdfsDistcp | Określa, czy pomocą distcp jest używany, gdy dane są kopiowane z systemu plików HDFS. | Wartość logiczna |
| effectiveIntegrationRuntime | Środowisko Integration Runtime (IR) lub środowisko uruchomieniowe służące do włączania uruchomienia działania w formacie `<IR name> (<region if it's Azure IR>)` . | Tekst (ciąg) |
| usedDataIntegrationUnits | Efektywna jednostka integracji danych podczas kopiowania. | Wartość Int32 |
| usedParallelCopies | Efektywna parallelCopies podczas kopiowania. | Wartość Int32 |
| logPath | Ścieżka do dziennika sesji pominiętych danych w magazynie obiektów BLOB. Zobacz [odporność na uszkodzenia](copy-activity-overview.md#fault-tolerance). | Tekst (ciąg) |
| executionDetails | Więcej szczegółów na temat etapów działania kopiowania oraz odpowiednich czynności, czasów trwania, konfiguracji itd. Nie zalecamy przeanalizowania tej sekcji, ponieważ może ona ulec zmianie. Aby lepiej zrozumieć, jak ułatwia zrozumienie i rozwiązywanie problemów z wydajnością kopiowania, zapoznaj się z sekcją [Monitoruj wizualizację](#monitor-visually) . | Tablica |
| perfRecommendation | Kopiuj wskazówki dotyczące dostrajania wydajności. Aby uzyskać szczegółowe informacje, zobacz [porady dotyczące dostrajania wydajności](copy-activity-performance-troubleshooting.md#performance-tuning-tips) . | Tablica |
| billingReference | Użycie rozliczeń dla danego przebiegu. Więcej informacji na temat [monitorowania zużycia na poziomie uruchomienia działania](plan-manage-costs.md#monitor-consumption-at-activity-run-level). | Obiekt |
| durationInQueue | Czas trwania kolejki na sekundę przed rozpoczęciem działania kopiowania. | Obiekt |

**Przykład:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

\-[Przegląd działania kopiowania](copy-activity-overview.md)

\- [Wydajność działania kopiowania](copy-activity-performance.md)