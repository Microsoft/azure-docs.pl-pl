---
title: Service Fabric klastra Menedżer zasobów — grupy aplikacji
description: Omówienie funkcji grupy aplikacji w klastrze Service Fabric Menedżer zasobów
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75452153"
---
# <a name="introduction-to-application-groups"></a>Wprowadzenie do grup aplikacji
Klaster Service Fabric Menedżer zasobów zazwyczaj zarządza zasobami klastra przez rozłożenie obciążenia (reprezentowanego przez [metryki](service-fabric-cluster-resource-manager-metrics.md)) nawet w całym klastrze. Service Fabric zarządza pojemnością węzłów w klastrze i w klastrze jako całość za pośrednictwem [pojemności](service-fabric-cluster-resource-manager-cluster-description.md). Metryki i wydajność doskonale nadaje się dla wielu obciążeń, ale wzorce, które znacznie wykorzystują różne Service Fabric wystąpienia aplikacji, czasami zapewniają dodatkowe wymagania. Na przykład możesz chcieć:

- Zarezerwuj pewną pojemność na węzłach w klastrze dla usług w ramach niektórych nazwanych wystąpień aplikacji
- Ogranicz łączną liczbę węzłów, w których uruchomione są usługi w nazwanym wystąpieniu aplikacji (zamiast rozłożyć je na cały klaster)
- Zdefiniuj pojemności dla nazwanego wystąpienia aplikacji, aby ograniczyć liczbę usług lub całkowite użycie zasobów przez usługi w nim

Aby spełnić te wymagania, klaster Service Fabric Menedżer zasobów obsługuje funkcję o nazwie grupy aplikacji.

## <a name="limiting-the-maximum-number-of-nodes"></a>Ograniczanie maksymalnej liczby węzłów
Najprostszym przypadkiem użycia dla pojemności aplikacji jest to, że wystąpienie aplikacji musi być ograniczone do określonej maksymalnej liczby węzłów. Powoduje to konsolidację wszystkich usług w ramach tego wystąpienia aplikacji na określoną liczbę maszyn. Konsolidacja jest przydatna w przypadku próby przeanalizowania lub zakończenia używania zasobów fizycznych przez usługi w ramach tego wystąpienia aplikacji o nazwie. 

Na poniższej ilustracji przedstawiono wystąpienie aplikacji z i bez zdefiniowanej maksymalnej liczby węzłów:

<center>

![Wystąpienie aplikacji definiujące maksymalną liczbę węzłów][Image1]
</center>

W lewym przykładzie aplikacja nie ma zdefiniowanej maksymalnej liczby węzłów i ma trzy usługi. Klaster Menedżer zasobów rozłożyć wszystkie repliki na sześć dostępnych węzłów w celu osiągnięcia najlepszego zrównoważenia w klastrze (zachowanie domyślne). W prawym przykładzie zobaczymy tę samą aplikację ograniczoną do trzech węzłów.

Parametr, który kontroluje to zachowanie, nosi nazwę MaximumNodes. Ten parametr można ustawić podczas tworzenia aplikacji lub aktualizowany dla wystąpienia aplikacji, które już działa.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

W ramach zestawu węzłów klaster Menedżer zasobów nie gwarantuje, które obiekty usługi są umieszczone razem lub które węzły są używane.

## <a name="application-metrics-load-and-capacity"></a>Metryki aplikacji, obciążenie i pojemność
Grupy aplikacji umożliwiają również definiowanie metryk skojarzonych z danym wystąpieniem aplikacji i pojemności tego wystąpienia aplikacji dla tych metryk. Metryki aplikacji umożliwiają śledzenie, rezerwowanie i ograniczanie zużycia zasobów przez usługi w ramach tego wystąpienia aplikacji.

Dla każdej metryki aplikacji istnieją dwie wartości, które można ustawić:

- **Całkowita pojemność aplikacji** — to ustawienie reprezentuje łączną pojemność aplikacji dla określonej metryki. Klaster Menedżer zasobów nie zezwala na tworzenie nowych usług w ramach tego wystąpienia aplikacji, które spowodują, że całkowite obciążenie przekracza tę wartość. Załóżmy na przykład, że wystąpienie aplikacji miało pojemność 10 i już załadowano pięć. Tworzenie usługi o całkowitym obciążeniu domyślnym 10 byłyby niedozwolone.
- **Maksymalna pojemność węzłów** — to ustawienie określa maksymalne całkowite obciążenie aplikacji w jednym węźle. Jeśli obciążenie przekroczy tę pojemność, klaster Menedżer zasobów przenosi repliki do innych węzłów, aby obciążenie było zmniejszane.


Narzędzia

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Rezerwowanie pojemności
Innym typowym zastosowaniem grup aplikacji jest upewnienie się, że zasoby w klastrze są zarezerwowane dla danego wystąpienia aplikacji. Miejsce jest zawsze rezerwowane podczas tworzenia wystąpienia aplikacji.

Rezerwowanie miejsca w klastrze dla aplikacji odbywa się natychmiast nawet wtedy, gdy:
- wystąpienie aplikacji jest tworzone, ale nie zawiera jeszcze żadnych usług
- liczba usług w ramach wystąpienia aplikacji jest zmieniana za każdym razem 
- usługi istnieją, ale nie zużywają zasobów 

Rezerwowanie zasobów dla wystąpienia aplikacji wymaga określenia dwóch dodatkowych parametrów: *MinimumNodes* i *NodeReservationCapacity*

- **MinimumNodes** — określa minimalną liczbę węzłów, na których wystąpienie aplikacji powinno być uruchamiane.  
- **NodeReservationCapacity** — to ustawienie ma na metrykę dla aplikacji. Wartość jest ilością tej metryki zarezerwowaną dla aplikacji w dowolnym węźle, w którym działają usługi w tej aplikacji.

Łączenie **MinimumNodes** i **NodeReservationCapacity** gwarantuje minimalną rezerwację obciążenia dla aplikacji w ramach klastra. Jeśli klaster nie ma mniejszej pojemności niż łączna wymagana rezerwacja, utworzenie aplikacji kończy się niepowodzeniem. 

Przyjrzyjmy się przykładowi rezerwacja pojemności:

<center>

![Wystąpienia aplikacji definiujące zarezerwowaną pojemność][Image2]
</center>

W lewym przykładzie aplikacje nie mają zdefiniowanej pojemności aplikacji. Klaster Menedżer zasobów zrównoważy wszystko zgodnie z normalnymi regułami.

W przykładzie po prawej stronie Załóżmy, że Application1 został utworzony z następującymi ustawieniami:

- MinimumNodes ustawiony na dwie
- Metryka aplikacji zdefiniowana z
  - NodeReservationCapacity 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric rezerwuje pojemność na dwóch węzłach dla Application1 i nie zezwala usługom z Application2 na korzystanie z tej pojemności nawet wtedy, gdy nie są używane żadne obciążenia przez usługi wewnątrz Application1 w danym momencie. Ta zarezerwowana pojemność aplikacji jest uważana za zużytą i jest liczona względem pozostałej pojemności w tym węźle i w klastrze.  Rezerwacja jest odejmowana od momentu pozostałej pojemności klastra natychmiast, ale użycie zarezerwowane jest odejmowane od pojemności określonego węzła tylko wtedy, gdy na tym jest umieszczony przynajmniej jeden obiekt usługi. Ta Późniejsza rezerwacja umożliwia elastyczność i lepsze wykorzystanie zasobów, ponieważ zasoby są zarezerwowane tylko w węzłach, gdy jest to możliwe.

## <a name="obtaining-the-application-load-information"></a>Uzyskiwanie informacji o ładowaniu aplikacji
Dla każdej aplikacji, która ma pojemność aplikacji zdefiniowaną dla jednej lub większej liczby metryk, można uzyskać informacje o ładowaniu zagregowanym raportowanym przez repliki usług.

Narzędzia

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

Zapytanie ApplicationLoad zwraca podstawowe informacje o pojemności aplikacji, która została określona dla aplikacji. Te informacje obejmują minimalny węzeł i informacje o maksymalnych węzłach oraz liczbę, jaką zajmuje aplikacja. Zawiera również informacje o każdej metryce obciążenia aplikacji, w tym:

* Nazwa metryki: Nazwa metryki.
* Pojemność rezerwacji: pojemność klastra zarezerwowaną w klastrze dla tej aplikacji.
* Ładowanie aplikacji: całkowite obciążenie replik podrzędnych tej aplikacji.
* Wydajność aplikacji: Maksymalna dozwolona wartość obciążenia aplikacji.

## <a name="removing-application-capacity"></a>Usuwanie pojemności aplikacji
Po ustawieniu parametrów pojemności aplikacji dla aplikacji można je usunąć za pomocą aktualizacji interfejsów API aplikacji lub poleceń cmdlet programu PowerShell. Na przykład:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

To polecenie usuwa wszystkie parametry zarządzania pojemnością aplikacji z wystąpienia aplikacji. Obejmuje to MinimumNodes, MaximumNodes i metryki aplikacji, o ile istnieją. Efekt polecenia jest natychmiastowy. Po zakończeniu wykonywania tego polecenia klaster Menedżer zasobów używa domyślnego zachowania do zarządzania aplikacjami. Parametry pojemności aplikacji można określić ponownie za pośrednictwem `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()` .

### <a name="restrictions-on-application-capacity"></a>Ograniczenia pojemności aplikacji
Istnieją pewne ograniczenia dotyczące parametrów pojemności aplikacji, które muszą być przestrzegane. Jeśli wystąpiły błędy sprawdzania poprawności, nie wprowadzono żadnych zmian.

- Wszystkie parametry całkowite nie mogą być liczbami ujemnymi.
- MinimumNodes nigdy nie może być większa niż MaximumNodes.
- Jeśli zdefiniowano pojemności dla metryki obciążenia, muszą one być zgodne z następującymi regułami:
  - Pojemność rezerwacji węzła nie może być większa niż maksymalna pojemność węzła. Na przykład nie można ograniczyć pojemności dla metryki "CPU" w węźle do dwóch jednostek, a następnie spróbować zarezerwować trzy jednostki na każdym węźle.
  - Jeśli MaximumNodes jest określony, produkt MaximumNodes i Maksymalna pojemność węzła nie może być większa niż całkowita pojemność aplikacji. Załóżmy na przykład, że maksymalna pojemność węzła dla metryki obciążenia "CPU" jest ustawiona na osiem. Załóżmy również, że ustawisz maksymalną liczbę węzłów na 10. W takim przypadku łączną pojemność aplikacji musi być większa niż 80 dla tej metryki obciążenia.

Ograniczenia są wymuszane zarówno podczas tworzenia aplikacji, jak i aktualizacji.

## <a name="how-not-to-use-application-capacity"></a>Jak nie korzystać z pojemności aplikacji
- Nie należy próbować korzystać z funkcji grupy aplikacji w celu ograniczenia aplikacji do _określonego_ podzestawu węzłów. Innymi słowy, możesz określić, że aplikacja działa na co najwyżej pięciu węzłach, ale nie na co pięć węzłów w klastrze. Ograniczanie aplikacji do określonych węzłów można osiągnąć przy użyciu ograniczeń umieszczania dla usług.
- Nie należy próbować korzystać z pojemności aplikacji, aby upewnić się, że dwie usługi z tej samej aplikacji są umieszczane w tych samych węzłach. Zamiast tego należy używać [koligacji](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) lub [ograniczeń umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług, [Dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)
- Aby dowiedzieć się, w jaki sposób klaster Menedżer zasobów zarządza i równoważenia obciążenia w klastrze, zapoznaj się z artykułem dotyczącym [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Zacznij od początku i [zapoznaj się z wprowadzeniem do klastra Service Fabric Menedżer zasobów](service-fabric-cluster-resource-manager-introduction.md)
- Aby uzyskać więcej informacji na temat ogólnego działania metryk, zapoznaj się z tematem [Service Fabric metryk obciążenia](service-fabric-cluster-resource-manager-metrics.md)
- Klaster Menedżer zasobów ma wiele opcji opisywania klastra. Aby dowiedzieć się więcej na ten temat, zapoznaj się z tym artykułem na temat [opisywania Service Fabric klastra](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
