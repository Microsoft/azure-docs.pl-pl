---
title: Równoważenie metryk klastrowanych
description: Efekt ograniczeń umieszczania na potrzeby równoważenia i sposobu ich obsługi
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82183124"
---
# <a name="balancing-of-subclustered-metrics"></a>Równoważenie metryk klastrowanych

## <a name="what-is-subclustering"></a>Co to jest klastrowanie

Klastrowanie jest wykonywane, gdy usługi z różnymi ograniczeniami umieszczania mają wspólną metrykę i ładują je. Jeśli obciążenie zgłoszone przez usługi różni się znacznie, całkowite obciążenie węzłów będzie miało duże odchylenie standardowe i będzie wyglądać tak, jakby klaster został niezrównoważony, nawet jeśli ma najlepsze możliwe saldo.

## <a name="how-subclustering-affects-load-balancing"></a>Jak klastrowanie ma wpływ na Równoważenie obciążenia

Jeśli obciążenie zgłoszone przez usługi w różnych węzłach różni się znacznie, może to wyglądać tak, jak w przypadku braku. Ponadto, jeśli fałszywa nierównoważność spowodowana przez klastrowanie jest większa niż faktyczny niezrównoważony, może to pomylić algorytm równoważenia Menedżer zasobów i utworzyć nieoptymalną równowagę w klastrze.

Załóżmy na przykład, że mamy cztery usługi, a wszystkie raporty załadują Metric1 metryki:

* Usługa A — ma ograniczenie umieszczania "NodeType = = fronton", zgłasza obciążenie 10
* Usługa B — ma ograniczenie umieszczania "NodeType = = fronton", zgłasza obciążenie 10
* Usługa C — ma ograniczenie umieszczania "NodeType = = zaplecze", zgłasza obciążenie 100
* Usługa D — ma ograniczenie umieszczania "NodeType = = zaplecze", zgłasza obciążenie 100
* Mamy cztery węzły. Dwa z nich mają NodeType ustawiony jako "fronton", a pozostałe dwa są "zapleczem"

Dostępne są następujące elementy:

<center>

![Przykład umieszczania klastrowanego klastra][Image1]
</center>

Klaster może wyglądać bez równoważenia i ma duże obciążenie węzłów 3 i 4, ale to umieszczenie w tej sytuacji umożliwia utworzenie najlepszego możliwego salda.

Menedżer zasobów może rozpoznać sytuacje klastrowania i prawie wszystkie przypadki mogą generować optymalną równowagę w danej sytuacji.

W przypadku niektórych wyjątkowych sytuacji, gdy Menedżer zasobów nie jest w stanie optymalnie zrównoważyć metrykę klastrowaną, nadal będzie wykrywał klaster i wygeneruje raport o kondycji w celu poinformowania o rozwiązaniu problemu.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Typy podklastrów i sposób ich obsługi

Sytuacje związane z klastrowaniem mogą być klasyfikowane do trzech różnych kategorii. Kategoria konkretnej sytuacji klastra określa, jak będzie ona obsługiwana przez Menedżer zasobów.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Pierwsza kategoria — płaskie podklastry z rozłączonymi grupami węzłów

Ta kategoria ma najprostszą postać klastrów, w której węzły mogą być rozdzielone na różne grupy, a każda usługa może być umieszczana tylko w węzłach w jednej z tych grup. Każdy węzeł należy do jednej grupy i tylko jedną grupę. Opisana powyżej sytuacja należy do tej kategorii jak w większości sytuacji klastra. 

W przypadku sytuacji w tej kategorii Menedżer zasobów mogą generować optymalne saldo i nie jest wymagana żadna dodatkowa interwencja.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Druga kategoria — klastrowanie przy użyciu hierarchicznych grup węzłów

Ta sytuacja występuje, gdy grupa węzłów dozwolona dla jednej usługi jest podzbiorem grupy węzłów dozwolonych dla innej usługi. Najbardziej typowym przykładem takiej sytuacji jest to, że niektóre usługi mają zdefiniowane ograniczenie umieszczania, a inna usługa nie ma ograniczenia umieszczania i można ją umieścić na dowolnym węźle.

Przykład:

* Usługa A: brak ograniczenia umieszczania
* Usługa B: ograniczenie umieszczania "NodeType = = fronton"
* Usługa C: ograniczenie umieszczania "NodeType = = zaplecze"

Ta konfiguracja tworzy relację podzbioru podzestawu między grupami węzłów dla różnych usług.

<center>

![Podklastry podzbioru podzestawu][Image2]
</center>

W takiej sytuacji istnieje prawdopodobieństwo, że nastąpi nieoptymalny bilans.

Menedżer zasobów rozpoznają tę sytuację i generują raport o kondycji z zaleceniem podziału usługi A na dwie usługi — usługi a1, która może zostać umieszczona w węzłach frontonu i w usłudze a2, które mogą być umieszczone w węzłach zaplecza. Spowoduje to powrót do pierwszej sytuacji kategorii, która może być zrównoważona optymalnie.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Trzecia kategoria — klastrowanie ze częściowo rozłożeniem między zestawami węzłów

Taka sytuacja występuje, gdy istnieje częściowa pokrywanie się między zestawami węzłów, na których można umieścić niektóre usługi.

Na przykład jeśli mamy właściwość węzła o nazwie NodeColor i mamy trzy węzły:

* Node 1: NodeColor = czerwony
* Node 2: NodeColor = Blue
* Node 2: NodeColor = zielony

Mamy dwie usługi:

* Usługa A: z ograniczeniem umieszczania "Color = = Red | | Color = = Blue "
* Usługa B: z ograniczeniem umieszczania "Color = = Blue | | Color = = zielony "

W związku z tym usługa A może być umieszczona w węzłach 1 i 2, a usługa B może być umieszczona w węzłach 2 i 3.

W takiej sytuacji istnieje prawdopodobieństwo, że nastąpi nieoptymalny bilans.

Menedżer zasobów będzie rozpoznawać tę sytuację i generować raport o kondycji z zaleceniem podzielenia niektórych usług.

W tej sytuacji Menedżer zasobów nie jest w stanie udzielić propozycji, jak podzielić usługi, ponieważ można wykonać wiele operacji dzielenia i nie ma możliwości oszacowania, który sposób będzie optymalny, aby podzielić usługi.

## <a name="configuring-subclustering"></a>Konfigurowanie klastrowania

Zachowanie Menedżer zasobów informacji o podklastrach można zmodyfikować, modyfikując następujące parametry konfiguracji:
* SubclusteringEnabled — określa, czy podczas równoważenia obciążenia usługa Menedżer zasobów będzie uwzględniać klastrowanie. Jeśli ten parametr jest wyłączony, Menedżer zasobów zignoruje klaster i podejmie próbę osiągnięcia optymalnego salda na poziomie globalnym. Wartość domyślna tego parametru to false.
* SubclusteringReportingPolicy — określa, w jaki sposób Menedżer zasobów będzie emitować raporty dotyczące kondycji w przypadku klastrowania hierarchicznego i częściowego nakładania się. Wartość zerowa oznacza, że raporty kondycji dotyczące klastrów są wyłączone. wartość "1" oznacza, że raporty kondycji ostrzegawczej zostaną wygenerowane w przypadku nieoptymalnych sytuacji klastra, a wartością "2" będzie generowanie raportów kondycji "OK". Wartość domyślna tego parametru to "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

za pomocą ClusterConfig.jsna potrzeby wdrożeń autonomicznych lub Template.jsna potrzeby klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się, w jaki sposób klaster Menedżer zasobów zarządza i równoważenia obciążenia w klastrze, zapoznaj się z artykułem dotyczącym [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
* Aby dowiedzieć się, w jaki sposób usługi mogą być ograniczone tylko do określonych węzłów [, zobacz Właściwości węzła i ograniczenia umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
