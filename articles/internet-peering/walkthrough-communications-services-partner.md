---
title: Przewodnik po usłudze Azure Internet Komunikacja równorzędna dla usług komunikacyjnych
titleSuffix: Azure
description: Przewodnik po usłudze Azure Internet Komunikacja równorzędna dla usług komunikacyjnych
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: ff6750883a904ff5ddbddd3ddfd1ed82e72aebbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499008"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Przewodnik po usłudze Azure Internet Komunikacja równorzędna dla usług komunikacyjnych

W tej sekcji opisano kroki, które musi wykonać dostawca usług komunikacyjnych w celu nawiązania bezpośredniego połączenia z firmą Microsoft.

**Dostawcy usług komunikacyjnych:**  Dostawcy usług komunikacyjnych są organizacjami oferującymi usługi komunikacyjne (komunikacji, wiadomości, konferencje itp.) i chcą zintegrować swoją infrastrukturę usług komunikacyjnych (bramą SBC/SIP itp.).  za pomocą usług Azure Communication Services i Microsoft Teams. 

Internetowa Komunikacja równorzędna Azure obsługuje dostawców usług komunikacyjnych w celu nawiązania bezpośredniego połączenia z firmą Microsoft w dowolnym z jego lokacji brzegowych (lokalizacje pop). Lista wszystkich witryn publicznych krawędzi jest dostępna w [PeeringDB](https://www.peeringdb.com/net/694).

Internetowa Komunikacja równorzędna Azure zapewnia wysoką niezawodność i funkcję QoS (Quality of Service), która umożliwia komunikację usług komunikacyjnych w celu zapewnienia wysokiej jakości i wydajności.

## <a name="technical-requirements"></a>Wymagania techniczne
Wymagania techniczne dotyczące nawiązywania bezpośredniego połączenia z usługami komunikacyjnymi są następujące:
-   Element równorzędny musi mieć własny numer systemu autonomicznego (ASN), który musi być publiczny.
-   Element równorzędny musi mieć nadmiarowy program Interconnect (PNI) w każdej lokalizacji połączenia, aby zapewnić nadmiarowość lokalną.
-   Element równorzędny musi mieć nadmiarowość geograficzną, aby zapewnić pracę w trybie failover w przypadku awarii lokacji w regionie/Metro.
-   Aby zapewnić wysoką dostępność i szybszą zbieżność, element równorzędny musi mieć sesje protokołu BGP jako aktywne-aktywne.
-   Element równorzędny musi zachować współczynnik 1:1 dla routerów komunikacji równorzędnej do obwodów komunikacji równorzędnej i nie jest stosowane ograniczanie szybkości.
-   Element równorzędny musi dostarczyć i zaanonsować własną publiczną przestrzeń adresową IPv4 używaną przez punkty końcowe usługi komunikacji równorzędnej (np. SBC). 
-   Element równorzędny musi podać szczegóły dotyczące klasy ruchu i punktów końcowych w każdej podsieci anonsowanej. 
-   Element równorzędny musi korzystać z protokołu BGP przez dwukierunkową funkcję wykrywania przekazywania dalej (BFD), aby umożliwić zbieżność podsieci sub Second.
-   Wszystkie prefiksy infrastruktury komunikacyjnej są rejestrowane w Azure Portal i anonsowane w ciągu społeczności 8075:8007.
-   Element równorzędny nie może kończyć komunikacji równorzędnej na urządzeniu z uruchomioną zaporą stanową. 
-   W związku z tym firma Microsoft domyślnie skonfiguruje wszystkie linki programu InterConnect jako ZWŁOKę (zbiory linków), więc element równorzędny musi obsługiwać LACP (łączenie protokołu kontroli agregacji) na linkach programu InterConnect.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Ustanawianie bezpośredniego połączenia z firmą Microsoft w zakresie usług komunikacyjnych.

Aby nawiązać bezpośrednie połączenie za pomocą komunikacji równorzędnej Azure Internet, wykonaj następujące czynności:

**1. Skojarz publiczny numer ASN elementu równorzędnego z subskrypcją platformy Azure:**

W przypadku elementu równorzędnego jest już skojarzony publiczny numer ASN z subskrypcją platformy Azure, Zignoruj ten krok.

[Kojarzenie równorzędnych elementów ASN z subskrypcją platformy Azure przy użyciu portalu Azure | Microsoft Docs](https://docs.microsoft.com/azure/internet-peering/howto-subscription-association-portal)

Następnym krokiem jest utworzenie bezpośredniego połączenia komunikacji równorzędnej dla usługi komunikacji równorzędnej.

> [!NOTE]
> Po zatwierdzeniu skojarzenia ASN Wyślij do nas wiadomość e-mail peeringservices@microsoft.com z numerem ASN i identyfikatorem subskrypcji, aby skojarzyć subskrypcję z usługami komunikacyjnymi. 

**2. Utwórz bezpośrednie połączenie komunikacji równorzędnej dla usługi komunikacji równorzędnej:**

Postępuj zgodnie z instrukcjami, aby [utworzyć lub zmodyfikować bezpośrednią komunikację równorzędną przy użyciu portalu](https://docs.microsoft.com/azure/internet-peering/howto-direct-portal)

Upewnij się, że spełnia ono wymagania dotyczące wysokiej dostępności.

Upewnij się, że wybierasz następujące opcje na stronie "Tworzenie komunikacji równorzędnej":

Typ komunikacji równorzędnej:   **bezpośrednie**

Sieć firmy Microsoft:  **8075**

Jednostka SKU:        **bezpłatna wersja Premium**


W obszarze "bezpośrednie połączenie komunikacji równorzędnej" Wybierz następujące opcje:

Dostawca adresów sesji:   **Microsoft**

Użyj dla usług komunikacji równorzędnej:   **włączone**

> [!NOTE] 
> Po wybraniu opcji aktywowania dla usług komunikacji równorzędnej zignoruj następujący komunikat.
> *Nie należy włączać, chyba że skontaktowano się z peering@microsoft.com dostawcą usług Maps.*


  **2a. Użyj istniejącego bezpośredniego połączenia komunikacji równorzędnej dla usług komunikacji równorzędnej**

Jeśli masz istniejącą bezpośrednią komunikację równorzędną, która ma być używana do obsługi komunikacji równorzędnej, możesz ją aktywować na Azure Portal.
1.  Postępuj zgodnie z instrukcjami, aby [skonwertować starszą bezpośrednią komunikację równorzędną do zasobu platformy Azure przy użyciu portalu](https://docs.microsoft.com/azure/internet-peering/howto-legacy-direct-portal).
W razie potrzeby Zamów dodatkowe obwody w celu spełnienia wymagań dotyczących wysokiej dostępności.

2.  Wykonaj kroki, aby [włączyć usługę komunikacji równorzędnej](https://docs.microsoft.com/azure/internet-peering/howto-peering-service-portal) w bezpośredniej komunikacji równorzędnej przy użyciu portalu.




**3. Zarejestruj prefiksy pod kątem zoptymalizowanego routingu**

Aby zoptymalizować Routing dla prefiksów infrastruktury usług komunikacyjnych, należy zarejestrować wszystkie prefiksy przy użyciu połączeń równorzędnych.
[Rejestrowanie usługi Komunikacja równorzędna Azure — Azure Portal | Microsoft Docs](https://docs.microsoft.com/azure/peering-service/azure-portal)

Klucz prefiksu jest wypełniany automatycznie dla partnerów usługi komunikacyjnej, więc partner nie musi używać żadnego klucza prefiksu do zarejestrowania. 

Upewnij się, że zarejestrowane prefiksy są ogłaszane za pośrednictwem bezpośrednich połączeń powiązanych dla regionu.


## <a name="faqs"></a>Często zadawane pytania:

**Pytania.**  Mam mniejsze podsieci (</24) dla usług komunikacyjnych. Czy mogę uzyskać dostęp do mniejszych podsieci również?

**Z.**  Tak, Usługa Komunikacja równorzędna Microsoft Azure obsługuje również krótszy Routing z prefiksem. Upewnij się, że rejestrujesz mniejsze prefiksy dla routingu, a te same są ogłaszane w ramach połączeń wzajemnych.

**Pytania.**  Jakie trasy firmy Microsoft otrzymamy za pośrednictwem tych połączeń?

**Z.** Firma Microsoft ogłasza wszystkie prefiksy usług publicznych firmy Microsoft w ramach tych połączeń wzajemnych. Dzięki temu nie tylko komunikacja, ale inne usługi w chmurze są dostępne z tego samego połączenia.

**Pytania.**  Muszę ustawić limit prefiksów, ile tras firma Microsoft może ogłaszać?

**Z.** Firma Microsoft ogłasza około 280 prefiksy w Internecie i może wzrosnąć o 10-15% w przyszłości. Dlatego bezpieczny limit 400-500 może być dobry do ustawienia jako "Maksymalna liczba prefiksów"

**Pytania.** Czy firma Microsoft będzie reanonsuje prefiksy równorzędne do Internetu?

**Z.** Nie.

**Pytania.** Czy jest naliczana opłata za tę usługę?

**Z.** Nie, jednak element równorzędny ma nawiązywać koszty połączenia krzyżowego z witryną.

**Pytania.** Jaka jest minimalna szybkość łącza dla połączenia?

**Z.** 10Gbps.

**Pytania.** Czy element równorzędny jest powiązany z umową SLA?

**Z.** Tak, gdy użycie osiągnie 40%, należy rozpocząć proces rozszerzania ZWŁOKi 45-60day.

**Pytania.** Jaka jest zaleta tej usługi w porównaniu do bieżącej bezpośredniej komunikacji równorzędnej lub ekspresowej trasy?

**Z.** Rozliczanie bezpłatna i cała ścieżka jest zoptymalizowana pod kątem ruchu głosowego za pośrednictwem sieci WAN firmy Microsoft, a zbieżność jest dostrajana dla podsieci BFD.

**Pytania.** Jak trwa proces dołączania?

**Z.** Godzina będzie zmienna w zależności od liczby i lokalizacji lokacji, a jeśli element równorzędny migruje istniejące prywatne elementy równorzędne lub ustanawiając nowe okablowanie. Przewoźnik powinien zaplanować trzy tygodnie.

**Pytania.** Czy można używać interfejsów API do dołączania?

**Z.** Obecnie nie ma obsługi interfejsu API, a konfiguracja musi być przeprowadzana za pośrednictwem portalu sieci Web. 
