---
title: Przepływ danych działania
description: Jak wykonywać przepływy danych z wewnątrz potoku fabryki danych.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 04/14/2021
ms.openlocfilehash: b0d42b42ab44b51294833e40b7fa0174256c655a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517190"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Przepływ danych aktywności w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj działania Przepływ danych do przekształcania i przenoszenia danych za pośrednictwem przepływów danych mapowania. Jeśli nie masz jeszcze dostępu do przepływów danych, zobacz Mapping Przepływ danych overview (Omówienie mapowania [Przepływ danych danych)](concepts-data-flow-overview.md)

## <a name="syntax"></a>Składnia

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
Dataflow | Odwołanie do wykonywanego Przepływ danych | DataFlowReference | Tak
integrationRuntime | Środowisko obliczeniowe, w którym działa przepływ danych. Jeśli nie zostanie określony, zostanie użyte automatyczne rozwiązanie środowiska Azure Integration Runtime. | IntegrationRuntimeReference | Nie
compute.coreCount | Liczba rdzeni używanych w klastrze Spark. Można określić tylko wtedy, gdy jest używane automatyczne rozwiązywanie problemów ze środowiskiem Azure Integration Runtime | 8, 16, 32, 48, 80, 144, 272 | Nie
compute.computeType | Typ obliczeń używanych w klastrze Spark. Można określić tylko wtedy, gdy jest używane automatyczne rozwiązywanie problemów ze środowiskiem Azure Integration Runtime | "Ogólne", "ComputeOptimized", "MemoryOptimized" | Nie
staging.linkedService | Jeśli używasz źródła lub ujścia Azure Synapse Analytics magazynu, określ konto magazynu używane do przemieszczania technologii PolyBase.<br/><br/>Jeśli usługa Azure Storage jest skonfigurowana przy użyciu punktu końcowego usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej z włączoną usługą "Zezwalaj na zaufaną usługę firmy Microsoft" na koncie magazynu. Zobacz Wpływ używania punktów końcowych usługi sieci wirtualnej w usłudze [Azure Storage.](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage) Poznaj również wymagane konfiguracje dla [usługi Azure Blob](connector-azure-blob-storage.md#managed-identity) i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) odpowiednio.<br/> | LinkedServiceReference | Tylko wtedy, gdy przepływ danych odczytuje lub zapisuje w Azure Synapse Analytics
staging.folderPath | Jeśli używasz źródła lub ujścia Azure Synapse Analytics, ścieżka folderu na koncie magazynu obiektów blob używana do przemieszczania technologii PolyBase | Ciąg | Tylko wtedy, gdy przepływ danych odczytuje lub zapisuje dane Azure Synapse Analytics
Tracelevel | Ustawianie poziomu rejestrowania wykonywania działania przepływu danych | Fine ,Zgrubne, Brak | Nie

![Wykonaj Przepływ danych](media/data-flow/activity-data-flow.png "Wykonaj Przepływ danych")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dynamiczne obliczanie przepływu danych w czasie wykonywania

Właściwości Liczba rdzeni i Typ obliczeniowy można ustawić dynamicznie, aby dostosować je do rozmiaru przychodzących danych źródłowych w czasie wykonywania. Użyj działań potoku, takich jak wyszukiwanie lub uzyskiwanie metadanych, aby znaleźć rozmiar danych źródłowego zestawu danych. Następnie użyj właściwości Dodaj zawartość dynamiczną Przepływ danych właściwości działania.

![Dynamiczne Przepływ danych](media/data-flow/dyna1.png "Dynamiczny przepływ danych")

[Oto krótki samouczek wideo wyjaśniający tę technikę](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Przepływ danych Integration Runtime

Wybierz, Integration Runtime do użycia na Przepływ danych wykonania działania. Domyślnie program Data Factory automatycznie rozpozna środowisko Azure Integration Runtime z czterema rdzeniami procesu roboczego. To ir ma typ obliczeniowy ogólnego przeznaczenia i działa w tym samym regionie co fabryka. W przypadku zoperacyjnych potoków zdecydowanie zaleca się utworzenie własnych środowisk Azure Integration Runtime, które definiują określone regiony, typ obliczeniowy, liczbę rdzeni i czas wygaśnięcia dla wykonywania działań przepływu danych.

Minimalny typ obliczeniowy usługi Ogólnego przeznaczenia (zoptymalizowany pod kątem obliczeń nie jest zalecany w przypadku dużych obciążeń) z konfiguracją 8+8 (16 rdzeni wirtualnych), a 10-minutowa jest minimalnym zaleceniem dla większości obciążeń produkcyjnych. Ustawiając mały czas wygaśnięcia, Azure IR może zachować ciepły klaster, który nie spowoduje naliczenie kilku minut czasu rozpoczęcia dla zimnego klastra. Możesz jeszcze bardziej przyspieszyć wykonywanie przepływów danych, klikając pozycję "Szybkie ponowne użycie" w konfiguracji Azure IR przepływu danych. Aby uzyskać więcej informacji, zobacz [Azure Integration Runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> Wybór Integration Runtime w działaniu Przepływ danych ma zastosowanie tylko do *wyzwalanych wykonań* potoku. Debugowanie potoku przy użyciu przepływów danych działa w klastrze określonym w sesji debugowania.

### <a name="polybase"></a>PolyBase

Jeśli używasz serwera usługi Azure Synapse Analytics jako ujścia lub źródła, musisz wybrać lokalizację przemieszczania dla ładowania wsadowego technologii PolyBase. Program PolyBase umożliwia zbiorcze ładowanie zamiast ładowania danych wiersz po wierszu. Program PolyBase znacząco skraca czas ładowania do Azure Synapse Analytics.

## <a name="logging-level"></a>Poziom rejestrowania

Jeśli nie wymagasz, aby każde wykonanie potoku działań przepływu danych było w pełni rejestrować wszystkie pełne dzienniki telemetrii, możesz opcjonalnie ustawić poziom rejestrowania na "Podstawowy" lub "Brak". Podczas wykonywania przepływów danych w trybie pełnej informacji (ustawienie domyślne) żądasz usługi ADF, aby w pełni rejestrować aktywność na każdym poziomie poszczególnych partycji podczas przekształcania danych. Może to być kosztowna operacja, więc tylko włączenie pełnych informacji podczas rozwiązywania problemów może poprawić ogólny przepływ danych i wydajność potoku. Tryb "Podstawowy" będzie rejestrować tylko czasy trwania przekształceń, natomiast tryb "Brak" będzie zawierał tylko podsumowanie czasów trwania.

![Poziom rejestrowania](media/data-flow/logging.png "Ustawianie poziomu rejestrowania")

## <a name="sink-properties"></a>Właściwości ujścia

Funkcja grupowania w przepływach danych umożliwia zarówno ustawienie kolejności wykonywania ujść, jak i grupowanie ujść przy użyciu tego samego numeru grupy. Aby ułatwić zarządzanie grupami, możesz poprosić platformę ADF o równoległe uruchamianie ujść w tej samej grupie. Możesz również ustawić grupę ujścia tak, aby kontynuowała pracę nawet wtedy, gdy jedno z ujść napotka błąd.

Domyślnym zachowaniem ujść przepływu danych jest sekwencyjne wykonywanie każdego ujścia w sposób szeregowy i niepowodzenie przepływu danych w przypadku napotkania błędu w ujściu. Ponadto wszystkie ujścia są domyślnie ustawiane na tę samą grupę, chyba że we właściwościach przepływu danych ustawisz różne priorytety dla ujść.

![Właściwości ujścia](media/data-flow/sink-properties.png "Ustawianie właściwości ujścia")

## <a name="parameterizing-data-flows"></a>Parametryzacja przepływów danych

### <a name="parameterized-datasets"></a>Sparametryzowane zestawy danych

Jeśli przepływ danych używa sparametryzowanego zestawu danych, ustaw wartości parametrów na **karcie** Ustawienia.

![Wykonywanie Przepływ danych parametrów](media/data-flow/params.png "Parametry")

### <a name="parameterized-data-flows"></a>Sparametryzowane przepływy danych

Jeśli przepływ danych jest sparametryzowane, ustaw dynamiczne wartości parametrów przepływu danych na **karcie** Parametry. Możesz użyć języka wyrażeń potoku ADF lub języka wyrażeń przepływu danych, aby przypisać wartości parametrów dynamicznych lub literałów. Aby uzyskać więcej informacji, [zobacz Przepływ danych Parameters](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Sparametryzowane właściwości obliczeniowe.

Jeśli używasz automatycznego rozpoznawania środowiska Azure Integration Runtime i określasz wartości compute.coreCount i compute.computeType, możesz sparametryzować liczbę rdzeni lub typ obliczeniowy.

![Przykład Przepływ danych wykonywania](media/data-flow/parameterize-compute.png "Przykład parametru")

## <a name="pipeline-debug-of-data-flow-activity"></a>Debugowanie potoku Przepływ danych danych

Aby wykonać uruchomienie potoku debugowania z Przepływ danych, musisz włączyć tryb debugowania przepływu danych za pomocą suwaka debugowania Przepływ danych **debugowania** na górnym pasku. Tryb debugowania umożliwia uruchamianie przepływu danych w aktywnym klastrze Spark. Aby uzyskać więcej informacji, zobacz [Tryb debugowania](concepts-data-flow-debug-mode.md).

![Przycisk Debugowanie](media/data-flow/debug-button-3.png "Przycisk Debugowanie")

Potok debugowania jest uruchamiany względem aktywnego klastra debugowania, a nie środowiska Integration Runtime określonego w ustawieniach Przepływ danych debugowania. Środowisko obliczeniowe debugowania można wybrać podczas uruchamiania trybu debugowania.

## <a name="monitoring-the-data-flow-activity"></a>Monitorowanie Przepływ danych danych

Działanie Przepływ danych ma specjalne środowisko monitorowania, w którym można wyświetlać informacje dotyczące partycjonowania, czasu etapu i danych. Otwórz okienko monitorowania za pomocą ikony okularów w **obszarze Akcje**. Aby uzyskać więcej informacji, zobacz [Monitorowanie przepływów danych](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Użycie Przepływ danych powoduje kolejne działanie

Działanie przepływu danych zwraca metryki dotyczące liczby wierszy zapisywanych w każdym ujściu i wierszy odczytanych z każdego źródła. Te wyniki są zwracane `output` w sekcji wyniku uruchomienia działania. Zwrócone metryki mają format poniższego pliku JSON.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Aby na przykład uzyskać dostęp do liczby wierszy zapisywanych w ujściu o nazwie "sink1" w działaniu o nazwie "dataflowActivity", użyj . `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`

Aby uzyskać liczbę wierszy odczytanych ze źródła o nazwie "source1" użytego w tym ujściu, `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` użyj .

> [!NOTE]
> Jeśli ujścia nie ma żadnych napisanych wierszy, nie będzie ono wyświetlane w metrykach. Istnienie można zweryfikować przy użyciu `contains` funkcji . Na przykład będzie `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` sprawdzać, czy jakiekolwiek wiersze zostały zapisane w ujściu 1.

## <a name="next-steps"></a>Następne kroki

Zobacz działania przepływu sterowania obsługiwane przez Data Factory: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline (Wykonywanie potoku)](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
