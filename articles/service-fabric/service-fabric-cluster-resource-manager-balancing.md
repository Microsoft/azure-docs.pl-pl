---
title: Równoważenie klastra sieci szkieletowej usług Azure
description: Wprowadzenie do równoważenia klastra z Menedżerem zasobów klastra sieci szkieletowej usług.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b6df25b525975f2d4fe6a02064e81f359a804c58
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416265"
---
# <a name="balancing-your-service-fabric-cluster"></a>Równoważenie klastra sieci szkieletowej usług
Menedżer zasobów klastra sieci szkieletowej usług obsługuje dynamiczne zmiany obciążenia, reagując na dodatki lub usunięcia węzłów lub usług. Automatycznie koryguje również naruszenia ograniczeń i proaktywnie równoważe klaster. Ale jak często podejmowane są te działania i co je wyzwala?

Istnieją trzy różne kategorie pracy wykonywane przez Menedżera zasobów klastra. Oto one:

1. Umieszczenie — ten etap zajmuje się umieszczaniem replik stanowych lub wystąpień bezstanowych, których brakuje. Umieszczenie obejmuje zarówno nowe usługi, jak i obsługę replik stanowych lub wystąpień bezstanowych, które nie powiodły się. Usuwanie i upuszczanie replik lub wystąpień są obsługiwane w tym miejscu.
2. Kontrole ograniczeń — ten etap sprawdza i koryguje naruszenia różnych ograniczeń umieszczania (reguł) w systemie. Przykładami reguł są takie rzeczy, jak zapewnienie, że węzły nie są ponad pojemność i że ograniczenia umieszczania usługi są spełnione.
3. Równoważenie — ten etap sprawdza, czy ponowne równoważenie jest konieczne na podstawie skonfigurowanego żądanego poziomu równowagi dla różnych metryk. Jeśli tak, próbuje znaleźć układ w klastrze, który jest bardziej zrównoważony.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurowanie czasomierzy Menedżera zasobów klastra
Pierwszy zestaw formantów wokół równoważenia są zestaw czasomierzy. Te czasomierze określają, jak często Menedżer zasobów klastra sprawdza klaster i podejmuje działania naprawcze.

Każdy z tych różnych typów poprawek, które menedżer zasobów klastra może wprowadzić, jest kontrolowany przez inny czasomierz, który reguluje jego częstotliwość. Gdy każdy czasomierz uruchamia, zadanie jest zaplanowane. Domyślnie Menedżer zasobów:

* skanuje jego stan i stosuje aktualizacje (np. rejestrowanie, że węzeł jest w dół) co 1/10 sekundy
* ustawia flagę wyboru miejsca docelowego co sekundę
* ustawia flagę sprawdzania ograniczeń co sekundę
* ustawia flagę równoważenia co pięć sekund

Poniżej znajdują się przykłady konfiguracji regulującej te czasomierze:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

za pośrednictwem clusterconfig.json dla wdrożeń autonomicznych lub Template.json dla klastrów hostowanych platformy Azure:

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

Obecnie Menedżer zasobów klastra wykonuje tylko jedną z tych akcji naraz, sekwencyjnie. Dlatego nazywamy te czasomierze "minimalnymi interwałami" i działaniami, które są podejmowane, gdy czasomierze wyłączają się jako "ustawianie flag". Na przykład Menedżer zasobów klastra zajmuje się oczekującymi żądaniami utworzenia usług przed zrównoważeniem klastra. Jak widać w określonych domyślnych odstępach czasu, Menedżer zasobów klastra skanuje wszystko, co musi często robić. Zwykle oznacza to, że zestaw zmian wprowadzonych podczas każdego kroku jest mały. Częste wprowadzanie niewielkich zmian umożliwia Menedżerowi zasobów klastra reagowanie, gdy coś się dzieje w klastrze. Domyślne czasomierze zapewniają pewne przetwarzanie wsadowe, ponieważ wiele z tych samych typów zdarzeń zwykle występuje jednocześnie. 

Na przykład, gdy węzły nie powiodą się, mogą to zrobić całe domeny błędów naraz. Wszystkie te błędy są przechwytywane podczas następnej aktualizacji stanu po *PLBRefreshGap*. Poprawki są określane podczas następujących przebiegów umieszczania, sprawdzania ograniczeń i równoważenia. Domyślnie Menedżer zasobów klastra nie skanuje godzin zmian w klastrze i próbuje rozwiązać wszystkie zmiany jednocześnie. W ten sposób doprowadzi do wybuchów churn.

Menedżer zasobów klastra potrzebuje również dodatkowych informacji, aby ustalić, czy klaster jest niezrównoważony. Do tego mamy dwa inne elementy konfiguracji: *BalancingThresholds* i *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Progi bilansowania
Próg równoważenia jest głównym formantem wyzwalania równoważenia. Próg równoważenia dla metryki jest _współczynnikiem_. Jeśli obciążenie metryki na najbardziej załadowany węzeł podzielone przez ilość obciążenia w najsłabiej załadowanym węźle przekracza tej metryki *BalancingThreshold*, a następnie klaster jest nierównowaga. W rezultacie równoważenie jest wyzwalane przy następnym sprawdzaniu przez Menedżera zasobów klastra. *Czasomierz MinLoadBalancingInterval* określa, jak często Menedżer zasobów klastra powinien sprawdzić, czy konieczne jest ponowne równoważenie. Sprawdzanie nie oznacza, że coś się dzieje. 

Progi równoważenia są definiowane na podstawie metryki jako część definicji klastra. Aby uzyskać więcej informacji na temat metryk, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

za pośrednictwem clusterconfig.json dla wdrożeń autonomicznych lub Template.json dla klastrów hostowanych platformy Azure:

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

W tym przykładzie każda usługa zużywa jedną jednostkę niektórych metryk. W górnym przykładzie maksymalne obciążenie węzła wynosi pięć, a minimum to dwa. Załóżmy, że próg równoważenia dla tej metryki wynosi trzy. Ponieważ stosunek w klastrze wynosi 5/2 = 2,5 i jest mniejszy niż określony próg równoważenia trzech, klaster jest zrównoważony. Podczas sprawdzania menedżera zasobów klastra nie jest wyzwalane równoważenie.

W dolnym przykładzie maksymalne obciążenie węzła wynosi 10, podczas gdy minimalna jest dwa, co powoduje stosunek pięciu. Pięć jest większa niż wyznaczony próg równoważenia trzech dla tej metryki. W rezultacie następna następna liczba uruchomieniowa równoważenia zostanie zaplanowana przy następnej serii czasomierza równoważenia. W takiej sytuacji niektóre obciążenia są zwykle dystrybuowane do węzła 3. Ponieważ Menedżer zasobów klastra sieci szkieletowej usług nie używa chciwego podejścia, niektóre obciążenia mogą być również dystrybuowane do węzła2. 

<center>

![Przykładowe akcje progu równoważenia][Image2]
</center>

> [!NOTE]
> "Równoważenie" obsługuje dwie różne strategie zarządzania obciążeniem w klastrze. Domyślną strategią używana przez Menedżer zasobów klastra jest dystrybucja obciążenia między węzłami w klastrze. Inną strategią jest [defragmentacja](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentacja jest wykonywana podczas tego samego przebiegu równoważenia. Strategie równoważenia i defragmentacji mogą służyć do różnych metryk w ramach tego samego klastra. Usługa może mieć zarówno równoważenia i defragmentacji metryki. W przypadku metryk defragmentacji stosunek obciążeń w klastrze wyzwala równoważenie, gdy jest _poniżej_ progu równoważenia. 
>

Poniżej progu równoważenia nie jest jawnym celem. Progi równoważenia to tylko *wyzwalacz.* Podczas równoważenia przebiegów Menedżer zasobów klastra określa, jakie ulepszenia może wprowadzić, jeśli takie istnieją. To, że wyszukiwanie równoważenia jest rozpoczęte, nie oznacza, że nic się rusza. Czasami klaster jest niezrównoważony, ale zbyt ograniczony, aby poprawić. Alternatywnie, ulepszenia wymagają ruchów, które są zbyt [kosztowne).](service-fabric-cluster-resource-manager-movement-cost.md)

## <a name="activity-thresholds"></a>Progi aktywności
Czasami chociaż węzły są stosunkowo nierówne, *całkowita* ilość obciążenia w klastrze jest niska. Brak obciążenia może być przejściowy spadek, lub dlatego, że klaster jest nowy i po prostu coraz bootstrapped. W obu przypadkach może nie chcesz spędzać czasu równoważenia klastra, ponieważ jest niewiele do zyskania. Jeśli klaster przeszedł równoważenie, można wydać sieci i zasobów obliczeniowych, aby przenieść rzeczy wokół bez dokonywania dużych *różnic bezwzględnych.* Aby uniknąć niepotrzebnych ruchów, istnieje inny formant znany jako progi aktywności. Progi aktywności umożliwia określenie niektórych bezwzględnych dolnej granicy dla działania. Jeśli żaden węzeł nie przekracza tego progu, równoważenie nie jest wyzwalane, nawet jeśli próg równoważenia jest spełniony.

Załóżmy, że zachowujemy próg równoważenia wynoszący trzy dla tej metryki. Załóżmy również, że mamy próg aktywności 1536. W pierwszym przypadku, gdy klaster jest nierównoważny na próg równoważenia nie ma żadnego węzła spełnia próg działania, więc nic się nie dzieje. W dolnym przykładzie Node1 przekracza próg działania. Ponieważ zarówno próg równoważenia, jak i próg działania dla metryki są przekroczone, zaplanowano równoważenie. Na przykład przyjrzyjmy się następującej diagramie: 

<center>

![Przykład progu działania][Image3]
</center>

Podobnie jak progi równoważenia, progi aktywności są definiowane dla metryki za pomocą definicji klastra:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

za pośrednictwem clusterconfig.json dla wdrożeń autonomicznych lub Template.json dla klastrów hostowanych platformy Azure:

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

Równoważenie i progi aktywności są powiązane z określoną metryką — równoważenie jest wyzwalane tylko wtedy, gdy próg równoważenia i próg działania zostanie przekroczony dla tej samej metryki.

> [!NOTE]
> Jeśli nie określono, próg równoważenia dla metryki wynosi 1, a próg działania wynosi 0. Oznacza to, że Menedżer zasobów klastra będzie starał się zachować tę metrykę idealnie zrównoważoną dla danego obciążenia. Jeśli używasz niestandardowych metryk, zaleca się jawne zdefiniowanie własnych progów równoważenia i aktywności dla metryk. 
>

## <a name="balancing-services-together"></a>Równoważenie usług razem
Czy klaster jest niezrównoważony, czy nie jest decyzja całego klastra. Jednak sposób, w jaki go naprawić jest przenoszenie poszczególnych replik usługi i wystąpień wokół. To ma sens, prawda? Jeśli pamięć jest skumulowana w jednym węźle, wiele replik lub wystąpień może przyczyniać się do niego. Naprawienie nierównowagi może wymagać przeniesienia dowolnej repliki stanowe lub wystąpienia bezstanowe, które używają metryki nierównowagi.

Czasami jednak usługa, która sama nie była niezrównoważona, zostaje przeniesiona (pamiętaj wcześniej o lokalnych i globalnych wagach). Dlaczego usługa zostanie przeniesiona, gdy wszystkie metryki tej usługi były zrównoważone? Zobaczmy przykład:

- Załóżmy, że istnieją cztery usługi, Service1, Service2, Service3 i Service4. 
- Service1 raportuje metryki Metric1 i Metric2. 
- Service2 raportuje metryki Metric2 i Metric3. 
- Service3 raportuje metryki Metric3 i Metric4.
- Service4 raportuje metrykę Metryka99. 

Na pewno można zobaczyć, gdzie idziemy tutaj: Jest łańcuch! Tak naprawdę nie mamy czterech niezależnych usług, mamy trzy usługi, które są powiązane i jedną, która jest wyłączona sama.

<center>

![Równoważenie usług razem][Image4]
</center>

Z powodu tego łańcucha jest możliwe, że brak równowagi w metryki 1-4 może spowodować repliki lub wystąpienia należące do usług 1-3 do poruszania się. Wiemy również, że brak równowagi w metrykach 1, 2 lub 3 nie może powodować ruchów w usłudze4. Nie byłoby sensu, ponieważ przeniesienie replik lub wystąpień należących do Service4 wokół nie może zrobić absolutnie nic, aby wpłynąć na równowagę metryki 1-3.

Menedżer zasobów klastra automatycznie wyliczył, jakie usługi są powiązane. Dodawanie, usuwanie lub zmienianie metryk dla usług może mieć wpływ na ich relacje. Na przykład między dwoma przebiegami równoważenia Service2 może zostać zaktualizowany, aby usunąć Metric2. Spowoduje to przerwanie łańcucha między Service1 i Service2. Teraz zamiast dwóch grup powiązanych usług, istnieją trzy:

<center>

![Równoważenie usług razem][Image5]
</center>

## <a name="next-steps"></a>Następne kroki
* Metryki są jak menedżer zasobów klastra sieci szkieletowej usług zarządza zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o danych i sposobie ich konfigurowania, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md)
* Koszt przemieszczenia jest jednym ze sposobów sygnalizowania Menedżerowi zasobów klastra, że niektóre usługi są droższe niż inne. Więcej informacji na temat kosztów ruchu można znaleźć w [tym artykule](service-fabric-cluster-resource-manager-movement-cost.md)
* Menedżer zasobów klastra ma kilka przepustów, które można skonfigurować, aby spowolnić zmiany w klastrze. Zwykle nie są one konieczne, ale jeśli ich potrzebujesz, możesz dowiedzieć się o nich [tutaj](service-fabric-cluster-resource-manager-advanced-throttling.md)
* Menedżer zasobów klastra może rozpoznawać i obsługiwać podklasterowanie (sytuacja, która czasami pojawia się podczas używania ograniczeń umieszczania i równoważenia). Aby dowiedzieć się, jak subklastrowanie może wpływać na równoważenie i jak sobie z tym radzić, zobacz [tutaj](cluster-resource-manager-subclustering.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
