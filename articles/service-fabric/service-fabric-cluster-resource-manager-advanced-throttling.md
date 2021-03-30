---
title: Ograniczanie przepustowości w Menedżerze zasobów klastra Service Fabric
description: Dowiedz się, jak skonfigurować ograniczenia zapewniane przez Menedżer zasobów klastra Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75452172"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Ograniczanie Menedżer zasobów klastra Service Fabric
Nawet jeśli klaster Menedżer zasobów prawidłowo skonfigurowany, może to spowodować zakłócenie klastra. Na przykład mogą wystąpić jednoczesne awarie domen węzła i błędów — co się stanie, jeśli wystąpił podczas uaktualniania? Klaster Menedżer zasobów zawsze próbuje naprawić wszystko, zużywając zasoby klastra próbujące zreorganizować i naprawić klaster. Ograniczenia ograniczają wydajność w celu zapewnienia, że klaster może korzystać z zasobów w celu stabilizacji — te węzły zostaną przywrócone, a partycje sieciowe zostały poprawione i wdrożone.

Aby pomóc w tym sortowaniu sytuacji, klaster Service Fabric Menedżer zasobów obejmuje kilka przepustnic. Te ograniczenia są dość dużymi młotami. Zazwyczaj nie należy ich zmieniać bez dokładnego planowania i testowania.

W przypadku zmiany ograniczenia przepustowości Menedżer zasobów klastra należy je dostosować do oczekiwanego rzeczywistego obciążenia. Możesz określić, że trzeba mieć pewne ograniczenia, nawet jeśli oznacza to, że w niektórych sytuacjach klaster trwa dłużej. Do określenia poprawnych wartości ograniczania są wymagane testy. Ograniczenia muszą być wystarczająco duże, aby umożliwić klastrowi reagowanie na zmiany w rozsądnym czasie, a ich niska ilość, aby faktycznie uniknąć zbyt dużej ilości zasobów. 

W większości przypadków, w których klienci korzystali z ograniczania przepustowości, ponieważ znajdowały się one już w środowisku ograniczonym do zasobów. Niektóre przykłady byłyby ograniczoną przepustowością sieci dla poszczególnych węzłów lub dysków, które nie mogą równolegle kompilować wiele replik stanowych ze względu na ograniczenia przepływności. Bez ograniczania, operacje mogą przeciążyć te zasoby, powodując niepowodzenie operacji lub powolne. W takich sytuacjach klienci korzystali z ograniczania i wiedziały, że przekroczą czas, przez jaki klaster osiągnął stabilny stan. Klienci rozumieją również, że mogą oni zakończyć działanie o niższej ogólnej niezawodności, podczas gdy zostały ograniczone.


## <a name="configuring-the-throttles"></a>Konfigurowanie ograniczania przepustowości

Service Fabric ma dwa mechanizmy ograniczania liczby przesunięć między replikami. Domyślny mechanizm, który istniał przed Service Fabric 5,7 reprezentuje ograniczenie jako bezwzględną liczbę dozwolonych ruchów. Nie działa to w przypadku klastrów o wszystkich rozmiarach. W szczególności w przypadku dużych klastrów wartość domyślna może być zbyt mała, co znacznie spowalnia równoważenie nawet wtedy, gdy jest to konieczne, ale nie ma wpływu na mniejsze klastry. Ten wcześniejszy mechanizm został zastąpiony przez procentowe ograniczenie przepustowości, które można skalować lepiej przy użyciu klastrów dynamicznych, w których liczba usług i węzłów regularnie zmienia się.

Ograniczenia są oparte na procentach liczby replik w klastrach. Ograniczenia oparte na procentach umożliwiają wyrażenie zasad: "nie przenoś więcej niż 10% replik w przypadku 10-minutowego interwału".

Ustawienia konfiguracji dla ograniczania procentowo są następujące:

  - GlobalMovementThrottleThresholdPercentage — Maksymalna liczba przesunięć dozwolonych w klastrze w dowolnym momencie wyrażona jako procent łącznej liczby replik w klastrze. wartość 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli oba te ustawienia i GlobalMovementThrottleThreshold są określone, zostanie użyty bardziej ostrożny limit.
  - GlobalMovementThrottleThresholdPercentageForPlacement — Maksymalna liczba przesunięć dozwolona w fazie umieszczania wyrażona jako procent łącznej liczby replik w klastrze. wartość 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli oba te ustawienia i GlobalMovementThrottleThresholdForPlacement są określone, zostanie użyty bardziej ostrożny limit.
  - GlobalMovementThrottleThresholdPercentageForBalancing — Maksymalna liczba przesunięć dozwolona w fazie równoważenia, wyrażona jako procent łącznej liczby replik w klastrze. wartość 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli oba te ustawienia i GlobalMovementThrottleThresholdForBalancing są określone, zostanie użyty bardziej ostrożny limit.

Podczas określania wartości procentowej ograniczenia przepustowości należy określić 5% jako 0,05. Interwałem, w którym podlegają te ograniczenia, jest GlobalMovementThrottleCountingInterval, który jest określony w sekundach.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

za pomocą ClusterConfig.jsna potrzeby wdrożeń autonomicznych lub Template.jsna potrzeby klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Domyślne ograniczenia oparte na liczbie
Te informacje są dostępne w przypadku starszych klastrów lub nadal zachowują te konfiguracje w klastrach, które zostały uaktualnione. Ogólnie rzecz biorąc, zaleca się, aby były one zastępowane powyżej opartych na procentach ograniczeniach. Ponieważ ograniczenie procentowe jest wyłączone domyślnie, te ograniczenia pozostają domyślnymi ograniczeniami klastra, dopóki nie zostaną wyłączone i zastąpione ograniczeniami procentowymi. 

  - GlobalMovementThrottleThreshold — to ustawienie steruje całkowitą liczbą przesunięć w klastrze przez jakiś czas. Ilość czasu jest określona w sekundach jako GlobalMovementThrottleCountingInterval. Wartość domyślna GlobalMovementThrottleThreshold to 1000, a wartość domyślna dla GlobalMovementThrottleCountingInterval to 600.
  - MovementPerPartitionThrottleThreshold — to ustawienie steruje łączną liczbą przesunięć dla każdej partycji usługi w pewnym czasie. Ilość czasu jest określona w sekundach jako MovementPerPartitionThrottleCountingInterval. Wartość domyślna MovementPerPartitionThrottleThreshold to 50, a wartość domyślna dla MovementPerPartitionThrottleCountingInterval to 600.

Konfiguracja tych ograniczeń jest zgodna z tym samym wzorcem co ograniczenie procentowe.

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się, w jaki sposób klaster Menedżer zasobów zarządza i równoważenia obciążenia w klastrze, zapoznaj się z artykułem dotyczącym [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Klaster Menedżer zasobów ma wiele opcji opisywania klastra. Aby dowiedzieć się więcej na ten temat, zapoznaj się z tym artykułem na temat [opisywania Service Fabric klastra](service-fabric-cluster-resource-manager-cluster-description.md)
