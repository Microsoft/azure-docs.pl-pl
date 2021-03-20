---
title: Architektura routingu z przodu platformy Azure | Microsoft Docs
description: Ten artykuł ułatwia zrozumienie elementu widoku globalnego architektury drzwi przednich.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449186"
---
# <a name="routing-architecture-overview"></a>Omówienie architektury routingu

Gdy drzwiczki frontonu platformy Azure odbierają żądania klientów, wykonają jedną z dwóch rzeczy. Odpowiedz je, jeśli włączysz buforowanie lub przekażesz je do odpowiedniego zaplecza aplikacji jako zwrotnego serwera proxy.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Wybieranie środowiska front-drzwi dla routingu ruchu sieciowego

Ruch kierowany do środowisk czołowych platformy Azure używa [emisji dowolnej](https://en.wikipedia.org/wiki/Anycast) dla ruchu systemu DNS (Domain Name System) i protokołu HTTP (Hypertext Transfer Protocol), który pozwala na żądania użytkowników do najbliższego środowiska w najmniej przeskokach sieci. Ta architektura oferuje lepszą liczbę godzin rundy dla użytkowników końcowych, maksymalizując zalety dzielenia protokołu TCP. Drzwi przede wszystkim organizują swoje środowiska w podstawowe i rezerwowe "pierścienie". Zewnętrzny pierścień ma środowiska, które są bliżej użytkowników, oferując krótsze opóźnienia.  Pierścień wewnętrzny zawiera środowiska, które mogą obsłużyć tryb failover dla środowiska zewnętrznego pierścienia w przypadku wystąpienia jakichkolwiek problemów. Zewnętrzny pierścień jest preferowanym elementem docelowym dla całego ruchu, a pierścień wewnętrzny ma obsłużyć przepełnienie ruchu z pierścienia zewnętrznego. Każdy host frontonu lub każda domena obsługiwana przez drzwiczki są przypisane do podstawowego adresu VIP (wirtualne adresy IP), który jest anonsowany przez środowiska zarówno w przypadku pierścienia wewnętrznego, jak i zewnętrznego. Rezerwowy adres VIP jest anonsowany tylko przez środowiska w pierścieniu wewnętrznym. 

Ta architektura zapewnia, że żądania od użytkowników końcowych zawsze docierają do najbliższego środowiska z przodu. Nawet jeśli preferowane środowisko z przodu jest w złej kondycji, cały ruch jest automatycznie przenoszony do następnego najbliższego środowiska.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Nawiązywanie połączenia z środowiskiem czołowym (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) to technika zmniejszania opóźnień i problemów z protokołem TCP przez przerwanie połączenia, które może pociągnąć za sobą wysoki czas błądzenia w mniejszych kawałkach. W przypadku środowisk przed drzwiami bliżej użytkowników końcowych połączenia TCP kończą się w środowisku ze drzwiami. Połączenie TCP, które ma duże czasu rundy (RTT) do zaplecza aplikacji, jest podzielone na dwa oddzielne połączenia. "Krótkie połączenie" między użytkownikiem końcowym i środowiskiem czołowym oznacza, że połączenie jest nawiązywane przez trzy krótkie podróże zamiast trzech długich rejsów, co skutkuje zapisaniem opóźnienia. "Długie połączenie" między środowiskiem przednim i zapleczem można wstępnie nawiązać, a następnie ponownie użyć w innych żądaniach użytkowników końcowych. zaoszczędź czas łączności. Efekt dzielenia protokołu TCP jest mnożony podczas ustanawiania połączenia SSL/TLS (Transport Layer Security), ponieważ istnieje więcej rund do zabezpieczania połączenia.

## <a name="processing-request-to-match-a-routing-rule"></a>Przetwarzanie żądania zgodnego z regułą routingu
Po ustanowieniu połączenia i zakończeniu uzgadniania protokołu TLS pierwszy krok po stronie żądania w środowisku przednim jest zgodny z regułą routingu. Dopasowanie jest określane przez konfiguracje na przednich drzwi, do których konkretna reguła routingu będzie zgodna z żądaniem. Dowiedz się więcej o tym, jak drzwi z przodu są [zgodne](front-door-route-matching.md) , aby dowiedzieć się więcej.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identyfikowanie dostępnych zaplecza w puli zaplecza dla reguły routingu
Gdy drzwiczki z przodu zostaną dopasowane do reguły routingu dla żądania przychodzącego, następnym krokiem jest uzyskanie stanu sondy kondycji dla puli zaplecza skojarzonej z regułą routingu, jeśli nie ma buforowania. Dowiedz się więcej o tym, jak tylne drzwi monitorują kondycję zaplecza przy użyciu [sond kondycji](front-door-health-probes.md) .

## <a name="forwarding-the-request-to-your-application-backend"></a>Przekazywanie żądania do zaplecza aplikacji
Na koniec przy założeniu, że buforowanie nie jest skonfigurowane, żądanie użytkownika zostanie przekazane do "najlepszej" zaplecza na podstawie konfiguracji [metody routingu](front-door-routing-methods.md) .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
