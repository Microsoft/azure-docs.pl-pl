---
title: Symulowanie błędów w aplikacjach Service Fabric platformy Azure
description: Dowiedz się więcej na temat zabezpieczania usług Service Fabric platformy Azure przed błędami i niepowodzeniem.
ms.topic: conceptual
ms.date: 06/15/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: c714ae30c64ea073cbac521eac5e15a8d968b7ea
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531314"
---
# <a name="simulate-failures-during-service-workloads"></a>Symulowanie błędów podczas obciążeń usług
Scenariusze testowania na platformie Azure Service Fabric umożliwiają deweloperom nie martwić się o poszczególnymi usterkami. Istnieją jednak scenariusze, w których może być konieczne jawne odchodzenie między obciążeniem klienta i niepowodzeń. Odłączanie obciążenia i błędów klienta zapewnia, że usługa rzeczywiście wykonuje pewne działania w przypadku wystąpienia awarii. Uwzględniając poziom kontroli, który zapewnia możliwości testowania, mogą one być precyzyjnymi punktami wykonywania obciążenia. Takie zapełnienie błędów w różnych stanach aplikacji może znaleźć błędy i poprawić jakość.

## <a name="sample-custom-scenario"></a>Przykładowy scenariusz niestandardowy
Ten test przedstawia scenariusz, który przeplotuje obciążenie biznesowe z użyciem [łagodnych i niepowodzeń](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). Błędy powinny być wywołane w trakcie operacji usługi lub obliczeń w celu uzyskania najlepszych wyników.

Zapoznaj się z przykładem usługi, która udostępnia cztery obciążenia: A, B, C i D. Każdy odnosi się do zestawu przepływów pracy i może być obliczeniowy, magazynem lub mieszanym. Ze względu na prostotę rozwiążemy obciążenia w naszym przykładzie. Różne błędy wykonywane w tym przykładzie są następujące:

* RestartNode: błąd nieprolongaty dotyczący symulowania ponownego uruchomienia komputera.
* RestartDeployedCodePackage: błąd nieprolongaty dotyczący symulowania awarii procesu hosta usługi.
* RemoveReplica: łagodny błąd w celu symulowania usuwania repliki.
* Operację moveprimary: łagodne błędy symulowania przenoszenia replik wyzwalane przez Service Fabric moduł równoważenia obciążenia.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.TestManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.FaultManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.FaultManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.FaultManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.FaultManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
