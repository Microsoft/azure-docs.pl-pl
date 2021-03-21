---
title: Wywoływanie chaos w klastrach Service Fabric
description: Używanie iniekcji błędów i interfejsów API usługi analizy klastrów do zarządzania chaos w klastrze.
ms.topic: conceptual
ms.date: 02/05/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 72b8f7e9e4934b516f843ae8bc9bb7adc1c349ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720514"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Chaos kontrolowane w klastrach Service Fabric
Systemy rozproszone o dużej skali, takie jak infrastruktury chmury, są z natury zawodowe. Dzięki platformie Azure Service Fabric deweloperzy mogą pisać niezawodne usługi rozproszone w oparciu o niezawodną infrastrukturę. Aby pisać niezawodne usługi rozproszone w oparciu o niezawodną infrastrukturę, deweloperzy muszą mieć możliwość przetestowania stabilności usług, podczas gdy podstawowa zawodna infrastruktura przechodzi przez skomplikowane przejścia stanu z powodu błędów.

[Iniekcja błędów i usługa analizy klastrów](./service-fabric-testability-overview.md) (znana również jako usługa błędu analizy) oferuje deweloperom możliwość wywołania błędów w celu przetestowania ich usług. Te wystąpienia symulowanych błędów, takich jak [Ponowne uruchamianie partycji](/powershell/module/servicefabric/start-servicefabricpartitionrestart), mogą pomóc w wykonywaniu najczęstszych przejść między Stanami. Jednak ukierunkowane błędy symulowane są rozdzielone przez definicję i w związku z tym mogą pominąć usterki, które są wyświetlane tylko w trudnej do przewidywania, długotrwałej i skomplikowanej sekwencji przejść stanu. W przypadku testowania nieobciążonego można użyć chaos.

Chaos symuluje okresowe, przeplatane błędy (zarówno bezpieczne, jak i nieprolongaty) w całym klastrze przez dłuższy czas. Płynna awaria składa się z zestawu Service Fabric wywołań interfejsu API, na przykład ponowne uruchomienie błędu repliki jest bezpieczne, ponieważ jest to zamknięcie, a następnie otwarty w replice. Usuń replikę, Przenieś replikę podstawową i Przenieś replikę pomocniczą na inne bezpieczne błędy wykonywane przez chaos. Błędne błędy to wyjścia procesów, takie jak ponowne uruchomienie węzła i ponowne uruchomienie pakietu kodu. 

Po skonfigurowaniu usługi chaos przy użyciu stawki i rodzaju błędów można uruchomić chaos za pomocą języka C#, programu PowerShell lub interfejsu API REST, aby rozpocząć generowanie błędów w klastrze i w usługach. Można skonfigurować chaos do uruchamiania przez określony czas (na przykład przez jedną godzinę), po którym chaos zostaje zatrzymana automatycznie, lub wywołać StopChaos API (C#, PowerShell lub REST), aby zatrzymać go w dowolnym momencie.

> [!NOTE]
> W bieżącej postaci chaos wywołuje tylko bezpieczne błędy, co oznacza, że w przypadku braku zewnętrznych błędów utraconych kworum lub utraty danych.
>

Podczas gdy Chaos jest uruchomiony, generuje inne zdarzenia, które w tej chwili przechwytują stan przebiegu. Na przykład ExecutingFaultsEvent zawiera wszystkie błędy, które chaos zdecydował się wykonać w tej iteracji. ValidationFailedEvent zawiera szczegóły błędu walidacji (problemy z kondycją lub stabilnością), które zostały znalezione podczas weryfikacji klastra. Można wywołać interfejs API raporcie getchaosreport (C#, PowerShell lub REST), aby uzyskać raport o uruchomieniach chaos. Te zdarzenia są utrwalane w [niezawodnym słowniku](./service-fabric-reliable-services-reliable-collections.md), który ma zasady obcinania podyktowane dwiema konfiguracjami: **MaxStoredChaosEventCount** (wartość domyślna to 25000) i **StoredActionCleanupIntervalInSeconds** (wartość domyślna to 3600). Każdy *StoredActionCleanupIntervalInSeconds* chaos sprawdza i wszystkie ostatnie zdarzenia *MaxStoredChaosEventCount* są czyszczone ze niezawodnego słownika.

## <a name="faults-induced-in-chaos"></a>Błędy wywołane w chaos
Chaos generuje błędy w całym klastrze Service Fabric i kompresuje błędy, które pojawiły się w ciągu kilku miesięcy lub lat, na kilka godzin. Kombinacja błędów z przeplotem o wysokim współczynniku błędów powoduje znalezienie przypadków narożnych, które mogą być pominięte. To ćwiczenie chaos prowadzi do znacznej poprawy jakości kodu usługi.

Chaos wywołuje błędy z następujących kategorii:

* Ponowne uruchamianie węzła
* Ponowne uruchamianie wdrożonego pakietu kodu
* Usuń replikę
* Uruchom ponownie replikę
* Przenieś replikę podstawową (konfigurowalną)
* Przenieś replikę pomocniczą (konfigurowalną)

Chaos działa w wielu iteracjach. Każda iteracja składa się z błędów i weryfikacji klastra w określonym przedziale czasu. Istnieje możliwość skonfigurowania czasu poświęcanego na stabilizację klastra i sprawdzenia poprawności. W przypadku znalezienia błędu w weryfikacji klastra chaos generuje i utrzymuje ValidationFailedEvent z sygnaturą czasową UTC oraz ze szczegółami błędu. Rozważmy na przykład wystąpienie chaos, które jest skonfigurowane do uruchamiania przez godzinę i maksymalnie trzy współbieżne błędy. Chaos wywołuje trzy usterki, a następnie sprawdza kondycję klastra. Wykonuje iterację w poprzednim kroku, dopóki nie zostanie on jawnie zatrzymany za pomocą interfejsu API StopChaosAsync lub jednogodzinnych przebiegów. Jeśli klaster ulegnie złej kondycji w dowolnej iteracji (to oznacza, że nie jest ustabilizowany lub nie stanie się w dobrej kondycji w ramach przekazaną MaxClusterStabilizationTimeout), chaos generuje ValidationFailedEvent. To zdarzenie wskazuje, że coś pozostało niewłaociwe i może wymagać dalszych badań.

Aby uzyskać chaos błędy, można użyć interfejsu API raporcie getchaosreport (PowerShell, C# lub REST). Interfejs API pobiera następny segment raportu chaos na podstawie pożądanego tokenu kontynuacji lub przenoszonego zakresu czasu. Można określić ContinuationToken, aby uzyskać następny segment raportu chaos lub określić zakres czasu za pomocą StartTimeUtc i EndTimeUtc, ale nie można określić zarówno ContinuationToken, jak i zakresu czasu w tym samym wywołaniu. Jeśli istnieje więcej niż 100 zdarzeń chaos, raport Chaos jest zwracany w segmentach, w których segment nie zawiera więcej niż 100 zdarzeń chaos.

## <a name="important-configuration-options"></a>Ważne opcje konfiguracji
* **TimeToRun**: łączny czas działania chaos przed zakończeniem z sukcesem. Można zatrzymać chaos przed uruchomieniem okresu TimeToRun za pomocą interfejsu API StopChaos.

* **MaxClusterStabilizationTimeout**: maksymalny czas oczekiwania, aż klaster stanie się w dobrej kondycji przed rozpoczęciem tworzenia ValidationFailedEvent. Jest to oczekiwane zmniejszenie obciążenia klastra podczas odzyskiwania. Wykonane sprawdzenia:
  * Jeśli kondycja klastra jest OK
  * Jeśli kondycja usługi jest prawidłowa
  * Jeśli docelowy rozmiar zestawu replik zostanie osiągnięty dla partycji usługi
  * Nie istnieją repliki inbuild
* **MaxConcurrentFaults**: Maksymalna liczba równoczesnych błędów, które zostały wywołane w każdej iteracji. Im większa liczba, tym bardziej agresywna chaos, a także przejścia w tryb failover i zmiany stanu, do których prowadzi klaster, są również bardziej skomplikowane. 

> [!NOTE]
> Bez względu na to, jak wysoka wartość *MaxConcurrentFaults* , chaos gwarancje — brak zewnętrznych błędów — nie ma utraty kworum ani utraty danych.
>

* **EnableMoveReplicaFaults**: włącza lub wyłącza błędy, które powodują przeniesienie repliki podstawowej lub pomocniczej. Te błędy są domyślnie włączone.
* **WaitTimeBetweenIterations**: ilość czasu oczekiwania między iteracjami. Oznacza to, że czas chaos będzie wstrzymywany po wykonaniu rundy błędów i zakończeniu odpowiedniej weryfikacji kondycji klastra. Im większa wartość, tym niższa jest wskaźnik średniego iniekcji błędów.
* **WaitTimeBetweenFaults**: ilość czasu oczekiwania między dwoma kolejnymi błędami w jednej iteracji. Im wyższa wartość, tym mniejsza liczba błędów współbieżności (lub nakładania się między).
* **ClusterHealthPolicy**: zasady kondycji klastra są używane do weryfikacji kondycji klastra między iteracjami chaos. Jeśli kondycja klastra wystąpi błąd lub jeśli wystąpił nieoczekiwany wyjątek podczas wykonywania, chaos będzie oczekiwać przez 30 minut przed kolejną kondycją — Sprawdź, czy klaster ma jakiś czas na recuperate.
* **Context**: kolekcja par klucz-wartość typu (String, String). Mapa może służyć do rejestrowania informacji o przebiegu chaos. Nie może być więcej niż 100 takich par, a każdy ciąg (klucz lub wartość) może mieć maksymalnie 4095 znaków. Ta mapa jest ustawiana przez Starter chaos Run, aby opcjonalnie przechowywać kontekst o określonym przebiegu.
* **ChaosTargetFilter**: ten filtr może służyć do kierowania błędów chaos tylko do określonych typów węzłów lub tylko do określonych wystąpień aplikacji. Jeśli ChaosTargetFilter nie jest używany, chaos wszystkie jednostki klastra. Jeśli ChaosTargetFilter jest używany, chaos błędy tylko dla jednostek, które spełniają wymagania specyfikacji ChaosTargetFilter. NodeTypeInclusionList i ApplicationInclusionList zezwalają tylko na semantykę Unii. Innymi słowy, nie można określić przedziału NodeTypeInclusionList i ApplicationInclusionList. Na przykład nie jest możliwe określenie "błąd tej aplikacji tylko wtedy, gdy znajduje się ona w danym typie węzła". Gdy jednostka jest uwzględniona w NodeTypeInclusionList lub ApplicationInclusionList, ta jednostka nie może zostać wykluczona przy użyciu ChaosTargetFilter. Nawet jeśli applicationX nie pojawia się w ApplicationInclusionList, w niektórych chaos iteracji applicationX może być uszkodzona, ponieważ występuje w węźle nodeTypeY, który jest zawarty w NodeTypeInclusionList. Jeśli zarówno NodeTypeInclusionList, jak i ApplicationInclusionList mają wartość null lub są puste, zostanie zgłoszony ArgumentException.
    * **NodeTypeInclusionList**: Lista typów węzłów do uwzględnienia w błędach chaos. Wszystkie typy błędów (węzeł ponownego uruchomienia, uruchom ponownie codepackage, Usuń replikę, uruchom replikę, Przenieś podstawowe i Przenieś pomocnicze) są włączone dla węzłów tych typów węzłów. Jeśli NodeType (Powiedz NodeTypeX) nie występuje w NodeTypeInclusionList, wówczas błędy na poziomie węzła (na przykład NodeRestart) nie będą nigdy włączane dla węzłów NodeTypeX, ale pakiet kodu i błędy replik nadal mogą być włączone dla NodeTypeX, jeśli aplikacja w ApplicationInclusionList stanie się znajdować w węźle NodeTypeX. Na tej liście można umieścić maksymalnie 100 nazw typów węzłów, aby zwiększyć tę liczbę, do konfiguracji MaxNumberOfNodeTypesInChaosTargetFilter jest wymagane uaktualnienie konfiguracji.
    * **ApplicationInclusionList**: Lista identyfikatorów URI aplikacji do uwzględnienia w błędach chaos. Wszystkie repliki należące do usług tych aplikacji są gotowe do błędów replik (ponownie uruchom replikę, Usuń replikę, Przenieś podstawową i Przenieś pomocniczą) przez chaos. Chaos może ponownie uruchomić pakiet kodu tylko wtedy, gdy pakiet kodu hostuje repliki tych aplikacji. Jeśli aplikacja nie jest wyświetlana na tej liście, nadal może być uszkodzona w pewnej iteracji chaos, jeśli aplikacja zostanie zakończona w węźle typu węzła, który jest zawarty w NodeTypeInclusionList. Jeśli jednak applicationX jest powiązany z nodeTypeY za pośrednictwem ograniczeń umieszczania, a applicationX nie jest obecny w ApplicationInclusionList i nodeTypeY nie jest w NodeTypeInclusionList, wówczas applicationX nie będzie nigdy występować. Na tej liście można umieścić maksymalnie 1000 nazw aplikacji, aby zwiększyć tę liczbę, do konfiguracji MaxNumberOfApplicationsInChaosTargetFilter jest wymagane uaktualnienie konfiguracji.

## <a name="how-to-run-chaos"></a>Jak uruchomić chaos

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
