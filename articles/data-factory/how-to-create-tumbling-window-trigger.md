---
title: Tworzenie wyzwalaczy okien tumbling w fabryce danych platformy Azure
description: Dowiedz się, jak utworzyć wyzwalacz w usłudze Azure Data Factory, który uruchamia potok w oknie blokowania.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 97c8f8a5bb2111264e9459a7d2128c1ab7c2503d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414427"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Tworzenie wyzwalacza uruchamiającego potok w oknie wirowania
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono kroki tworzenia, uruchamiania i monitorowania wyzwalacza okna brzusznego. Aby uzyskać ogólne informacje na temat wyzwalaczy i obsługiwanych typów, zobacz [Wykonywanie potoku i wyzwalacze](concepts-pipeline-execution-triggers.md).

Wyzwalacze okna wirowania to rodzaj wyzwalaczy uruchamianych w określonych odstępach czasu od wskazanego czasu rozpoczęcia przy zachowaniu stanu. Okna wirowania to ciągłe, nienakładające się na siebie serie odstępów czasu o stałych rozmiarach. Wyzwalacz okna tumbling ma relację jeden do jednego z potoku i może odwoływać się tylko do potoku pojedynczej.

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

1. Aby utworzyć wyzwalacz okna w interfejsie użytkownika fabryki danych, wybierz kartę **Wyzwalacze,** a następnie wybierz pozycję **Nowy**. 
1. Po otwarciu okienka konfiguracji wyzwalacza wybierz pozycję **Okno tumbling**, a następnie zdefiniuj właściwości wyzwalacza okna. 
1. Po zakończeniu wybierz pozycję **Zapisz**.

![Tworzenie wyzwalacza okna w witrynie Azure portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Właściwości typu wyzwalacza okna tumbling

Okno tumbling ma następujące właściwości typu wyzwalacza:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: <<datetime – optional>>,
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

Poniższa tabela zawiera omówienie wysokiego poziomu głównych elementów JSON, które są związane z cykli i planowania wyzwalacza okna tumbling:

| Element JSON | Opis | Typ | Dozwolone wartości | Wymagany |
|:--- |:--- |:--- |:--- |:--- |
| **Typu** | Typ wyzwalacza. Typ jest stałą wartością "TumblingWindowTrigger". | Ciąg | "TumblingWindowTrigger" | Tak |
| **stan środowiska uruchomieniowego** | Bieżący stan czasu wykonywania wyzwalacza.<br/>**Uwaga:** Ten \<element jest odczytywanytylko>. | Ciąg | "Rozpoczęty", "Zatrzymany", "Wyłączony" | Tak |
| **Częstotliwości** | Ciąg reprezentujący jednostkę częstotliwości (minuty lub godziny), przy której wyzwalacz powtarza się. Jeśli wartości daty **startTime** są bardziej szczegółowe niż wartość **częstotliwości,** **daty startTime** są uwzględniane podczas obliczania granic okna. Na przykład jeśli wartość **częstotliwości** jest co godzinę, a wartość **startTime** to 2017-09-01T10:10:10Z, pierwsze okno jest (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Ciąg | "minuta", "godzina"  | Tak |
| **Interwał** | Dodatnia liczba całkowita oznaczająca interwał wartości właściwości **frequency**, która określa częstotliwość uruchamiania wyzwalacza. Na przykład jeśli **interwał** wynosi 3, a **częstotliwość** to "godzina", wyzwalacz powtarza się co 3 godziny. <br/>**Uwaga:** Minimalny interwał okna wynosi 5 minut. | Liczba całkowita | Dodatnia wartość całkowita. | Tak |
| **startTime**| Pierwsze wystąpienie, które może być w przeszłości. Pierwszy interwał wyzwalacza to (**startTime**, **startTime** + **interval**). | DateTime | Wartość DateTime. | Tak |
| **Endtime**| Ostatnie wystąpienie, które może być w przeszłości. | DateTime | Wartość DateTime. | Tak |
| **Opóźnienie** | Czas opóźnienia rozpoczęcia przetwarzania danych dla okna. Uruchomienie potoku jest uruchamiane po oczekiwanym czasie wykonania plus ilość **opóźnienia**. **Opóźnienie** określa, jak długo wyzwalacz czeka poza odpowiednim czasie przed wyzwoleniem nowego uruchomienia. **Opóźnienie** nie zmienia okna **startTime**. Na przykład wartość **opóźnienia** 00:10:00 oznacza opóźnienie 10 minut. | Zakres czasu<br/>(hh:mm:ss)  | Wartość timespan, w której wartość domyślna to 00:00:00. | Nie |
| **Maxconcurrency** | Liczba równoczesnych uruchomień wyzwalacza, które są uruchamiane dla okien, które są gotowe. Na przykład, aby przywrócić wypełnienie godzinowe działa dla wczoraj wyniki w 24 oknach. Jeśli **maxConcurrency** = 10, zdarzenia wyzwalacza są uruchamiane tylko dla pierwszych 10 okien (00:00-01:00 - 09:00-10:00). Po zakończeniu pierwszych 10 uruchomień potoku wyzwalacza uruchamiane są uruchamiane dla następnych 10 okien (10:00-11:00 - 19:00-20:00). Kontynuując ten przykład **maxConcurrency** = 10, jeśli istnieje 10 okna gotowe, istnieje 10 całkowita liczba uruchomień potoku. Jeśli jest tylko 1 okno gotowe, jest tylko 1 uruchomić potoku. | Liczba całkowita | Liczą całkowitą z 1 do 50. | Tak |
| **ponów próbęPolityka: Liczba** | Liczba ponownych prób przed uruchomieniem potoku jest oznaczona jako "Nie powiodło się".  | Liczba całkowita | Liczba całkowita, gdzie wartość domyślna to 0 (brak ponownych prób). | Nie |
| **retryPolicy: intervalInSeconds** | Opóźnienie między próbami ponawiania określone w sekundach. | Liczba całkowita | Liczba sekund, w których wartość domyślna to 30. | Nie |
| **dependsOn: typ** | Typ TumblingWindowTriggerReference. Wymagane, jeśli ustawiono zależność. | Ciąg |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Nie |
| **dependsOn: rozmiar** | Rozmiar okna tumbling zależności. | Zakres czasu<br/>(hh:mm:ss)  | Dodatnia wartość przedziału czasu, w której domyślnym rozmiarem okna wyzwalacza podrzędnego jest rozmiar okna  | Nie |
| **dependsOn: przesunięcie** | Przesunięcie wyzwalacza zależności. | Zakres czasu<br/>(hh:mm:ss) |  Wartość timespan, która musi być ujemna w zależności od siebie. Jeśli nie określono wartości, okno jest taka sama jak sam wyzwalacz. | Zależność własna: Tak<br/>Inny: Nie  |

### <a name="windowstart-and-windowend-system-variables"></a>Zmienne systemowe WindowStart i WindowEnd

Można użyć **WindowStart** i **WindowEnd** zmiennych systemowych wyzwalacza okna tumbling w definicji **potoku** (czyli dla części kwerendy). Przekaż zmienne systemowe jako parametry do potoku w definicji **wyzwalacza.** W poniższym przykładzie pokazano, jak przekazać te zmienne jako parametry:

```
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

Aby użyć **WindowStart** i **WindowEnd** wartości zmiennych systemowych w definicji potoku, należy użyć parametrów "MyWindowStart" i "MyWindowEnd", odpowiednio.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Kolejność wykonywania okien w scenariuszu wypełniania zapasów
Gdy istnieje wiele okien do wykonania (szczególnie w scenariuszu wypełniania), kolejność wykonywania dla okien jest deterministic, od najstarszych do najnowszych interwałów. Obecnie nie można zmodyfikować tego zachowania.

### <a name="existing-triggerresource-elements"></a>Istniejące elementy TriggerResource
Następujące punkty dotyczą istniejących elementów **TriggerResource:**

* Jeśli wartość elementu **częstotliwości** (lub rozmiaru okna) wyzwalacza ulegnie zmianie, stan już przetworzonych okien *nie* zostanie zresetowany. Wyzwalacz nadal uruchamia okna z ostatniego okna, które zostało wykonane przy użyciu nowego rozmiaru okna.
* Jeśli wartość **endTime** element wyzwalacza zmiany (dodane lub zaktualizowane), stan okien, które są już przetwarzane *nie* jest resetowany. Wyzwalacz honoruje nową wartość **endTime.** Jeśli nowa wartość **endTime** jest przed oknami, które są już wykonane, wyzwalacz zatrzymuje. W przeciwnym razie wyzwalacz zatrzymuje się po napotkaniu nowej wartości **endTime.**

### <a name="tumbling-window-trigger-dependency"></a>Zależność wyzwalacza okna tumbling

Jeśli chcesz się upewnić, że wyzwalacz okna tumbling jest wykonywany dopiero po pomyślnym wykonaniu innego wyzwalacza okna w fabryce danych, [utwórz zależność wyzwalacza okna.](tumbling-window-trigger-dependency.md) 

## <a name="sample-for-azure-powershell"></a>Przykład dla programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tej sekcji pokazano, jak używać programu Azure PowerShell do tworzenia, uruchamiania i monitorowania wyzwalacza.

1. Utwórz plik JSON o nazwie **MyTrigger.json** w folderze C:\ADFv2QuickStartPSH\ z następującą zawartością:

    > [!IMPORTANT]
    > Przed zapisaniem pliku JSON należy ustawić wartość elementu **startTime** na bieżący czas UTC. Ustaw wartość **endTime** element na jedną godzinę po bieżącym czasie UTC.

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

2. Utwórz wyzwalacz przy użyciu polecenia cmdlet **Set-AzDataFactoryV2Trigger:**

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Upewnij się, że stan wyzwalacza jest **zatrzymany** przy użyciu polecenia cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Uruchom wyzwalacz przy użyciu polecenia cmdlet **Start-AzDataFactoryV2Trigger:**

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Upewnij się, że stan wyzwalacza jest **uruchomiony** przy użyciu polecenia cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Pobierz wyzwalacz uruchamia się w programie Azure PowerShell przy użyciu polecenia cmdlet **Get-AzDataFactoryV2TriggerRun.** Aby uzyskać informacje o uruchomieniu wyzwalacza, należy okresowo wykonywać następujące polecenie. Zaktualizuj **triggerrunstartedafter** i **TriggerRunStartedPrzed wartości,** aby dopasować wartości w definicji wyzwalacza:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Aby monitorować przebiegi wyzwalaczy i przebiegi potoku w portalu Azure, zobacz [Monitorowanie przebiegów potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [Wykonywanie potoku i wyzwalacze](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Tworzenie zależności wyzwalacza okna wirowania](tumbling-window-trigger-dependency.md)
