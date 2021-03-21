---
title: Analiza filmów wideo na żywo na IoT Edge przydziałów i ograniczeń — platforma Azure
description: W tym artykule opisano przydziały i ograniczenia dotyczące usługi wideo na żywo w IoT Edge.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97400528"
---
# <a name="quotas-and-limitations"></a>Limity przydziału i ograniczenia

W tym artykule opisano przydziały i ograniczenia dotyczące analizy filmów wideo na żywo w module IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Maksymalny okres odłączonego użycia

Moduł Edge może utrzymywać tymczasową utratę łączności z Internetem. Jeśli moduł pozostaje niepołączony przez ponad 36 godzin, dezaktywuje wszystkie uruchomione wystąpienia grafu. Wszystkie dalsze wywołania metody bezpośredniej zostaną zablokowane.

Aby wznowić działanie modułu brzegowego do stanu operacyjnego, należy przywrócić połączenie z Internetem, aby moduł mógł pomyślnie komunikować się z kontem usługi Azure Media.

## <a name="maximum-number-of-graph-instances"></a>Maksymalna liczba wystąpień grafu

Dla każdego modułu (utworzonego za pośrednictwem GraphInstanceSet) obsługiwane są maksymalnie 1000 wystąpienia grafu.

## <a name="maximum-number-of-graph-topologies"></a>Maksymalna liczba topologii grafu

Dla każdego modułu (utworzonego za pośrednictwem GraphTopologySet) obsługiwane są maksymalnie 50 topologii wykresów.

## <a name="limitations-on-graph-topologies-at-preview"></a>Ograniczenia dotyczące topologii grafu w wersji zapoznawczej

W wersji zapoznawczej istnieją ograniczenia w różnych węzłach, które można połączyć ze sobą w topologii programu Media Graph.

* Źródło RTSP
   * Dozwolone jest tylko jedno źródło RTSP dla topologii wykresu.
* Procesor wykrywania ruchu
   * Musi być natychmiast od źródła RTSP.
   * Nie można użyć w ramach procesora HTTP lub gRPC rozszerzenia.
* Procesor bramy sygnałów
   * Musi być natychmiast od źródła RTSP.
* Ujścia zasobów 
   * Musi być od razu przypadać od źródła RTSP lub procesora bramy sygnałów.
* Ujścia plików
   * Musi być natychmiast od procesora sygnałów bramy.
   * Nie można bezpośrednio przypadać na procesor rozszerzenia HTTP lub gRPC lub procesor wykrywania ruchu
* IoT Hub ujścia
   * Nie może być bezpośrednio elementem podrzędnym źródła danych IoT Hub.

## <a name="limitations-on-media-service-operations-at-preview"></a>Ograniczenia operacji usługi multimediów w wersji zapoznawczej

W momencie wydania wersji zapoznawczej analiza filmów wideo na żywo na IoT Edge nie obsługuje następujących elementów:

* Możliwość migrowania konta usługi multimediów z jednej subskrypcji do innej bez przerw w działaniu.
* Możliwość korzystania z więcej niż jednego konta magazynu z kontem usługi Media.
* Możliwość dynamicznego zmieniania informacji o jednostce usługi w odpowiednich właściwościach modułu bez ponownego uruchomienia.

Można używać tylko kamer IP obsługujących protokół RTSP. Kamery IP obsługujące protokół RTSP można znaleźć na stronie [ONVIF zgodne produkty](https://www.onvif.org/conformant-products) . Wyszukaj urządzenia zgodne z profilami G, S lub T.

Ponadto należy skonfigurować te aparaty do korzystania z wideo H. 264 i audio AAC. Inne kodery-dekoder nie są obecnie obsługiwane. 

## <a name="next-steps"></a>Następne kroki

[Omówienie](overview.md)
