---
title: Planowanie i skalowanie pojemności dla Service Fabric platformy Azure
description: Najlepsze rozwiązania związane z planowaniem i skalowaniem Service Fabric klastrów i aplikacji.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: 41cfff11e44a3d052614aa3c81a4623f59bbbbf5
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095291"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Planowanie i skalowanie pojemności dla Service Fabric platformy Azure

Przed utworzeniem dowolnego klastra usługi Azure Service Fabric lub skalowaniem zasobów obliczeniowych, które obsługują klaster, ważne jest zaplanowanie pojemności. Aby uzyskać więcej informacji o planowaniu pojemności, zobacz [Planowanie pojemności klastra Service Fabric](./service-fabric-cluster-capacity.md). Aby uzyskać dalsze wskazówki dotyczące skalowalności klastra, zobacz [zagadnienia dotyczące skalowalności Service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Oprócz uwzględniania właściwości typu węzła i klastra należy oczekiwać skalowania operacji trwających dłużej niż godzinę dla środowiska produkcyjnego. Ta kwestia jest prawdziwa niezależnie od liczby dodawanych maszyn wirtualnych.

## <a name="autoscaling"></a>Skalowanie automatyczne
Należy wykonywać operacje skalowania za pomocą szablonów Azure Resource Manager, ponieważ najlepszym rozwiązaniem jest traktowanie [konfiguracji zasobów jako kodu](./service-fabric-best-practices-infrastructure-as-code.md). 

Używanie skalowania automatycznego za pośrednictwem zestawów skalowania maszyn wirtualnych sprawia, że wersja szablonu Menedżer zasobów w niewłaściwy sposób definiuje liczbę wystąpień dla zestawów skalowania maszyn wirtualnych. Niedokładne definicje zwiększają ryzyko, że przyszłe wdrożenia spowodują niezamierzone operacje skalowania. Ogólnie rzecz biorąc należy używać skalowania automatycznego, jeśli:

* Wdrażanie szablonów Menedżer zasobów z wykorzystaniem odpowiednich pojemności nie obsługuje Twojego przypadku użycia.
     
   Oprócz skalowania ręcznego można skonfigurować [potok ciągłej integracji i dostarczania w Azure DevOps Services przy użyciu projektów wdrażania grupy zasobów platformy Azure](../azure-resource-manager/templates/add-template-to-azure-pipelines.md). Ten potok jest często wyzwalany przez aplikację logiki, która używa metryk wydajności maszyny wirtualnej, które są wysyłane z [Azure monitor interfejsu API REST](../azure-monitor/platform/rest-api-walkthrough.md). Potok jest efektywnie skalowany automatycznie na podstawie dowolnych metryk, podczas optymalizacji pod kątem Menedżer zasobów szablonów.
* W danym momencie trzeba skalować w poziomie tylko jeden węzeł zestawu skalowania maszyn wirtualnych.
   
   Aby skalować w poziomie o trzy lub więcej węzłów jednocześnie, należy [skalować klaster Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych](virtual-machine-scale-set-scale-node-type-scale-out.md). Najbezpieczniejszym sposobem skalowania i skalowania zestawów skalowania maszyn wirtualnych w poziomie jest jeden węzeł w danym momencie.
* W przypadku klastra Service Fabric masz Silver lub wyższą niezawodność, a wersja Silver lub nowsza na dowolnej skali, w której można skonfigurować reguły skalowania automatycznego.
  
   Minimalna pojemność reguł skalowania automatycznego musi być równa lub większa niż pięć wystąpień maszyn wirtualnych. Musi ona również być równa lub większa od warstwy niezawodności minimalnej dla typu węzła podstawowego.

> [!NOTE]
> Service Fabric stanowa Usługa Service Fabric:/system/InfastructureService/<NODE_TYPE_NAME> działa na każdym typie węzła, którego trwałość to Silver lub wyższa. Jest to jedyna usługa systemowa, która jest obsługiwana na platformie Azure dla dowolnego typu węzła klastra.

> [!IMPORTANT]
> Service Fabric Skalowanie automatyczne obsługuje konfiguracje skalowania `Default` `NewestVM` maszyn [](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md)wirtualnych z zestawem skalowania.

## <a name="vertical-scaling-considerations"></a>Zagadnienia dotyczące skalowania w pionie

[Skalowanie w pionie](./virtual-machine-scale-set-scale-node-type-scale-out.md) typu węzła w usłudze Azure Service Fabric wymaga kilku kroków i kwestii. Przykład:

* Aby można było skalować klaster, należy go dobrać w dobrej kondycji. W przeciwnym razie bardziej stabilny jest klaster.
* Dla wszystkich typów węzłów klastra Service Fabric, które obsługują usługi stanowe, wymagany jest poziom trwałości Silver lub nowszy.

> [!NOTE]
> Podstawowy typ węzła, który hostuje stanowe Service Fabric usługi systemowe, musi mieć poziom trwałości Silver lub wyższy. Po włączeniu trwałości Silver operacje klastra, takie jak uaktualnienia, Dodawanie lub usuwanie węzłów, są wolniejsze, ponieważ system optymalizuje się pod kątem bezpieczeństwa danych przez szybkość operacji.

Skalowanie w pionie zestawu skalowania maszyn wirtualnych przez po prostu zmianę jego jednostki SKU zasobów jest operacją niszczącą, ponieważ ponownie tworzy obrazy na hostach w taki sposób, aby usunąć cały stan trwały lokalnie. Zamiast tego należy przeskalować klaster w poziomie, dodając nowy zestaw skalowania z odpowiednią jednostką SKU, a następnie Przeprowadź migrację usług do nowego zestawu skalowania, aby ukończyć bezpieczną operację skalowania w pionie.

Klaster używa [właściwości węzła Service Fabric i ograniczeń umieszczania](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) , aby zdecydować, gdzie hostować usługi aplikacji. W przypadku skalowania w pionie typu węzła podstawowego należy wdrożyć drugi typ węzła podstawowego, a następnie ustawić ( `"isPrimary": false` ) dla oryginalnego typu węzła podstawowego i wyłączyć jego węzły oraz usunąć jego zestaw skalowania i powiązane z nim zasoby. Aby uzyskać szczegółowe informacje, zobacz [skalowanie w górę do podstawowego typu węzła klastra Service Fabric](service-fabric-scale-up-primary-node-type.md).

> [!NOTE]
> Zawsze Weryfikuj operacje w środowiskach testowych przed podjęciem zmian w środowisku produkcyjnym. Domyślnie usługi systemu Service Fabric klastra mają ograniczenie umieszczania tylko dla docelowego typu węzła podstawowego.

Po zadeklarowaniu właściwości węzła i ograniczeń położenia wykonaj następujące kroki w jednym wystąpieniu maszyny wirtualnej naraz. Dzięki temu usługi systemowe (i usługi stanowe) mogą być bezpiecznie zamykane w wystąpieniu maszyny wirtualnej, która jest usuwana w innym miejscu.

1. W programie PowerShell uruchom polecenie `Disable-ServiceFabricNode` with, `RemoveNode` Aby wyłączyć węzeł, który ma zostać usunięty. Usuń typ węzła o największej liczbie. Na przykład jeśli masz klaster z sześcioma węzłami, Usuń wystąpienie maszyny wirtualnej "MyNodeType_5".
2. Uruchom `Get-ServiceFabricNode` , aby upewnić się, że węzeł został przeniesiony do wyłączenia. Jeśli nie, zaczekaj, aż węzeł zostanie wyłączony. Może to potrwać kilka godzin dla każdego węzła. Nie należy przechodzić do czasu, aż węzeł zostanie przeniesiony do wyłączenia.
3. Zmniejsz liczbę maszyn wirtualnych przez jedną w tym typie węzła. Największe wystąpienie maszyny wirtualnej zostanie teraz usunięte.
4. Powtórz kroki od 1 do 3, jeśli jest to potrzebne, ale nigdy nie Skaluj w liczbę wystąpień w typach węzłów głównych poniżej, co gwarantuje warstwa niezawodności. Zapoznaj się z tematem [Planowanie pojemności klastra Service Fabric](./service-fabric-cluster-capacity.md) , aby zapoznać się z listą zalecanych wystąpień.
5. Gdy wszystkie maszyny wirtualne zostaną usunięte (reprezentowane jako "w dół"), w obszarze Sieć szkieletowa:/system/InfrastructureService/[nazwa węzła] zostanie wyświetlony stan błędu. Następnie można zaktualizować zasób klastra, aby usunąć typ węzła. Możesz użyć wdrożenia szablonu ARM lub edytować zasób klastra za pomocą [usługi Azure Resource Manager](https://resources.azure.com). Spowoduje to uruchomienie uaktualnienia klastra, co spowoduje usunięcie usługi sieci szkieletowej:/system/InfrastructureService/[Node Type], która jest w stanie błędu.
 6. Po wybraniu opcjonalnego usunięcia VMScaleSet nadal będą wyświetlane węzły jako "w dół" w widoku Service Fabric Explorer. Ostatnim krokiem jest oczyszczenie ich przy użyciu `Remove-ServiceFabricNodeState` polecenia.

## <a name="horizontal-scaling"></a>Skalowanie w poziomie

Skalowanie w poziomie można przeprowadzić [ręcznie](./service-fabric-cluster-scale-in-out.md) lub [programowo](./service-fabric-cluster-programmatic-scaling.md).

> [!NOTE]
> W przypadku skalowania typu węzła, który ma trwałość Silver lub Gold, skalowanie będzie powolne.

### <a name="scaling-out"></a>Skalowanie w poziomie

Skalowanie klastra Service Fabric przez zwiększenie liczby wystąpień określonego zestawu skalowania maszyn wirtualnych. Można programowo skalować w poziomie przy użyciu `AzureClient` i identyfikatora żądanego zestawu skalowania w celu zwiększenia pojemności.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Aby ręcznie skalować w poziomie, zaktualizuj pojemność we właściwości SKU żądanego zasobu [zestawu skalowania maszyn wirtualnych](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) .

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Skalowanie w

Skalowanie w górę wymaga większego nacisku niż skalowanie w górę. Na przykład:

* Usługi systemowe Service Fabric uruchamiane w podstawowym typie węzła w klastrze. Nigdy nie zamykaj ani nie skaluje liczby wystąpień tego typu węzła, aby mieć mniejszą liczbę wystąpień niż to, co gwarantuje warstwa niezawodności. 
* W przypadku usługi stanowej potrzebna jest pewna liczba węzłów, które są zawsze do utrzymania dostępności i zachowania stanu usługi. Wymagana jest co najmniej liczba węzłów równa liczbie docelowych zestawu replik partycji lub usługi.

Aby ręcznie skalować, wykonaj następujące kroki:

1. W programie PowerShell uruchom polecenie `Disable-ServiceFabricNode` with, `RemoveNode` Aby wyłączyć węzeł, który ma zostać usunięty. Usuń typ węzła o największej liczbie. Na przykład jeśli masz klaster z sześcioma węzłami, Usuń wystąpienie maszyny wirtualnej "MyNodeType_5".
2. Uruchom `Get-ServiceFabricNode` , aby upewnić się, że węzeł został przeniesiony do wyłączenia. Jeśli nie, zaczekaj, aż węzeł zostanie wyłączony. Może to potrwać kilka godzin dla każdego węzła. Nie należy przechodzić do czasu, aż węzeł zostanie przeniesiony do wyłączenia.
3. Zmniejsz liczbę maszyn wirtualnych przez jedną w tym typie węzła. Największe wystąpienie maszyny wirtualnej zostanie teraz usunięte.
4. Powtórz kroki od 1 do 3, dopóki nie zostanie zainicjowana wymagana pojemność. Nie stosuj skalowania do liczby wystąpień w typach węzła podstawowego do mniej niż to, co gwarantuje warstwa niezawodności. Zapoznaj się z tematem [Planowanie pojemności klastra Service Fabric](./service-fabric-cluster-capacity.md) , aby zapoznać się z listą zalecanych wystąpień.

Aby ręcznie skalować, zaktualizuj pojemność we właściwości SKU żądanego zasobu [zestawu skalowania maszyn wirtualnych](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) .

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Aby można było programowo skalować w poziomie, należy przygotować węzeł do zamknięcia. Znajdź węzeł, który ma zostać usunięty (węzeł najwyższego wystąpienia). Przykład:

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

Dezaktywuj i Usuń węzeł przy użyciu tego samego `FabricClient` wystąpienia ( `client` w tym przypadku) i wystąpienia węzła ( `instanceIdString` w tym przypadku), które zostało użyte w poprzednim kodzie:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> W przypadku skalowania w klastrze zobaczysz usunięte wystąpienie węzła/maszyny wirtualnej wyświetlone w złej kondycji w Service Fabric Explorer. Aby uzyskać wyjaśnienie tego zachowania, zobacz [zachowania, które można obserwować w Service Fabric Explorer](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer). Można:
> * Wywołaj [polecenie Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps&preserve-view=true) z odpowiednią nazwą węzła.
> * Wdróż [aplikację pomocnika automatycznego skalowania Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) w klastrze. Ta aplikacja zapewnia, że węzły skalowane w dół są wyczyszczone z Service Fabric Explorer.

## <a name="reliability-levels"></a>Poziomy niezawodności

[Poziom niezawodności](./service-fabric-cluster-capacity.md) jest właściwością zasobu klastra Service Fabric. Nie można go skonfigurować inaczej dla poszczególnych typów węzłów. Kontroluje współczynnik replikacji usług systemowych dla klastra i jest ustawieniem na poziomie zasobów klastra. 

Poziom niezawodności określi minimalną liczbę węzłów, które musi mieć typ węzła podstawowego. Warstwa niezawodności może przyjmować następujące wartości:

* Pakiet Platinum: uruchamia usługi systemowe z docelową liczbą zestawu replik siedmiu i dziewięciu węzłów inicjatora.
* Złoty: uruchamia usługi systemowe z docelową liczbą zestawów replik siedmiu i siedmiu węzłów inicjatora.
* Silver: uruchamia usługi systemowe z docelową liczbą zestawów replik 5 i 5 węzłów inicjatora.
* Brąz: uruchamia usługi systemowe z docelową liczbą zestawów replik trzech i trzech węzłów inicjatora.

Minimalny zalecany poziom niezawodności to Silver.

Poziom niezawodności jest ustawiany w sekcji właściwości [zasobu Microsoft. servicefabric/klastrów](/azure/templates/microsoft.servicefabric/2018-02-01/clusters), w tym:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Poziomy trwałości

> [!WARNING]
> Typy węzłów działające z trwałością Bronze nie uzyskują _żadnych uprawnień_. Zadania infrastruktury mające wpływ na obciążenia bezstanowe nie zostaną zatrzymane ani opóźnione, co może wpłynąć na obciążenia. 
>
> Użyj trwałości Bronze tylko dla typów węzłów, które uruchamiają obciążenia bezstanowe. W przypadku obciążeń produkcyjnych należy uruchomić Silver lub nowszy, aby zapewnić spójność stanu. Wybierz odpowiednią niezawodność w oparciu o wskazówki zawarte w dokumentacji dotyczącej [planowania pojemności](./service-fabric-cluster-capacity.md).

Poziom trwałości musi być ustawiony w dwóch zasobach. Jeden to profil rozszerzenia [zasobu zestawu skalowania maszyn wirtualnych](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Inny zasób znajduje się `nodeTypes` w obszarze [zasobów Microsoft. servicefabric/klastrów](/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md).
* Dowiedz się więcej o [opcjach pomocy technicznej Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png