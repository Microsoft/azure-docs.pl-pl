---
title: Monitorowanie działania kopiowania
description: Dowiedz się, jak monitorować wykonywanie działań kopiowania w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 47824095e892ca3c919d2d871feb612758ab2308
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417848"
---
# <a name="monitor-copy-activity"></a>Monitorowanie działania kopiowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób monitorowania wykonywania działania kopiowania w usłudze Azure Data Factory. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="monitor-visually"></a>Monitorowanie wizualne

Po utworzeniu i opublikowaniu potoku w usłudze Azure Data Factory można skojarzyć go z wyzwalaczem lub ręcznie rozpocząć uruchamianie ad hoc. Można monitorować wszystkie uruchomień potoku natywnie w środowisku użytkownika usługi Azure Data Factory. Dowiedz się więcej o monitorowaniu usługi Azure Data Factory w ujęciu ogólnym, [monitoruj wizualnie usługę Azure Data Factory.](monitor-visually.md)

Aby monitorować działanie Kopiowanie, przejdź do fabryki danych **Author & Monitor interfejsu** użytkownika. Na karcie **Monitor** zobaczysz listę uruchomień potoku, kliknij łącze **nazwa potoku,** aby uzyskać dostęp do listy działań uruchamianych w potoku.

![Monitorowanie przebiegu aktywności kopiowania](./media/copy-activity-overview/monitor-pipeline-run.png)

Na tym poziomie można zobaczyć łącza do kopiowania danych wejściowych, danych wyjściowych i błędów (jeśli działanie kopiowania nie powiedzie się), a także statystyki, takie jak czas trwania/stan. Kliknięcie przycisku **Szczegóły** (okulary) obok nazwy działania kopiowania daje głębokie szczegóły dotyczące wykonywania działania kopiowania. 

![Monitorowanie przebiegu aktywności kopiowania](./media/copy-activity-overview/monitor-copy-activity-run.png)

W tym graficznym widoku monitorowania usługa Azure Data Factory przedstawia informacje o wykonaniu działania kopiowania, w tym wolumin odczytu/zapisu danych, liczbę plików/wierszy danych skopiowanych ze źródła do ujścia, przepływność, konfiguracje zastosowane dla scenariusza kopiowania, kroki działania kopiowania przechodzi z odpowiednimi czasami trwania i szczegółami i inne. Zapoznaj się z [tą tabelą](#monitor-programmatically) na każdej możliwej metryki i jej szczegółowy opis. 

W niektórych scenariuszach po uruchomieniu działania kopiowania w fabryce danych zobaczysz **"Porady dotyczące dostrajania wydajności"** u góry widoku monitorowania aktywności kopiowania, jak pokazano w przykładzie. Porady informują o wąskim gardle zidentyfikowanym przez podajnikU ADF dla określonego uruchomienia kopii, wraz z sugestią, co należy zmienić, aby zwiększyć przepływność kopiowania. Dowiedz się więcej o [poradach dotyczących automatycznego dostrajania wydajności](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Szczegóły **wykonania** na dole i czas trwania opisuje kluczowe kroki działania kopiowania przechodzi, co jest szczególnie przydatne do rozwiązywania problemów z wydajnością kopiowania. Wąskie gardło przebiegu kopii jest tym, który ma najdłuższy czas trwania. Zobacz [Rozwiązywanie problemów z wydajnością działania kopiowania,](copy-activity-performance-troubleshooting.md) co reprezentuje każdy etap, oraz szczegółowe wskazówki dotyczące rozwiązywania problemów.

**Przykład: Kopiowanie z amazon S3 do usługi Azure Data Lake Storage Gen2**

![Szczegóły uruchomienia działania kopiowania monitoruj](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitoruj programowo

Szczegóły wykonania działania kopiowania i właściwości wydajności są również zwracane w sekcji **Wynik** > uruchomienia kopiowania**Output,** który jest używany do renderowania widoku monitorowania interfejsu użytkownika. Poniżej znajduje się pełna lista właściwości, które mogą być zwracane. Zobaczysz tylko właściwości, które mają zastosowanie do scenariusza kopiowania. Aby uzyskać informacje dotyczące sposobu monitorowania działań działania w ujęciu programowym w ogóle, zobacz [Programowo monitorować fabrykę danych platformy Azure](monitor-programmatically.md).

| Nazwa właściwości  | Opis | Jednostka w wyjściu |
|:--- |:--- |:--- |
| Dataread | Rzeczywista ilość danych odczytywanych ze źródła. | Wartość Int64 w bajtach |
| dataWritten (daneWritten) | Rzeczywiste instalowanie danych zapisanych/zatwierdzonych do ujścia. Rozmiar może się `dataRead` różnić od rozmiaru, ponieważ odnosi się do sposobu przechowywania danych przez każdy magazyn danych. | Wartość Int64 w bajtach |
| plikiCzytaj | Liczba plików odczytywanych ze źródła opartego na plikach. | Wartość Int64 (bez jednostki) |
| filesWritten (plikSpis) | Liczba plików zapisanych/zatwierdzonych do ujścia opartego na plikach. | Wartość Int64 (bez jednostki) |
| źródłoPeakConnections | Szczytowa liczba równoczesnych połączeń nawiązanych z magazynem danych źródłowych podczas wykonywania działania Kopiowanie. | Wartość Int64 (bez jednostki) |
| sinkPeakConnections (zlew) | Szczytowa liczba równoczesnych połączeń nawiązanych do magazynu danych ujścia podczas wykonywania działania Kopiowanie. | Wartość Int64 (bez jednostki) |
| wierszeCzyczytaj | Liczba wierszy odczytanych ze źródła (nie dotyczy kopii binarnej). | Wartość Int64 (bez jednostki) |
| Rowscopied | Liczba wierszy skopiowanych do ujścia (nie dotyczy kopii binarnej). | Wartość Int64 (bez jednostki) |
| rzędySkowane | Liczba niezgodnych wierszy, które zostały pominięte. Niezgodne wiersze można pominąć, ustawiając `enableSkipIncompatibleRow` wartość true. | Wartość Int64 (bez jednostki) |
| kopiowanieDuration | Czas trwania uruchomienia kopii. | Wartość Int32, w sekundach |
| danych | Szybkość przesyłania danych. | Liczba zmiennoprzecinkowy w KBps |
| źródłoPeakConnections | Szczytowa liczba równoczesnych połączeń nawiązanych z magazynem danych źródłowych podczas wykonywania działania Kopiowanie. | Wartość Int32 (bez jednostki) |
| sinkPeakConnections (zlew)| Szczytowa liczba równoczesnych połączeń nawiązanych do magazynu danych ujścia podczas wykonywania działania Kopiowanie.| Wartość Int32 (bez jednostki) |
| sqlDwPolyBase | Czy PolyBase jest używany, gdy dane są kopiowane do magazynu danych SQL. | Wartość logiczna |
| redshiftUnload | Czy UNLOAD jest używany, gdy dane są kopiowane z Redshift. | Wartość logiczna |
| hdfsDistcp | Czy DistCp jest używany, gdy dane są kopiowane z hdfs. | Wartość logiczna |
| effectiveIntegrationRuntime | Środowisko wykonawcze integracji (IR) lub środowiska wykonawcze używane `<IR name> (<region if it's Azure IR>)`do zasilania uruchomienia działania w formacie . | Tekst (ciąg znaków) |
| używanejintegracji danychjednostki | Skuteczne jednostki integracji danych podczas kopiowania. | Wartość Int32 |
| używaneParallelCopies | Efektywne równolegleCopies podczas kopiowania. | Wartość Int32 |
| redirectRowPath | Ścieżka do dziennika pominiętych niekompatybilnych wierszy w `redirectIncompatibleRowSettings` magazynie obiektów blob skonfigurowanych we właściwości. Patrz [Odporność na uszkodzenia](copy-activity-overview.md#fault-tolerance). | Tekst (ciąg znaków) |
| executionDetails | Więcej szczegółów na temat etapów, przez które przechodzi działanie Kopiowanie, oraz odpowiednich kroków, czasów trwania, konfiguracji itd. Nie zaleca się analizować tej sekcji, ponieważ może się to zmienić. Aby lepiej zrozumieć, w jaki sposób pomaga zrozumieć i rozwiązywać problemy z wydajnością kopiowania, zapoznaj się [z sekcją Monitoruj wizualnie.](#monitor-visually) | Tablica |
| perfRekomematację | Kopiuj wskazówki dotyczące dostrajania wydajności. Szczegółowe informacje można znaleźć w [poradach dotyczących dostrajania wydajności.](copy-activity-performance-troubleshooting.md#performance-tuning-tips) | Tablica |

**Przykład:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
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
Zobacz inne artykuły dotyczące działania kopiowania:

\-[Omówienie działania kopiowania](copy-activity-overview.md)

\- [Wydajność działania kopiowania](copy-activity-performance.md)