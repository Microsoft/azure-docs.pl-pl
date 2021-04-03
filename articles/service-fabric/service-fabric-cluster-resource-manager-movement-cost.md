---
title: 'Service Fabric klastra Menedżer zasobów: koszt przenoszenia'
description: Dowiedz się więcej o kosztach przenoszenia usług Service Fabric i sposobach ich określenia do dopasowania do potrzeb związanych z architekturą, w tym konfiguracji dynamicznej.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fdcfb02851d56ed996ae4bf32671ab545782733
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89005347"
---
# <a name="service-movement-cost"></a>Koszt przeniesienia usługi
Jest to czynnik, który Menedżer zasobów klastrze Service Fabric podczas próby ustalenia, które zmiany należy wprowadzić w klastrze, jest kosztem tych zmian. Pojęcie "koszt" jest wymieniane z informacjami o tym, jak dużo można ulepszyć klaster. Koszt jest przynoszony podczas przechodzenia usług na potrzeby równoważenia, defragmentacji i innych wymagań. Celem jest spełnienie wymagań dotyczących najmniej niezakłóconych lub kosztownych sposobów.

Przeniesienie usług na minimalny koszt czasu procesora i przepustowości sieci. W przypadku usług stanowych wymagane jest skopiowanie stanu tych usług, zużywających dodatkową pamięć i dysk. Minimalizacja kosztu rozwiązań, które Menedżer zasobów z klastrem platformy Azure Service Fabric, pomaga zapewnić, że zasoby klastra nie są niepotrzebne. Jednak nie ma potrzeby ignorowania rozwiązań, które znacząco zwiększą przydział zasobów w klastrze.

Klaster Menedżer zasobów ma dwa sposoby obliczania kosztów obliczeniowych i ograniczania ich podczas próby zarządzania klastrem. Pierwszy mechanizm jest po prostu zliczany po każdym przeniesieniu. W przypadku wygenerowania dwóch rozwiązań o takim samym saldzie (wyniku), klaster Menedżer zasobów preferuje ten sam koszt (łącznie z liczbą przeniesień).

Ta strategia działa dobrze. Ale tak jak w przypadku obciążeń domyślnych lub statycznych, jest mało prawdopodobne, że wszystkie przenoszone systemy są równe. Niektóre z nich mogą być znacznie droższe.

## <a name="setting-move-costs"></a>Ustawianie kosztów przenoszenia 
Można określić domyślny koszt przenoszenia dla usługi po jej utworzeniu:

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Można również określić lub zaktualizować MoveCost dynamicznie dla usługi po utworzeniu usługi: 

Program PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamiczne określanie kosztu przeniesienia dla poszczególnych replik

Poprzedzające fragmenty kodu są przeznaczone do określenia MoveCost dla całej usługi jednocześnie spoza usługi. Koszt przeniesienia jest jednak najbardziej przydatny, gdy koszt przeniesienia określonego obiektu usługi zostanie zmieniony na jego cykl życia. Ze względu na to, że same usługi są prawdopodobnie najlepszym pomysłem do przenoszenia danego czasu, istnieje interfejs API służący do zgłaszania indywidualnych kosztów przenoszenia w czasie wykonywania. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Wpływ kosztu przenoszenia
MoveCost ma pięć poziomów: zero, niski, średni, wysoki i VeryHigh. Mają zastosowanie następujące zasady:

* MoveCosts są względem siebie, z wyjątkiem wartości zero i VeryHigh. 
* Zero kosztu przeniesienia oznacza, że przenoszenie jest bezpłatne i nie powinno być wliczane do wyniku rozwiązania.
* Ustawienie kosztu przenoszenia na wartość High lub VeryHigh *nie gwarantuje* , że replika *nigdy* nie zostanie przeniesiona.
* Repliki z kosztem przeniesienia VeryHigh będą przenoszone tylko w przypadku naruszenia ograniczenia w klastrze, którego nie można naprawić w żaden inny sposób (nawet jeśli wymaga przenoszenia wielu innych replik w celu naprawy naruszenia)



<center>

![Przenieś koszt jako czynnik w wyborze replik do przeniesienia][Image1]
</center>

Program MoveCost ułatwia znalezienie rozwiązań, które powodują, że najtańsze zakłócenia i najłatwiej osiągnąć przy zachowaniu równoważnej równowagi. Pojęcie kosztu usługi może być względem wielu rzeczy. Najczęstszymi czynnikami związanymi ze obliczaniem kosztów przeprowadzki są:

- Ilość lub dane, które usługa ma przenieść.
- Koszt odłączenia klientów. Przeniesienie repliki podstawowej zwykle jest droższe niż koszt przeniesienia repliki pomocniczej.
- Koszt przerwania operacji w locie. Niektóre operacje na poziomie magazynu danych lub operacje wykonywane w odpowiedzi na wywołanie klienta są kosztowne. Po pewnym momencie nie chcesz ich zatrzymać, jeśli nie musisz. W związku z tym podczas trwania operacji można zwiększyć koszt przenoszenia tego obiektu usługi, aby zmniejszyć prawdopodobieństwo jego przeniesienia. Po zakończeniu operacji należy ustawić koszt z powrotem na normalny.

> [!IMPORTANT]
> Użycie kosztu przeniesienia VeryHigh należy uważnie traktować, ponieważ znacząco ogranicza Menedżer zasobów możliwość znajdowania w klastrze rozwiązania do umieszczania na globalnie optymalnych rozwiązań. Repliki z kosztem przeniesienia VeryHigh będą przenoszone tylko w przypadku naruszenia ograniczenia w klastrze, którego nie można naprawić w żaden inny sposób (nawet jeśli wymaga przenoszenia wielu innych replik w celu naprawy naruszenia)

## <a name="enabling-move-cost-in-your-cluster"></a>Włączanie kosztu przenoszenia w klastrze
Aby bardziej szczegółowo MoveCosts wziąć pod uwagę, MoveCost musi być włączona w klastrze. Bez tego ustawienia domyślny tryb obliczeń jest używany do obliczania MoveCost, a raporty MoveCost są ignorowane.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

za pomocą ClusterConfig.jsna potrzeby wdrożeń autonomicznych lub Template.jsna potrzeby klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Następne kroki
- Service Fabric Menedżer zasobów klastra używa metryk do zarządzania zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o metrykach i sposobach ich konfigurowania, zapoznaj się z tematem [Zarządzanie użyciem zasobów i obciążeniem Service Fabric za pomocą metryk](service-fabric-cluster-resource-manager-metrics.md).
- Aby dowiedzieć się, jak klaster Menedżer zasobów zarządza i zrównoważy obciążenie w klastrze, sprawdź [równoważenie Service Fabric klastra](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
