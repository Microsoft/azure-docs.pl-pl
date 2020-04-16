---
title: Programowo monitoruj fabrykę danych platformy Azure
description: Dowiedz się, jak monitorować potok w fabryce danych przy użyciu różnych zestawów programistów (SDK).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: d416a4a2bace2aeced6961d4959b0478feb0e650
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398839"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programowo monitoruj fabrykę danych platformy Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób monitorowania potoku w fabryce danych przy użyciu różnych zestawów programistów (SDK). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Zakres danych

Usługa Data Factory przechowuje tylko dane przebiegu potoku przez 45 dni. Podczas wykonywania zapytań programowo dotyczących danych dotyczących przebiegów potoku usługi `Get-AzDataFactoryV2PipelineRun` Data Factory — na `LastUpdatedAfter` przykład `LastUpdatedBefore` za pomocą polecenia programu PowerShell — nie ma maksymalnych dat dla parametrów opcjonalnych i parametrów. Ale jeśli kwerendy dla danych w ubiegłym roku, na przykład kwerenda nie zwraca błąd, ale zwraca tylko dane uruchomienia potoku z ostatnich 45 dni.

Jeśli chcesz utrwalić dane uruchamiania potoku przez ponad 45 dni, skonfiguruj własne rejestrowanie diagnostyczne za pomocą [usługi Azure Monitor.](monitor-using-azure-monitor.md)

## <a name="net"></a>.NET
Aby uzyskać pełną instruktaż tworzenia i monitorowania potoku przy użyciu pliku .NET SDK, zobacz [Tworzenie fabryki danych i potoku przy użyciu platformy .NET](quickstart-create-data-factory-dot-net.md).

1. Dodaj następujący kod, aby stale sprawdzać stan przebiegu potoku, dopóki nie zakończy kopiowania danych.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Dodaj następujący kod do tego pobiera szczegóły uruchamiania działania kopiowania, na przykład rozmiar danych odczytu/zapisu.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Aby uzyskać pełną dokumentację dotyczącą sdk .NET, zobacz [Data Factory .NET SDK reference](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Aby uzyskać pełną instruktaż tworzenia i monitorowania potoku przy użyciu zestawu SDK języka Python, zobacz [Tworzenie fabryki danych i potoku przy użyciu języka Python](quickstart-create-data-factory-python.md).

Aby monitorować przebieg potoku, dodaj następujący kod:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Aby uzyskać pełną dokumentację dotyczącą zestawu SDK języka Python, zobacz [odwołanie do SDK SDK usługi Data Factory Python](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>Interfejs API REST
Aby uzyskać pełną instruktaż tworzenia i monitorowania potoku przy użyciu interfejsu API REST, zobacz [Tworzenie fabryki danych i potoku przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md).
 
1. Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Uruchom następujący skrypt, aby pobrać szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytanych/zapisanych danych.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Aby uzyskać pełną dokumentację dotyczącą interfejsu API REST, zobacz [odwołanie do interfejsu API REST fabryki danych](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Aby uzyskać pełną instruktaż tworzenia i monitorowania potoku za pomocą programu PowerShell, zobacz [Tworzenie fabryki danych i potoku przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md).

1. Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Uruchom następujący skrypt, aby pobrać szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytanych/zapisanych danych.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Aby uzyskać pełną dokumentację dotyczącą poleceń cmdlet programu PowerShell, zobacz [Odwołanie do polecenia cmdlet fabryki danych programu PowerShell](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Następne kroki
Zobacz [Monitorowanie potoków przy użyciu usługi Azure Monitor](monitor-using-azure-monitor.md) artykuł, aby dowiedzieć się więcej na temat korzystania z usługi Azure Monitor do monitorowania potoków fabryki danych. 

