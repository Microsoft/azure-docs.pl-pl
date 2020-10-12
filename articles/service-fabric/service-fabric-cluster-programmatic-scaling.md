---
title: Skalowanie programistyczne Service Fabric platformy Azure
description: Programowe skalowanie klastra Service Fabric platformy Azure w sposób programistyczny i wychodzący zgodnie z wyzwalaczami niestandardowymi
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: bd47e5e39684bd4b684cd1e12dd9a3d420640ee2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89005823"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Programowe skalowanie klastra Service Fabricowego 

Klastry Service Fabric działające na platformie Azure są oparte na zestawach skalowania maszyn wirtualnych.  [Skalowanie klastra](./service-fabric-cluster-scale-in-out.md) opisuje, jak klastry Service Fabric mogą być skalowane ręcznie lub przy użyciu reguł skalowania automatycznego. W tym artykule opisano sposób zarządzania poświadczeniami i skalowania klastra do lub z użyciem usługi Azure COMPUTE SDK, która jest bardziej zaawansowanym scenariuszem. Aby zapoznać się z omówieniem, zapoznaj się [z programowanymi metodami koordynacji operacji skalowania platformy Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Zarządzanie poświadczeniami
Jednym z wyzwań zapisywania usługi do obsługi skalowania jest to, że usługa musi mieć dostęp do zasobów zestawu skalowania maszyn wirtualnych bez interakcyjnego logowania. Uzyskiwanie dostępu do klastra Service Fabric jest proste, jeśli usługa skalowania modyfikuje własną aplikację Service Fabric, ale do uzyskania dostępu do zestawu skalowania są potrzebne poświadczenia. Aby się zalogować, można użyć jednostki [usługi](/cli/azure/create-an-azure-service-principal-azure-cli) utworzonej przy użyciu [interfejsu wiersza polecenia platformy Azure](https://github.com/azure/azure-cli).

Jednostkę usługi można utworzyć przy użyciu następujących kroków:

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure ( `az login` ) jako użytkownik z dostępem do zestawu skalowania maszyn wirtualnych
2. Tworzenie jednostki usługi za pomocą `az ad sp create-for-rbac`
    1. Zanotuj identyfikator appId (nazywany "IDENTYFIKATORem klienta" w innym miejscu), nazwę, hasło i dzierżawcę do późniejszego użycia.
    2. Wymagany jest również Identyfikator subskrypcji, który można wyświetlić za pomocą `az account list`

Biblioteka obliczeń Fluent może zalogować się przy użyciu tych poświadczeń w następujący sposób (należy zauważyć, że podstawowe typy Fluent platformy Azure, takie jak `IAzure` znajdują się w pakiecie [Microsoft. Azure. Management. Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) ):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Po zalogowaniu licznik wystąpienia zestawu skalowania może być badany za pomocą `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` .

## <a name="scaling-out"></a>Skalowanie w poziomie
Korzystając z zestawu Azure COMPUTE SDK, wystąpienia można dodać do zestawu skalowania maszyn wirtualnych z zaledwie kilkoma wywołaniami —

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternatywnie można także zarządzać rozmiarem zestawu skalowania maszyn wirtualnych za pomocą poleceń cmdlet programu PowerShell. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) może pobrać obiekt zestawu skalowania maszyn wirtualnych. Bieżąca pojemność jest dostępna za pomocą `.sku.capacity` właściwości. Po zmianie pojemności na żądaną wartość zestaw skalowania maszyn wirtualnych na platformie Azure można zaktualizować za pomocą [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) polecenia.

Podobnie jak podczas ręcznego dodawania węzła dodanie wystąpienia zestawu skalowania powinno być wszystkie konieczne do uruchomienia nowego węzła Service Fabric, ponieważ szablon zestawu skalowania zawiera rozszerzenia umożliwiające automatyczne dołączanie nowych wystąpień do klastra Service Fabric. 

## <a name="scaling-in"></a>Skalowanie w

Skalowanie w programie jest podobne do skalowania w górę. Rzeczywiste zmiany zestawu skalowania maszyn wirtualnych są praktycznie takie same. Jednak jak zostało wcześniej omówione, Service Fabric automatycznie czyści usunięte węzły z trwałością Gold lub Silver. Tak więc w przypadku skalowania w trybie Bronze — w razie potrzeby należy wykonać działania z klastrem Service Fabric, aby zamknąć węzeł, który ma zostać usunięty, a następnie usunąć jego stan.

Przygotowanie węzła do zamknięcia obejmuje znalezienie węzła do usunięcia (ostatnio dodane wystąpienie zestawu skalowania maszyn wirtualnych) i jego dezaktywowanie. Wystąpienia zestawu skalowania maszyn wirtualnych są numerowane w kolejności, w jakiej zostały dodane, więc można znaleźć nowsze węzły, porównując sufiks liczby w nazwach węzłów (które pasują do nazw wystąpień zestawu skalowania maszyn wirtualnych). 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Po znalezieniu węzła zostanie on zdezaktywowany i usunięty przy użyciu tego samego `FabricClient` wystąpienia i `IAzure` wystąpienia ze starszych wersji.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Podobnie jak w przypadku skalowania w poziomie, polecenia cmdlet programu PowerShell służące do modyfikowania pojemności zestawu skalowania maszyn wirtualnych można także użyć w tym miejscu, jeśli podejście do obsługi skryptów jest preferowane. Po usunięciu wystąpienia maszyny wirtualnej można usunąć stan węzła Service Fabric.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć implementowanie własnej logiki automatycznego skalowania, zapoznaj się z następującymi pojęciami i przydatnymi interfejsami API:

- [Skalowanie ręczne lub przy użyciu reguł skalowania automatycznego](./service-fabric-cluster-scale-in-out.md)
- [Biblioteki zarządzania systemu Azure Fluent dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (przydatne do współdziałania z podstawowymi zestawami skalowania maszyn wirtualnych w klastrze Service Fabric)
- [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient) (przydatny do współpracy z klastrem Service Fabric i jego węzłami)
