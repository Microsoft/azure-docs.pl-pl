---
title: Architektura routingu z przodu platformy Azure | Microsoft Docs
description: Ten artykuł ułatwia zrozumienie elementu widoku globalnego architektury drzwi przednich.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: a088e52f742f96a13ba61969c2d7a6697c96b145
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879296"
---
# <a name="routing-architecture-overview"></a>Omówienie architektury routingu

Drzwi frontonu platformy Azure odbierają żądania klientów, a następnie odpowiadają na nie (Jeśli buforowanie jest włączone) lub przekazuje je do odpowiedniego zaplecza aplikacji (jako zwrotny serwer proxy).

</br>Istnieją możliwości optymalizacji ruchu w przypadku kierowania do frontonu platformy Azure, a także do przesyłania danych do innych celów.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Wybieranie środowiska front-drzwi dla routingu ruchu sieciowego

Kierowanie do środowisk czołowych platformy Azure wykorzystuje [emisje](https://en.wikipedia.org/wiki/Anycast) w systemie DNS (system nazw domen) i ruch HTTP (Hypertext Transfer Protocol), więc ruch użytkowników przechodzi do najbliższego środowiska w zakresie topologii sieci (najmniejszą liczbę przeskoków). Ta architektura zazwyczaj oferuje lepszą liczbę godzin rundy dla użytkowników końcowych (maksymalizując zalety dzielenia TCP). Drzwi przede wszystkim organizują swoje środowiska w podstawowe i rezerwowe "pierścienie".  Zewnętrzny pierścień ma środowiska, które są bliżej użytkowników, oferując krótsze opóźnienia.  Wewnętrzny pierścień zawiera środowiska, które mogą obsłużyć tryb failover dla środowiska zewnętrznego pierścienia w przypadku, gdy występuje problem. Zewnętrzny pierścień jest preferowanym elementem docelowym dla całego ruchu, ale wewnętrzny pierścień jest niezbędny do obsługi przepełnienia ruchu z pierścienia zewnętrznego. W odniesieniu do adresów VIP (adresy wirtualnych protokołów internetowych) każdy host frontonu lub domenę obsługiwane przez drzwiczki są przypisywane do podstawowego adresu VIP, który jest anonsowany przez środowiska zarówno z pierścieniem wewnętrznym, jak i zewnętrznym, jak również do rezerwowego adresu VIP, który jest anonsowany tylko przez środowiska w pierścieniu wewnętrznym. 

</br>Ta ogólna strategia zapewnia, że żądania od użytkowników końcowych zawsze docierają do najbliższego środowiska z przodu, a nawet jeśli preferowane środowisko z przodu jest w złej kondycji, ruch jest automatycznie przenoszony do następnego najbliższego środowiska.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Nawiązywanie połączenia z środowiskiem czołowym (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) to technika zmniejszania opóźnień i problemów z protokołem TCP przez przerwanie połączenia, które może pociągnąć za sobą wysoki czas błądzenia w mniejszych kawałkach.  Umieszczenie środowisk z przodu bliżej użytkowników końcowych i przerwanie połączeń TCP w środowisku przednim, jedno połączenie TCP z dużą ilością czasu błądzenia (RTT) do zaplecza aplikacji jest podzielone na dwa połączenia TCP. Krótkie połączenie między użytkownikiem końcowym i środowiskiem czołowym oznacza, że połączenie jest nawiązywane przez trzy krótkie podróże zamiast trzech długich rejsów, co oszczędza opóźnienie.  Długie połączenie między środowiskiem z drzwiami i zapleczem można wstępnie nawiązać i ponownie użyć w wielu wywołaniach użytkownika końcowego, a następnie zapisać czas połączenia TCP.  Efekt jest mnożony podczas ustanawiania połączenia SSL/TLS (Transport Layer Security), ponieważ istnieje więcej rund do zabezpieczenia połączenia.

## <a name="processing-request-to-match-a-routing-rule"></a>Przetwarzanie żądania zgodnego z regułą routingu
Po ustanowieniu połączenia i przeprowadzeniu uzgadniania protokołu TLS, gdy żądanie jest pokrywane ze środowiskiem czołowym, dopasowanie reguły routingu to pierwszy krok. Jest to zasadniczo zgodne ze wszystkimi konfiguracjami w drzwiach zewnętrznych, które są określone dla reguły routingu w celu dopasowania do żądania. Dowiedz się więcej o tym, jak drzwi z przodu są [zgodne](front-door-route-matching.md) , aby dowiedzieć się więcej.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identyfikowanie dostępnych zaplecza w puli zaplecza dla reguły routingu
Gdy przód są zgodne z regułą routingu opartą na żądaniu przychodzącym i jeśli nie istnieje buforowanie, następnym krokiem jest pociągnięcie stanu sondy kondycji dla puli zaplecza skojarzonej z dopasowaną trasą. Dowiedz się więcej o tym, jak tylne drzwi monitorują kondycję zaplecza przy użyciu [sond kondycji](front-door-health-probes.md) .

## <a name="forwarding-the-request-to-your-application-backend"></a>Przekazywanie żądania do zaplecza aplikacji
Na koniec przy założeniu, że nie skonfigurowano buforowania, żądanie użytkownika zostanie przekazane do "najlepszej" zaplecza na podstawie konfiguracji [metody routingu drzwi](front-door-routing-methods.md) .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
