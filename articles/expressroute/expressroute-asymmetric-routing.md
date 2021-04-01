---
title: 'Azure ExpressRoute: Routing asymetryczny'
description: W tym artykule przedstawiono problemy związane z routingiem asymetrycznym w sieci, które można napotkać, jeśli sieć zawiera wiele połączeń z miejscem docelowym.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97560512"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Routing asymetryczny z wieloma ścieżkami sieciowymi
W tym artykule wyjaśniono, jak ruch sieciowy może przyjmować różne ścieżki, gdy wiele tras jest dostępnych między źródłem sieci a miejscem docelowym.

Istnieją dwie koncepcje, które należy znać, aby zrozumieć Routing asymetryczny. Pierwszy jest efektem wielu ścieżek sieciowych. Druga to sposób, w jaki urządzenia, takie jak zapora, zachowują stan. Tego rodzaju urządzenia są nazywane urządzeniami stanowymi. Gdy te dwa czynniki są łączone, mogą tworzyć scenariusze, w których ruch sieciowy zostaje porzucony przez urządzenie stanowe.  Ruch został porzucony, ponieważ nie wykrył, że ruch pochodzi z samego siebie.

## <a name="multiple-network-paths"></a>Wiele ścieżek sieciowych
Gdy sieć przedsiębiorstwa ma tylko jedno połączenie z Internetem za pośrednictwem dostawcy usług internetowych, cały ruch do i z Internetu porusza się w tej samej ścieżce. Często istnieją firmy, które kupują wiele obwodów w celu poprawienia czasu działania sieci. W przypadku tego typu konfiguracji możliwe jest, że ruch przechodzi przez jedno łącze do Internetu i zwraca przez inny link. Ten scenariusz jest często znany jako Routing asymetryczny. W przypadku routingu asymetrycznego ruch sieciowy zwraca inną ścieżkę od oryginalnego przepływu.

![Sieć z wieloma ścieżkami](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Chociaż Routing asymetryczny zwykle odbywa się podczas przechodzenia do Internetu. Występuje również po wprowadzeniu kombinacji wielu ścieżek. Pierwszy przykład ma miejsce, gdy użytkownik dysponuje ścieżką internetową i ścieżką prywatną do tego samego miejsca docelowego. Drugi przykład występuje, gdy istnieje wiele ścieżek prywatnych, które również przechodzą do tego samego miejsca docelowego.

Każdy router wzdłuż ścieżki między lokalizacją źródłową i docelową będzie obliczać najlepszą ścieżkę do przełączenia do miejsca docelowego. Router określa najlepszą możliwą ścieżkę w oparciu o dwa główne czynniki:

* Routing między sieciami zewnętrznymi opiera się na protokole routingu o nazwie Border Gateway Protocol (BGP). Protokół BGP przyjmuje anonse z najbliższego otoczenia i wykonuje na nich pewne działania celem określenia najlepszej ścieżki do miejsca docelowego. Takie ścieżki przechowuje w swojej tabeli routingu.
* Długość maski podsieci skojarzonej z trasą ma wpływ na ścieżki routingu. Jeśli router odbiera wiele anonsów dla tego samego adresu IP, router wybierze ścieżkę o dłuższej masce podsieci, ponieważ jest ona traktowana jako bardziej określona trasa.

## <a name="stateful-devices"></a>Urządzenia stanowe
Routery sprawdzają nagłówek IP pakietu w celach związanych z routingiem. Jednakże niektóre urządzenia sprawdzają pakiety bardziej szczegółowo. Zazwyczaj te urządzenia są przeszukiwane w nagłówkach warstwy 4 — Transmission Control Protocol (TCP) lub UDP (User Datagram Protocol), a nawet w przypadku nagłówków warstwy 7 (warstwy aplikacji). Urządzenia te to urządzenia zabezpieczające lub urządzenia optymalizujące przepustowość. 

Zapora jest typowym przykładem urządzenia stanowego. Zapora zezwala na przekazywanie lub odrzucanie pakietów na podstawie różnych kryteriów. Kryteria te obejmują między innymi nagłówki protokołów, TCP/UDP i URL. Ten poziom inspekcji pakietów może spowodować duże obciążenie urządzenia. 

W celu poprawienia wydajności zapora bada pierwszy pakiet przepływu. Jeśli zezwala na przekazywanie pakietu przez jego interfejsy, utrzymuje informacje o przepływie w tabeli Stanów. Wszystkie pakiety, które są powiązane z tym przepływem, są następnie dozwolone w oparciu o początkowe oznaczenie. Pakiet, który jest częścią istniejącego przepływu może dotrzeć do zapory, z której nie pochodzi. Ponieważ nie ma informacji o wcześniejszym stanie dotyczących przepływu początkowego, Zapora odrzuca pakiet.

## <a name="asymmetric-routing-with-expressroute"></a>Routing asymetryczny przy użyciu usługi ExpressRoute
Po połączeniu się z firmą Microsoft przy użyciu usługi Azure ExpressRoute w sieci następują poniższe zmiany:

* Dostępnych jest wiele połączeń z firmą Microsoft. Jedno łącze to istniejące połączenie internetowe, a drugie — za pomocą połączenia usługi ExpressRoute. Określony ruch przeznaczony dla firmy Microsoft może przekroczyć połączenie internetowe, ale wrócić przez połączenie usługi ExpressRoute. Może się to zdarzyć również wtedy, gdy ruch przechodzi przez ExpressRoute, ale wraca przez ścieżkę internetową.
* Odebrano bardziej szczegółowe adresy IP ze obwodu usługi ExpressRoute. W związku z tym, gdy ruch z sieci przechodzi do firmy Microsoft dla usług oferowanych za pośrednictwem ExpressRoute, routery zawsze preferują połączenie ExpressRoute.

Aby zrozumieć wpływ tych dwóch zmian w sieci, rozważmy kilka scenariuszy. Na przykład masz obwód do Internetu i korzystasz ze wszystkich usług firmy Microsoft za pomocą Internetu. Ruch z sieci do i z firmy Microsoft przechodzi przez ten sam Link internetowy i przechodzi przez zaporę. Zapora rejestruje przepływ, gdy widzi pierwszy pakiet. Wszystkie pakiety tej konwersacji są dozwolone, ponieważ przepływ istnieje w tabeli Stanów.

![Routing asymetryczny przy użyciu usługi ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Następnie można wypróbować obwód usługi ExpressRoute, aby korzystać z usług oferowanych przez firmę Microsoft przez program ExpressRoute. Wszystkie inne usługi firmy Microsoft są używane przez Internet. Należy wdrożyć oddzielną zaporę na krawędzi, która jest połączona z usługą ExpressRoute Connection. Firma Microsoft anonsuje bardziej szczegółowe prefiksy sieci za pośrednictwem usługi ExpressRoute dla niektórych usług. Infrastruktura routingu wybiera usługę ExpressRoute jako preferowaną ścieżkę dla tych prefiksów. 

Jeśli Twoje publiczne adresy IP nie są anonsowane firmie Microsoft przez ExpressRoute. Firma Microsoft będzie komunikować się z publicznymi adresami IP za pomocą Internetu. Ruch wysyłany z sieci do firmy Microsoft korzysta z połączenia ExpressRoute, ale ruch powrotny z firmy Microsoft będzie używał ścieżki internetowej. Gdy Zapora na krawędzi widzi pakiet odpowiedzi dla przepływu, którego nie wiadomo, spowoduje to usunięcie tych pakietów.

W przypadku wybrania anonsowania tej samej puli translatora adresów sieciowych (NAT) dla ExpressRoute i dla Internetu. Podobne problemy związane z klientami w sieci można zobaczyć przy użyciu prywatnych adresów IP. Żądania dotyczące usług takich jak Windows Update przechodzą przez Internet, ponieważ adresy IP dla tych usług nie są anonsowane przez usługę ExpressRoute. Jednak ruch powrotny powróci przez ExpressRoute. Ponieważ firma Microsoft otrzymała adres IP o tej samej masce podsieci z Internetu i ExpressRoute, preferowaną ścieżką jest zawsze ExpressRoute. Jeśli zapora lub inne urządzenie stanowe w sieci brzegowej po połączeniu z ExpressRoute nie ma żadnych wcześniejszych informacji o przepływie, spowoduje to usunięcie tych pakietów.

## <a name="asymmetric-routing-solutions"></a>Rozwiązania w zakresie routingu asymetrycznego
Dostępne są dwie opcje rozwiązania problemu związanego z routingiem asymetrycznym. Pierwszy jest za pośrednictwem routingu, a drugi jest przy użyciu translatora adresów sieciowych opartych na źródle.

### <a name="routing"></a>Routing
Upewnij się, że publiczne adresy IP są anonsowane odpowiednim linkom sieci rozległej (WAN). Na przykład jeśli chcesz użyć Internetu na potrzeby ruchu uwierzytelniania i ExpressRoute dla ruchu pocztowego. Nie Anonsuj publicznych adresów IP Active Directory Federation Services (AD FS) za pośrednictwem ExpressRoute. Należy również pamiętać, aby nie ujawniać lokalnego serwera AD FS do adresów IP, które router odbiera przez ExpressRoute. Trasy odebrane za pośrednictwem usługi ExpressRoute są bardziej szczegółowe, w związku z tym usługa ExpressRoute będzie ścieżką preferowaną dla ruchu uwierzytelniania do firmy Microsoft. Jeśli nie musisz zwrócić uwagę na sposób, w jaki Routing odbywa się w przypadku problemów z routingiem asymetrycznym w sieci mogą wystąpić problemy.

Jeśli chcesz użyć usługi ExpressRoute do uwierzytelniania, upewnij się, że masz anonsowanie AD FS publicznych adresów IP za pośrednictwem ExpressRoute bez translatora adresów sieciowych. Po skonfigurowaniu tej metody ruch pochodzący z firmy Microsoft do lokalnego serwera AD FS przejdzie przez ExpressRoute. Ruch powrotny z sieci, który przechodzi do firmy Microsoft, będzie używać ExpressRoute, ponieważ jest preferowaną trasą przez Internet.

### <a name="source-based-nat"></a>Translator adresów sieciowych oparty na źródle
Innym sposobem rozwiązania problemu związanego z routingiem asymetrycznym jest użycie tego translatora adresów sieciowych. Na przykład nie można anonsować publicznego adresu IP serwera lokalnego Simple Mail Transfer Protocol (SMTP) za pośrednictwem usługi ExpressRoute. Zamiast tego należy korzystać z Internetu w przypadku tego typu komunikacji. Żądanie pochodzące z firmy Microsoft, które przechodzi do lokalnego serwera SMTP, przechodzi przez Internet. Translator adresów sieciowych oparty na źródle przetwarza żądanie przychodzące i przekazuje do wewnętrznego adresu IP. Ruch powrotny z serwera SMTP przechodzi do zapory brzegowej (używanej przez translatora adresów sieciowych) zamiast za pośrednictwem usługi ExpressRoute. W związku z tym ruch powrotny przejdzie ze ścieżki internetowej.

![Konfiguracja translatora adresów sieciowych opartych na źródle](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Wykrywanie routingu asymetrycznego
Polecenie traceroute jest najlepszym sposobem zapewnienia, że ruch sieciowy jest kierowany oczekiwaną ścieżką. Jeśli oczekujesz, że ruch z lokalnego serwera SMTP do firmy Microsoft ma mieć ścieżkę internetową, oczekiwany traceroute pochodzi z serwera SMTP do Microsoft 365. Wyniki sprawdzają, czy ruch rzeczywiście opuszcza sieć w kierunku Internetu, a nie w kierunku ExpressRoute.

