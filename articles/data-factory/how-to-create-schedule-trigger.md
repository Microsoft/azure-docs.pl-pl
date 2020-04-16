---
title: Tworzenie wyzwalaczy harmonogramu w usłudze Azure Data Factory
description: Dowiedz się, jak utworzyć wyzwalacz w usłudze Azure Data Factory, który uruchamia potok zgodnie z harmonogramem.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2018
ms.openlocfilehash: 100f95c20743f70bb4a9f2ac7e74853eab80f3e9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414487"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Tworzenie wyzwalacza uruchamiającego potok zgodnie z harmonogramem
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł zawiera informacje o wyzwalaczu harmonogramu i krokach tworzenia, uruchamiania i monitorowania wyzwalacza harmonogramu. W przypadku innych typów wyzwalaczy zobacz [Wykonywanie potoku i wyzwalacze](concepts-pipeline-execution-triggers.md).

Podczas tworzenia wyzwalacza harmonogramu należy określić harmonogram (data rozpoczęcia, cykl, data zakończenia itp.) dla wyzwalacza i skojarzyć z potokiem. Między potokami i wyzwalaczami występuje relacja wiele-do-wielu. Wiele wyzwalaczy może uruchomić jeden potok. Jeden wyzwalacz może uruchamiać wiele potoków.

W poniższych sekcjach przedstawiono kroki, aby utworzyć wyzwalacz harmonogramu na różne sposoby. 

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory
Można utworzyć **wyzwalacz harmonogramu,** aby zaplanować okresowe uruchamianie potoku (co godzinę, codziennie itp.). 

> [!NOTE]
> Aby uzyskać pełną instruktaż tworzenia potoku i wyzwalacza harmonogramu, kojarzenia wyzwalacza z potokiem oraz uruchamiania i monitorowania potoku, zobacz [Szybki start: tworzenie fabryki danych przy użyciu interfejsu użytkownika fabryki danych](quickstart-create-data-factory-portal.md).

1. Przejdź do karty **Edycja**. 

    ![Przechodzenie do karty Edycja](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Kliknij pozycję **Wyzwalacz** w menu, a następnie kliknij pozycję **Nowy/Edytuj**. 

    ![Menu Nowy wyzwalacz](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. Na stronie **Dodawanie wyzwalaczy** kliknij pozycję **Wybierz wyzwalacz... **, a następnie kliknij przycisk **Nowy**. 

    ![Dodawanie wyzwalaczy — nowy wyzwalacz](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. Na stronie **Nowy wyzwalacz** wykonaj następujące czynności: 

    1. Upewnij się, że dla **typu**jest zaznaczona opcja **Harmonogram** . 
    2. Określ datę rozpoczęcia wyzwalacza dla **daty rozpoczęcia (UTC)**. Domyślnie jest ustawiona na bieżącą datę i godziny. 
    3. Określ **cykl** wyzwalacza. Wybierz jedną z wartości z listy rozwijanej (Co minutę, Co minutę, Co godzinę, Codziennie, Co tydzień i Co miesiąc). Wprowadź mnożnik w polu tekstowym. Na przykład, jeśli wyzwalacz ma być uruchamiany raz na każde 15 minut, wybierz **opcję Co minutę**i wprowadź **15** w polu tekstowym. 
    4. W polu **Koniec,** jeśli nie chcesz określać daty zakończenia dla wyzwalacza, wybierz opcję **Nie koniec**. Aby określić datę zakończenia, wybierz pozycję **Na dacie**i określ datę zakończenia, a następnie kliknij przycisk **Zastosuj**. Z każdym uruchomieniem potoku wiąże się koszt. Jeśli testujesz, możesz się upewnić, że potok jest wyzwalany tylko kilka razy. Upewnij się również, że okres między czasem publikowania i czasem zakończenia będzie wystarczający do uruchomienia potoku. Wyzwalacz zaczyna obowiązywać dopiero po opublikowaniu rozwiązania w fabryce Data Factory, a nie po zapisaniu go w interfejsie użytkownika.

        ![Ustawienia wyzwalacza](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. W oknie **Nowy wyzwalacz** zaznacz opcję **Aktywowano** i kliknij przycisk **Dalej**. To pole wyboru służy do późniejszego dezaktywacji wyzwalacza. 

    ![Ustawienia wyzwalacza — przycisk Dalej](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. Na stronie **Nowy wyzwalacz** zapoznaj się z komunikatem ostrzegawczym, a następnie kliknij przycisk **Zakończ**.

    ![Ustawienia wyzwalacza — przycisk Zakończ](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Kliknij pozycję **Opublikuj**, aby opublikować zmiany w fabryce Data Factory. Dopóki nie opublikujesz zmian w fabryce danych, wyzwalacz nie uruchamia uruchomienia potoku. 

    ![Przycisk Opublikuj](./media/how-to-create-schedule-trigger/publish-2.png)
8. Przejdź do karty **Monitorowanie** po lewej stronie. Kliknij pozycję **Odśwież**, aby odświeżyć listę. Zostanie wyświetlony potok uruchamia wyzwalane przez zaplanowanego wyzwalacza. Zwróć uwagę na wartości w kolumnie **Wyzwolone przez**. Jeśli używasz opcji **Wyzwalaj teraz,** na liście pojawi się ręczny wyzwalacz uruchamiany. 

    ![Monitorowanie wyzwolonych uruchomień](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Kliknij strzałkę w dół obok pozycji **Uruchomienia potoków**, aby przejść do widoku **Uruchomienia wyzwalacza**. 

    ![Monitorowanie uruchomień wyzwalacza](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tej sekcji pokazano, jak używać programu Azure PowerShell do tworzenia, uruchamiania i monitorowania wyzwalacza harmonogramu. Aby zobaczyć ten przykład pracy, najpierw przejdź przez [Szybki start: Tworzenie fabryki danych przy użyciu programu Azure PowerShell](quickstart-create-data-factory-powershell.md). Następnie dodaj następujący kod do metody głównej, która tworzy i uruchamia wyzwalacz harmonogramu, który jest uruchamiany co 15 minut. Wyzwalacz jest skojarzony z potokiem o nazwie **Adfv2QuickStartPipeline** utworzonym w ramach przewodnika Szybki start.

1. Utwórz plik JSON o nazwie **MyTrigger.json** w folderze C:\ADFv2QuickStartPSH\ z następującą zawartością:

    > [!IMPORTANT]
    > Przed zapisaniem pliku JSON należy ustawić wartość elementu **startTime** na bieżący czas UTC. Ustaw wartość **endTime** element na jedną godzinę po bieżącym czasie UTC.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    We urywek JSON:
    - Element **typu** wyzwalacza jest ustawiony na "ScheduleTrigger".
    - Element **częstotliwości** jest ustawiony na "Minuta", a element **interwału** jest ustawiony na 15. W związku z tym wyzwalacz uruchamia potoku co 15 minut między godziną rozpoczęcia i zakończenia.
    - Element **endTime** jest jedną godzinę po wartości **elementu startTime.** W związku z tym wyzwalacz uruchamia potoku 15 minut, 30 minut i 45 minut po godzinie rozpoczęcia. Nie zapomnij zaktualizować czasu rozpoczęcia do bieżącego czasu UTC, a czas zakończenia do jednej godziny po godzinie rozpoczęcia. 
    - Wyzwalacz jest skojarzony z potokiem **Adfv2QuickStartPipeline.** Aby skojarzyć wiele potoków z wyzwalaczem, dodaj więcej **pipelineReference** sekcje.
    - Potok w przewodniku Szybki start przyjmuje dwie wartości **parametrów:** **inputPath** i **outputPath**. W związku z tym należy przekazać wartości dla tych parametrów z wyzwalacza.

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

6.  Pobierz wyzwalacz uruchamia się w programie Azure PowerShell przy użyciu polecenia cmdlet **Get-AzDataFactoryV2TriggerRun.** Aby uzyskać informacje o uruchomieniu wyzwalacza, należy okresowo wykonywać następujące polecenie. Zaktualizuj **triggerrunstartedafter** i **TriggerRunStartedPrzed wartości,** aby dopasować wartości w definicji wyzwalacza:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Aby monitorować przebiegi wyzwalacza i przebiegi potoku w portalu Azure, zobacz [Monitorowanie przebiegów potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>Zestaw SDK .NET
W tej sekcji pokazano, jak używać pliku .NET SDK do tworzenia, uruchamiania i monitorowania wyzwalacza. Aby zobaczyć, jak ten przykład działa, najpierw przejdź przez [przewodnik Szybki start: Tworzenie fabryki danych przy użyciu pliku .NET SDK](quickstart-create-data-factory-dot-net.md). Następnie dodaj następujący kod do metody głównej, która tworzy i uruchamia wyzwalacz harmonogramu, który jest uruchamiany co 15 minut. Wyzwalacz jest skojarzony z potokiem o nazwie **Adfv2QuickStartPipeline** utworzonym w ramach przewodnika Szybki start.

Aby utworzyć i uruchomić wyzwalacz harmonogramu, który jest uruchamiany co 15 minut, dodaj następujący kod do metody głównej:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Aby monitorować uruchomienie wyzwalacza, dodaj `Console.WriteLine` następujący kod przed ostatnią instrukcją w przykładzie:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Aby monitorować przebiegi wyzwalacza i przebiegi potoku w portalu Azure, zobacz [Monitorowanie przebiegów potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Zestaw SDK dla języka Python
W tej sekcji pokazano, jak używać SDK języka Python do tworzenia, uruchamiania i monitorowania wyzwalacza. Aby zobaczyć, jak ten przykład działa, najpierw przejdź przez [przewodnik Szybki start: Tworzenie fabryki danych przy użyciu zestawu SDK języka Python](quickstart-create-data-factory-python.md). Następnie dodaj następujący blok kodu po bloku kodu "monitoruj uruchomienie potoku" w skrypcie języka Python. Ten kod tworzy wyzwalacz harmonogramu, który jest uruchamiany co 15 minut między określonymi godzinami rozpoczęcia i zakończenia. Zaktualizuj zmienną **start_time** do bieżącego czasu UTC, a zmienną **end_time** do jednej godziny po bieżącym czasie UTC.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Aby monitorować przebiegi wyzwalacza i przebiegi potoku w portalu Azure, zobacz [Monitorowanie przebiegów potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Aby utworzyć wyzwalacz, można użyć szablonu usługi Azure Resource Manager. Aby uzyskać instrukcje krok po kroku, zobacz [Tworzenie fabryki danych platformy Azure przy użyciu szablonu Menedżera zasobów](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Przekazywanie czasu rozpoczęcia wyzwalacza do potoku
Usługa Azure Data Factory w wersji 1 obsługuje odczytywanie lub zapisywanie danych podzielonych na partycje przy użyciu zmiennych systemowych: **SliceStart**, **SliceEnd**, **WindowStart**i **WindowEnd**. W bieżącej wersji usługi Azure Data Factory można osiągnąć to zachowanie przy użyciu parametru potoku. Czas rozpoczęcia i zaplanowany czas wyzwalacza są ustawiane jako wartość parametru potoku. W poniższym przykładzie zaplanowany czas wyzwalacza jest przekazywany jako wartość do potoku **scheduledRunTime** parametru:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>Schemat JSON
Następująca definicja JSON pokazuje, jak utworzyć wyzwalacz harmonogramu z planowaniem i cyklem:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  Właściwość **parameters** jest obowiązkową wartością elementu właściwości **pipelines**. Jeśli potok nie przyjmuje żadnych parametrów, należy dodać pustą definicję JSON dla właściwości **parameters**.


### <a name="schema-overview"></a>Przegląd schematu
Poniższa tabela zawiera ogólne omówienie głównych elementów schematu odnoszących się do powtarzania i planowania wyzwalacza:

| Właściwość JSON | Opis |
|:--- |:--- |
| **startTime** | Wartość daty i godziny. W przypadku prostych harmonogramów wartość właściwości **startTime** dotyczy pierwszego wystąpienia. W przypadku harmonogramów złożonych wyzwalacz nie jest uruchamiany wcześniej niż określona wartość właściwości **startTime**. |
| **Endtime** | Data i godzina zakończenia wyzwalacza. Wyzwalacz nie jest wykonywany po określonej dacie i godzinie zakończenia. Wartość właściwości nie może odnosić się do przeszłości. Ta właściwość jest opcjonalna. |
| **timeZone** | Strefa czasowa. Obecnie jest obsługiwana tylko strefa czasowa UTC. |
| **Cyklu** | Obiekt cyklu określający reguły powtarzania wyzwalacza. Obiekt cyklu obsługuje elementy właściwości **frequency**, **interval**, **endTime**, **count** i **schedule**. Po zdefiniowaniu obiektu cyklu wymagany jest element właściwości **frequency**. Inne elementy obiektu cyklu są opcjonalne. |
| **Częstotliwości** | Jednostka częstotliwości powtarzania wyzwalacza. Obsługiwane wartości to „minute” („minuta”), „hour” („godzina”), „day” („dzień”), „week” („tydzień”) i „month” („miesiąc”). |
| **Interwał** | Dodatnia liczba całkowita oznaczająca interwał wartości właściwości **frequency**, która określa częstotliwość uruchamiania wyzwalacza. Jeśli na przykład właściwość **interval** ma wartość 3, a właściwość **frequency** ma wartość „week” („tydzień”), wyzwalacz jest powtarzany co 3 tygodnie. |
| **Harmonogram** | Harmonogram cyklu wyzwalacza. Wyzwalacz z określoną wartością właściwości **frequency** zmienia swój cykl na podstawie harmonogramu cyklu. Właściwość **schedule** zawiera modyfikacje cyklu oparte na minutach, godzinach, dniach tygodnia, dniach miesiąca i numerze tygodnia.


### <a name="schema-defaults-limits-and-examples"></a>Wartości domyślne, limity i przykłady harmonogramów

| Właściwość JSON | Typ | Wymagany | Wartość domyślna | Prawidłowe wartości | Przykład |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Ciąg | Tak | Brak | Daty i godziny ISO-8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **Cyklu** | Obiekt | Tak | Brak | Obiekt cyklu | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **Interwał** | Liczba | Nie | 1 | Od 1 do 1000 | `"interval":10` |
| **Endtime** | Ciąg | Tak | Brak | Wartość daty i godziny reprezentująca godzinę w przyszłości. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **Harmonogram** | Obiekt | Nie | Brak | Obiekt harmonogramu | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Właściwość startTime
W poniższej tabeli przedstawiono, w jaki sposób właściwość **startTime** kontroluje uruchamianie wyzwalacza:

| Wartość startTime | Cykl bez harmonogramu | Cykl z harmonogramem |
|:--- |:--- |:--- |
| Godzina rozpoczęcia w przeszłości | Oblicza pierwszy przyszły czas wykonania po czasie rozpoczęcia i uruchamia wyzwalacz w tym czasie.<br/><br/>Uruchamia kolejne wykonania w oparciu o obliczenia wykonane na podstawie ostatniego czasu wykonania.<br/><br/>Zobacz przykład znajdujący się pod tabelą. | Wyzwalacz jest uruchamiany _nie wcześniej niż_ w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br/><br/>Kolejne wykonania opierają na harmonogramie cyklu. |
| Godzina rozpoczęcia w przyszłości lub obecna | Uruchamiany raz o określonym czasie rozpoczęcia.<br/><br/>Uruchamia kolejne wykonania w oparciu o obliczenia wykonane na podstawie ostatniego czasu wykonania. | Wyzwalacz rozpoczyna się _nie wcześniej_ niż w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br/><br/>Kolejne wykonania opierają na harmonogramie cyklu. |

Zobaczmy przykład zdarzeń w przypadku, gdy czas rozpoczęcia przypada w przeszłości, z cyklem, ale bez harmonogramu. Załóżmy, że obecna data i godzina to `2017-04-08 13:00`, czas rozpoczęcia to `2017-04-07 14:00`, a cykl jest określony na 2 dni. (Wartość **cyklu** jest definiowana przez ustawienie właściwości **frequency** na "dzień", a właściwość **interwału** na 2.) Należy zauważyć, że **wartość startTime** jest w przeszłości i występuje przed bieżącym czasem.

W tych warunkach pierwsze wykonanie następuje o `2017-04-09 at 14:00`. Aparat harmonogramu oblicza wystąpienia wykonania od czasu rozpoczęcia. Wszystkie wystąpienia w przeszłości są odrzucane. Aparat wykorzystuje następne wystąpienie, które ma miejsce w przyszłości. W tym przypadku czas rozpoczęcia to `2017-04-07 at 2:00pm`, w związku z czym kolejne wystąpienie ma miejsce 2 dni po tym czasie, czyli `2017-04-09 at 2:00pm`.

Czas pierwszego wykonania jest identyczny, nawet jeśli właściwość **startTime** ma wartość `2017-04-05 14:00` lub `2017-04-01 14:00`. Po pierwszym wykonaniu kolejne wykonania są obliczane przy użyciu harmonogramu. W związku z tym kolejne wykonania zostaną uruchomione `2017-04-11 at 2:00pm`, `2017-04-13 at 2:00pm`, `2017-04-15 at 2:00pm` itd.

Gdy z kolei w harmonogramie wyzwalacza nie ustawiono godzin lub minut, wartością domyślną będą godziny lub minuty pierwszego wykonania.

### <a name="schedule-property"></a>Wartość schedule
Użycie harmonogramu może ograniczyć liczbę wykonań wyzwalacza. Jeśli na przykład uruchomienie wyzwalacza z częstotliwością miesięczną zaplanowano na 31. dzień, wyzwalacz będzie uruchamiany tylko w miesiącach, której mają 31 dni.

Harmonogram może także zwiększyć liczbę wykonań wyzwalacza. Na przykład wyzwalacz z częstotliwością miesięczną, którego uruchomienie zaplanowano na 1. i 2. dzień miesiąca, będzie uruchamiany 1. i 2. dnia miesiąca, a nie raz na miesiąc.

W przypadku określenia wielu elementów właściwości **schedule** ocena następuje od największego do najmniejszego elementu. Ocena jest wykonywana w następującej kolejności: numer tygodnia, dzień miesiąca, dzień tygodnia, godzina i minuta.

W poniższej tabeli opisano szczegółowo elementy właściwości **schedule**:


| Element JSON | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |
| **minutes** | Minuty godziny, o których uruchamiany jest wyzwalacz. | <ul><li>Liczba całkowita</li><li>Tablica liczb całkowitych</li></ul>
| **Godzin** | Godziny dnia, o których uruchamiany jest wyzwalacz. | <ul><li>Liczba całkowita</li><li>Tablica liczb całkowitych</li></ul> |
| **weekDays** | Dni tygodnia, w których uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością tygodniową. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday lub Sunday</li><li>Tablica wartości dni (maksymalny rozmiar tablicy to 7)</li><li>W wartościach dni nie są uwzględniane wielkości liter</li></ul> |
| **monthlyOccurrences** | Dni miesiąca, w których uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. | <ul><li>Tablica **miesięcznychWzdlenia:** `{ "day": day,  "occurrence": occurrence }`.</li><li>Atrybut **day** jest dniem tygodnia, w którym uruchamiany jest wyzwalacz. Na przykład właściwość **monthlyOccurrences** o wartości **day** wynoszącej `{Sunday}` oznacza każdą niedzielę miesiąca. Atrybut **day** jest wymagany.</li><li>Atrybut **occurence** jest wystąpieniem określonej wartości **day** w miesiącu. Na przykład właściwość **monthlyOccurrences** o wartościach **day** i **occurence** wynoszących `{Sunday, -1}` oznacza ostatnią niedzielę miesiąca. Atrybut **occurence** jest opcjonalny.</li></ul> |
| **monthDays** | Dzień miesiąca, w którym uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. | <ul><li>Dowolna wartość <= -1 i >= -31</li><li>Dowolna wartość >= 1 i <= 31</li><li>Tablica wartości</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Przykłady harmonogramów cyklu wyzwalaczy
Ta sekcja zawiera przykłady harmonogramów cyklu i koncentruje się na obiekcie właściwości **schedule** oraz jego elementach.

W przykładach założono, ze wartość właściwości **interval** wynosi 1 oraz że wartość właściwości **frequency** jest prawidłowa i zgodna z definicją harmonogramu. Na przykład nie można mieć wartość **częstotliwości** "dzień", a także "monthDays" modyfikacji w obiekcie **harmonogramu.** Tego typu ograniczenia są wymienione w tabeli w poprzedniej sekcji.

| Przykład | Opis |
|:--- |:--- |
| `{"hours":[5]}` | Uruchamiany o godz. 5:00 każdego dnia. |
| `{"minutes":[15], "hours":[5]}` | Uruchamiany o godz. 5:15 każdego dnia. |
| `{"minutes":[15], "hours":[5,17]}` | Uruchamiany o godz. 5:15 i 17:15 każdego dnia. |
| `{"minutes":[15,45], "hours":[5,17]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 każdego dnia. |
| `{"minutes":[0,15,30,45]}` | Uruchamiany co 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Uruchamiany co godzinę. Ten wyzwalacz jest uruchamiany co godzinę. Minuty są kontrolowane przez wartość właściwości **startTime**, gdy wartość jest określona. Jeśli wartość nie jest określona, minuty są kontrolowane przez czas utworzenia. Jeśli na przykład czas rozpoczęcia lub czas utworzenia (zależnie od sytuacji) to 12:25, wyzwalacz jest uruchamiany o godzinie 0:25, 1:25, 2:25, ..., 23:25.<br/><br/>Ten harmonogram jest odpowiednikiem wyzwalacza z właściwością **frequency** o wartości „hour”, właściwością **interval** o wartości 1 i bez właściwości **schedule**.  Tego harmonogramu można także używać z innymi wartościami właściwości **frequency** i **interval** do tworzenia innych wyzwalaczy. Gdy na przykład właściwość **frequency** ma wartość „month”, harmonogram jest uruchamiany tylko raz w miesiącu, a nie każdego dnia, gdy właściwość **frequency** ma wartość „day”. |
| `{"minutes":[0]}` | Uruchamiany co godzinę o pełnej godzinie. Ten wyzwalacz jest uruchamiany co godzinę o pełnej godzinie począwszy od godz. 0:00, a następnie o 1:00, 2:00 itd.<br/><br/>Ten harmonogram jest odpowiednikiem wyzwalacza z właściwością **frequency** o wartości „hour” i właściwością **startTime** o wartości 0 minut lub bez właściwości **schedule**, ale z właściwością **frequency** o wartości „day”. Jeśli wartość **częstotliwości** to "tydzień" lub "miesiąc", harmonogram wykonuje odpowiednio jeden dzień w tygodniu lub jeden dzień w miesiącu. |
| `{"minutes":[15]}` | Uruchamiany 15 minut po pełnej godzinie. Ten wyzwalacz jest uruchamiany 15 minut po pełnej godzinie począwszy od godz. 0:15, a następnie o 1:15, 2:15 itd. aż do 23.15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Uruchamiany o godz. 17:00 w każdą sobotę. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Uruchamiany o godz. 17:00 w każdy poniedziałek, środę i piątek. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Uruchamiany o godz. 17:15 i 17:45 w każdy poniedziałek, środę i piątek. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Uruchamiany co 15 minut w dni robocze. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Uruchamiany co 15 minut w dni robocze, między godz. 9:00 a 16:45. |
| `{"weekDays":["tuesday", "thursday"]}` | Uruchamiany we wtorki i czwartki o określonej godzinie. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Uruchamiany o godz. 6:00 28. dnia każdego miesiąca (zakładając, że właściwość **frequency** ma wartość „month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Uruchamiany o godz. 6:00 ostatniego dnia miesiąca. Aby uruchomić wyzwalacz ostatniego dnia miesiąca, użyj wartości -1 zamiast 28, 29, 30 lub 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Uruchamiany o godz. 6:00 pierwszego i ostatniego dnia każdego miesiąca. |
| `{monthDays":[1,14]}` | Uruchamiany 1. i 14. dnia każdego miesiąca o określonej godzinie. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Uruchamiany w pierwszy piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Uruchamiany w pierwszy piątek każdego miesiąca o określonej godzinie. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Uruchamiany w trzeci piątek od końca każdego miesiąca o określonej godzinie. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o określonej godzinie. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Uruchamiany w piąty piątek każdego miesiąca o określonej godzinie. Jeśli miesiąc nie ma piątego piątku, potok nie jest uruchamiany, ponieważ jego harmonogram określa uruchamianie tylko w piąty piątek. Aby uruchomić wyzwalacz w ostatni piątek miesiąca, rozważ użycie wartości -1 zamiast 5 dla właściwości **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Uruchamiany co 15 minut w ostatni piątek miesiąca. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 w trzecią środę każdego miesiąca. |


## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [Wykonywanie potoku i wyzwalacze](concepts-pipeline-execution-triggers.md#trigger-execution).
