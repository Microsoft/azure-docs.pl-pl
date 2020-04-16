---
title: Używanie działań niestandardowych w potoku
description: Dowiedz się, jak tworzyć niestandardowe działania i używać ich w potoku usługi Azure Data Factory.
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 74e381a9ad32acdaa8cbb719824d74ca6d339f30
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418953"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-use-custom-activities.md)
> * [Aktualna wersja](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Istnieją dwa typy działań, których można użyć w potoku usługi Azure Data Factory.

- [Działania związane z przenoszeniem danych](copy-activity-overview.md) w celu przenoszenia danych między [obsługiwanymi magazynami danych źródłowych i magazynów danych ujścia.](copy-activity-overview.md#supported-data-stores-and-formats)
- [Działania przekształcania danych](transform-data.md) w celu przekształcania danych przy użyciu usług obliczeniowych, takich jak Usługi Azure HDInsight, Usługa Azure Batch i Azure Machine Learning.

Aby przenieść dane do/z magazynu danych, który usługa Data Factory nie obsługuje, lub aby przekształcić/przetworzyć dane w sposób, który nie jest obsługiwany przez fabrykę danych, można utworzyć **działanie niestandardowe** z własną logiką przenoszenia lub przekształcania danych i użyć działania w potoku. Działanie niestandardowe uruchamia logikę kodu dostosowane na **puli usługi Azure Batch** maszyn wirtualnych.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zobacz następujące artykuły, jeśli jesteś nowy w usłudze Azure Batch:

* [Podstawowe informacje o usłudze Azure Batch](../batch/batch-technical-overview.md) dla przeglądu usługi Azure Batch.
* Polecenie cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) do utworzenia konta usługi Azure Batch (lub) [witryny Azure Portal](../batch/batch-account-create-portal.md) w celu utworzenia konta usługi Azure Batch przy użyciu witryny Azure portal. Zobacz [korzystanie z programu PowerShell do zarządzania kontem wsadowym platformy Azure](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) artykuł szczegółowe instrukcje dotyczące korzystania z polecenia cmdlet.
* Polecenie cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) do utworzenia puli usługi Azure Batch.

## <a name="azure-batch-linked-service"></a>Usługa połączona z usługą Azure Batch

Poniższy JSON definiuje przykładową usługę połączona usługi Azure Batch. Aby uzyskać szczegółowe informacje, zobacz [Środowiska obliczeniowe obsługiwane przez usługę Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Aby dowiedzieć się więcej o usłudze połączonej usługi Azure Batch, zobacz Artykuł [dotyczący usług połączonych obliczeń.](compute-linked-services.md)

## <a name="custom-activity"></a>Działanie niestandardowe

Poniższy fragment kodu JSON definiuje potok z prostego działania niestandardowego. Definicja działania ma odwołanie do usługi połączonej usługi Azure Batch.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

W tym przykładzie helloworld.exe jest niestandardową aplikacją przechowywaną w folderze customactv2/helloworld konta usługi Azure Storage używanego w resourceLinkedService. Działanie niestandardowe przesyła tę niestandardową aplikację do wykonania w usłudze Azure Batch. Można zastąpić polecenie dowolną preferowaną aplikacją, która może być wykonywana w docelowym systemie operacyjnym węzłów puli wsadowej platformy Azure.

W poniższej tabeli opisano nazwy i opisy właściwości, które są specyficzne dla tego działania.

| Właściwość              | Opis                              | Wymagany |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Nazwa działania w potoku     | Tak      |
| description           | Tekst opisujący działanie.  | Nie       |
| type                  | W przypadku działania niestandardowego typem działania jest **Niestandardowy**. | Tak      |
| linkedServiceName     | Usługa połączona z usługą Azure Batch. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł [dotyczący powiązanych usług obliczeń.](compute-linked-services.md)  | Tak      |
| command               | Polecenie aplikacji niestandardowej do wykonania. Jeśli aplikacja jest już dostępna w węźle puli wsadowej platformy Azure, można pominąć resourceLinkedService i folderPath. Na przykład można określić polecenie, które ma być `cmd /c dir`, które jest natywnie obsługiwane przez węzeł Puli wsadowej systemu Windows. | Tak      |
| resourceLinkedService (Usługalinked) | Usługa Linked Service usługi Azure Storage na koncie Storage, na którym jest przechowywana aplikacja niestandardowa | Bez &#42;       |
| folderPath            | Ścieżka do folderu aplikacji niestandardowej i wszystkich jej zależności<br/><br/>Jeśli masz zależności przechowywane w podfolderach — to znaczy w hierarchicznej strukturze folderów w obszarze *folderPath* - struktura folderów jest obecnie spłaszczona, gdy pliki są kopiowane do usługi Azure Batch. Oznacza to, że wszystkie pliki są kopiowane do jednego folderu bez podfolderów. Aby obejść to zachowanie, należy rozważyć kompresję plików, skopiowanie skompresowanego pliku, a następnie rozpakowywanie go za pomocą kodu niestandardowego w żądanej lokalizacji. | Bez &#42;       |
| referenceObjects      | Tablica istniejących usług połączonych i zestawów danych. Dowodowane usługi linked services i zestawy danych są przekazywane do aplikacji niestandardowej w formacie JSON, dzięki czemu kod niestandardowy może odwoływać się do zasobów fabryki danych | Nie       |
| Extendedproperties    | Właściwości zdefiniowane przez użytkownika, które mogą być przekazywane do aplikacji niestandardowej w formacie JSON, dzięki czemu kod niestandardowy może odwoływać się do dodatkowych właściwości | Nie       |
| retentionTimeInDays | Czas przechowywania plików przesłanych do działania niestandardowego. Wartość domyślna to 30 dni. | Nie |

&#42; Właściwości `resourceLinkedService` i `folderPath` muszą być określone zarówno lub oba zostaną pominięte.

> [!NOTE]
> Jeśli przekazujesz połączone usługi jako referenceObjects w usłudze Niestandardowej, jest dobrą praktyką zabezpieczeń, aby przekazać usługę linked z włączoną usługą Azure Key Vault (ponieważ nie zawiera żadnych bezpiecznych ciągów) i pobrać poświadczenia przy użyciu tajnej nazwy bezpośrednio z usługi Key Vault z kodu. Można znaleźć przykład [tutaj,](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) który odwołuje się do usługi połączonej z włączoną funkcją AKV, pobiera poświadczenia z usługi Key Vault, a następnie uzyskuje dostęp do magazynu w kodzie.

## <a name="custom-activity-permissions"></a>Niestandardowe uprawnienia działania

Działanie niestandardowe ustawia konto automatycznego użytkownika usługi Azure Batch na *dostęp niebędący administratorem z zakresem zadania* (domyślna specyfikacja użytkownika automatycznego). Nie można zmienić poziomu uprawnień konta użytkownika automatycznego. Aby uzyskać więcej informacji, zobacz [Uruchamianie zadań w obszarze konta użytkowników w uliczce | Konta użytkowników automatycznych](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Wykonywanie poleceń

Można bezpośrednio wykonać polecenie przy użyciu działania niestandardowego. W poniższym przykładzie uruchamia polecenie "echo hello world" w węzłach docelowej puli wsadowej platformy Azure i drukuje dane wyjściowe do stdout.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Przekazywanie obiektów i właściwości

W tym przykładzie pokazano, jak można użyć referenceObjects i extendedProperties do przekazywania obiektów fabryki danych i właściwości zdefiniowane przez użytkownika do aplikacji niestandardowej.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {          
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

Podczas wykonywania działania, referenceObjects i extendedProperties są przechowywane w następujących plikach, które są wdrażane w tym samym folderze wykonywania sampleapp.exe:

- `activity.json`

  Przechowuje rozszerzonewłaściściściści i właściwości działania niestandardowego.

- `linkedServices.json`

  Przechowuje tablicę usług połączonych zdefiniowanych we właściwości referenceObjects.

- `datasets.json`

  Przechowuje tablicę zestawów danych zdefiniowanych we właściwości referenceObjects.

Poniższy przykładowy kod pokazuje, jak program SampleApp.exe może uzyskać dostęp do wymaganych informacji z plików JSON:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Pobieranie wyników wykonywania

Uruchomienie potoku można uruchomić za pomocą następującego polecenia programu PowerShell:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Po uruchomieniu potoku można sprawdzić dane wyjściowe wykonania za pomocą następujących poleceń:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

**Stdout** i **stderr** aplikacji niestandardowej są zapisywane w kontenerze **adfjobs** w usłudze linked azure storage zdefiniowane podczas tworzenia usługi Azure Batch Linked Service z identyfikatorem GUID zadania. Szczegółową ścieżkę można uzyskać z danych wyjściowych przebiegu działania, jak pokazano w poniższym urywek:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Jeśli chcesz korzystać z zawartości pliku stdout.txt w działaniach podrzędnych, możesz uzyskać ścieżkę do\@pliku stdout.txt w wyrażeniu " activity('MyCustomActivity').output.outputs[0]".

> [!IMPORTANT]
> - Activity.json, linkedServices.json i datasets.json są przechowywane w folderze środowiska wykonawczego zadania Batch. W tym przykładzie activity.json, linkedServices.json i datasets.json są przechowywane w `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` ścieżce. W razie potrzeby należy je oczyścić oddzielnie.
> - W przypadku usług połączonych korzystających ze środowiska wykonawczego integracji hostowanej poufne informacje, takie jak klucze lub hasła, są szyfrowane przez środowisko uruchomieniowe integracji hostowanej przez osoby korzystające z funkcji hostowanych, aby zapewnić pozostanie poświadczeń w środowisku sieci prywatnej zdefiniowanym przez klienta. Niektóre poufne pola mogą brakować, gdy odwołuje się do kodu aplikacji niestandardowej w ten sposób. Użyj SecureString w extendedProperties zamiast przy użyciu odwołania usługi połączonej w razie potrzeby.

## <a name="pass-outputs-to-another-activity"></a>Przekazywanie wyjść do innego działania

Można wysłać wartości niestandardowe z kodu w aktywność niestandardową z powrotem do usługi Azure Data Factory. Można to zrobić, zapisując je z `outputs.json` aplikacji. Fabryka danych kopiuje zawartość `outputs.json` i dołącza ją do danych `customOutput` wyjściowych działania jako wartość właściwości. (Limit rozmiaru wynosi 2 MB). Jeśli chcesz korzystać z `outputs.json` zawartości w działaniach podrzędnych, możesz uzyskać `@activity('<MyCustomActivity>').output.customOutput`wartość za pomocą wyrażenia .

## <a name="retrieve-securestring-outputs"></a>Pobieranie wyjść SecureString

Wartości właściwości poufnych oznaczonych jako typ *SecureString*, jak pokazano w niektórych przykładach w tym artykule, są maskowane na karcie Monitorowanie w interfejsie użytkownika fabryki danych.  W rzeczywistym wykonaniu potoku, jednak *SecureString* właściwość jest `activity.json` serializowany jako JSON w pliku jako zwykły tekst. Przykład:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Ta serializacja nie jest naprawdę bezpieczne i nie ma być bezpieczne. Celem jest podpowiedź do fabryki danych, aby zamaskować wartość na karcie Monitorowanie.

Aby uzyskać dostęp do właściwości typu *SecureString* `activity.json` z działania niestandardowego, przeczytaj plik, który jest umieszczany w tym samym folderze co plik . EXE, deserialize JSON, a następnie uzyskać dostęp do właściwości JSON (extendedProperties => [propertyName] => wartość).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a>Porównanie działania niestandardowego w wersji 2 i aktywności dotnetu w wersji 1 (niestandardowej)

W usłudze Azure Data Factory w wersji 1 implementujesz (niestandardowe) działanie DotNet, tworząc `Execute` projekt `IDotNetActivity` biblioteki klas .NET z klasą implementującą metodę interfejsu. Połączone usługi, zestawy danych i właściwości rozszerzone w ładunku JSON (niestandardowe) Działanie DotNet są przekazywane do metody wykonywania jako obiekty silnie typizowane. Aby uzyskać szczegółowe informacje na temat zachowania w wersji 1, zobacz [(Niestandardowy) DotNet w wersji 1](v1/data-factory-use-custom-activities.md). Z powodu tej implementacji kod działania DotNet w wersji 1 musi być ukierunkowany na platformę .NET Framework 4.5.2. Działanie DotNet w wersji 1 również musi być wykonywane w węzłach puli wsadowej platformy Azure w oparciu o system Windows.

W usłudze Azure Data Factory V2 działania niestandardowego nie są wymagane do zaimplementowania interfejsu .NET. Teraz można bezpośrednio uruchamiać polecenia, skrypty i własny kod niestandardowy, skompilowany jako plik wykonywalny. Aby skonfigurować tę implementację, należy określić `Command` właściwość wraz z właściwością. `folderPath` Działanie niestandardowe przekazuje plik wykonywalny i jego zależności do `folderpath` i wykonuje polecenie dla Ciebie.

Połączone usługi, zestawy danych (zdefiniowane w referenceObjects) i właściwości rozszerzone zdefiniowane w ładunku JSON działania niestandardowego fabryki danych w wersji 2 są dostępne przez plik wykonywalny jako pliki JSON. Można uzyskać dostęp do wymaganych właściwości przy użyciu serializatora JSON, jak pokazano w poprzednim przykładzie kodu SampleApp.exe.

Dzięki zmianom wprowadzonym w usłudze niestandardowej Data Factory V2 można napisać logikę kodu niestandardowego w preferowanym języku i wykonać ją w systemach operacyjnych Windows i Linux obsługiwanych przez usługę Azure Batch.

W poniższej tabeli opisano różnice między działaniem niestandardowym fabryki danych V2 a aktywnością DotNet w wersji 1 (niestandardowa) w wersji danych:

|Różnice      | Działanie niestandardowe      | Działanie dotNet w wersji 1 (niestandardowej)      |
| ---- | ---- | ---- |
|Jak definiowana jest logika niestandardowa      |Poprzez zapewnienie pliku wykonywalnego      |Implementując bibliotekę DLL .NET      |
|Środowisko wykonywania logiki niestandardowej      |Windows lub Linux      |Windows (.NET Framework 4.5.2)      |
|Wykonywanie skryptów      |Obsługuje wykonywanie skryptów bezpośrednio (na przykład "cmd /c echo hello world" na maszynie Wirtualnej systemu Windows)      |Wymaga implementacji w biblioteki DLL .NET      |
|Wymagany zestaw danych      |Optional (Opcjonalność)      |Wymagane do prowadzenia działań łańcuchowych i przekazywania informacji      |
|Przekazywanie informacji z działania do logiki niestandardowej      |Poprzez ReferenceObjects (LinkedServices and Datasets) i ExtendedProperties (właściwości niestandardowe)      |Za pośrednictwem ExtendedProperties (właściwości niestandardowe), input i output datasets      |
|Pobieranie informacji w logice niestandardowej      |Analizuje activity.json, linkedServices.json i datasets.json przechowywane w tym samym folderze pliku wykonywalnego      |Za pośrednictwem pliku .NET SDK (.NET Frame 4.5.2)      |
|Rejestrowanie      |Zapisuje bezpośrednio do STDOUT      |Implementowanie rejestratora w biblioteki DLL .NET      |

Jeśli masz istniejący kod platformy .NET napisany dla działania dotnetu w wersji 1 (niestandardowej), należy zmodyfikować kod, aby działał z bieżącą wersją działania niestandardowego. Zaktualizuj kod, postępujący zgodnie z następującymi wytycznymi wysokiego poziomu:

  - Zmień projekt z biblioteki klas .NET na aplikację konsoli.
  - Uruchom aplikację za `Main` pomocą metody. Metoda `Execute` `IDotNetActivity` interfejsu nie jest już wymagana.
  - Odczytuj i analizuj połączone usługi, zestawy danych i działanie za pomocą serializatora JSON, a nie jako silnie typizowanych obiektów. Przekaż wartości wymaganych właściwości do głównej logiki kodu niestandardowego. Jako przykład można znaleźć poprzedni kod SampleApp.exe.
  - Logger obiekt nie jest już obsługiwany. Dane wyjściowe z pliku wykonywalnego można wydrukować na konsoli i zapisać na stdout.txt.
  - Pakiet Microsoft.Azure.Management.DataFactories NuGet nie jest już wymagany.
  - Skompiluj kod, przekaż plik wykonywalny i jego `folderPath` zależności do usługi Azure Storage i zdefiniuj ścieżkę we właściwości.

Aby uzyskać pełną próbkę, w jaki sposób end-to-end DLL i próbki potoku opisane w fabryce danych w wersji 1 artykułu [Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) można przepisać jako działanie niestandardowe fabryki danych, zobacz przykład działania [niestandardowego fabryki danych](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatyczne skalowanie usługi Azure Batch

Można również utworzyć pulę usługi Azure Batch z funkcją **skalowania automatycznego.** Na przykład można utworzyć pulę wsadową platformy Azure z 0 dedykowanymi maszynami wirtualnymi i formułą skalowania automatycznego na podstawie liczby oczekujących zadań.

Przykładowa formuła w tym miejscu osiąga następujące zachowanie: Gdy pula jest początkowo tworzona, zaczyna się od 1 maszyny Wirtualnej. $PendingTasks metryka definiuje liczbę zadań w stanie uruchomione + aktywne (w kolejce). Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio ustawia targetDedicated. Zapewnia, że TargetDedicated nigdy nie wykracza poza 25 maszyn wirtualnych. Tak jak nowe zadania są przesyłane, puli automatycznie rośnie i jak zadania ukończone, maszyny wirtualne stają się wolne jeden po drugim i skalowanie automatyczne zmniejsza te maszyny wirtualne. uruchamianieNumberOfVMs i maxNumberofVMs mogą być dostosowane do Twoich potrzeb.

Formuła skalowania automatycznego:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zobacz [Automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch, aby](../batch/batch-automatic-scaling.md) uzyskać szczegółowe informacje.

Jeśli pula używa domyślnej [funkcji autoScaleEvaluationInterval,](https://msdn.microsoft.com/library/azure/dn820173.aspx)przygotowanie maszyny wirtualnej może potrwać 15-30 minut. Jeśli pula używa innego autoScaleEvaluationInterval, usługa Batch może potrwać autoScaleEvaluationInterval + 10 minut.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność w serwisie Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Działanie wsadowe uczenia maszynowego](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
