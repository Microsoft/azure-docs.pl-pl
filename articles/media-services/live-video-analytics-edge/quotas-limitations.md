---
title: Analiza filmów wideo na żywo na IoT Edge przydziałów i ograniczeń — platforma Azure
description: W tym artykule opisano przydziały i ograniczenia dotyczące usługi wideo na żywo w IoT Edge.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: df1978de4ee1bbbe15d0df3b02a70fb51491e9d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90529234"
---
# <a name="quotas-and-limitations"></a>Limity przydziału i ograniczenia

W tym artykule opisano przydziały i ograniczenia dotyczące analizy filmów wideo na żywo w module IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Maksymalny okres odłączonego użycia

Moduł Edge może utrzymywać tymczasową utratę łączności sieciowej. Jeśli moduł pozostaje niepołączony przez ponad 36 godzin, dezaktywuje wszystkie wystąpienia grafów, które były uruchomione, a kolejne bezpośrednie wywołania metody zostaną zablokowane.

Aby wznowić działanie modułu brzegowego do stanu operacyjnego, należy przywrócić łączność sieciową, a moduł musi być w stanie pomyślnie komunikować się z kontem usługi Azure Media Service.

## <a name="maximum-number-of-graph-instances"></a>Maksymalna liczba wystąpień grafu

Dla każdego modułu można mieć co najwyżej 1000 wystąpień grafów (utworzonych za pośrednictwem GraphInstanceSet).

## <a name="maximum-number-of-graph-topologies"></a>Maksymalna liczba topologii grafu

W każdym module można mieć co najwyżej 50 topologii grafów (utworzonych za pośrednictwem GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Ograniczenia dotyczące topologii grafu w wersji zapoznawczej

W wersji zapoznawczej istnieją ograniczenia w różnych węzłach, które można połączyć ze sobą w topologii programu Media Graph.

* Źródło RTSP
   * Dozwolone jest tylko jedno źródło RTSP dla topologii wykresu.
* Procesor filtru szybkości klatek
   * Musi być od razu przechodząca od procesora lub od wykrywania ruchu.
   * Nie można użyć z procesora rozszerzenia HTTP lub gRPC.
   * Nie może być elementem nadrzędnym z procesora wykrywania ruchu.
* Procesor rozszerzeń HTTP
   * Może być co najwyżej jeden taki procesor na topologię grafu.
* Procesor rozszerzenia gRPC
   * Może być co najwyżej jeden taki procesor na topologię grafu.
* Procesor wykrywania ruchu
   * Musi być natychmiast od źródła RTSP.
   * Może być co najwyżej jeden taki procesor na topologię grafu.
   * Nie można użyć w ramach procesora HTTP lub gRPC rozszerzenia.
* Procesor bramy sygnałów
   * Musi być natychmiast od źródła RTSP.
* Ujścia zasobów 
   * Musi być od razu przypadać od źródła RTSP lub procesora bramy sygnałów.
* Ujścia plików
   * Musi być natychmiast od procesora sygnałów bramy.
   * Nie może być bezpośrednio przypadające na procesor rozszerzenia HTTP lub gRPC lub procesor wykrywania ruchu
* IoT Hub ujścia
   * Nie może być bezpośrednio elementem podrzędnym źródła danych IoT Hub.

Jeśli są używane węzły procesora wykrywania ruchu i częstotliwości filtrowania, powinny one znajdować się w tym samym łańcuchu węzłów prowadzących do węzła źródłowego RTSP.

## <a name="limitations-on-media-service-operations-at-preview"></a>Ograniczenia operacji usługi multimediów w wersji zapoznawczej

W momencie wydania wersji zapoznawczej analiza filmów wideo na żywo na IoT Edge nie obsługuje następujących elementów:

* Możliwość migrowania konta usługi multimediów z jednej subskrypcji do innej bez przerw w działaniu.
* Możliwość korzystania z więcej niż jednego konta magazynu z kontem usługi Media.
* Możliwość dynamicznego zmieniania informacji o jednostce usługi w odpowiednich właściwościach modułu bez ponownego uruchomienia.

Można używać tylko kamer IP obsługujących protokół RTSP. Kamery IP obsługujące protokół RTSP można znaleźć na stronie [ONVIF zgodne produkty](https://www.onvif.org/conformant-products) . Wyszukaj urządzenia zgodne z profilami G, S lub T.

Ponadto należy skonfigurować te aparaty do korzystania z wideo H. 264 i audio AAC. Inne kodery-dekoder nie są obecnie obsługiwane. 

## <a name="next-steps"></a>Następne kroki

[Omówienie](overview.md)
