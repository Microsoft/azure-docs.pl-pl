---
title: Równoważenie metryk subklastrowanych
description: Wpływ ograniczeń dotyczących rozmieszczenia na równoważenie i sposób radzenia sobie z nim
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430645"
---
# <a name="balancing-of-subclustered-metrics"></a>Równoważenie metryk subklastrowanych

## <a name="what-is-subclustering"></a>Co to jest subklastring

Podklastrowanie ma miejsce, gdy usługi z różnymi ograniczeniami umieszczania mają wspólną metrykę i obie zgłaszają obciążenie dla niego. Jeśli obciążenie zgłaszane przez usługi różni się znacznie, całkowite obciążenie węzłów będzie miało duże odchylenie standardowe i wygląda na to, że klaster jest niezrównoważony, nawet jeśli ma najlepszą możliwą równowagę.

## <a name="how-subclustering-affects-load-balancing"></a>Jak podklasterowanie wpływa na równoważenie obciążenia

Jeśli obciążenie zgłaszane przez usługi w różnych węzłach różni się znacznie, może się wydawać, że występuje duża nierównowaga, w której istnieje brak. Ponadto jeśli false nierównowagi spowodowane przez subclustering jest większy niż rzeczywista nierównowaga, ma potencjał, aby mylić algorytm równoważenia Menedżera zasobów i do produkcji nieoptymalne równowagi w klastrze.

Załóżmy na przykład, że mamy cztery usługi i wszystkie zgłaszają obciążenie metryki Metric1:

* Usługa A – ma ograniczenie umieszczania "NodeType==Type1", zgłasza obciążenie 10
* Usługa B – ma ograniczenie umieszczania "NodeType==Type1", zgłasza obciążenie 10
* Usługa C – ma ograniczenie umieszczania "NodeType==Type2", zgłasza obciążenie 100
* Usługa D – ma ograniczenie umieszczania "NodeType==Type2", zgłasza obciążenie 100
* Mamy cztery węzły. Dwa z nich mają NodeType ustawiony jako "Type1", a pozostałe dwa to "Type2"

I mamy następujące miejsce:

<center>

![Przykład rozmieszczenia z podklastrowanym][Image1]
</center>

Klaster może wyglądać niezrównoważony, mamy duże obciążenie dla węzłów 3 i 4, ale to miejsce docelowe tworzy najlepszą możliwą równowagę w tej sytuacji.

Menedżer zasobów może rozpoznawać sytuacje podklasteringowe i w prawie wszystkich przypadkach może uzyskać optymalną równowagę dla danej sytuacji.

W niektórych wyjątkowych sytuacjach, gdy Menedżer zasobów nie jest w stanie optymalnie zrównoważyć subklastrowanej metryki, nadal będzie wykrywał subkluzyny i wygeneruje raport o kondycji, aby doradzić ci rozwiązać problem.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Rodzaje podklusteringu i sposób ich obsługi

Sytuacje podklastrowania można podzielić na trzy różne kategorie. Kategoria określonej sytuacji podklasterowania określa sposób obsługi menedżera zasobów.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Pierwsza kategoria – płaska podkluczenie z rozłączne grupy węzłów

Ta kategoria ma najprostszą formę podkluza, gdzie węzły mogą być podzielone na różne grupy i każda usługa może być umieszczona tylko w węzłach w jednej z tych grup. Każdy węzeł należy do jednej grupy i tylko jednej grupy. Sytuacja opisana powyżej należy do tej kategorii, podobnie jak większość sytuacji subkluzywnych. 

W sytuacjach w tej kategorii Menedżer zasobów może uzyskać optymalną równowagę i nie jest wymagana żadna dalsza interwencja.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Druga kategoria – podklastrowanie z hierarchicznymi grupami węzłów

Taka sytuacja ma miejsce, gdy grupa węzłów dozwolonych dla jednej usługi jest podzbiorem grupy węzłów dozwolonych dla innej usługi. Najczęstszym przykładem tej sytuacji jest, gdy niektóre usługi ma zdefiniowane ograniczenie umieszczania i innej usługi nie ma ograniczenia umieszczania i mogą być umieszczone w dowolnym węźle.

Przykład:

* Usługa A: brak ograniczenia umieszczania
* Usługa B: ograniczenie umieszczania "NodeType==Type1"
* Usługa C: ograniczenie umieszczania "NodeType==Type2"

Ta konfiguracja tworzy relację podzestawu między grupami węzłów dla różnych usług.

<center>

![Podgromaż podzbiorem podzbioru podzbioru podzbioru][Image2]
</center>

W tej sytuacji istnieje szansa, że zostanie wykonana nieoptymalna równowaga.

Menedżer zasobów rozpozna tę sytuację i sporządzi raport o kondycji z informacją o podziale usługi A na dwie usługi — usługę A1, którą można umieścić w węzłach typu1 i usługę A2, która może być umieszczona w węzłach Type2. To doprowadzi nas z powrotem do pierwszej kategorii sytuacji, które mogą być zrównoważone optymalnie.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Trzecia kategoria – podklasterowanie z częściowym nakładaniem się między zestawami węzłów

Taka sytuacja ma miejsce, gdy istnieje częściowe nakładanie się między zestawami węzłów, na których można umieścić niektóre usługi.

Na przykład jeśli mamy właściwość węzła o nazwie NodeColor i mamy trzy węzły:

* Węzeł 1: NodeColor=Czerwony
* Węzeł 2: NodeColor=Niebieski
* Węzeł 2: NodeColor=Zielony

I mamy dwie usługi:

* Usługa A: z ograniczeniem umieszczania "Color==Red || Color==Niebieski"
* Usługa B: z ograniczeniem umieszczania "Color==Blue || Color==Zielony"

Z tego powodu usługa A może być umieszczona w węzłach 1 i 2, a usługa B może być umieszczona w węzłach 2 i 3.

W tej sytuacji istnieje szansa, że zostanie wykonana nieoptymalna równowaga.

Menedżer zasobów rozpozna tę sytuację i sporządzi raport o kondycji z informacją o podziale niektórych usług.

W tej sytuacji Menedżer zasobów nie jest w stanie podać propozycję, jak podzielić usługi, ponieważ można wykonać wiele podziałów i nie ma sposobu, aby oszacować, który sposób byłby optymalny do podziału usług.

## <a name="configuring-subclustering"></a>Konfigurowanie podklastrowania

Zachowanie Menedżera zasobów dotyczące podklasteringu można zmodyfikować, modyfikując następujące parametry konfiguracji:
* SubclusteringEnabled - parametr określa, czy Menedżer zasobów weźmie podklastering pod uwagę podczas równoważenia obciążenia. Jeśli ten parametr jest wyłączony, Menedżer zasobów zignoruje podklastering i spróbuje osiągnąć optymalną równowagę na poziomie globalnym. Domyślna wartość tego parametru jest false.
* SubclusteringReportingPolicy — określa, jak Menedżer zasobów będzie emitować raporty kondycji dla hierarchicznego i częściowego nakładania subkluzyny. Wartość zero oznacza, że raporty dotyczące kondycji dotyczące subkluzyzny są wyłączone, "1" oznacza, że raporty zdrowotne ostrzeżenia będą tworzone dla nieoptymalnych sytuacji subkluzywnych, a wartość "2" będzie tworzyć "OK" raporty zdrowotne. Wartością domyślną dla tego parametru jest "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

za pośrednictwem clusterconfig.json dla wdrożeń autonomicznych lub Template.json dla klastrów hostowanych platformy Azure:

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
* Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zapoznaj się z artykułem na temat [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
* Aby dowiedzieć się, w jaki sposób usługi mogą być ograniczone tylko do umieszczenia w niektórych węzłach, zobacz [Właściwości węzła i ograniczenia umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
