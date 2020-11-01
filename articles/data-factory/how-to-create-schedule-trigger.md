---
title: Utwórz wyzwalacze harmonogramu w Azure Data Factory
description: Dowiedz się, jak utworzyć wyzwalacz w Azure Data Factory, który uruchamia potok zgodnie z harmonogramem.
services: data-factory
documentationcenter: ''
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0f147a317b16a4a581fad18bd48dbd38059e9a8c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146074"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Tworzenie wyzwalacza uruchamiającego potok zgodnie z harmonogramem
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł zawiera informacje na temat wyzwalacza harmonogramu oraz czynności służących do tworzenia, uruchamiania i monitorowania wyzwalacza harmonogramu. W przypadku innych typów wyzwalaczy zapoznaj się z tematem [wykonywanie i wyzwalacze potoku](concepts-pipeline-execution-triggers.md).

Podczas tworzenia wyzwalacza harmonogramu należy określić harmonogram (datę rozpoczęcia, cykl, datę końcową itp.) dla wyzwalacza i skojarzyć z potokiem. Między potokami i wyzwalaczami występuje relacja wiele-do-wielu. Wiele wyzwalaczy może uruchomić jeden potok. Jeden wyzwalacz może uruchamiać wiele potoków.

W poniższych sekcjach przedstawiono procedurę tworzenia wyzwalacza harmonogramu na różne sposoby. 

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory
Można utworzyć **wyzwalacz harmonogramu** , aby zaplanować okresowe uruchamianie potoku (co godzinę, codziennie itd.). 

> [!NOTE]
> Pełny Przewodnik tworzenia potoku i wyzwalacza harmonogramu kojarzy wyzwalacz z potokiem i uruchamia i monitoruje potok, zobacz [Szybki Start: Tworzenie fabryki danych przy użyciu interfejsu użytkownika Data Factory](quickstart-create-data-factory-portal.md).

1. Przejdź do karty **Edycja** , która jest wyświetlana z symbolem ołówka. 

    ![Przechodzenie do karty Edycja](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. Wybierz pozycję **wyzwalacz** w menu, a następnie wybierz pozycję **Nowy/Edytuj** . 

    ![Menu Nowy wyzwalacz](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. Na stronie **Dodawanie wyzwalaczy** wybierz pozycję **Wybierz wyzwalacz...** , a następnie wybierz pozycję **+ Nowy** . 

    ![Dodawanie wyzwalaczy — nowy wyzwalacz](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. Na stronie **Nowy wyzwalacz** wykonaj następujące czynności: 

    1. Upewnij się, że wybrano **harmonogram** dla **typu** .
    1. Określ początkową datę i godzinę dla wyzwalacza dla **daty rozpoczęcia** . Jest ona domyślnie ustawiona na bieżącą datę i godzinę w uniwersalnym czasie koordynowanym (UTC).
    1. Określ strefę czasową, w której zostanie utworzony wyzwalacz. Ustawienie strefy czasowej będzie miało zastosowanie do **daty rozpoczęcia** , **daty zakończenia** i **czasów wykonywania** w zaawansowanych opcjach cyklu. Zmiana ustawienia strefy czasowej nie spowoduje automatycznego zmiany daty rozpoczęcia. Upewnij się, że data rozpoczęcia jest poprawna w określonej strefie czasowej

        > [!NOTE]
        > W przypadku stref czasowych, które obserwują zmiany czasu, czas wyzwalacza zostanie automatycznie dostosowany przez dwukrotne zmianę roku. Aby zrezygnować z zmiany czasu letniego, wybierz strefę czasową, która nie obserwuje czasu letniego, na czas UTC

    1. Określ **cykl** dla wyzwalacza. Wybierz jedną z wartości z listy rozwijanej (co minutę, co godzinę, codziennie, co tydzień i co miesiąc). Wprowadź mnożnik w polu tekstowym. Na przykład jeśli wyzwalacz ma być uruchamiany raz przez co 15 minut, należy wybrać **co minutę** i wprowadzić wartość **15** w polu tekstowym. 
    1. Aby określić datę i godzinę zakończenia, wybierz pozycję **Określ datę końcową** i określ, czy ma zostać _zakończone_ , a następnie wybierz **przycisk OK** . Z każdym uruchomieniem potoku wiąże się koszt. W przypadku testowania można upewnić się, że potok jest wyzwalany tylko kilka razy. Upewnij się również, że okres między czasem publikowania i czasem zakończenia będzie wystarczający do uruchomienia potoku. Wyzwalacz zaczyna obowiązywać dopiero po opublikowaniu rozwiązania w fabryce Data Factory, a nie po zapisaniu go w interfejsie użytkownika.

        ![Ustawienia wyzwalacza](./media/how-to-create-schedule-trigger/trigger-settings-01.png)

        ![Ustawienia wyzwalania dla daty zakończenia](./media/how-to-create-schedule-trigger/trigger-settings-02.png)

1. W oknie **Nowy wyzwalacz** wybierz opcję **tak** w **aktywowanej** opcji, a następnie wybierz przycisk **OK** . To pole wyboru służy do dezaktywowania wyzwalacza później. 

    ![Ustawienia wyzwalacza — przycisk Dalej](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. W oknie **Nowy wyzwalacz** Przejrzyj komunikat ostrzegawczy, a następnie wybierz przycisk **OK** .

    ![Ustawienia wyzwalacza — przycisk Zakończ](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. Wybierz pozycję **Opublikuj wszystkie** , aby opublikować zmiany w Data Factory. Do momentu opublikowania zmian w Data Factory wyzwalacz nie rozpocznie uruchamiania uruchamiania potoków. 

    ![Przycisk Publikuj](./media/how-to-create-schedule-trigger/publish-2.png)

1. Przejdź do karty **uruchomienia potoku** po lewej stronie, a następnie wybierz pozycję **Odśwież** , aby odświeżyć listę. Zostaną wyświetlone uruchomienia potoku wyzwalane przez zaplanowany wyzwalacz. Zwróć uwagę na wartości w kolumnie **Wyzwolone przez** . W przypadku użycia opcji **Wyzwól teraz** na liście zostanie wyświetlony ręczny wyzwalacz uruchomienia. 

    ![Monitorowanie wyzwolonych uruchomień](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. Przejdź do widoku **harmonogramu uruchamiania wyzwalacza**  \  **Schedule** . 

    ![Monitorowanie uruchomień wyzwalacza](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tej sekcji przedstawiono sposób użycia Azure PowerShell do tworzenia, uruchamiania i monitorowania wyzwalacza harmonogramu. Aby zobaczyć ten przykład pracy, najpierw przejdź do [przewodnika Szybki Start: Tworzenie fabryki danych przy użyciu Azure PowerShell](quickstart-create-data-factory-powershell.md). Następnie Dodaj następujący kod do metody Main, która tworzy i uruchamia wyzwalacz harmonogramu, który jest uruchamiany co 15 minut. Wyzwalacz jest skojarzony z potokiem o nazwie **Adfv2QuickStartPipeline** , który tworzysz w ramach przewodnika Szybki Start.

1. Utwórz plik JSON o nazwie **MyTrigger.js** w folderze C:\ADFv2QuickStartPSH\ o następującej zawartości:

    > [!IMPORTANT]
    > Przed zapisaniem pliku JSON ustaw wartość parametru **StartTime** na bieżący czas UTC. Ustaw wartość elementu **Endtime** na godzinę wcześniejszą od bieżącego czasu UTC.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00Z",
                    "endTime": "2017-12-08T01:00:00Z",
                    "timeZone": "UTC"
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

    W fragmencie kodu JSON:
    - Element **Type** wyzwalacza jest ustawiony na wartość "ScheduleTrigger".
    - Element **częstotliwości** jest ustawiony na wartość "minuta", a element **Interval** ma wartość 15. W związku z tym wyzwalacz uruchamia potok co 15 minut między czasem rozpoczęcia i zakończenia.
    - Element **timeZone** określa strefę czasową, w której jest tworzony wyzwalacz. To ustawienie wpływa na wartość **StartTime** i **Endtime** .
    - Element **Endtime** ma godzinę po wartości elementu **StartTime** . W związku z tym wyzwalacz uruchamia potok 15 minut, 30 minut i 45 minut po czasie rozpoczęcia. Nie zapomnij zaktualizować czasu rozpoczęcia do bieżącego czasu UTC i godziny zakończenia do godziny poza godziną rozpoczęcia. 

        > [!IMPORTANT]
        > W przypadku strefy czasowej UTC wartość startTime i endTime muszą być zgodne z formatem "RRRR-MM-DDTgg: mm: SS **Z** ", natomiast w przypadku innych stref czasowych, StartTime i Endtime postępuj zgodnie z "RRRR-MM-DDTgg: mm: SS". 
        > 
        > Zgodnie ze standardem ISO 8601, sufiks _z_ sygnaturą czasową do znacznika czasu oznacza wartość DateTime do strefy czasowej UTC, a pole "czas operacji" jest bezużyteczne Podczas gdy brakuje sufiksu _Z_ dla strefy czasowej UTC, spowoduje to błąd podczas _aktywacji_ wyzwalacza.

    - Wyzwalacz jest skojarzony z potokiem **Adfv2QuickStartPipeline** . Aby skojarzyć wiele potoków z wyzwalaczem, Dodaj więcej sekcji **pipelineReference** .
    - Potok w szybkim samouczku pobiera dwie wartości **parametrów** : **inputPath** i **outputPath** . I przekazują wartości tych parametrów z wyzwalacza.

1. Utwórz wyzwalacz za pomocą polecenia cmdlet **Set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. Upewnij się, że stan wyzwalacza został **zatrzymany** za pomocą polecenia cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Uruchom wyzwalacz za pomocą polecenia cmdlet **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Upewnij się, że stan wyzwalacza został **uruchomiony** przy użyciu polecenia cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  Pobierz wyzwalacz uruchomiony w Azure PowerShell przy użyciu polecenia cmdlet **Get-AzDataFactoryV2TriggerRun** . Aby uzyskać informacje o przebiegu wyzwalacza, należy wykonać następujące polecenie okresowo. Zaktualizuj wartości **TriggerRunStartedAfter** i **TriggerRunStartedBefore** w taki sposób, aby odpowiadały wartościom w definicji wyzwalacza:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    > [!NOTE]
    > Czas wyzwalacza wyzwalaczy harmonogramu jest określony w sygnaturze czasowej UTC. _TriggerRunStartedAfter_ i _TriggerRunStartedBefore_ oczekuje również SYGNATURy czasowej UTC

    Aby monitorować uruchomienia wyzwalacza i uruchomienia potoków w Azure Portal, zobacz [monitorowanie uruchomień potoków](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).



## <a name="net-sdk"></a>Zestaw SDK .NET
W tej sekcji pokazano, jak utworzyć, uruchomić i monitorować wyzwalacz przy użyciu zestawu .NET SDK. Aby zobaczyć ten przykład pracy, najpierw przejdź do [przewodnika Szybki Start: Tworzenie fabryki danych przy użyciu zestawu .NET SDK](quickstart-create-data-factory-dot-net.md). Następnie Dodaj następujący kod do metody Main, która tworzy i uruchamia wyzwalacz harmonogramu, który jest uruchamiany co 15 minut. Wyzwalacz jest skojarzony z potokiem o nazwie **Adfv2QuickStartPipeline** , który tworzysz w ramach przewodnika Szybki Start.

Aby utworzyć i uruchomić wyzwalacz harmonogramu, który jest uruchamiany co 15 minut, Dodaj następujący kod do metody Main:

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

Aby utworzyć Wyzwalacze w innej strefie czasowej niż UTC, wymagane są następujące ustawienia:
```csharp
<<ClientInstance>>.SerializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.SerializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
<<ClientInstance>>.SerializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.DeserializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
```

Aby monitorować uruchomienie wyzwalacza, Dodaj następujący kod przed ostatnią `Console.WriteLine` instrukcją w przykładzie:

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

Aby monitorować uruchomienia wyzwalacza i uruchomienia potoków w Azure Portal, zobacz [monitorowanie uruchomień potoków](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Zestaw SDK dla języka Python
W tej sekcji pokazano, jak używać zestawu SDK języka Python do tworzenia, uruchamiania i monitorowania wyzwalacza. Aby zobaczyć ten przykład pracy, najpierw przejdź do [przewodnika Szybki Start: Tworzenie fabryki danych przy użyciu zestawu SDK języka Python](quickstart-create-data-factory-python.md). Następnie Dodaj następujący blok kodu po bloku kod "Monitoruj uruchomienie potoku" w skrypcie języka Python. Ten kod tworzy wyzwalacz harmonogramu, który jest uruchamiany co 15 minut między określonymi godzinami rozpoczęcia i zakończenia. Zaktualizuj zmienną **start_time** do bieżącego czasu UTC, a zmienna **end_time** na godzinę wcześniejszą od bieżącego czasu UTC.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00Z', end_time='2017-12-12T05:00:00Z', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Aby monitorować uruchomienia wyzwalacza i uruchomienia potoków w Azure Portal, zobacz [monitorowanie uruchomień potoków](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Aby utworzyć wyzwalacz, można użyć szablonu Azure Resource Manager. Aby uzyskać instrukcje krok po kroku, zobacz [Tworzenie fabryki danych Azure przy użyciu szablonu Menedżer zasobów](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Przekaż czas rozpoczęcia wyzwalacza do potoku
Azure Data Factory wersja 1 obsługuje odczytywanie lub zapisywanie danych partycjonowanych przy użyciu zmiennych systemowych: **parametru slicestart** , **SliceEnd** , **WindowStart** i **WindowEnd** . W bieżącej wersji Azure Data Factory można osiągnąć takie zachowanie przy użyciu parametru potoku. Czas rozpoczęcia i zaplanowany czas dla wyzwalacza są ustawiane jako wartość parametru potoku. W poniższym przykładzie zaplanowany czas dla wyzwalacza jest przenoszona jako wartość do parametru **scheduledRunTime** potoku:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>Schemat JSON
Poniższa definicja JSON pokazuje, jak utworzyć wyzwalacz harmonogramu z harmonogramem i cyklem:

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
>  Właściwość **parameters** jest obowiązkową wartością elementu właściwości **pipelines** . Jeśli potok nie przyjmuje żadnych parametrów, należy dodać pustą definicję JSON dla właściwości **parameters** .


### <a name="schema-overview"></a>Omówienie schematu
Poniższa tabela zawiera ogólne omówienie głównych elementów schematu odnoszących się do powtarzania i planowania wyzwalacza:

| Właściwość JSON | Opis |
|:--- |:--- |
| **Rozpoczęcia** | Wartość daty i godziny. W przypadku prostych harmonogramów wartość właściwości **startTime** dotyczy pierwszego wystąpienia. W przypadku harmonogramów złożonych wyzwalacz nie jest uruchamiany wcześniej niż określona wartość właściwości **startTime** . <br> W przypadku strefy czasowej UTC format to `'yyyy-MM-ddTHH:mm:ssZ'` , dla innej strefy czasowej, format to `'yyyy-MM-ddTHH:mm:ss'` . |
| **endTime** | Data i godzina zakończenia wyzwalacza. Wyzwalacz nie jest wykonywany po określonej dacie i godzinie zakończenia. Wartość właściwości nie może odnosić się do przeszłości. Ta właściwość jest opcjonalna.  <br> W przypadku strefy czasowej UTC format to `'yyyy-MM-ddTHH:mm:ssZ'` , dla innej strefy czasowej, format to `'yyyy-MM-ddTHH:mm:ss'` . |
| **timeZone** | Strefa czasowa, w której jest tworzony wyzwalacz. To ustawienie ma wpływ na czas **rozpoczęcia** , **Endtime** i **harmonogram** . Zobacz [listę obsługiwanych stref czasowych](#time-zone-option) |
| **wystąpieniu** | Obiekt cyklu określający reguły powtarzania wyzwalacza. Obiekt cyklu obsługuje elementy właściwości **frequency** , **interval** , **endTime** , **count** i **schedule** . Po zdefiniowaniu obiektu cyklu wymagany jest element właściwości **frequency** . Inne elementy obiektu cyklu są opcjonalne. |
| **jaką** | Jednostka częstotliwości powtarzania wyzwalacza. Obsługiwane wartości to „minute” („minuta”), „hour” („godzina”), „day” („dzień”), „week” („tydzień”) i „month” („miesiąc”). |
| **dat** | Dodatnia liczba całkowita oznaczająca interwał wartości właściwości **frequency** , która określa częstotliwość uruchamiania wyzwalacza. Jeśli na przykład właściwość **interval** ma wartość 3, a właściwość **frequency** ma wartość „week” („tydzień”), wyzwalacz jest powtarzany co 3 tygodnie. |
| **rozkład** | Harmonogram cyklu wyzwalacza. Wyzwalacz z określoną wartością właściwości **frequency** zmienia swój cykl na podstawie harmonogramu cyklu. Właściwość **schedule** zawiera modyfikacje cyklu oparte na minutach, godzinach, dniach tygodnia, dniach miesiąca i numerze tygodnia.

> [!IMPORTANT]
> W przypadku strefy czasowej UTC wartość startTime i endTime muszą być zgodne z formatem "RRRR-MM-DDTgg: mm: SS **Z** ", natomiast w przypadku innych stref czasowych, StartTime i Endtime postępuj zgodnie z "RRRR-MM-DDTgg: mm: SS". 
> 
> Zgodnie ze standardem ISO 8601, sufiks _z_ sygnaturą czasową do znacznika czasu oznacza wartość DateTime do strefy czasowej UTC, a pole "czas operacji" jest bezużyteczne Podczas gdy brakuje sufiksu _Z_ dla strefy czasowej UTC, spowoduje to błąd podczas _aktywacji_ wyzwalacza.

### <a name="schema-defaults-limits-and-examples"></a>Wartości domyślne, limity i przykłady harmonogramów

| Właściwość JSON | Typ | Wymagane | Wartość domyślna | Prawidłowe wartości | Przykład |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **Rozpoczęcia** | String | Tak | Brak | Daty i godziny ISO-8601 | dla strefy czasowej UTC `"startTime" : "2013-01-09T09:30:00-08:00Z"` <br> dla innej strefy czasowej `"2013-01-09T09:30:00-08:00"` |
| **timeZone** | String | Tak | Brak | [Wartości strefy czasowej](#time-zone-option)  | `"UTC"` |
| **wystąpieniu** | Obiekt | Tak | Brak | Obiekt cyklu | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **dat** | Liczba | Nie | 1 | Od 1 do 1000 | `"interval":10` |
| **endTime** | String | Tak | Brak | Wartość daty i godziny reprezentująca godzinę w przyszłości. | dla strefy czasowej UTC `"endTime" : "2013-02-09T09:30:00-08:00Z"` <br> dla innej strefy czasowej `"endTime" : "2013-02-09T09:30:00-08:00"`|
| **rozkład** | Obiekt | Nie | Brak | Obiekt harmonogramu | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="time-zone-option"></a>Opcja strefy czasowej

Oto niektóre strefy czasowe obsługiwane przez wyzwalacze harmonogramu:

| Strefa czasowa | Przesunięcie czasu UTC (bez zmiany czasu letniego) | Wartość strefy czasowej | Obserwowanie czasu letniego | Format sygnatury czasowej |
| :--- | :--- | :--- | :--- | :--- |
| Uniwersalny czas koordynowany | 0 | `UTC` | Nie | `'yyyy-MM-ddTHH:mm:ssZ'`|
| Czas pacyficzny (PT) | -8 | `Pacific Standard Time` | Tak | `'yyyy-MM-ddTHH:mm:ss'` |
| Czas środkowy (CT) | -6 | `Central Standard Time` | Tak | `'yyyy-MM-ddTHH:mm:ss'` |
| Czas wschodni (ET) | -5 | `Eastern Standard Time` | Tak | `'yyyy-MM-ddTHH:mm:ss'` |
| Czas uniwersalny Greenwich (GMT) | 0 | `GMT Standard Time` | Tak | `'yyyy-MM-ddTHH:mm:ss'` |
| Środkowoeuropejski czas standardowy | +1 | `W. Europe Standard Time` | Tak | `'yyyy-MM-ddTHH:mm:ss'` |
| Indie (czas standardowy) | + 5:30 | `India Standard Time` | Nie | `'yyyy-MM-ddTHH:mm:ss'` |
| Chiny (czas standardowy) | + 8 | `China Standard Time` | Nie | `'yyyy-MM-ddTHH:mm:ss'` |

Ta lista jest niekompletna. Aby zapoznać się z pełną listą opcji strefy czasowej, zobacz [stronę tworzenia wyzwalacza](#data-factory-ui) portalu Data Factory

### <a name="starttime-property"></a>Właściwość startTime
W poniższej tabeli przedstawiono, w jaki sposób właściwość **startTime** kontroluje uruchamianie wyzwalacza:

| Wartość startTime | Cykl bez harmonogramu | Cykl z harmonogramem |
|:--- |:--- |:--- |
| Godzina rozpoczęcia w przeszłości | Oblicza pierwszy przyszły czas wykonania po czasie rozpoczęcia i uruchamia wyzwalacz w tym czasie.<br/><br/>Uruchamia kolejne wykonania w oparciu o obliczenia wykonane na podstawie ostatniego czasu wykonania.<br/><br/>Zobacz przykład znajdujący się pod tabelą. | Wyzwalacz jest uruchamiany _nie wcześniej niż_ w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br/><br/>Kolejne wykonania opierają na harmonogramie cyklu. |
| Godzina rozpoczęcia w przyszłości lub obecna | Uruchamiany raz o określonym czasie rozpoczęcia.<br/><br/>Uruchamia kolejne wykonania w oparciu o obliczenia wykonane na podstawie ostatniego czasu wykonania. | Wyzwalacz jest uruchamiany _nie wcześniej_ niż w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br/><br/>Kolejne wykonania opierają na harmonogramie cyklu. |

Zobaczmy przykład zdarzeń w przypadku, gdy czas rozpoczęcia przypada w przeszłości, z cyklem, ale bez harmonogramu. Załóżmy, że obecna data i godzina to `2017-04-08 13:00`, czas rozpoczęcia to `2017-04-07 14:00`, a cykl jest określony na 2 dni. (Wartość **cyklu** jest definiowana przez ustawienie właściwości **częstotliwość** na wartość "dzień" i Właściwość **Interwał** na 2). Należy zauważyć, że wartość **StartTime** jest w przeszłości i występuje przed bieżącą godziną.

W tych warunkach pierwsze wykonanie ma wartość o `2017-04-09` `14:00` . Aparat harmonogramu oblicza wystąpienia wykonania od czasu rozpoczęcia. Wszystkie wystąpienia w przeszłości są odrzucane. Aparat wykorzystuje następne wystąpienie, które ma miejsce w przyszłości. W tym scenariuszu czas rozpoczęcia jest `2017-04-07` o godzinie `2:00pm` , więc następne wystąpienie jest w ciągu dwóch dni od tego czasu, czyli od `2017-04-09` `2:00pm` .

Czas pierwszego wykonania jest identyczny, nawet jeśli właściwość **startTime** ma wartość `2017-04-05 14:00` lub `2017-04-01 14:00`. Po pierwszym wykonaniu kolejne wykonania są obliczane przy użyciu harmonogramu. W związku z tym kolejne wykonania są o `2017-04-11` o godzinie `2:00pm` ,, `2017-04-13` `2:00pm` , `2017-04-15` , `2:00pm` i tak dalej.

Gdy z kolei w harmonogramie wyzwalacza nie ustawiono godzin lub minut, wartością domyślną będą godziny lub minuty pierwszego wykonania.

### <a name="schedule-property"></a>Wartość schedule
Użycie harmonogramu może ograniczyć liczbę wykonań wyzwalacza. Jeśli na przykład uruchomienie wyzwalacza z częstotliwością miesięczną zaplanowano na 31. dzień, wyzwalacz będzie uruchamiany tylko w miesiącach, której mają 31 dni.

Harmonogram może także zwiększyć liczbę wykonań wyzwalacza. Na przykład wyzwalacz z częstotliwością miesięczną, którego uruchomienie zaplanowano na 1. i 2. dzień miesiąca, będzie uruchamiany 1. i 2. dnia miesiąca, a nie raz na miesiąc.

W przypadku określenia wielu elementów właściwości **schedule** ocena następuje od największego do najmniejszego elementu. Ocena jest wykonywana w następującej kolejności: numer tygodnia, dzień miesiąca, dzień tygodnia, godzina i minuta.

W poniższej tabeli opisano szczegółowo elementy właściwości **schedule** :


| Element JSON | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |
| **minut** | Minuty godziny, o których uruchamiany jest wyzwalacz. | <ul><li>Liczba całkowita</li><li>Tablica liczb całkowitych</li></ul>
| **liczb** | Godziny dnia, o których uruchamiany jest wyzwalacz. | <ul><li>Liczba całkowita</li><li>Tablica liczb całkowitych</li></ul> |
| **weekDays** | Dni tygodnia, w których uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością tygodniową. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday lub Sunday</li><li>Tablica wartości dni (maksymalny rozmiar tablicy to 7)</li><li>W wartościach dni nie są uwzględniane wielkości liter</li></ul> |
| **monthlyOccurrences** | Dni miesiąca, w których uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. | <ul><li>Tablica obiektów **monthlyOccurrence** : `{ "day": day,  "occurrence": occurrence }` .</li><li>Atrybut **day** jest dniem tygodnia, w którym uruchamiany jest wyzwalacz. Na przykład właściwość **monthlyOccurrences** o wartości **day** wynoszącej `{Sunday}` oznacza każdą niedzielę miesiąca. Atrybut **day** jest wymagany.</li><li>Atrybut **occurence** jest wystąpieniem określonej wartości **day** w miesiącu. Na przykład właściwość **monthlyOccurrences** o wartościach **day** i **occurence** wynoszących `{Sunday, -1}` oznacza ostatnią niedzielę miesiąca. Atrybut **occurence** jest opcjonalny.</li></ul> |
| **monthDays** | Dzień miesiąca, w którym uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. | <ul><li>Dowolna wartość <= -1 i >= -31</li><li>Dowolna wartość >= 1 i <= 31</li><li>Tablica wartości</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Przykłady harmonogramów cyklu wyzwalaczy
Ta sekcja zawiera przykłady harmonogramów cyklu i koncentruje się na obiekcie właściwości **schedule** oraz jego elementach.

W przykładach założono, ze wartość właściwości **interval** wynosi 1 oraz że wartość właściwości **frequency** jest prawidłowa i zgodna z definicją harmonogramu. Na przykład nie można mieć wartości **częstotliwości** "Day" i ma także modyfikację "monthDays" w obiekcie **Schedule** . Tego typu ograniczenia są wymienione w tabeli w poprzedniej sekcji.

| Przykład | Opis |
|:--- |:--- |
| `{"hours":[5]}` | Uruchamiany o godz. 5:00 każdego dnia. |
| `{"minutes":[15], "hours":[5]}` | Uruchamiany o godz. 5:15 każdego dnia. |
| `{"minutes":[15], "hours":[5,17]}` | Uruchamiany o godz. 5:15 i 17:15 każdego dnia. |
| `{"minutes":[15,45], "hours":[5,17]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 każdego dnia. |
| `{"minutes":[0,15,30,45]}` | Uruchamiany co 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Uruchamiany co godzinę. Ten wyzwalacz jest uruchamiany co godzinę. Minuty są kontrolowane przez wartość właściwości **startTime** , gdy wartość jest określona. Jeśli wartość nie jest określona, minuty są kontrolowane przez czas utworzenia. Jeśli na przykład czas rozpoczęcia lub czas utworzenia (zależnie od sytuacji) to 12:25, wyzwalacz jest uruchamiany o godzinie 0:25, 1:25, 2:25, ..., 23:25.<br/><br/>Ten harmonogram jest odpowiednikiem wyzwalacza z właściwością **frequency** o wartości „hour”, właściwością **interval** o wartości 1 i bez właściwości **schedule** .  Tego harmonogramu można także używać z innymi wartościami właściwości **frequency** i **interval** do tworzenia innych wyzwalaczy. Gdy na przykład właściwość **frequency** ma wartość „month”, harmonogram jest uruchamiany tylko raz w miesiącu, a nie każdego dnia, gdy właściwość **frequency** ma wartość „day”. |
| `{"minutes":[0]}` | Uruchamiany co godzinę o pełnej godzinie. Ten wyzwalacz jest uruchamiany co godzinę o pełnej godzinie począwszy od godz. 0:00, a następnie o 1:00, 2:00 itd.<br/><br/>Ten harmonogram jest odpowiednikiem wyzwalacza z właściwością **frequency** o wartości „hour” i właściwością **startTime** o wartości 0 minut lub bez właściwości **schedule** , ale z właściwością **frequency** o wartości „day”. Jeśli wartość **częstotliwości** to "tydzień" lub "miesiąc", harmonogram jest wykonywany tylko raz w tygodniu lub raz dziennie, odpowiednio. |
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
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Uruchamiany w piąty piątek każdego miesiąca o określonej godzinie. Jeśli miesiąc nie ma piątego piątku, potok nie jest uruchamiany, ponieważ jego harmonogram określa uruchamianie tylko w piąty piątek. Aby uruchomić wyzwalacz w ostatni piątek miesiąca, rozważ użycie wartości -1 zamiast 5 dla właściwości **occurrence** . |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Uruchamiany co 15 minut w ostatni piątek miesiąca. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 w trzecią środę każdego miesiąca. |


## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wykonywanie i wyzwalacze potoku](concepts-pipeline-execution-triggers.md#trigger-execution).
