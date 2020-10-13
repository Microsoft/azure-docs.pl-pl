---
title: Korzystanie z działań niestandardowych w potoku
description: Dowiedz się, jak tworzyć niestandardowe działania przy użyciu platformy .NET, a następnie używać działań w potoku Azure Data Factory.
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 8b8114a6abf5579ed0750862d59a5d13178339f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276503"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-use-custom-activities.md)
> * [Bieżąca wersja](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Istnieją dwa typy działań, których można użyć w potoku Azure Data Factory.

- [Działania przenoszenia danych](copy-activity-overview.md) w celu przenoszenia danych między [obsługiwanymi magazynami danych źródłowych i ujścia](copy-activity-overview.md#supported-data-stores-and-formats).
- [Działania przekształcania danych](transform-data.md) w celu przekształcania danych przy użyciu usług obliczeniowych, takich jak Azure HDInsight, Azure Batch i Azure Machine Learning.

Aby przenieść dane do/z magazynu danych, który Data Factory nie obsługuje, lub aby przekształcić/przetwarzać dane w sposób, który nie jest obsługiwany przez Data Factory, można utworzyć **niestandardowe działanie** z własnym przepływem danych lub logiką transformacji oraz użyć działania w potoku. Działanie niestandardowe uruchamia dostosowaną logikę kodu na **Azure Batch** puli maszyn wirtualnych.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli jesteś nowym usługą Azure Batch Service, zobacz następujące artykuły:

* [Azure Batch podstawy](../batch/batch-technical-overview.md) dotyczące omówienia usługi Azure Batch.
* Polecenie cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) służące do tworzenia konta Azure Batch (lub) [Azure Portal](../batch/batch-account-create-portal.md) do tworzenia konta Azure Batch przy użyciu Azure Portal. Szczegółowe instrukcje dotyczące korzystania z polecenia cmdlet można znaleźć w artykule [Używanie programu PowerShell do zarządzania artykułem konta Azure Batch](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) .
* Polecenie cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) w celu utworzenia puli Azure Batch.

## <a name="azure-batch-linked-service"></a>Azure Batch połączona usługa

Poniższy kod JSON definiuje przykład Azure Batch połączonej usługi. Aby uzyskać szczegółowe informacje, zobacz [środowiska obliczeniowe obsługiwane przez Azure Data Factory](compute-linked-services.md)

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

 Aby dowiedzieć się więcej na temat Azure Batch połączonej usługi, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) .

## <a name="custom-activity"></a>Działanie niestandardowe

Poniższy fragment kodu JSON definiuje potok z prostym działaniem niestandardowym. Definicja działania ma odwołanie do połączonej usługi Azure Batch.

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

W tym przykładzie helloworld.exe jest aplikacją niestandardową przechowywaną w folderze customactv2/HelloWorld konta usługi Azure Storage używanym w resourceLinkedService. Działanie niestandardowe przesyła tę aplikację niestandardową do wykonania na Azure Batch. Możesz zamienić polecenie na dowolną preferowaną aplikację, którą można wykonać w docelowym systemie operacyjnym węzłów puli Azure Batch.

W poniższej tabeli opisano nazwy i opisy właściwości, które są specyficzne dla tego działania.

| Właściwość              | Opis                              | Wymagane |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Nazwa działania w potoku     | Tak      |
| description           | Tekst opisujący działanie działania.  | Nie       |
| typ                  | W przypadku działania niestandardowego typem działania jest **niestandardowy**. | Tak      |
| linkedServiceName     | Połączona usługa do Azure Batch. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) .  | Tak      |
| command               | Polecenie aplikacji niestandardowej do wykonania. Jeśli aplikacja jest już dostępna w węźle puli Azure Batch, można pominąć resourceLinkedService i folderPath. Na przykład możesz określić polecenie `cmd /c dir` , które ma być natywnie obsługiwane przez węzeł puli usługi Windows Batch. | Tak      |
| resourceLinkedService | Połączona usługa Azure Storage z kontem magazynu, w którym jest przechowywana aplikacja niestandardowa | Brak &#42;       |
| folderPath            | Ścieżka do folderu aplikacji niestandardowej i wszystkich jej zależności<br/><br/>Jeśli istnieją zależności przechowywane w podfolderach, czyli w hierarchicznej strukturze folderów w obszarze *folderPath* — struktura folderów jest obecnie spłaszczona, gdy pliki są kopiowane do Azure Batch. Oznacza to, że wszystkie pliki są kopiowane do jednego folderu bez podfolderów. Aby obejść ten problem, rozważ kompresję plików, Skopiowanie skompresowanego pliku, a następnie rozpakowywanie go z niestandardowym kodem w odpowiedniej lokalizacji. | Brak &#42;       |
| referenceObjects      | Tablica istniejących połączonych usług i zestawów danych. Połączone usługi i zestawy danych, do których istnieją odwołania, są przesyłane do aplikacji niestandardowej w formacie JSON, dzięki czemu kod niestandardowy może odwoływać się do zasobów Data Factory | Nie       |
| Właściwości ExtendedProperties    | Właściwości zdefiniowane przez użytkownika, które można przekazywać do aplikacji niestandardowej w formacie JSON, dzięki czemu kod niestandardowy może odwoływać się do dodatkowych właściwości | Nie       |
| retentionTimeInDays | Czas przechowywania dla plików przesłanych dla działania niestandardowego. Wartość domyślna to 30 dni. | Nie |

&#42; właściwości `resourceLinkedService` i `folderPath` oba muszą być określone lub być pominięte.

> [!NOTE]
> Jeśli przekazujesz połączone usługi jako referenceObjects w działaniu niestandardowym, dobrym sposobem zapewnienia bezpieczeństwa jest przekazanie połączonej usługi Azure Key Vault włączonej (ponieważ nie zawiera ona żadnych zabezpieczonych ciągów) i pobranie poświadczeń przy użyciu nazwy tajnej bezpośrednio z Key Vault kodu. W [tym miejscu](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) znajdziesz przykład, który odwołuje się do połączonej usługi AKV Enabled, Pobiera poświadczenia z Key Vault, a następnie uzyskuje dostęp do magazynu w kodzie.

## <a name="custom-activity-permissions"></a>Niestandardowe uprawnienia działania

Niestandardowe działanie ustawia konto Azure Batch autoużytkownika na *dostęp niebędący administratorem z zakresem zadania* (domyślna Specyfikacja użytkownika). Nie można zmienić poziomu uprawnień konta autoużytkownika. Aby uzyskać więcej informacji, zobacz [Uruchamianie zadań w obszarze konta użytkowników w usłudze Batch | Konta użytkowników](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Wykonywanie poleceń

Możesz bezpośrednio wykonać polecenie za pomocą działania niestandardowego. W poniższym przykładzie uruchomiono polecenie "Echo Hello World" w docelowym węźle puli Azure Batch i drukuje dane wyjściowe do stdout.

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

Ten przykład pokazuje, jak można użyć referenceObjects i właściwości ExtendedProperties do przekazywania Data Factory obiektów i właściwości zdefiniowanych przez użytkownika do niestandardowej aplikacji.

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

Gdy działanie jest wykonywane, referenceObjects i właściwości ExtendedProperties są przechowywane w następujących plikach, które są wdrażane w tym samym folderze wykonywania SampleApp.exe:

- `activity.json`

  Przechowuje właściwości ExtendedProperties i właściwości działania niestandardowego.

- `linkedServices.json`

  Przechowuje tablicę połączonych usług zdefiniowaną we właściwości referenceObjects.

- `datasets.json`

  Przechowuje tablicę zestawów danych zdefiniowanych we właściwości referenceObjects.

Następujący przykładowy kod przedstawia sposób, w jaki SampleApp.exe może uzyskać dostęp do wymaganych informacji z plików JSON:

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

## <a name="retrieve-execution-outputs"></a>Pobierz dane wyjściowe wykonywania

Uruchomienie potoku można uruchomić za pomocą następującego polecenia programu PowerShell:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Gdy potok jest uruchomiony, możesz sprawdzić dane wyjściowe wykonania przy użyciu następujących poleceń:

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

**Stdout** i **stderr** aplikacji niestandardowej są zapisywane w kontenerze **Adfjobs** w połączonej usłudze Azure Storage zdefiniowanej podczas tworzenia połączonej usługi Azure Batch z identyfikatorem GUID zadania. Możesz uzyskać szczegółową ścieżkę z danych wyjściowych przebiegu działania, jak pokazano w poniższym fragmencie kodu:

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

Jeśli chcesz korzystać z zawartości stdout.txt w działaniach podrzędnych, możesz uzyskać ścieżkę do pliku stdout.txt w wyrażeniu " \@ Activity (" Moja Customs "). Output. wyjść [0]".

> [!IMPORTANT]
> - activity.json, linkedServices.json i datasets.json są przechowywane w folderze Runtime zadania wsadowego. W tym przykładzie activity.json, linkedServices.json i datasets.json jest przechowywany w `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` ścieżce. W razie potrzeby należy je usunąć osobno.
> - W przypadku połączonych usług korzystających z Self-Hosted Integration Runtime informacje poufne, takie jak klucze lub hasła, są szyfrowane przez Self-Hosted Integration Runtime w celu zapewnienia pozostawania poświadczeń w środowisku sieci prywatnej zdefiniowanej przez klienta. W ten sposób może brakować pewnych poufnych pól w przypadku odwołania się do niestandardowego kodu aplikacji. W razie konieczności Użyj elementu SecureString w Właściwości ExtendedProperties zamiast odwołania do połączonej usługi.

## <a name="pass-outputs-to-another-activity"></a>Przekazywanie danych wyjściowych do innego działania

Możesz wysyłać niestandardowe wartości z kodu w niestandardowym działaniu z powrotem do Azure Data Factory. Możesz to zrobić, pisząc je do `outputs.json` aplikacji. Data Factory kopiuje zawartość `outputs.json` i dołącza ją do danych wyjściowych działania jako wartość `customOutput` właściwości. (Limit rozmiaru wynosi 2 MB). Jeśli chcesz korzystać z zawartości `outputs.json` w działaniach podrzędnych, możesz uzyskać wartość przy użyciu wyrażenia `@activity('<MyCustomActivity>').output.customOutput` .

## <a name="retrieve-securestring-outputs"></a>Pobieranie danych wyjściowych SecureString

Poufne wartości właściwości wyznaczono jako typ *SecureString*, jak pokazano w niektórych przykładach w tym artykule, są maskowane na karcie monitorowanie w interfejsie użytkownika Data Factory.  Jednak w rzeczywistym wykonaniu potoku Właściwość *SecureString* jest serializowana jako kod JSON w `activity.json` pliku jako zwykły tekst. Na przykład:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Ta Serializacja nie jest naprawdę bezpieczna i nie jest przeznaczona do zabezpieczenia. Celem jest podpowiedź Data Factory, aby zamaskować wartość na karcie monitorowanie.

Aby uzyskać dostęp do właściwości typu *SecureString* z działania niestandardowego, zapoznaj się z `activity.json` plikiem umieszczonym w tym samym folderze co plik. EXE, deserializacji pliku JSON, a następnie uzyskać dostęp do właściwości JSON (Właściwości ExtendedProperties => [propertyName] => Value).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a> Porównanie działań niestandardowych w wersji 2 i 1 (niestandardowe) działania DotNet

W Azure Data Factory wersji 1 zaimplementowano działanie programu DotNet (Custom), tworząc projekt biblioteki klas .NET z klasą implementującą `Execute` metodę `IDotNetActivity` interfejsu. Połączone usługi, zestawy danych i właściwości rozszerzone w ładunku JSON (Custom) działania DotNet są przesyłane do metody wykonywania jako obiekty silnie określone. Aby uzyskać szczegółowe informacje o zachowaniu wersji 1, zobacz [(niestandardowe) dotnet w wersji 1](v1/data-factory-use-custom-activities.md). Ze względu na tę implementację kod działania DotNet w wersji 1 musi wskazywać na .NET Framework 4.5.2. Działanie programu DotNet w wersji 1 należy również wykonać w węzłach puli Azure Batch opartych na systemie Windows.

W niestandardowym działaniu Azure Data Factory v2 nie trzeba implementować interfejsu .NET. Teraz można bezpośrednio uruchamiać polecenia, skrypty i własne niestandardowe kod skompilowane jako plik wykonywalny. Aby skonfigurować tę implementację, należy określić `Command` Właściwość wraz z `folderPath` właściwością. Działanie niestandardowe przekazuje plik wykonywalny wraz z jego zależnościami do `folderpath` i wykonuje polecenie.

Połączone usługi, zestawy danych (zdefiniowane w referenceObjects) i rozszerzone właściwości zdefiniowane w ładunku JSON dla niestandardowego działania Data Factory v2 mogą być dostępne przez plik wykonywalny jako pliki JSON. Można uzyskać dostęp do wymaganych właściwości przy użyciu serializatora JSON, jak pokazano w powyższym przykładzie kodu SampleApp.exe.

Ze zmianami wprowadzonymi w działaniu niestandardowym Data Factory v2 można napisać niestandardową logikę kodu w preferowanym języku i wykonać ją w systemach operacyjnych Windows i Linux obsługiwanych przez Azure Batch.

W poniższej tabeli opisano różnice między działaniem niestandardowym Data Factory v2 a działaniem programu DotNet Data Factory w wersji 1 (Custom):

|Różnice      | Działanie niestandardowe      | Działanie programu DotNet w wersji 1 (niestandardowej)      |
| ---- | ---- | ---- |
|Sposób definiowania logiki niestandardowej      |Dostarczając plik wykonywalny      |Przez implementację biblioteki DLL platformy .NET      |
|Środowisko wykonawcze logiki niestandardowej      |System Windows lub Linux      |Windows (.NET Framework 4.5.2)      |
|Wykonywanie skryptów      |Obsługuje wykonywanie skryptów bezpośrednio (na przykład "cmd/c echo Hello World" na maszynie wirtualnej z systemem Windows)      |Wymaga implementacji w bibliotece DLL platformy .NET      |
|Wymagany zestaw danych      |Opcjonalne      |Wymagane do łańcucha działań i przekazywania informacji      |
|Przekaż informacje z działania do logiki niestandardowej      |Do ReferenceObjects (LinkedServices i zestawy danych) i właściwości ExtendedProperties (właściwości niestandardowe)      |Za poorednictwem Właściwości ExtendedProperties (właściwości niestandardowe), wejściowych i wyjściowych zestawów danych      |
|Pobieranie informacji z logiki niestandardowej      |Analizuje activity.js, linkedServices.json i datasets.jsprzechowywane w tym samym folderze pliku wykonywalnego      |Za poorednictwem zestawu .NET SDK (ramka .NET w wersji 4.5.2)      |
|Rejestrowanie      |Zapisuje bezpośrednio w strumieniu STDOUT      |Implementowanie rejestratora w bibliotece DLL platformy .NET      |

Jeśli masz już zapisany kod platformy .NET dla działania programu DotNet w wersji 1 (niestandardowej), musisz zmodyfikować swój kod, aby działał z bieżącą wersją działania niestandardowego. Zaktualizuj swój kod, wykonując następujące wytyczne wysokiego poziomu:

  - Zmień projekt z biblioteki klas .NET na aplikację konsolową.
  - Uruchom aplikację za pomocą `Main` metody. `Execute`Metoda `IDotNetActivity` interfejsu nie jest już wymagana.
  - Odczytuj i Analizuj połączone usługi, zestawy danych i działanie z serializatorem JSON, a nie jako obiekty o jednoznacznie określonym typie. Przekaż wartości wymaganych właściwości do głównej logiki kodu niestandardowego. Zapoznaj się z powyższym kodem SampleApp.exe.
  - Obiekt rejestratora nie jest już obsługiwany. Dane wyjściowe z pliku wykonywalnego można wydrukować w konsoli programu i zapisać w stdout.txt.
  - Pakiet NuGet Microsoft. Azure. Management. datafactors nie jest już wymagany.
  - Skompiluj swój kod, Przekaż plik wykonywalny wraz z jego zależnościami do usługi Azure Storage i zdefiniuj ścieżkę we `folderPath` właściwości.

Pełny przykład sposobu, w jaki kompleksowa Biblioteka DLL i potok potoku opisane w artykule Data Factory w wersji 1 [używają działań niestandardowych w potoku Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) , można je zapisać jako działanie niestandardowe Data Factory, zobacz [Data Factory aktywność niestandardowa](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatyczne skalowanie Azure Batch

Możesz również utworzyć pulę Azure Batch za pomocą funkcji **automatycznego skalowania** . Można na przykład utworzyć pulę usługi Azure Batch z użyciem 0 dedykowanych maszyn wirtualnych i formułę skalowania automatycznego na podstawie liczby oczekujących zadań.

Przykładowa formuła ma następujące zachowanie: po początkowym utworzeniu puli rozpoczyna się od 1 maszyny wirtualnej. Metryka $PendingTasks definiuje liczbę zadań w stanie uruchomienia + aktywny (w kolejce). Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio ustawia TargetDedicated. Gwarantuje to, że TargetDedicated nigdy nie przekracza 25 maszyn wirtualnych. Tak więc w miarę przesyłania nowych zadań Pula automatycznie rośnie i kończy zadania, maszyny wirtualne stają się bezpłatne po jednym, a skalowanie automatyczne zmniejsza te maszyny wirtualne. startingNumberOfVMs i maxNumberofVMs można dostosować do własnych potrzeb.

Formuła automatycznego skalowania:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Aby uzyskać szczegółowe informacje [, zobacz Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch](../batch/batch-automatic-scaling.md) .

Jeśli pula używa domyślnej [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), przed uruchomieniem działania niestandardowego może upłynąć 15-30 minut, aby przygotować maszynę wirtualną. Jeśli pula korzysta z innego autoScaleEvaluationInterval, usługa Batch może wykonać autoScaleEvaluationInterval + 10 minut.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Działanie wykonywania wsadowego Machine Learning](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
