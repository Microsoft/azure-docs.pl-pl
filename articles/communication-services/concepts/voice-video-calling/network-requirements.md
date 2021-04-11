---
title: Przygotowywanie sieci organizacji na potrzeby usług Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o wymaganiach dotyczących sieci dla połączeń głosowych i wideo w usłudze Azure Communication Services
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 22bf8af89771e57621a0d8851ca31bb750b7cda2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108353"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Zapewnianie wysokiej jakości multimediów w usłudze Azure Communications Services

Ten dokument zawiera omówienie czynników i najlepszych rozwiązań, które należy wziąć pod uwagę podczas tworzenia wysokiej jakości środowiska komunikacji multimedialnej przy użyciu usług Azure Communications Services.

## <a name="factors-that-affect-media-quality-and-reliability"></a>Czynniki wpływające na jakość i niezawodność multimediów

Istnieje wiele różnych czynników, które przyczyniają się do jakości multimediów w czasie rzeczywistym (audio, wideo i udostępnianie aplikacji). Są to między innymi konfiguracje jakości sieci, przepustowości, zapory, hosta i urządzenia.


### <a name="network-quality"></a>Jakość sieci

Jakość multimediów w czasie rzeczywistym za pośrednictwem protokołu IP ma znaczny wpływ na jakość podstawowej łączności sieciowej, ale szczególnie przez ilość:
* **Opóźnienie**. Jest to czas potrzebny na uzyskanie pakietu IP z punktu A do punktu B w sieci. Opóźnienie propagacji sieci jest określane przez fizyczną odległość między dwoma punktami i dodatkowymi kosztami ponoszonymi przez urządzenia, przez które przechodzi ruch. Opóźnienie jest mierzone jako czas jednokierunkowy lub błądzenia (RTT).
* **Utrata pakietów**. Procent pakietów, które są tracone w danym przedziale czasu. Utrata pakietów bezpośrednio wpływa na jakość dźwięku — od małych i pojedynczych utraconych pakietów, które prawie nie wpływają na kopie zapasowe z powrotem do tyłu, które powodują wycinanie dźwięku.
* **Wahanie lub nabycie między pakietami**. Jest to średnia zmiana opóźnienia między kolejnymi pakietami. Usługi komunikacyjne platformy Azure mogą dostosowywać się do niektórych poziomów wahania za pomocą buforowania. Tylko wtedy, gdy wahanie przekroczy bufor, że uczestnik zobaczy jego wpływ.

### <a name="network-bandwidth"></a>Przepustowość sieci

Upewnij się, że sieć jest skonfigurowana do obsługi przepustowości wymaganej przez współbieżne sesje multimedialne usług Azure Communication Services i inne aplikacje biznesowe. Testowanie kompleksowej ścieżki sieciowej pod kątem wąskich gardeł przepustowości ma kluczowe znaczenie dla pomyślnego wdrożenia rozwiązania multimedialnego usług komunikacyjnych.

Poniżej przedstawiono wymagania dotyczące przepustowości dla zestawów SDK języka JavaScript:

|Przepustowość|Scenariusze|
|:--|:--|
|40 KB/s|Wywołanie audio peer-to-peer|
|500 KB/s|Połączenie audio równorzędne i udostępnianie ekranu|
|500 KB/s|Wywołanie wideo z jakością równorzędną do elementów równorzędnych 360p na 30fps|
|1,2 MB/s|Film wideo z jakością równorzędną dla sieci równorzędnej z rozdzielczością HD 720 w 30fps|
|500 KB/s|Grupuj wideo wywołujące 360p w 30fps|
|1,2 MB/s|Wideo z grupą HD, wywoływanie przy użyciu rozdzielczości HD 720 w 30fps| 

Poniżej przedstawiono wymagania dotyczące przepustowości dla natywnych zestawów SDK dla systemów Android i iOS:

|Przepustowość|Scenariusze|
|:--|:--|
|30 KB/s|Wywołanie audio peer-to-peer |
|130 kb/s|Połączenie audio równorzędne i udostępnianie ekranu|
|500 KB/s|Wywołanie wideo z jakością równorzędną do elementów równorzędnych 360p na 30fps|
|1,2 MB/s|Film wideo z jakością równorzędną dla sieci równorzędnej z rozdzielczością HD 720 w 30fps|
|1,5 MB/s|Film wideo z jakością równorzędną dla sieci równorzędnej z rozdzielczością HD 1080p o 30fps |
|500kbps/1Mbps|Wywoływanie wideo grupy|
|1Mbps/sekundę|Wywoływanie wideo z grupą HD (wideo 540p na ekranie 1080p)|

### <a name="firewalls-configuration"></a>Konfiguracja zapór

Połączenia z usługami Azure Communications Services wymagają łączności z Internetem z określonymi portami i adresami IP w celu zapewnienia wysokiej jakości środowiska multimedialnego. W przypadku braku dostępu do tych portów i adresów IP usługi Azure Communication Services mogą nadal funkcjonować. Jednak optymalne środowisko jest dostępne po otwarciu zalecanych portów i zakresów adresów IP.

| Kategoria | Zakresy adresów IP lub nazwy FQDN | Porty | 
| :-- | :-- | :-- |
| Ruch związany z multimediami | [Zakres adresów IP chmury publicznej platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 do 3481, porty TCP 443 |
| Sygnalizowanie, dane telemetryczne, rejestracja| *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Optymalizacja sieci

Poniższe zadania są opcjonalne i nie są wymagane do wdrażania usług Azure Communications Services. Skorzystaj z tych wskazówek, aby zoptymalizować wydajność sieci i usług Azure Communications Services lub wiedzieć, że masz pewne ograniczenia sieci.
Możesz chcieć zoptymalizować więcej, jeśli:
* Usługi komunikacyjne platformy Azure działają wolno (być może masz niewystarczającą przepustowość)
* Wywołania nadal opuszczają (mogą być ze względu na zaporę lub blokowanie proxy)
* Wywołania mają postać statyczną i wycinania lub głosy, takie jak roboty (mogą być zakłóceniami lub utratam pakietów)

| Zadanie optymalizacji sieci | Szczegóły |
| :-- | :-- |
| Planowanie sieci | W tej dokumentacji można znaleźć minimalne wymagania dotyczące sieci dla wywołań. Zapoznaj się z [przykładem Teams w celu zaplanowania sieci](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example) |
| Rozpoznawanie nazw zewnętrznych | Upewnij się, że wszystkie komputery z uruchomionymi zestawami SDK usług Azure Communications Services mogą rozpoznawać zewnętrzne zapytania DNS w celu odnajdywania usług oferowanych przez usługi komunikacyjne platformy Azure i uniemożliwiania dostępu przez zapory. Upewnij się, że zestawy SDK mogą rozpoznawać adresy *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io  |
| Utrzymywanie trwałości sesji | Upewnij się, że Zapora nie zmienia adresów translatora adresów sieciowych (NAT) ani portów dla protokołu UDP
Sprawdzanie poprawności rozmiaru puli NAT | Sprawdź poprawność rozmiaru puli translatora adresów sieciowych (NAT) wymaganego do połączenia z użytkownikiem. Gdy wielu użytkowników i urządzeń uzyskują dostęp do usług Azure Communication Services przy użyciu translatora [adresów sieciowych (NAT) lub translacji adresów portów](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365), należy się upewnić, że urządzenia ukryte za każdy publiczny adres IP routingu nie przekraczają obsługiwanej liczby. Upewnij się, że odpowiednie publiczne adresy IP są przypisane do pul NAT, aby zapobiec wyczerpaniu portów. Wyczerpanie portów będzie współtworzyć użytkowników wewnętrznych i urządzeń, które nie są w stanie połączyć się z usługami Azure Communications Services |
| Wskazówki dotyczące wykrywania i zapobiegania włamaniom | Jeśli środowisko ma system wykrywania lub zapobiegania [włamaniu](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools) (identyfikatory/adresy IP) wdrożony w celu uzyskania dodatkowej warstwy zabezpieczeń dla połączeń wychodzących, Zezwól na wszystkie adresy URL usług Azure Communications Services |
| Konfigurowanie sieci VPN z podziałem tunelu | Firma Microsoft zaleca, aby zapewnić alternatywną ścieżkę dla ruchu zespołu, który pomija wirtualną sieć prywatną (VPN), powszechnie znaną jako [Sieć VPN z podziałem tunelu](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing). Tunelowanie podzielone oznacza, że ruch dla usług Azure Communications Services nie przechodzi przez sieć VPN, ale zamiast tego przechodzi bezpośrednio do platformy Azure. Pomijanie sieci VPN będzie miało pozytywny wpływ na jakość nośnika i zmniejsza obciążenie urządzeń sieci VPN i sieci organizacji. Aby wdrożyć sieć VPN z podziałem tunelu, należy skontaktować się z dostawcą sieci VPN. Inne powody, dla których zalecamy ominięcie sieci VPN: <ul><li> Sieci VPN zwykle nie są zaprojektowane ani skonfigurowane do obsługi nośników w czasie rzeczywistym.</li><li> Sieci VPN mogą również nie obsługiwać protokołu UDP (co jest wymagane w przypadku usług Azure Communications Services).</li><li>Sieci VPN również wprowadzają dodatkową warstwę szyfrowania na ruch multimedialny, który jest już szyfrowany.</li><li>Łączność z usługami Azure Communications Services może nie być skuteczna ze względu na ruch w sieci VPN za pośrednictwem urządzenia.</li></ul>|
| Implementowanie QoS | [Użyj Quality of Service (QoS)](https://docs.microsoft.com/microsoftteams/qos-in-teams) , aby skonfigurować priorytetyzację pakietów. Poprawi to jakość wywołań i ułatwia monitorowanie i rozwiązywanie problemów z jakością wywołań. Jakość usług (QoS) powinna być implementowana dla wszystkich segmentów zarządzanej sieci. Nawet w przypadku, gdy sieć jest odpowiednio obsługiwana w celu zapewnienia przepustowości, funkcja QoS zapewnia Łagodzenie ryzyka w przypadku nieoczekiwanych zdarzeń sieci. W przypadku opcji QoS ruch głosowy jest określany według priorytetów, dzięki czemu te nieprzewidziane zdarzenia nie wpływają negatywnie na jakość. | 
| Optymalizacja sieci Wi-Fi | Podobnie jak w przypadku sieci VPN, sieci Wi-Fi nie muszą być zaprojektowane ani skonfigurowane do obsługi nośników w czasie rzeczywistym. Planowanie i optymalizacja sieci Wi-Fi do obsługi usług Azure Communication Services jest ważnym zagadnieniem dotyczącym wdrażania wysokiej jakości. Należy wziąć pod uwagę następujące czynniki: <ul><li>Zaimplementuj jakość usług (QoS) lub Wi-Fi (WMM), aby zapewnić odpowiednie Ustawianie priorytetów ruchu multimedialnego w sieciach Wi-Fi.</li><li>Planuj i Optymalizuj rozmieszczenie sieci Wi-Fi i punktu dostępu. Zakres 2,4 GHz może zapewnić odpowiednie środowisko w zależności od położenia punktu dostępu, ale punkty dostępu są często objęte innymi urządzeniami konsumenta, które działają w tym zakresie. Zakres 5 GHz jest lepiej dostosowany do nośnika w czasie rzeczywistym ze względu na jego gęsty zakres, ale wymaga więcej punktów dostępu w celu uzyskania wystarczającego pokrycia. Punkty końcowe również muszą obsługiwać ten zakres i być skonfigurowane tak, aby odpowiednio wykorzystać te grupy.</li><li>Jeśli używasz sieci Wi-Fi Dual-Band, rozważ zaimplementowanie mechanizmu kierującego pasmem. Kierowanie pasmem jest techniką wdrożoną przez dostawców sieci Wi-Fi w celu wywierania wpływu na klientów z procesorem dwupasmowym na użycie zakresu 5 GHz</li><li>Gdy punkty dostępu tego samego kanału są zbyt blisko siebie, mogą powodować nakładanie się sygnałów i niezamierzone konkurowanie, co spowodowało spadek wydajności użytkownika. Upewnij się, że punkty dostępu obok siebie znajdują się na kanałach, które nie nakładają się na siebie.</li></ul> Każdy dostawca sieci bezprzewodowej ma własne zalecenia dotyczące wdrażania rozwiązania bezprzewodowego. Aby uzyskać szczegółowe wskazówki, skontaktuj się z dostawcą sieci Wi-Fi.|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>System operacyjny i przeglądarki (dla zestawów SDK języka JavaScript)

Zestawy SDK głosu/wideo usługi Azure Communication Services obsługują pewne systemy operacyjne i przeglądarki.
Informacje na temat systemów operacyjnych i przeglądarek obsługiwanych przez wywołania zestawów SDK w [dokumentacji dotyczącej pojęć wywołujących](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

## <a name="next-steps"></a>Następne kroki

Następujące dokumenty mogą być interesujące:

- Dowiedz się więcej o [wywoływaniu bibliotek](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)
- Informacje o [architekturze klient-serwer](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture)
- Informacje o [topologii przepływu wywołań](https://docs.microsoft.com/azure/communication-services/concepts/call-flows)
