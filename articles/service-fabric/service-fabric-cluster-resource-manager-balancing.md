---
title: Równoważenie obciążenia klastra usługi Azure Service Fabric
description: Wprowadzenie do równoważenia klastra przy użyciu Menedżer zasobów klastra Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 192aca589c3b1e660667dbe8377afe7802b56f17
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146198"
---
# <a name="balancing-your-service-fabric-cluster"></a>Równoważenie klastra usługi Service Fabric
Klaster Service Fabric Menedżer zasobów obsługuje dynamiczne zmiany obciążenia, oddziałanie w celu dodania lub usunięcia węzłów lub usług. Automatycznie koryguje naruszenia ograniczeń i aktywnie ponownie równoważy klaster. Ale jak często wykonywane są te akcje i jakie są wyzwalacze?

Istnieją trzy różne kategorie pracy wykonywanej przez klaster Menedżer zasobów. Oto one:

1. Umieszczanie — ten etap zajmuje się umieszczeniem niestanowych replik lub bezstanowych wystąpień. Umieszczanie obejmuje nowe usługi i obsługujące repliki stanowe lub bezstanowe, które zakończyły się niepowodzeniem. Usuwanie i upuszczanie replik lub wystąpień jest tutaj obsługiwane.
2. Sprawdzanie ograniczeń — ten etap sprawdza i koryguje naruszenia różnych ograniczeń położenia (reguł) w systemie. Przykłady reguł są takie jak upewnienie się, że węzły nie przekraczają pojemności i że są spełnione ograniczenia dotyczące umieszczania usługi.
3. Równoważenie — ten etap sprawdza, czy jest konieczne ponowne zrównoważenie na podstawie skonfigurowanego żądanego poziomu równoważenia dla różnych metryk. Jeśli tak, próbuje znaleźć układ w klastrze, który jest bardziej zrównoważony.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurowanie czasomierzy Menedżer zasobów klastra
Pierwszy zestaw kontrolek wokół równoważenia jest zestawem czasomierzy. Te czasomierze określają częstotliwość, Menedżer zasobów klaster bada klaster i wykonuje działania naprawcze.

Każdy z tych różnych typów poprawek, Menedżer zasobów może być określony przez klaster, jest kontrolowany przez inny czasomierz, który reguluje jego częstotliwość. Po uruchomieniu każdego czasomierza zadanie zostało zaplanowane. Domyślnie Menedżer zasobów:

* skanuje swój stan i stosuje aktualizacje (na przykład rejestrowanie, że węzeł nie działa) co 1/10 sekund
* ustawia flagę sprawdzania położenia co sekundę
* ustawia flagę sprawdzania ograniczeń co sekundę
* ustawia flagę równoważenia co pięć sekund

Poniżej wymieniono przykłady konfiguracji, które dotyczą tych czasomierzy:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

za pomocą ClusterConfig.jsna potrzeby wdrożeń autonomicznych lub Template.jsna potrzeby klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Obecnie klaster Menedżer zasobów wykonuje tylko jedną z tych akcji w danym momencie, sekwencyjnie. Jest to przydatne w przypadku tych czasomierzy jako "minimalnych interwałów" oraz akcji, które zostaną wykonane, gdy czasomierze są wyłączone jako "flagi ustawień". Na przykład klaster Menedżer zasobów ma zadbać o oczekujące żądania utworzenia usług przed przekazaniem klastra. Jak widać domyślne przedziały czasu, klaster Menedżer zasobów skanuje w poszukiwaniu wszystkiego, czego potrzebują. Zwykle oznacza to, że zestaw zmian wprowadzonych w każdym kroku jest mały. W przypadku częstego wprowadzania małych zmian klaster Menedżer zasobów reaguje, gdy wystąpią problemy w klastrze. Domyślne czasomierze zapewniają wiele operacji związanych z przetwarzaniem wsadowym, ponieważ wielu z tych samych typów zdarzeń ma być jednocześnie wykonywana. 

Na przykład w przypadku awarii węzłów można w danym momencie wykonać wszystkie domeny błędów. Wszystkie te błędy są przechwytywane podczas kolejnej aktualizacji stanu po *PLBRefreshGap* . Poprawki są określane podczas następujących operacji umieszczania, sprawdzania ograniczeń i uruchamiania równoważenia. Domyślnie klaster Menedżer zasobów nie skanuje się w godzinach zmian w klastrze i próbuje rozwiązać wszystkie zmiany jednocześnie. Dzięki temu można prowadzić do rozbicie zmian.

Klaster Menedżer zasobów również potrzebuje dodatkowych informacji, aby określić, czy klaster jest niezrównoważony. W przypadku mamy dwie inne fragmenty konfiguracji: *BalancingThresholds* i *ActivityThresholds* .

## <a name="balancing-thresholds"></a>Progi równoważenia
Próg równoważenia jest głównym formantem służącym do wyzwalania ponownego równoważenia. Próg równoważenia dla metryki jest _wskaźnikiem_ . Jeśli obciążenie dla metryki w najbardziej załadowanym węźle podzielone przez ilość obciążenia w najmniej załadowanym węźle przekracza tę metrykę *BalancingThreshold* , klaster jest niezrównoważony. W związku z tym podczas następnego sprawdzania Menedżer zasobów klastra zostanie wyzwolone równoważenie wyniku. Czasomierz *MinLoadBalancingInterval* definiuje, jak często klaster Menedżer zasobów powinien sprawdzać, czy konieczne jest ponowne zrównoważenie. Sprawdzenie nie oznacza, że coś się dzieje. 

Progi równoważenia są definiowane dla poszczególnych metryk jako część definicji klastra. Aby uzyskać więcej informacji na temat metryk, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

za pomocą ClusterConfig.jsna potrzeby wdrożeń autonomicznych lub Template.jsna potrzeby klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![Przykład progu równoważenia][Image1]
</center>

W tym przykładzie każda usługa korzysta z jednej jednostki pewnej metryki. W górnym przykładzie maksymalne obciążenie w węźle wynosi pięć, a wartość minimalna to dwa. Załóżmy, że próg równoważenia dla tej metryki wynosi trzy. Ze względu na to, że współczynnik w klastrze to 5/2 = 2,5 i jest mniejszy niż określony próg równoważenia równy trzy, klaster jest zrównoważony. Podczas sprawdzania Menedżer zasobów klastra nie jest wyzwalane Równoważenie obciążenia.

W dolnym przykładzie maksymalne obciążenie w węźle wynosi 10, podczas gdy minimalny jest dwa, co skutkuje stosunkiem pięciu. Pięć jest większe niż wyznaczony próg równoważenia dla tej metryki. W efekcie uruchomienie ponownego równoważenia zostanie zaplanowane przy następnym uruchomieniu czasomierza równoważenia obciążenia. W takiej sytuacji takie obciążenie jest zazwyczaj dystrybuowane do Węzeł3. Ponieważ klaster Service Fabric Menedżer zasobów nie używa podejścia zachłanne, część obciążenia może być również dystrybuowana do Węzeł2. 

<center>

![Przykładowe akcje dotyczące progu równoważenia obciążenia][Image2]
</center>

> [!NOTE]
> "Równoważenie" obsługuje dwie różne strategie zarządzania obciążeniem w klastrze. Domyślną strategią używaną przez Menedżer zasobów klaster jest rozproszenie obciążenia między węzłami w klastrze. Inne strategie są [defragmentacją](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentacja jest wykonywana podczas tego samego przebiegu równoważenia. Strategie równoważenia i defragmentacji mogą być używane dla różnych metryk w tym samym klastrze. Usługa może mieć zarówno metryki równoważenia, jak i defragmentacji. W przypadku metryk defragmentacji stosunek obciążeń w klastrze jest wyzwalany w przypadku, gdy jest _poniżej_ progu równoważenia. 
>

Poniżej progu równoważenia nie jest jawny cel. Progi równoważenia są tylko *wyzwalaczem* . Po uruchomieniu usługi równoważenia obciążenia klaster Menedżer zasobów określa, jakie ulepszenia mogą wprowadzić (jeśli istnieją). Tylko ze względu na to, że wyszukiwanie przy użyciu funkcji równoważenia obciążenia nie oznacza żadnych ruchów. Czasami klaster jest niezrównoważony, ale jest zbyt ograniczony do poprawnego działania. Ponadto ulepszenia wymagają zbyt [kosztownych](service-fabric-cluster-resource-manager-movement-cost.md)ruchów.

## <a name="activity-thresholds"></a>Progi działania
Czasami chociaż węzły są stosunkowo niezrównoważone, *łączna* ilość obciążenia w klastrze jest niska. Brak obciążenia może być przejściowym DIPM lub, ponieważ klaster jest nowy i dopiero zaczyna się. W obu przypadkach możesz nie chcieć poświęcać równoważenia czasu na klaster, ponieważ trudno jest uzyskać. Jeśli klaster został poddany zrównoważeniu, spędzasz zasoby sieciowe i obliczeniowe, aby poruszać się bez wprowadzania dużej *absolutnej* różnicy. Aby uniknąć niepotrzebnych ruchów, istnieje inna kontrolka znana jako progi aktywności. Progi działania umożliwiają określenie bezwzględnej dolnej granicy dla działania. Jeśli żaden węzeł nie przekracza tego progu, równoważenie nie zostanie wyzwolone nawet wtedy, gdy zostanie osiągnięty próg równoważenia obciążenia.

Załóżmy, że zachowamy nasz próg równoważenia dla tej metryki. Załóżmy również, że mamy próg aktywności wynoszący 1536. W pierwszym przypadku, gdy klaster jest niezrównoważony dla progu równoważenia, nie ma żadnego węzła odpowiadającego temu działaniu, więc nic się nie dzieje. W dolnym przykładzie Węzeł1 przekracza próg działania. Ponieważ został przekroczony próg równoważenia i próg działania dla metryki, zostanie zaplanowana usługa równoważenia obciążenia. Na przykład przyjrzyjmy się na poniższym diagramie: 

<center>

![Przykład progu działania][Image3]
</center>

Podobnie jak w przypadku progów równoważenia obciążenia, progi aktywności są definiowane na poziomie metryki za pomocą definicji klastra:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

za pomocą ClusterConfig.jsna potrzeby wdrożeń autonomicznych lub Template.jsna potrzeby klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Progi równoważenia i aktywności są powiązane z konkretnym zrównoważeniem, ale tylko wtedy, gdy zostanie przekroczony próg równoważenia i próg działania dla tej samej metryki.

> [!NOTE]
> Gdy nie zostanie określony, próg równoważenia dla metryki wynosi 1, a próg działania wynosi 0. Oznacza to, że klaster Menedżer zasobów spróbuje zapewnić, że pomiar jest idealnie zrównoważony dla każdego danego obciążenia. W przypadku korzystania z metryk niestandardowych zaleca się jawne zdefiniowanie własnych progów równoważenia i aktywności dla metryk. 
>

## <a name="balancing-services-together"></a>Usługi równoważenia obciążenia razem
Bez względu na to, czy klaster jest niezrównoważony, czy nie jest decyzją obejmującą cały klaster. Jednak sposób, w jaki należymy rozwiązać ten sposób, przenosi poszczególne repliki usług i wystąpienia. To ma sens, że to prawo? Jeśli pamięć jest rzutowana na jeden węzeł, może być do niej współautorem wiele replik lub wystąpień. Naprawianie nierównowagi może wymagać przeniesienia wszelkich replik stanowych lub bezstanowych, które używają niezrównoważonej metryki.

Czasami usługa, która nie była sama niezrównoważona, zostaje przeniesiona (Zapamiętaj dyskusję na temat wag lokalnych i globalnych wcześniej). Dlaczego usługa zostanie przeniesiona, gdy wszystkie metryki usługi zostały zrównoważone? Zobaczymy przykład:

- Załóżmy, że istnieją cztery usługi, Service1, Językowej2, Service3 i Service4. 
- Service1 raporty metryki Metric1 i Metric2. 
- Językowej2 raporty metryki Metric2 i Metric3. 
- Service3 raporty metryki Metric3 i Metric4.
- Metric99 metryki raportów Service4. 

W tym miejscu możesz zobaczyć, gdzie się znajdujemy: istnieje łańcuch! Firma Microsoft nie korzysta z czterech niezależnych usług, ale mamy trzy usługi, które są ze sobą powiązane.

<center>

![Diagram pokazujący, jak zrównoważyć usługi.][Image4]
</center>

Ze względu na ten łańcuch istnieje możliwość, że niezrównoważone metryki 1-4 mogą spowodować, że repliki lub wystąpienia należące do usług 1-3. Wiemy również, że niezrównoważone metryki 1, 2 i 3 nie mogą spowodować przesunięcia w Service4. Nie będzie to miało miejsca, ponieważ przeniesienie replik lub wystąpień należących do Service4 wokół nie będzie miało znaczenia, aby wpływać na bilansowanie metryk 1-3.

Klaster Menedżer zasobów automatycznie określa, jakie usługi są powiązane. Dodawanie, usuwanie lub zmienianie metryk dla usług może mieć wpływ na ich relacje. Na przykład między dwoma uruchomieniami równoważenia Językowej2 mogły zostać zaktualizowane w celu usunięcia Metric2. Spowoduje to przerwanie łańcucha między Service1 i Językowej2. Teraz zamiast dwóch grup powiązanych usług istnieją trzy:

<center>

![Diagram pokazujący, że Menedżer zasobów klastra określa, które usługi są powiązane.][Image5]
</center>

## <a name="next-steps"></a>Następne kroki
* Metryki to sposób, w jaki Menedżer zasobów klastra Service Fabric zarządza zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o metrykach i sposobach ich konfigurowania, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md)
* Koszt przenoszenia jest jednym ze sposobów sygnalizowania klastra Menedżer zasobów, że niektóre usługi są droższe do przeniesienia niż inne. Aby uzyskać więcej informacji na temat kosztu przeniesienia, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-movement-cost.md) .
* Klaster Menedżer zasobów ma kilka ograniczań, które można skonfigurować, aby spowalniać zmiany w klastrze. Nie są one zwykle konieczne, ale jeśli będziesz ich potrzebować, możesz dowiedzieć się [tutaj](service-fabric-cluster-resource-manager-advanced-throttling.md)
* Klaster Menedżer zasobów może rozpoznać i obsłużyć podklastry (sytuację, która czasami występuje w przypadku używania ograniczeń i równoważenia umieszczania). Aby dowiedzieć się, jak może to wpływać na Równoważenie i jak można je obsłużyć, zobacz [tutaj](cluster-resource-manager-subclustering.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
