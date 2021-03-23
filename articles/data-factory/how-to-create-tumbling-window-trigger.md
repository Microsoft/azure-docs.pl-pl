---
title: Utwórz wyzwalacze okna wirowania w Azure Data Factory
description: Dowiedz się, jak utworzyć wyzwalacz w Azure Data Factory, który uruchamia potok w oknie wirowania.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: ad397b62adcbcf6a0e117950c0dc3be33e6522db
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779821"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Tworzenie wyzwalacza uruchamiającego potok w oknie wirowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł zawiera instrukcje dotyczące tworzenia, uruchamiania i monitorowania wyzwalacza okna wirowania. Aby uzyskać ogólne informacje o wyzwalaczach i obsługiwanych typach, zobacz temat [wykonywanie i wyzwalacze potoku](concepts-pipeline-execution-triggers.md).

Wyzwalacze okna wirowania to rodzaj wyzwalaczy uruchamianych w określonych odstępach czasu od wskazanego czasu rozpoczęcia przy zachowaniu stanu. Okna wirowania to ciągłe, nienakładające się na siebie serie odstępów czasu o stałych rozmiarach. Wyzwalacz okna wirowania ma relację jeden do jednego z potokiem i może odwoływać się tylko do pojedynczego potoku. Wyzwalacz okna wirowania jest bardziej silną alternatywą dla wyzwalacza harmonogramu oferującego zestaw funkcji dla złożonych scenariuszy ([zależność od innych wyzwalaczy okna wirowania](#tumbling-window-trigger-dependency), ponowne [uruchomienie zadania zakończonego niepowodzeniem](tumbling-window-trigger-dependency.md#monitor-dependencies) i [Ustawianie ponowień użytkownika dla potoków](#user-assigned-retries-of-pipelines)). Aby dowiedzieć się więcej o różnicach między wyzwalaczem harmonogramu i wyzwalaczem okna wirowania, odwiedź to [tutaj](concepts-pipeline-execution-triggers.md#trigger-type-comparison).

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

1. Aby utworzyć wyzwalacz okna wirowania w interfejsie użytkownika Data Factory, wybierz kartę **wyzwalacze** , a następnie wybierz pozycję **Nowy**. 
1. Po otwarciu okienka konfiguracja wyzwalacza wybierz pozycję **wirowania okno**, a następnie zdefiniuj właściwości wyzwalacza okna wirowania. 
1. Po zakończeniu wybierz pozycję **Zapisz**.

![Utwórz wyzwalacz okna wirowania w Azure Portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Właściwości typu wyzwalacza okna wirowania

Okno wirowania ma następujące właściwości typu wyzwalacza:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

Poniższa tabela zawiera ogólne omówienie głównych elementów JSON, które są związane z cyklem i harmonogramem wyzwalacza okna wirowania:

| Element JSON | Opis | Typ | Dozwolone wartości | Wymagane |
|:--- |:--- |:--- |:--- |:--- |
| **Wprowadź** | Typ wyzwalacza. Typ to stała wartość "TumblingWindowTrigger". | Ciąg | "TumblingWindowTrigger" | Tak |
| **runtimeState** | Bieżący stan czasu uruchomienia wyzwalacza.<br/>**Uwaga**: ten element to \<readOnly> . | Ciąg | "Uruchomiona", "zatrzymana", "wyłączona" | Tak |
| **jaką** | Ciąg, który reprezentuje jednostkę częstotliwości (minuty lub godziny), przy której wyzwalacz powtarza się. Jeśli wartości daty **rozpoczęcia** są bardziej szczegółowe niż wartość **częstotliwości** , daty **rozpoczęcia** są brane pod uwagę podczas obliczania granic okna. Na przykład jeśli wartość **częstotliwości** wynosi co godzinę, a wartość **StartTime** to 2017-09-01T10:10:10Z, pierwsze okno to (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Ciąg | "minuta", "godzina"  | Tak |
| **dat** | Dodatnia liczba całkowita oznaczająca interwał wartości właściwości **frequency**, która określa częstotliwość uruchamiania wyzwalacza. Jeśli na przykład **Interwał** wynosi 3, a **częstotliwość** to "godzina", wyzwalacz powtarza się co 3 godziny. <br/>**Uwaga**: minimalny interwał okna to 5 minut. | Liczba całkowita | Dodatnia liczba całkowita. | Tak |
| **Rozpoczęcia**| Pierwsze wystąpienie, które może znajdować się w przeszłości. Pierwszy interwał wyzwalacza to (**StartTime**,   +  **Interwał** StartTime). | DateTime | Wartość daty i godziny. | Tak |
| **endTime**| Ostatnie wystąpienie, które może znajdować się w przeszłości. | DateTime | Wartość daty i godziny. | Tak |
| **opóźnienie** | Czas opóźnienia rozpoczęcia przetwarzania danych w oknie. Uruchomienie potoku jest uruchamiane po upływie oczekiwanego czasu wykonania i ilości **opóźnienia**. **Opóźnienie** określa, jak długo wyzwalacz czeka poza czas oczekiwania przed wyzwoleniem nowego uruchomienia. **Opóźnienie** nie zmienia okna **StartTime**. Na przykład wartość **opóźnienia** 00:10:00 oznacza opóźnienie wynoszące 10 minut. | Zakres czasu<br/>(hh: mm: SS)  | Wartość TimeSpan, której wartością domyślną jest 00:00:00. | Nie |
| **maxConcurrency** | Liczba równoczesnych uruchomień wyzwalaczy, które są teraz uruchamiane dla systemu Windows. Na przykład, aby wypełniać z powrotem przebiegi godzinowe dla wczoraj wyniki w 24 Windows. Jeśli **maxConcurrency** = 10, zdarzenia wyzwalania są uruchamiane tylko dla pierwszych 10 okien (00:00-01:00-09:00-10:00). Po ukończeniu pierwszych 10 wyzwolonych uruchomień potoków uruchomienia wyzwalacza są generowane dla następnych 10 okien (10:00-11:00-19:00-20:00). Kontynuując ten przykład **maxConcurrency** = 10, jeśli jest 10 gotowych do użycia systemu Windows, istnieje 10 całkowitej liczby uruchomień potoku. Jeśli istnieje tylko jedno okno z gotowością, istnieje tylko jedno uruchomienie potoku. | Liczba całkowita | Liczba całkowita z zakresu od 1 do 50. | Tak |
| **retryPolicy: Count** | Liczba ponownych prób zanim uruchomienie potoku zostanie oznaczone jako "Niepowodzenie".  | Liczba całkowita | Liczba całkowita, gdzie wartość domyślna to 0 (brak ponownych prób). | Nie |
| **retryPolicy: intervalInSeconds** | Opóźnienie między ponownymi próbami określonymi w sekundach. | Liczba całkowita | Liczba sekund, w których wartość domyślna to 30. | Nie |
| **dependsOn: typ** | Typ TumblingWindowTriggerReference. Wymagane, jeśli określono zależność. | Ciąg |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Nie |
| **dependsOn: rozmiar** | Rozmiar okna wirowania zależności. | Zakres czasu<br/>(hh: mm: SS)  | Dodatnia wartość TimeSpan, w której domyślnym rozmiarem okna wyzwalacza podrzędnego  | Nie |
| **dependsOn: przesunięcie** | Przesunięcie wyzwalacza zależności. | Zakres czasu<br/>(hh: mm: SS) |  Wartość TimeSpan, która musi być ujemna w zależności od siebie. Jeśli żadna wartość nie zostanie określona, okno jest takie samo jak wyzwalacz. | Samozależność: tak<br/>Inne: nie  |

> [!NOTE]
> Po opublikowaniu wyzwalacza okna wirowaniaa nie można edytować **interwału** i **częstotliwości** .

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart i WindowEnd zmienne systemowe

Możesz użyć zmiennych systemowych **WindowStart** i **WindowEnd** wyzwalacza okna wirowania w definicji **potoku** (czyli w przypadku części zapytania). Przekaż zmienne systemowe jako parametry do potoku w definicji **wyzwalacza** . Poniższy przykład pokazuje, jak przekazać te zmienne jako parametry:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Aby użyć wartości zmiennej systemowej **WindowStart** i **WindowEnd** w definicji potoku, należy odpowiednio użyć parametrów "MyWindowStart" i "MyWindowEnd".

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Kolejność wykonywania systemu Windows w scenariuszu wypełniania

Jeśli czas rozpoczęcia wyzwalacza jest w przeszłości, a następnie na podstawie tej formuły, M = (CurrentTime-TriggerStartTime)/TumblingWindowSize, wyzwalacz generuje (w przeszłości) następującą liczbę uruchomień: {M} Kolejność wykonywania dla systemu Windows jest deterministyczna, od najstarszych do najnowszych interwałów. Obecnie nie można zmodyfikować tego zachowania.

### <a name="existing-triggerresource-elements"></a>Istniejące elementy TriggerResource

Następujące punkty mają zastosowanie do aktualizacji istniejących elementów **TriggerResource** :

* Nie można zmienić wartości elementu **częstotliwości** (lub rozmiaru okna) wyzwalacza wraz z elementem **Interval** , gdy wyzwalacz zostanie utworzony. Jest to wymagane do prawidłowego działania triggerRun ponowne uruchomienia i oceny zależności
* Jeśli wartość elementu **Endtime** wyzwalacza ulegnie zmianie (dodano lub Zaktualizowano), stan systemu Windows, który jest już przetwarzany, *nie* zostanie zresetowany. Wyzwalacz honoruje nową wartość **Endtime** . Jeśli nowa wartość **Endtime** jest wcześniejsza niż system Windows, który jest już wykonywany, wyzwalacz zostaje zatrzymany. W przeciwnym razie wyzwalacz zostaje zatrzymany po napotkaniu nowej wartości **Endtime** .

### <a name="user-assigned-retries-of-pipelines"></a>Użytkownik przypisał ponowną próbę potoków

W przypadku awarii potokowych wyzwalacz okna wirowania może ponowić próbę wykonania potoku, przy użyciu tych samych parametrów wejściowych, bez interwencji użytkownika. Można to określić przy użyciu właściwości "retryPolicy" w definicji wyzwalacza.

### <a name="tumbling-window-trigger-dependency"></a>Zależność wyzwalacza okna wirowania

Jeśli chcesz upewnić się, że wyzwalacz okna wirowania jest wykonywany tylko po pomyślnym wykonaniu innego wyzwalacza okna wirowania w fabryce danych, [Utwórz zależność wyzwalacza okna wirowania](tumbling-window-trigger-dependency.md).

### <a name="cancel-tumbling-window-run"></a>Anuluj uruchomienie okna wirowania

Można anulować przebiegi dla wyzwalacza okna wirowania, jeśli określone okno jest w stanie _oczekiwania_, _oczekiwanie na zależność_ lub stan _uruchomienia_

* Jeśli okno jest w stanie **uruchomienia** , Anuluj skojarzone _uruchomienie potoku_, a uruchomienie wyzwalacza zostanie oznaczone jako _anulowane_ później
* Jeśli okno jest w stanie **oczekiwania** lub **oczekiwanie na stan zależności** , można anulować monitorowanie okna:

![Anuluj wyzwalacz okna wirowania ze strony monitorowania](media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger.png)

Możesz również ponownie uruchomić anulowane okno. Ponowne uruchomienie spowoduje wykonanie _najnowszych_ opublikowanych definicji wyzwalacza, a zależności dla określonego okna zostaną _ponownie ocenione po ponownym_ uruchomieniu

![Uruchom ponownie wyzwalacz okna wirowania dla wcześniej anulowanych przebiegów](media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger.png)

## <a name="sample-for-azure-powershell"></a>Przykład dla Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tej sekcji przedstawiono sposób użycia Azure PowerShell do tworzenia, uruchamiania i monitorowania wyzwalacza.

1. Utwórz plik JSON o nazwie **MyTrigger.js** w folderze C:\ADFv2QuickStartPSH\ o następującej zawartości:

    > [!IMPORTANT]
    > Przed zapisaniem pliku JSON ustaw wartość parametru **StartTime** na bieżący czas UTC. Ustaw wartość elementu **Endtime** na godzinę wcześniejszą od bieżącego czasu UTC.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Utwórz wyzwalacz za pomocą polecenia cmdlet **Set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Upewnij się, że stan wyzwalacza został **zatrzymany** za pomocą polecenia cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Uruchom wyzwalacz za pomocą polecenia cmdlet **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Upewnij się, że stan wyzwalacza został **uruchomiony** przy użyciu polecenia cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Pobierz wyzwalacz uruchomiony w Azure PowerShell przy użyciu polecenia cmdlet **Get-AzDataFactoryV2TriggerRun** . Aby uzyskać informacje o przebiegu wyzwalacza, należy wykonać następujące polecenie okresowo. Zaktualizuj wartości **TriggerRunStartedAfter** i **TriggerRunStartedBefore** w taki sposób, aby odpowiadały wartościom w definicji wyzwalacza:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

Aby monitorować uruchomienia wyzwalacza i uruchomienia potoków w Azure Portal, zobacz [monitorowanie uruchomień potoków](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wykonywanie i wyzwalacze potoku](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Utwórz zależność wyzwalacza okna wirowania](tumbling-window-trigger-dependency.md).
* Dowiedz się, jak odwoływać się do metadanych wyzwalacza w potoku, zobacz [metadane wyzwalacza odwołań w uruchomieniach potoków](how-to-use-trigger-parameterization.md)
