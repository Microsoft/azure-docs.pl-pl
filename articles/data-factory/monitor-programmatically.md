---
title: Programowe monitorowanie fabryki danych Azure
description: Dowiedz się, jak monitorować potok w fabryce danych przy użyciu różnych zestawów SDK (Software Development Kit).
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 6c913c7c623c77baea0c575d06d2c44709af43fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740447"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programowe monitorowanie fabryki danych Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób monitorowania potoku w fabryce danych przy użyciu różnych zestawów SDK (Software Development Kit). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Zakres danych

Data Factory przechowuje tylko dane uruchomienia potoku przez 45 dni. Podczas wykonywania zapytania programistycznego w celu uzyskania danych o Data Factory uruchomieniach potoku — na przykład przy użyciu polecenia programu PowerShell `Get-AzDataFactoryV2PipelineRun` nie są dostępne żadne maksymalne daty dla parametrów opcjonalnych `LastUpdatedAfter` i `LastUpdatedBefore` . Ale jeśli kwerenda dotyczy danych przez ostatni rok, na przykład nie zostanie wyświetlony błąd, ale tylko dane uruchomienia potoku z ostatnich 45 dni.

Jeśli chcesz zachować dane przebiegu potoku przez ponad 45 dni, skonfiguruj własne rejestrowanie diagnostyczne przy użyciu [Azure monitor](monitor-using-azure-monitor.md).

## <a name="pipeline-run-information"></a>Informacje o uruchamianiu potoku

Aby uzyskać właściwości przebiegu potoku, zapoznaj się z dokumentacją [interfejsu API PipelineRun](/rest/api/datafactory/pipelineruns/get#pipelinerun). Uruchomienie potoku ma inny stan w trakcie jego cyklu życia. poniżej przedstawiono możliwe wartości stanu uruchomienia:

* W kolejce
* Toku
* Powodzenie
* Niepowodzenie
* Anulowanie
* Anulowane

## <a name="net"></a>.NET
Aby uzyskać kompletny przewodnik tworzenia i monitorowania potoku przy użyciu zestawu .NET SDK, zobacz [Tworzenie fabryki danych i potoku przy użyciu platformy .NET](quickstart-create-data-factory-dot-net.md).

1. Dodaj następujący kod, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Dodaj następujący kod do elementu, który pobiera szczegóły uruchomienia działania kopiowania, na przykład rozmiar danych odczytywanych/zapisywana.

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

Aby uzyskać pełną dokumentację dotyczącą zestawu SDK platformy .NET, zobacz temat [informacje dotyczące Data Factory .NET SDK](/dotnet/api/microsoft.azure.management.datafactory).

## <a name="python"></a>Python
Aby uzyskać kompletny przewodnik tworzenia i monitorowania potoku przy użyciu zestawu SDK języka Python, zobacz [Tworzenie fabryki danych i potoku przy użyciu języka Python](quickstart-create-data-factory-python.md).

Aby monitorować uruchomienie potoku, Dodaj następujący kod:

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

Aby uzyskać pełną dokumentację dotyczącą zestawu SDK języka Python, zobacz [Data Factory Dokumentacja zestawu SDK języka Python](/python/api/overview/azure/datafactory).

## <a name="rest-api"></a>Interfejs API REST
Aby uzyskać kompletny przewodnik tworzenia i monitorowania potoku przy użyciu interfejsu API REST, zobacz [Tworzenie fabryki danych i potoku przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md).
 
1. Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
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

Aby uzyskać pełną dokumentację interfejsu API REST, zobacz [Data Factory Dokumentacja interfejsu API REST](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Aby uzyskać pełny Przewodnik po tworzeniu i monitorowaniu potoku przy użyciu programu PowerShell, zobacz [Tworzenie fabryki danych i potoku przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md).

1. Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ( ($run.Status -ne "InProgress") -and ($run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $run.Status)
                $run
                break
            }
            Write-Output ("Pipeline is running...status: " + $run.Status)
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

Aby uzyskać pełną dokumentację poleceń cmdlet programu PowerShell, zobacz [Data Factory dokumentacja poleceń cmdlet programu PowerShell](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Następne kroki
Zobacz temat [monitorowanie potoków przy użyciu Azure monitor](monitor-using-azure-monitor.md) artykułu, aby dowiedzieć się więcej o używaniu Azure monitor do monitorowania potoków Data Factory.