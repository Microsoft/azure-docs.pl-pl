---
title: Tworzenie testów chaos i trybu failover dla usługi Azure Service Fabric
description: Przy użyciu scenariuszy testowania Service Fabric chaos i przełączenia w tryb failover w celu wywołania błędów i zweryfikowania niezawodności usług.
ms.topic: conceptual
ms.date: 10/1/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 9939ef12dcbcc2b5d8733a68166c8e7162c4b0ea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594989"
---
# <a name="testability-scenarios"></a>Scenariusze testowania
Duże systemy rozproszone, takie jak infrastruktury chmury, są z natury zawodowe. Dzięki platformie Azure Service Fabric deweloperzy mogą pisać usługi do uruchamiania w oparciu o niezawodną infrastrukturę. Aby można było napisać wysokiej jakości usługi, deweloperzy muszą mieć możliwość wywołujący taką niezawodną infrastrukturę w celu przetestowania stabilności usług.

Usługa błędu Analysis Services umożliwia deweloperom wywoływanie działań błędów w celu przetestowania usług w przypadku wystąpienia awarii. Niemniej jednak symulowane błędy są możliwe tylko do tej pory. Aby kontynuować testowanie, można użyć scenariuszy testowych w Service Fabric: test chaos i Test trybu failover. Te scenariusze symulują ciągłe i nieprzerwane błędy w całym klastrze przez dłuższy czas. Gdy test zostanie skonfigurowany z szybkością i rodzajem błędów, można go uruchomić za pomocą interfejsów API języka C# lub programu PowerShell, aby generować błędy w klastrze i usłudze.

> [!WARNING]
> ChaosTestScenario jest zastępowana przez bardziej odporną chaos opartą na usługach. Aby uzyskać więcej informacji, zapoznaj się z artykułem o [kontrolowanej chaos](service-fabric-controlled-chaos.md) .
> 
> 

## <a name="chaos-test"></a>Test chaos
Scenariusz chaos generuje błędy w całym klastrze Service Fabric. Scenariusz kompresuje błędy ogólnie widoczne w miesiącach lub latach do kilku godzin. Połączenie z przeplotem błędów o wysokim współczynniku błędów powoduje znalezienie przypadków narożnych, które w przeciwnym razie nie zostały pominięte. Prowadzi to do znacznego zwiększenia jakości kodu usługi.

### <a name="faults-simulated-in-the-chaos-test"></a>Błędy symulowane w teście chaos
* Ponowne uruchamianie węzła
* Ponowne uruchamianie wdrożonego pakietu kodu
* Usuń replikę
* Uruchom ponownie replikę
* Przenieś replikę podstawową (opcjonalnie)
* Przenoszenie repliki pomocniczej (opcjonalnie)

Test chaos uruchamia wiele iteracji błędów i walidacji klastra w określonym przedziale czasu. Można również skonfigurować czas oczekiwania na stabilizację klastra i pomyślne sprawdzenie poprawności. Scenariusz kończy się niepowodzeniem w przypadku wystąpienia pojedynczego błędu podczas sprawdzania poprawności klastra.

Rozważmy na przykład, że zestaw testów ma być uruchamiany przez jedną godzinę, a maksymalnie trzy współbieżne błędy. Test będzie powodować trzy błędy, a następnie sprawdza kondycję klastra. Test przeprowadzi iterację w poprzednim kroku, aż klaster stanie się w złej kondycji lub upłynie godzinę. Jeśli klaster ulegnie złej kondycji w dowolnej iteracji, czyli nie jest ustabilizowany w skonfigurowanym czasie, test zakończy się niepowodzeniem z wyjątkiem. Ten wyjątek wskazuje na to, że coś pozostało niewłaociwe i wymaga dalszych badań.

W bieżącym formularzu aparat generacji błędów w teście chaos wywołuje tylko bezpieczne błędy. Oznacza to, że w przypadku braku zewnętrznych błędów kworum lub utracie danych nigdy nie wystąpi.

### <a name="important-configuration-options"></a>Ważne opcje konfiguracji
* **TimeToRun**: łączny czas uruchomienia testu przed zakończeniem sukcesem. Test może zakończyć się wcześniej zamiast błędu walidacji.
* **MaxClusterStabilizationTimeout**: maksymalny czas oczekiwania, aż klaster przestanie działać w dobrej kondycji przed zakończeniem testu. Przeprowadzane sprawdzenia polegają na tym, czy kondycja klastra jest prawidłowa, kondycja usługi to OK, rozmiar docelowego zestawu replik jest osiągany dla partycji usługi i nie istnieją repliki inbuild.
* **MaxConcurrentFaults**: Maksymalna liczba współbieżnych błędów wywołanych w każdej iteracji. Im większa liczba, tym bardziej agresywny test, co spowodowało bardziej skomplikowane przejścia w tryb failover i kombinacje przejść. Test gwarantuje, że w przypadku braku zewnętrznych błędów nie będzie kworum lub utracie danych, niezależnie od tego, jak wysoka jest taka konfiguracja.
* **EnableMoveReplicaFaults**: włącza lub wyłącza błędy, które powodują przeniesienie repliki podstawowej lub pomocniczej. Te błędy są domyślnie wyłączone.
* **WaitTimeBetweenIterations**: ilość czasu oczekiwania między iteracjami, tj. po rozpoczęciu rundy błędów i odpowiadającej jej weryfikacji.

### <a name="how-to-run-the-chaos-test"></a>Jak uruchomić test chaos
Przykład w języku C#

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

Moduł Service Fabric PowerShell zawiera dwa sposoby rozpoczęcia scenariusza chaos. `Invoke-ServiceFabricChaosTestScenario` jest oparty na kliencie, a jeśli komputer kliencki jest zamykany w połowie przez test, nie będą wprowadzane żadne dalsze błędy. Alternatywnie istnieje zestaw poleceń, które mają na celu zachowanie przebiegu testu w przypadku jego zamknięcia. `Start-ServiceFabricChaos` używa stanowej i niezawodnej usługi systemowej o nazwie FaultAnalysisService, dzięki czemu błędy będą nadal wprowadzane do momentu, gdy TimeToRun się nie. `Stop-ServiceFabricChaos` może służyć do ręcznego zatrzymania scenariusza i `Get-ServiceFabricChaosReport` uzyskiwania raportu. Aby uzyskać więcej informacji, zobacz [informacje dotyczące platformy Azure Service Fabric PowerShell](/powershell/module/servicefabric/) i [powodowania kontrolowanego chaos w klastrach Service Fabric](service-fabric-controlled-chaos.md).

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Test pracy w trybie failover
Scenariusz testowania trybu failover jest wersją scenariusza testowania chaos, który jest przeznaczony dla określonej partycji usługi. Testuje efekt przejścia w tryb failover na określonej partycji usługi, pozostawiając pozostałe usługi bez zmian. Po skonfigurowaniu z informacjami o partycji docelowej i innych parametrów działa jako narzędzie po stronie klienta, które używa interfejsów API języka C# lub programu PowerShell do generowania błędów dla partycji usługi. Scenariusz wykonuje iterację przez sekwencję symulowanych błędów i walidacji usługi, podczas gdy logika biznesowa jest uruchamiana po stronie w celu zapewnienia obciążenia. Błąd weryfikacji usługi wskazuje na problem wymagający dalszych badań.

### <a name="faults-simulated-in-the-failover-test"></a>Błędy symulowane w teście trybu failover
* Uruchom ponownie wdrożony pakiet kodu, w którym jest hostowana partycja
* Usuń replikę podstawową/pomocniczą lub wystąpienie bezstanowe
* Uruchom ponownie podstawową replikę pomocniczą (jeśli trwała usługa)
* Przenoszenie repliki podstawowej
* Przenoszenie repliki pomocniczej
* Ponowne uruchamianie partycji

Test trybu failover polega na wybranym błędzie, a następnie uruchamia weryfikację w usłudze w celu zapewnienia jej stabilności. Test pracy awaryjnej wywołuje tylko jeden błąd w czasie, w przeciwieństwie do możliwej wielu błędów testu chaos. Jeśli partycja usługi nie jest stabilna w skonfigurowanym limicie czasu po każdym błędzie, test zakończy się niepowodzeniem. Test wywołuje tylko bezpieczne błędy. Oznacza to, że w przypadku braku zewnętrznych błędów kworum lub utracie danych nie nastąpi.

### <a name="important-configuration-options"></a>Ważne opcje konfiguracji
* **PartitionSelector**: obiekt selektora określający partycję, która musi być celem.
* **TimeToRun**: łączny czas, po upływie którego test zostanie uruchomiony przed zakończeniem.
* **MaxServiceStabilizationTimeout**: maksymalny czas oczekiwania, aż klaster przestanie działać w dobrej kondycji przed zakończeniem testu. Sprawdzenia wykonywane polegają na tym, czy kondycja usługi jest prawidłowa, docelowy rozmiar zestawu replik zostanie osiągnięty dla wszystkich partycji i nie istnieją repliki inbuild.
* **WaitTimeBetweenFaults**: ilość czasu oczekiwania między każdym cyklem błędu i walidacji.

### <a name="how-to-run-the-failover-test"></a>Jak uruchomić test pracy w trybie failover
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
