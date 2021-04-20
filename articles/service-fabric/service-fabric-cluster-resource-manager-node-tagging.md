---
title: Tagi Service Fabric azure Service Fabric węzłów dynamicznych
description: Usługa Azure Service Fabric umożliwia dynamiczne dodawanie i usuwanie tagów węzłów.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: 712e6422060619e5567a74d6335320eff9ed8e66
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741928"
---
# <a name="introduction-to-dynamic-node-tags"></a>Wprowadzenie do dynamicznych tagów węzłów
Tagi węzłów umożliwiają dynamiczne dodawanie i usuwanie tagów z węzłów w celu wywierania wpływu na umieszczanie usług. Tagowanie węzłów jest bardzo elastyczne i umożliwia zmiany rozmieszczenia usług bez uaktualnień aplikacji lub klastra. Tagi można dodawać lub usuwać z węzłów w dowolnym momencie, a usługi mogą określać wymagania dotyczące niektórych tagów podczas ich tworzenia. Wymagania dotyczące tagów usługi mogą być aktualizowane dynamicznie, gdy jest uruchomiona.

Tagowanie węzłów jest podobne do [ograniczeń umieszczania](service-fabric-cluster-resource-manager-configure-services.md) i jest zwykle używane do kontrolowania węzłów, w których jest uruchamiana usługa. Każdą Service Fabric można skonfigurować tak, aby wymagała umieszczenia tagu lub utrzymania działania.

Tagowanie węzłów jest obsługiwane dla Service Fabric hostowanych typów usług (Reliable Services, plików wykonywalnych gościa i kontenerów). Aby używać tagowania węzłów, musisz mieć uruchomiony program w wersji 8.0 lub Service Fabric uruchomieniowej.

W pozostałej części tego artykułu opisano sposoby włączania lub wyłączania tagowania węzłów oraz przedstawiono przykłady użycia tej funkcji.


## <a name="describing-dynamic-node-tags"></a>Opisywanie dynamicznych tagów węzłów
Usługi mogą określać wymagane tagi. Istnieją dwa typy tagów:
* **Tagi wymagane do umieszczania** opisują zestaw tagów, które są wymagane tylko w przypadku umieszczania usługi. Po umieszczeniu repliki te tagi można usunąć bez przerywania działania usługi. Jeśli którykolwiek z tych tagów zostanie usunięty z węzła, replika usługi będzie nadal działać i Service Fabric nie usunie usługi

* **Tagi wymagane do uruchomienia** opisują zestaw tagów, które są wymagane zarówno do umieszczania, jak i uruchamiania usługi. Jeśli dowolny z wymaganych uruchomionych tagów zostanie usunięty, Service Fabric usługę do innego węzła, który ma te tagi określone.

Przykład: Wymagane dla tagów umieszczania mogą być używane, gdy używasz jakiegoś rodzaju usługi aktywatora kontenera i potrzebujesz tej usługi do umieszczenia kontenera, a po aktywowaniu kontenera nie potrzebujesz już aktywatora i możesz usunąć skojarzony z nim tag, ale kontener powinien być nadal uruchomiony.
Wymagane do uruchamiania tagów mogą być używane, gdy masz usługę rozliczeniową, która jest przydatna do współpracy z usługą dostępną dla użytkowników. Gdy usługa rozliczeń ulegnie awarii w węźle, usuniesz skojarzony z nią tag, a usługa skierowana do użytkownika zostanie przeniesiona do innego węzła z usługą rozliczeń i jej tagiem.

Tag lub zestaw tagów można dodawać, aktualizować lub usuwać z jednego węzła przy użyciu standardowych mechanizmów interfejsu Service Fabric, takich jak interfejsy API języka C#, interfejsy API REST lub polecenia programu PowerShell.

> [!NOTE]
> Service Fabric nie obsługuje dystrybucji UD/FD podczas korzystania z tagów węzłów. Zarządzaj odpowiednio tagami węzłów, aby nie dotyczyć naruszeń FD/UD z powodu złej dystrybucji tagów między domenami.

## <a name="enabling-dynamic-node-tags"></a>Włączanie tagów węzłów dynamicznych
Aby ta funkcja działała, należy włączyć konfigurację NodeTaggingEnabled w sekcji PlacementAndLoadBalancing manifestu klastra przy użyciu kodu XML lub JSON:

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

za ClusterConfig.jswł. dla wdrożeń autonomicznych lub Template.jswł. dla klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": " NodeTaggingEnabled ",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>Ustawianie dynamicznych tagów węzłów

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

Dodawanie tagów węzłów do węzła:

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
To polecenie spowoduje dodanie tagów "SampleTag1" i "SampleTag2" w węźle DB.1.

Usuń tagi węzłów z węzła:

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
To polecenie spowoduje usunięcie tagów "SampleTag1" i "SampleTag2" w węźle DB.1.

### <a name="using-c-apis"></a>Korzystanie z interfejsów API języka C#

Dodawanie tagów węzłów do węzła:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

Usuń tagi węzłów z węzła:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>Ustawianie wymaganych tagów dla usług

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

Tworzenie nowej usługi:

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
To polecenie tworzy usługę, która wymaga, aby w węźle była obecna "SampleTag2", aby usługa została tam umieszczona, i "SampleTag1", aby usługa kontynuowała działanie w tym węźle.

Aktualizowanie istniejącej usługi:

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
To polecenie aktualizuje usługę, która wymaga, aby w węźle była obecna "SampleTag2", aby usługa została umieszczona w tym węźle, i aby usługa "SampleTag1" była obecna w tym węźle.

### <a name="using-c-apis"></a>Korzystanie z interfejsów API języka C#

Tworzenie nowej usługi:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Aktualizowanie istniejącej usługi:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

Wszystkie te polecenia dotyczą w równym stopniu usług bez stanowych.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [ograniczeniach umieszczania](service-fabric-cluster-resource-manager-configure-services.md)
