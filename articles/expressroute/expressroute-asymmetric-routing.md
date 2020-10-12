---
title: 'Azure ExpressRoute: Routing asymetryczny'
description: W tym artykule przedstawiono problemy związane z routingiem asymetrycznym w sieci, które można napotkać, jeśli sieć zawiera wiele połączeń z miejscem docelowym.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.openlocfilehash: ee96abb51e03fb1dc567b66d180cb520de299d95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566538"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Routing asymetryczny z wieloma ścieżkami sieciowymi
W tym artykule wyjaśniono, jak ruch sieciowy w obie strony może podążać różnymi trasami w przypadku dostępności wielu ścieżek między miejscem źródłowym i docelowym.

Poznanie routingu asymetrycznego wymaga zrozumienia dwóch zagadnień. Jednym z nich jest efekt wielu ścieżek sieciowych. Drugim jest sposób zachowywania stanu przez urządzenia takie jak zapory. Tego rodzaju urządzenia są nazywane urządzeniami stanowymi. Połączenie tych dwóch czynników tworzy scenariusze, w których ruch sieciowy zostaje przerwany przez urządzenie stanowe, ponieważ nie wykryło ono ruchu pochodzącego od niego samego.

## <a name="multiple-network-paths"></a>Wiele ścieżek sieciowych
Gdy sieć przedsiębiorstwa ma tylko jedno połączenie internetowe za pośrednictwem dostawcy usług internetowych, cały ruch przychodzący i wychodzący z Internetu przepływa tą samą ścieżką. Często firmy kupują wiele obwodów jako ścieżki nadmiarowe, aby skrócić przestoje pracy sieci. W takich przypadkach istnieje możliwość, że ruch wychodzący poza sieć w kierunku Internetu przepływa przez jedno połączenie, podczas gdy ruch powrotny przepływa przez inne. Jest to często określane mianem routingu asymetrycznego. W routingu asymetrycznym odwrotny ruch sieciowy ma ścieżkę inną niż przepływ oryginalny.

![Sieć z wieloma ścieżkami](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Chociaż routingu asymetryczny występuje głównie w Internecie, ma on również zastosowanie w przypadku innych kombinacji wielu ścieżek. Przykładami mogą być ścieżka internetowa i prywatna prowadząca do tego samego miejsca docelowego lub wiele ścieżek prywatnych prowadzących do tego samego miejsca docelowego.

Każdy router po drodze ze źródła do miejsca docelowego oblicza najlepszą ścieżkę do miejsca docelowego. Wybór najlepszej możliwej ścieżki jest dokonywany w oparciu o dwa główne czynniki:

* Routing między sieciami zewnętrznymi opiera się na protokole routingu o nazwie Border Gateway Protocol (BGP). Protokół BGP przyjmuje anonse z najbliższego otoczenia i wykonuje na nich pewne działania celem określenia najlepszej ścieżki do miejsca docelowego. Takie ścieżki przechowuje w swojej tabeli routingu.
* Długość maski podsieci skojarzonej z trasą ma wpływ na ścieżki routingu. W przypadku odebrania kilku anonsów o tym samym adresie IP, ale różnych maskach podsieci, preferowany jest anons o dłuższej masce podsieci, ponieważ zakłada się, że określa ona trasę bardziej szczegółowo.

## <a name="stateful-devices"></a>Urządzenia stanowe
Routery sprawdzają nagłówek IP pakietu w celach związanych z routingiem. Jednakże niektóre urządzenia sprawdzają pakiety bardziej szczegółowo. Zazwyczaj te urządzenia sprawdzają nagłówki warstwy 4 (protokół TCP lub UDP), a nawet warstwy 7 (warstwa aplikacji). Urządzenia te to urządzenia zabezpieczające lub urządzenia optymalizujące przepustowość. 

Zapora jest typowym przykładem urządzenia stanowego. Zapora zezwala na przesłanie pakietu przez swoje interfejsy lub blokuje je na podstawie różnych pól, takich jak protokół, port TCP/UDP czy nagłówki URL. Inspekcja pakietów na tym poziomie powoduje duże obciążenie urządzenia. W celu poprawienia wydajności zapora bada pierwszy pakiet przepływu. Jeśli przesłanie pakietu zostanie dozwolone, zapora zachowuje informacje o przepływie w swojej tabeli stanów. Wszystkie kolejne pakiety związane z tym przepływem są przesyłane na podstawie decyzji dotyczącej pierwszego pakietu. Pakiet, który jest częścią istniejącego przepływu, może pojawić się w zaporze. Jeśli zapora nie ma wcześniejszej informacji o stanie odnoszącej się do tego pakietu, wówczas odrzuca pakiet.

## <a name="asymmetric-routing-with-expressroute"></a>Routing asymetryczny przy użyciu usługi ExpressRoute
Po połączeniu się z firmą Microsoft przy użyciu usługi Azure ExpressRoute w sieci następują poniższe zmiany:

* Dostępnych jest wiele połączeń z firmą Microsoft. Jedno połączenie to istniejące połączenie internetowe, a drugie to usługa ExpressRoute. Część ruchu do firmy Microsoft może wychodzić przez Internet ale wracać przez usługę ExpressRoute lub odwrotnie.
* Usługa ExpressRoute zapewnia bardziej szczegółowe adresy IP. Dlatego w przypadku ruchu z sieci do firmy Microsoft dla usług oferowanych przez usługę ExpressRoute routery zawsze preferują usługę ExpressRoute.

Aby poznać efekt wywoływany przez te dwie zmiany w sieci, rozważmy niektóre scenariusze. Załóżmy, że dysponujesz tylko jednym obwodem łączącym z Internetem i korzystasz ze wszystkich usług firmy Microsoft za pośrednictwem Internetu. Ruch z sieci do firmy Microsoft i ruch powrotny przepływają przez to samo połączenie internetowe i przechodzą przez zaporę. Zapora rejestruje przepływ w momencie wykrycia pierwszego pakietu, a pakiety powrotne są przepuszczane, ponieważ informacja o przepływie znajduje się w tabeli stanów.

![Routing asymetryczny przy użyciu usługi ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Załóżmy teraz, że usługa ExpressRoute została włączona i korzystasz z usług oferowanych przez firmę Microsoft za jej pośrednictwem. Wszystkie inne usługi firmy Microsoft są używane za pośrednictwem Internetu. Wdrożono oddzielną zaporę na krawędzi sieci łączącej się z usługą ExpressRoute. Firma Microsoft anonsuje bardziej specyficzne prefiksy sieci za pośrednictwem usługi ExpressRoute dla określonych usług. Infrastruktura routingu wybiera usługę ExpressRoute jako preferowaną ścieżkę dla tych prefiksów. Jeśli nie anonsujesz swoich publicznych adresów IP firmie Microsoft za pośrednictwem usługi ExpressRoute, firma Microsoft komunikuje się z tymi publicznymi adresami IP za pośrednictwem Internetu. Ruch wychodzący z sieci do firmy Microsoft korzysta z usługi ExpressRoute, a ruch powrotny z firmy Microsoft przechodzi przez Internet. Gdy zapora na krawędzi wykryje pakiet odpowiedzi dla przepływu, który nie jest zapisany w tabeli stanów, odrzuci ruch powrotny.

Jeśli zdecydujesz się na anonsowanie tej samej puli translatora adresów sieciowych (NAT) dla usługi ExpressRoute i dla Internetu, zobaczysz podobne problemy z klientami w sieci przy użyciu prywatnych adresów IP. Żądania dla usług takich jak Windows Update przechodzą za pośrednictwem Internetu, ponieważ adresy IP dla tych usług nie są anonsowane przez usługę ExpressRoute. Jednak ruch powrotny przechodzi przez usługę ExpressRoute. Jeśli firma Microsoft odbierze adres IP o tej samej masce podsieci z Internetu i usługi ExpressRoute, preferuje usługę ExpressRoute. Jeśli zapora lub inne urządzenie stanowe znajdujące się na krawędzi sieci i korzystające z usługi ExpressRoute nie posiadają wcześniejszych informacji o przepływie, pakiety należące do tego przepływu zostaną odrzucone.

## <a name="asymmetric-routing-solutions"></a>Rozwiązania w zakresie routingu asymetrycznego
Dostępne są dwie główne opcje rozwiązania problemu związanego z routingiem asymetrycznym. Jeden polega na wykorzystaniu routingu, a drugi na użyciu translatora adresów sieciowych opartego na źródle (SNAT).

### <a name="routing"></a>Routing
Należy upewnić się, że Twoje publiczne adresy IP są anonsowane odpowiednim połączeniom rozległej sieci komputerowej WAN. Jeśli na przykład chcesz użyć Internetu dla ruchu uwierzytelniania, a usługi ExpressRoute dla ruchu pocztowego, nie możesz anonsować publicznych adresów IP Usług federacyjnych Active Directory (AD FS) przez usługę ExpressRoute. Podobnie, upewnij się, że lokalny serwer usługi AD FS nie jest uwidaczniany dla adresów IP, które router odbiera za pośrednictwem usługi ExpressRoute. Trasy odebrane za pośrednictwem usługi ExpressRoute są bardziej szczegółowe, w związku z tym usługa ExpressRoute będzie ścieżką preferowaną dla ruchu uwierzytelniania do firmy Microsoft. Wywołuje to asymetryczny routing.

Jeśli chcesz użyć usługi ExpressRoute do uwierzytelniania, musisz upewnić się, że publiczne adresy IP usługi AD FS są anonsowane przez usługę ExpressRoute bez translatora adresów sieciowych (NAT). W ten sposób ruch, który pochodzi od firmy Microsoft i przechodzi do lokalnego serwera usług AD FS, korzysta z usługi ExpressRoute. Powrotny ruch danych od klienta do firmy Microsoft korzysta z usługi ExpressRoute, ponieważ jest ona trasą preferowaną.

### <a name="source-based-nat"></a>Translator adresów sieciowych oparty na źródle
Innym sposobem rozwiązania problemów z routingiem asymetrycznym jest translator adresów sieciowych oparty na źródle (SNAT). Na przykład na lokalnym serwerze SMTP możesz mieć nieanonsowane za pośrednictwem usługi ExpressRoute publiczne adresy IP, ponieważ zamierzasz używać Internetu dla tego typu komunikacji. Żądanie pochodzące z firmy Microsoft i przychodzące do lokalnego serwera SMTP jest przesyłane za pośrednictwem Internetu. Translator adresów sieciowych oparty na źródle przetwarza żądanie przychodzące i przekazuje do wewnętrznego adresu IP. Ruch odwrotny z serwera SMTP przechodzi do zapory na krawędzi (która jest używana do translacji adresów sieciowych) zamiast za pośrednictwem usługi ExpressRoute. Powrotny ruch danych jest przesyłany za pośrednictwem Internetu.

![Konfiguracja translatora adresów sieciowych opartych na źródle](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Wykrywanie routingu asymetrycznego
Polecenie traceroute jest najlepszym sposobem zapewnienia, że ruch sieciowy jest kierowany oczekiwaną ścieżką. Jeśli oczekujesz, że ruch z lokalnego serwera SMTP do firmy Microsoft ma mieć ścieżkę internetową, oczekiwany traceroute pochodzi z serwera SMTP do Microsoft 365. Wynik pozwoli sprawdzić, czy po opuszczeniu sieci ruch rzeczywiście jest kierowany do Internetu, a nie do usługi ExpressRoute.

