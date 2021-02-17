---
title: Symulowanie błędów w mikrousługach platformy Azure
description: W tym artykule omówiono akcje dotyczące testowania, które znajdują się w Microsoft Azure Service Fabric.
author: georgewallace
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: aea918f75ad9c8aa58b5201d2fdd967794085438
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547081"
---
# <a name="testability-actions"></a>Akcje dotyczące testowania
W celu zasymulowania niezawodnej infrastruktury usługa Azure Service Fabric zapewnia deweloperom sposoby symulowania różnych rzeczywistych awarii i przejść do stanu. Są one dostępne jako akcje testowania. Akcje są interfejsami API niskiego poziomu, które powodują konkretną iniekcję błędów, zmianę stanu lub weryfikację. Łącząc te akcje, można napisać kompleksowe scenariusze testów dla usług.

Service Fabric udostępnia niektóre typowe scenariusze testowe składające się z tych akcji. Zdecydowanie zalecamy korzystanie z tych wbudowanych scenariuszy, które są starannie wybierane w celu przetestowania typowych przejść między Stanami i przypadkami niepowodzeń. Jednak akcje mogą służyć do tworzenia niestandardowych scenariuszy testowych, gdy chcesz dodać pokrycie dla scenariuszy, które nie są jeszcze objęte wbudowanymi scenariuszami lub które są niestandardowe dostosowane do aplikacji.

Implementacje w języku C# akcji są dostępne w zestawie System.Fabric.dll. Moduł PowerShell programu System Fabric znajduje się w zestawie Microsoft.ServiceFabric.Powershell.dll. W ramach instalacji środowiska uruchomieniowego moduł servicefabric PowerShell jest instalowany, aby zapewnić łatwość użycia.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Bezpieczne i nieprolongaty akcje dotyczące błędów
Akcje dotyczące testowania są klasyfikowane do dwóch głównych zasobników:

* Błędne błędy: te błędy symulują błędy, takie jak ponowne uruchomienia komputera i awarie procesów. W takich przypadkach niepowodzenia kontekst wykonywania procesu przestanie nagle. Oznacza to, że nie można uruchomić oczyszczania stanu przed ponownym uruchomieniem aplikacji.
* Bezpieczne błędy: te błędy symulują bezpieczne akcje, takie jak replika przenoszona i spadnie wyzwalane przez funkcję równoważenia obciążenia. W takich przypadkach usługa otrzymuje powiadomienie o zamknięciu i może oczyścić stan przed zamknięciem.

Aby zapewnić lepszą weryfikację jakości, należy uruchomić usługę i obciążenie biznesowe przy jednoczesnym wykorzystaniu różnych błędów. Niebłędne scenariusze ćwiczeń, w których proces usługi nieoczekiwanie opuszcza się w środku pewnego przepływu pracy. Spowoduje to sprawdzenie ścieżki odzyskiwania po przywróceniu repliki usługi przez Service Fabric. Pomoże to w testowaniu spójności danych oraz o tym, czy stan usługi jest prawidłowo obsługiwany po błędach. Drugi zestaw błędów (łagodne błędy) test, że usługa prawidłowo reaguje na repliki, które są przenoszone przez Service Fabric. To testuje obsługę anulowania w metodzie RunAsync. Usługa musi sprawdzić, czy ustawiono token anulowania, prawidłowo zapisać jego stan i zamknąć metodę RunAsync.

## <a name="testability-actions-list"></a>Lista działań związanych z testowaniem
| Akcja | Opis | Zarządzany interfejs API | Polecenie cmdlet programu PowerShell | Błędy bezpiecznego/nieprolongaty |
| --- | --- | --- | --- | --- |
| CleanTestState |Usuwa wszystkie Stany testów z klastra w przypadku nieprawidłowego zamknięcia sterownika testowego. |CleanTestStateAsync |Remove-ServiceFabricTestState |Nie dotyczy |
| InvokeDataLoss |Wywołuje utratę danych w partycji usługi. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Zamknięcie |
| InvokeQuorumLoss |Umieszcza daną partycję usługi stanowej w utracie kworum. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Zamknięcie |
| Operację moveprimary |Przenosi określoną replikę podstawową usługi stanowej do określonego węzła klastra. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Zamknięcie |
| MoveSecondary |Przenosi bieżącą replikę pomocniczą usługi stanowej do innego węzła klastra. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Zamknięcie |
| RemoveReplica |Symuluje awarię repliki przez usunięcie repliki z klastra. Spowoduje to zamknięcie repliki i przeniesienie jej do roli "none" i usunięcie wszystkich jej stanu z klastra. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Zamknięcie |
| RestartDeployedCodePackage |Symuluje niepowodzenie procesu pakietu kodu przez ponowne uruchomienie pakietu kodu wdrożonego w węźle w klastrze. Powoduje to przerwanie procesu pakietu kodu, co spowoduje ponowne uruchomienie wszystkich replik usługi użytkownika hostowanych w tym procesie. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Nieprawidłowym |
| RestartNode |Symuluje awarię węzła klastra Service Fabric przez ponowne uruchomienie węzła. |RestartNodeAsync |Restart-ServiceFabricNode |Nieprawidłowym |
| RestartPartition |Symuluje niedostępność w centrum danych lub scenariusz niedostępności klastra przez ponowne uruchomienie niektórych lub wszystkich replik partycji. |RestartPartitionAsync |Restart-ServiceFabricPartition |Zamknięcie |
| RestartReplica |Symuluje awarię repliki, uruchamiając ponownie utrwaloną replikę w klastrze, zamykając replikę, a następnie otwierając ją ponownie. |RestartReplicaAsync |Restart-ServiceFabricReplica |Zamknięcie |
| Parametr StartNode |Uruchamia węzeł w klastrze, który został już zatrzymany. |StartNodeAsync |Start-ServiceFabricNode |Nie dotyczy |
| StopNode |Symuluje awarię węzła, zatrzymując węzeł w klastrze. Węzeł pozostanie niewyłączony do momentu wywołania parametr StartNode. |StopNodeAsync |Stop-ServiceFabricNode |Nieprawidłowym |
| ValidateApplication |Sprawdza dostępność i kondycję wszystkich usług Service Fabric w aplikacji, zwykle po wystąpieniu błędu w systemie. |ValidateApplicationAsync |Test-ServiceFabricApplication |Nie dotyczy |
| ValidateService |Sprawdza dostępność i kondycję usługi Service Fabric, zazwyczaj po wystąpieniu błędu w systemie. |ValidateServiceAsync |Test-ServiceFabricService |Nie dotyczy |

## <a name="running-a-testability-action-using-powershell"></a>Uruchamianie akcji testowania przy użyciu programu PowerShell
W tym samouczku pokazano, jak uruchomić akcję testowania przy użyciu programu PowerShell. Dowiesz się, jak uruchomić akcję testowania dla lokalnego klastra (z jedną ramką) lub klastra platformy Azure. Microsoft.Fabric.Powershell.dll — Service Fabric module programu PowerShell — jest instalowany automatycznie podczas instalacji programu Microsoft Service Fabric MSI. Moduł jest ładowany automatycznie po otwarciu wiersza polecenia programu PowerShell.

Segmenty samouczka:

* [Uruchamianie akcji w przypadku klastra z jedną ramką](#run-an-action-against-a-one-box-cluster)
* [Uruchamianie akcji względem klastra platformy Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Uruchamianie akcji w przypadku klastra z jedną ramką
Aby uruchomić akcję testowania względem klastra lokalnego, najpierw Nawiąż połączenie z klastrem i Otwórz wiersz polecenia programu PowerShell w trybie administratora. Poczekaj na wykonanie akcji **restart-ServiceFabricNode** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Tutaj Akcja **restart-ServiceFabricNode** jest uruchamiana w węźle o nazwie "Węzeł1". Tryb uzupełniania określa, że nie powinien sprawdzać, czy akcja ponownego uruchomienia węzła rzeczywiście zakończyła się pomyślnie. Określenie trybu uzupełniania jako "verify" spowoduje, że zostanie on zweryfikowany, czy akcja ponownego uruchamiania rzeczywiście zakończyła się pomyślnie. Zamiast bezpośrednio określać węzeł według jego nazwy, można go określić za pomocą klucza partycji i rodzaju repliki w następujący sposób:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

Aby ponownie uruchomić węzeł Service Fabric w klastrze, należy **ponownie uruchomić ServiceFabricNode** . Spowoduje to zatrzymanie procesu Fabric.exe, co spowoduje ponowne uruchomienie wszystkich replik usługi systemowej i usługi użytkownika hostowanych w tym węźle. Użycie tego interfejsu API do testowania usługi ułatwia odkrywanie usterek w ścieżkach odzyskiwania trybu failover. Ułatwia symulowanie awarii węzłów w klastrze.

Poniższy zrzut ekranu przedstawia polecenie **"Uruchom ponownie ServiceFabricNode"** w akcji.

![Zrzut ekranu przedstawiający uruchamianie polecenia Restart-ServiceFabricNode w programie PowerShell.](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Dane wyjściowe pierwszego polecenia **Get-ServiceFabricNode** (polecenie cmdlet z modułu Service Fabric PowerShell) pokazują, że klaster lokalny ma pięć węzłów: Node. 1 do Node. 5. Po wykonaniu akcji testowania (cmdlet) polecenie **restart-ServiceFabricNode** jest wykonywane w węźle o nazwie Node. 4. zobaczymy, że czas działania węzła został zresetowany.

### <a name="run-an-action-against-an-azure-cluster"></a>Uruchamianie akcji względem klastra platformy Azure
Uruchamianie akcji testowania (przy użyciu programu PowerShell) w odniesieniu do klastra platformy Azure jest podobne do uruchamiania akcji w klastrze lokalnym. Jedyną różnicą jest to, że przed uruchomieniem akcji zamiast łączenia się z lokalnym klastrem należy najpierw nawiązać połączenie z klastrem platformy Azure.

## <a name="running-a-testability-action-using-c35"></a>Uruchamianie akcji testowania przy użyciu&#35; C
Aby uruchomić akcję testowania przy użyciu języka C#, najpierw należy nawiązać połączenie z klastrem za pomocą FabricClient. Następnie uzyskaj parametry, które są konieczne do uruchomienia akcji. Do uruchamiania tej samej akcji można użyć różnych parametrów.
Oglądając akcję RestartServiceFabricNode, jednym ze sposobów uruchamiania jest użycie informacji o węźle (nazwa węzła i identyfikator wystąpienia węzła) w klastrze.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Wyjaśnienie parametru:

* **Completemode** określa, że tryb nie powinien sprawdzać, czy akcja ponownego uruchamiania rzeczywiście zakończyła się pomyślnie. Określenie trybu uzupełniania jako "verify" spowoduje, że zostanie on zweryfikowany, czy akcja ponownego uruchamiania rzeczywiście zakończyła się pomyślnie.  
* **OperationTimeout** ustawia czas zakończenia operacji przed wyrzucaniem wyjątku TimeoutException.
* **CancellationToken** umożliwia Anulowanie oczekującego wywołania.

Zamiast bezpośrednio określać węzeł według jego nazwy, można go określić za pomocą klucza partycji i rodzaju repliki.

Aby uzyskać więcej informacji, zobacz PartitionSelector i ReplicaSelector.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector i ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector to pomocnik narażony na testowanie i służy do wybrania konkretnej partycji, na której ma zostać wykonana jakakolwiek akcja testowania. Można go użyć do wybrania konkretnej partycji, jeśli wcześniej identyfikator partycji jest znany. Można też podać klucz partycji, a operacja spowoduje wewnętrzne rozwiązanie identyfikatora partycji. Dostępna jest również opcja wyboru partycji losowej.

Aby użyć tego pomocnika, należy utworzyć obiekt PartitionSelector i wybrać partycję za pomocą jednej z metod SELECT *. Następnie przekaż obiekt PartitionSelector do interfejsu API, który go wymaga. Jeśli opcja nie jest zaznaczona, domyślnie jest to partycja losowa.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector to pomocnik narażony na testowanie i służy do wybrania repliki, w której ma zostać wykonana jakakolwiek operacja testowania. Można go użyć do wybrania określonej repliki, jeśli wcześniej identyfikator repliki jest znany. Ponadto istnieje możliwość wybrania repliki podstawowej lub losowej pomocniczej. ReplicaSelector pochodzi z PartitionSelector, więc należy wybrać replikę i partycję, na której ma zostać wykonana operacja testowania.

Aby użyć tego pomocnika, Utwórz obiekt ReplicaSelector i Ustaw sposób, w jaki chcesz wybrać replikę i partycję. Następnie można przekazać go do interfejsu API, który go wymaga. Jeśli opcja nie jest zaznaczona, domyślnie jest to replika Losowa i partycja losowa.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Następne kroki
* [Scenariusze testowania](service-fabric-testability-scenarios.md)
* Testowanie usługi
  * [Symulowanie błędów podczas obciążeń usług](service-fabric-testability-workload-tests.md)
  * [Awarie komunikacji między usługami](service-fabric-testability-scenarios-service-communication.md)

