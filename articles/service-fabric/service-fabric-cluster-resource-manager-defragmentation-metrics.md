---
title: Defragmentacja metryk w usłudze Azure Service Fabric
description: Dowiedz się więcej o korzystaniu z defragmentacji lub pakowania jako strategii dla metryk w Service Fabric. Ta technika jest przydatna w przypadku bardzo dużych usług.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6dee9a845df305b06153d99c2bafe6d74524a139
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449207"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentacja metryk i obciążenia w Service Fabric
Domyślna strategia zarządzania metrykami obciążenia w klastrze Service Fabric Menedżer zasobów klastra polega na rozproszeniu obciążenia. Upewnienie się, że węzły są z bezwzględnie wykorzystane, pozwala uniknąć gorąca i zimnych punktów, które prowadzą do rywalizacji o zawartość i zasoby. Rozkładanie obciążeń w klastrze jest również najbezpieczniejszą wartością w przypadku awarii, ponieważ gwarantuje to, że błąd nie zajmie znaczną częścią danego obciążenia. 

Menedżer zasobów klastra Service Fabric obsługuje inną strategię zarządzania ładowaniem, która jest defragmentacją. Defragmentacja oznacza, że zamiast próbować rozpowszechnić wykorzystanie metryki w klastrze, jest on skonsolidowany. Konsolidacja jest tylko niewersją domyślnej strategii równoważenia obciążenia — zamiast minimalizowania średniego odchylenia standardowego w obciążeniu metryk, klaster Menedżer zasobów próbuje go zwiększyć.

## <a name="when-to-use-defragmentation"></a>Kiedy używać defragmentacji
Dystrybucja obciążenia w klastrze zużywa część zasobów w każdym węźle. Niektóre obciążenia tworzą usługi, które są wyjątkowo duże i zużywają większość lub wszystkie węzły. W takich przypadkach istnieje możliwość, że w przypadku tworzenia dużych obciążeń nie ma wystarczającej ilości miejsca na każdym węźle, aby je uruchomić. Duże obciążenia nie są problemem w Service Fabric; w takich przypadkach klaster Menedżer zasobów określa, że należy zreorganizować klaster, aby zwolnić miejsce na duże obciążenie. Jednak w międzyczasie czas oczekiwania na zaplanowanie obciążenia w klastrze.

Jeśli istnieje wiele usług i stan, które należy przenieść, może to zająć dużo czasu, aby duże obciążenie było umieszczane w klastrze. Jest to bardziej podobne, jeśli inne obciążenia w klastrze są również duże i nie trzeba ich reorganizować. Zespół Service Fabric przecenił czasy tworzenia w symulacjich tego scenariusza. Znaleźliśmy, że tworzenie dużych usług trwało znacznie dłużej, gdy wykorzystanie klastra pozostało powyżej 30% i 50%. Aby obsłużyć ten scenariusz, wprowadziliśmy defragmentację jako strategię równoważenia. Znaleźliśmy, że w przypadku dużych obciążeń, szczególnie w przypadku, gdy czas utworzenia był istotny, defragmentacja naprawdę pomogła w zaplanowaniu obsługi nowych obciążeń w klastrze.

Metryki defragmentacji można skonfigurować tak, aby klaster Menedżer zasobów aktywnie próbować zaciążać obciążenie usług do mniejszej liczby węzłów. Pozwala to zagwarantować, że prawie zawsze jest pomieszczenie dla dużych usług bez konieczności restrukturyzacji klastra. Niekonieczność ponownego organizowania klastra pozwala szybko tworzyć duże obciążenia.

Większość osób nie potrzebuje defragmentacji. Usługi są zwykle małe, dlatego nie trudno znaleźć pokoju dla nich w klastrze. Gdy jest możliwe przeprowadzenie reorganizacji, będzie ona szybko dostępna, ponieważ większość usług jest niewielka i można ją przenieść szybko i równolegle. Jeśli jednak masz duże usługi i chcesz je szybko utworzyć, strategia defragmentacji jest dla Ciebie. Omawiamy wady dotyczące korzystania z defragmentacji dalej. 

## <a name="defragmentation-tradeoffs"></a>Wady defragmentacji
Defragmentacja może zwiększyć impactfulness błędów, ponieważ więcej usług jest uruchomionych w węzłach, które nie powiodły się. Defragmentacja może również zwiększyć koszty, ponieważ zasoby w klastrze muszą być przechowywane w rezerwie, czekając na tworzenie dużych obciążeń.

Na poniższym diagramie przedstawiono wizualną reprezentację dwóch klastrów, które są pofragmentowane i jeden z nich nie jest. 

<center>

![Porównywanie zrównoważonych i pofragmentowanych klastrów][Image1]
</center>

W zrównoważonym przypadku należy wziąć pod uwagę liczbę przesunięć, które byłyby konieczne do umieszczenia jednego z największych obiektów usługi. W przypadku klastra z defragmentacją duże obciążenie może być umieszczane na węzłach w czterech lub pięciu, bez konieczności oczekiwania na przeniesienie innych usług.

## <a name="defragmentation-pros-and-cons"></a>Zalety i wady defragmentacji
Co to są te inne kompromisy związane z koncepcją? Poniżej przedstawiono szybką tabelę rzeczy, które należy wziąć pod uwagę:

| Zalety defragmentacji | Wady defragmentacji |
| --- | --- |
| Umożliwia szybsze tworzenie dużych usług |Koncentruje się na obciążeniu mniejszą liczbą węzłów, zwiększając rywalizację |
| Włącza niższy ruch danych podczas tworzenia |Awarie mogą mieć wpływ na więcej usług i spowodować zwiększenie liczby operacji |
| Umożliwia rozbudowany opis wymagań i odzyskiwanie miejsca |Bardziej złożona ogólna Konfiguracja zarządzania zasobami |

Można mieszać pofragmentowane i normalne metryki w tym samym klastrze. Klaster Menedżer zasobów próbuje skonsolidować metryki defragmentacji tak, jak to możliwe, podczas rozpraszania innych. Wyniki mieszania i strategie dotyczące rozróżniania są zależne od kilku czynników, w tym:
  - Liczba metryk równoważenia obciążenia a liczba metryk defragmentacji
  - Czy jakakolwiek usługa używa obu typów metryk 
  - wagi metryk
  - bieżące obciążenia metryk
  
Eksperymentowanie jest wymagane do określenia dokładnej konfiguracji. Zalecamy dokładne pomiary obciążeń przed włączeniem metryk defragmentacji w środowisku produkcyjnym. Jest to szczególnie prawdziwe w przypadku mieszania defragmentacji i zrównoważonych metryk w ramach tej samej usługi. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurowanie metryk defragmentacji
Konfigurowanie metryk defragmentacji jest globalną decyzją w klastrze, a poszczególne metryki można wybrać do defragmentacji. Poniższe fragmenty konfiguracji pokazują, jak skonfigurować metryki na potrzeby defragmentacji. W tym przypadku "Metric1" jest skonfigurowany jako Metryka defragmentacji, podczas gdy "Metric2" będzie nadal zrównoważony. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

za pomocą ClusterConfig.jsna potrzeby wdrożeń autonomicznych lub Template.jsna potrzeby klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Następne kroki
- Klaster Menedżer zasobów ma wiele opcji opisywania klastra. Aby dowiedzieć się więcej na ten temat, zapoznaj się z tym artykułem na temat [opisywania Service Fabric klastra](service-fabric-cluster-resource-manager-cluster-description.md)
- Metryki to sposób, w jaki Menedżer zasobów klastra Service Fabric zarządza zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o metrykach i sposobach ich konfigurowania, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
