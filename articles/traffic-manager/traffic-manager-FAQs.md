---
title: Traffic Manager platformy Azure — często zadawane pytania
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: 0d4f1ed6bab5775c44b2a745e1edc5fc07e0c06d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215463"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager często zadawane pytania

## <a name="traffic-manager-basics"></a>Podstawowe informacje Traffic Manager

### <a name="what-ip-address-does-traffic-manager-use"></a>Jakiego adresu IP używa Traffic Manager?

Zgodnie z opisem w temacie [jak działa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie systemu DNS. Wysyła odpowiedzi DNS do bezpośrednich klientów do odpowiedniego punktu końcowego usługi. Następnie klienci nawiązują połączenie z punktem końcowym usługi bezpośrednio, a nie za pomocą Traffic Manager.

W związku z tym Traffic Manager nie zapewnia punktu końcowego ani adresu IP, z którym klienci mogą się połączyć. Jeśli chcesz mieć statyczny adres IP dla usługi, który musi być skonfigurowany w usłudze, a nie w Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Jakiego rodzaju ruch może być kierowany przy użyciu Traffic Manager?
Zgodnie z opisem w temacie [jak działa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), punkt końcowy Traffic Manager może być dowolną usługą internetową hostowaną w ramach platformy Azure lub poza nią. W związku z tym Traffic Manager może kierować ruch pochodzący z publicznej sieci Internet do zestawu punktów końcowych, które są również połączone z Internetem. Jeśli masz punkty końcowe, które znajdują się w sieci prywatnej (na przykład w wewnętrznej wersji programu [Azure Load Balancer](../load-balancer/components.md#frontend-ip-configurations)) lub użytkownicy wprowadzają żądania DNS z takich sieci wewnętrznych, nie możesz użyć Traffic Manager do skierowania tego ruchu.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Czy Traffic Manager obsługuje sesje "Sticky"?

Zgodnie z opisem w temacie [jak działa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie systemu DNS. Używa odpowiedzi DNS do kierowania klientów do odpowiedniego punktu końcowego usługi. Klienci łączą się bezpośrednio z punktem końcowym usługi, a nie za pomocą Traffic Manager. W związku z tym Traffic Manager nie widzi ruchu HTTP między klientem a serwerem.

Ponadto źródłowy adres IP zapytania DNS odbieranego przez Traffic Manager należy do cyklicznej usługi DNS, a nie do klienta programu. W związku z tym Traffic Manager nie ma możliwości śledzenia poszczególnych klientów i nie może implementować sesji "Sticky". To ograniczenie jest wspólne dla wszystkich systemów zarządzania ruchem opartym na systemie DNS i nie jest specyficzne dla Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Dlaczego podczas korzystania z Traffic Manager pojawia się błąd HTTP?

Zgodnie z opisem w temacie [jak działa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie systemu DNS. Używa odpowiedzi DNS do kierowania klientów do odpowiedniego punktu końcowego usługi. Następnie klienci nawiązują połączenie z punktem końcowym usługi bezpośrednio, a nie za pomocą Traffic Manager. Traffic Manager nie widzi ruchu HTTP między klientem i serwerem. W związku z tym wszelkie widoczne błędy HTTP muszą pochodzić z aplikacji. Aby klient mógł połączyć się z aplikacją, wszystkie kroki rozpoznawania nazw DNS są kompletne. Obejmuje wszelkie interakcje Traffic Manager w przepływie ruchu aplikacji.

W związku z tym należy skoncentrować się na aplikacji.

Nagłówek hosta HTTP wysłany z przeglądarki klienta to najbardziej typowe Źródło problemów. Upewnij się, że aplikacja jest skonfigurowana do akceptowania poprawnego nagłówka hosta dla używanej nazwy domeny. Dla punktów końcowych używających Azure App Service, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci Web w Azure App Service przy użyciu Traffic Manager](../app-service/configure-domain-traffic-manager.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Jaki jest wpływ na wydajność korzystania z Traffic Manager?

Zgodnie z opisem w temacie [jak działa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie systemu DNS. Ze względu na to, że klienci łączą się bezpośrednio z punktami końcowymi usługi, nie ma wpływu na wydajność podczas korzystania z Traffic Manager po nawiązaniu połączenia.

Ponieważ Traffic Manager integruje się z aplikacjami na poziomie systemu DNS, do łańcucha rozpoznawania nazw DNS trzeba wstawiać dodatkowe wyszukiwanie DNS. Wpływ Traffic Manager na czas rozpoznawania nazw DNS jest minimalny. Traffic Manager korzysta z globalnej sieci serwerów nazw i korzysta z sieci [emisji](https://en.wikipedia.org/wiki/Anycast) pojedynczej, aby zapewnić, że zapytania DNS są zawsze kierowane do najbliższego dostępnego serwera nazw. Ponadto buforowanie odpowiedzi DNS oznacza, że dodatkowe opóźnienie DNS wynikające z używania Traffic Manager ma zastosowanie tylko do części sesji.

Metoda wydajności kieruje ruch do najbliższego dostępnego punktu końcowego. Wynikiem jest fakt, że ogólny wpływ na wydajność związany z tą metodą powinien być minimalny. Każdy wzrost opóźnienia DNS powinien być przesunięty o mniejsze opóźnienie sieci do punktu końcowego.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Jakich protokołów aplikacji można używać z Traffic Manager?

Zgodnie z opisem w temacie [jak działa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie systemu DNS. Po zakończeniu wyszukiwania DNS klienci łączą się bezpośrednio z punktem końcowym aplikacji, a nie za pomocą Traffic Manager. W związku z tym połączenie może korzystać z dowolnego protokołu aplikacji. W przypadku wybrania opcji TCP jako protokołu monitorowania można wykonać Traffic Manager monitorowania kondycji punktu końcowego bez użycia żadnych protokołów aplikacji. Jeśli zdecydujesz się na zweryfikowanie kondycji przy użyciu protokołu aplikacji, punkt końcowy musi być w stanie odpowiedzieć na żądania HTTP lub HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Czy można użyć Traffic Manager z "owies" nazwą domeny?

Tak. Aby dowiedzieć się, jak utworzyć rekord aliasu dla nazwy domeny wierzchołka, aby odwołać się do profilu usługi Azure Traffic Manager, zobacz [Konfigurowanie rekordu aliasu w celu obsługi nazw domen wierzchołków z Traffic Manager](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Czy Traffic Manager uwzględnić adres podsieci klienta podczas obsługi zapytań DNS? 

Tak, oprócz źródłowego adresu IP zapytania DNS, które otrzymuje (zazwyczaj jest to adres IP programu rozpoznawania nazw DNS), podczas wyszukiwania dla metod routingu geograficznego, wydajności i podsieci, Usługa Traffic Manager traktuje także adres podsieci klienta, jeśli jest zawarty w zapytaniu przez mechanizm rozwiązywania konfliktów, wysyłając żądanie w imieniu użytkownika końcowego.  
W odniesieniu do programu [RFC 7871 — podsieć klienta w zapytaniach DNS](https://tools.ietf.org/html/rfc7871) , które udostępnia [mechanizm rozszerzenia dla systemu DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) , który może przekazać adres podsieci klienta z resolverów, które je obsługują.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Co to jest czas wygaśnięcia systemu DNS i jak ma to wpływ na moich użytkowników?

Gdy zapytanie DNS jest gruntem Traffic Manager, ustawia wartość w odpowiedzi o nazwie czas wygaśnięcia (TTL). Ta wartość, której jednostka jest w sekundach, wskazuje na rozpoznawanie nazw DNS w czasie, gdy ta odpowiedź jest buforowana. Chociaż funkcje rozpoznawania nazw DNS nie są gwarantowane w celu buforowania tego wyniku, buforowanie umożliwia reagowanie na jakiekolwiek kolejne zapytania poza pamięć podręczną, a nie Traffic Manager serwerów DNS. Ma to wpływ na odpowiedzi w następujący sposób:

- wyższy czas TTL zmniejsza liczbę zapytań, które są używane na serwerach DNS Traffic Manager, co może zmniejszyć koszt dla klienta, ponieważ Liczba obsłużonych zapytań to użycie płatne.
- wyższa wartość czasu wygaśnięcia może potencjalnie skrócić czas potrzebny do przeprowadzenia wyszukiwania DNS.
- wyższy czas TTL oznacza również, że dane nie odzwierciedlają najnowszych informacji o kondycji, które Traffic Manager uzyskane za pośrednictwem agentów sondowania.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Jak wysoka lub niska można ustawić czas wygaśnięcia dla Traffic Manager odpowiedzi?

Można ustawić, na poziomie profilu, wartość czasu wygaśnięcia DNS na wartość równą 0 sekund i maksymalnie 2 147 483 647 sekund (maksymalny zakres zgodny ze standardem [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Czas wygaśnięcia równy 0 oznacza, że podrzędne rozpoznawania nazw DNS nie buforują odpowiedzi zapytań, a wszystkie zapytania powinny dotrzeć do Traffic Manager serwerów DNS w celu rozpoznania.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Jak zrozumieć ilość zapytań przesyłanych do mojego profilu? 

Jedną z metryk dostarczonych przez Traffic Manager jest liczba zapytań wysłanych przez profil. Te informacje można uzyskać przy użyciu agregacji poziomu profilu lub można je podzielić w celu wyświetlenia ilości zapytań, w których zostały zwrócone określone punkty końcowe. Ponadto można skonfigurować alerty, które będą powiadamiać o tym, czy wolumin odpowiedzi zapytania przekracza ustawione warunki. Aby uzyskać więcej informacji, [Traffic Manager metryki i alerty](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager Metoda routingu ruchu geograficznego

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Jakie są przypadki użycia, w których funkcja routingu geograficznego jest przydatna?

Typ routingu geograficznego może być używany w każdym scenariuszu, w którym klient platformy Azure musi odróżnić swoich użytkowników na podstawie regionów geograficznych. Na przykład przy użyciu metody routingu ruchu geograficznego można dać użytkownikom z określonych regionów inne środowisko użytkownika niż te z innych regionów. Inny przykład jest zgodny z przydziałami lokalnych danych, które wymagają, aby użytkownicy z określonego regionu byli obsługiwani tylko przez punkty końcowe w tym regionie.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Jak mogę zdecydować, czy należy użyć metody routingu wydajności czy metody routingu geograficznego?

Kluczową różnicą między tymi dwiema popularnymi metodami routingu jest to, że w metodzie routingu wydajności podstawowym celem jest wysyłanie ruchu do punktu końcowego, który może zapewnić najniższe opóźnienie do obiektu wywołującego, natomiast w przypadku routingu geograficznego głównym celem jest wymuszenie ogrodzenia geograficznego dla dzwoniących, dzięki czemu można celowo kierować je do określonego punktu końcowego. Nakładanie się odbywa, ponieważ istnieje korelacja między bliskością geograficzną i niższym opóźnieniem, chociaż nie jest to zawsze prawdziwe. Może istnieć punkt końcowy w różnych lokalizacjach geograficznych, które mogą zapewniać lepszy czas oczekiwania dla obiektu wywołującego, a w takim przypadku Routing geograficzny zostanie wysłany do tego punktu końcowego, ale funkcja routingu geograficznego będzie zawsze wysyłać je do punktu końcowego zamapowanego dla regionu geograficznego. Aby dodatkowo uczynić to jasne, rozważmy poniższy przykład — z routingiem geograficznym można tworzyć nietypowe mapowania, takie jak wysyłanie całego ruchu z Azji do punktów końcowych w Stanach Zjednoczonych i cały ruch w Stanach Zjednoczonych do punktów końcowych w Azji. W takim przypadku funkcja routingu geograficznego będzie świadomie wykonywać dokładnie te elementy, które zostały skonfigurowane, a Optymalizacja wydajności nie jest brana pod uwagę. 
>[!NOTE]
>Mogą istnieć sytuacje, w których mogą być potrzebne zarówno możliwości routingu w zakresie wydajności, jak i geograficznej, w tych scenariuszach zagnieżdżone profile mogą być doskonałym wyborem. Można na przykład skonfigurować profil nadrzędny przy użyciu routingu geograficznego, w którym można wysłać cały ruch z Ameryka Północna do profilu zagnieżdżonego, który ma punkty końcowe w Stanach Zjednoczonych, i użyć routingu wydajności, aby wysłać ten ruch do najlepszego punktu końcowego w tym zestawie. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Jakie regiony są obsługiwane przez Traffic Manager na potrzeby routingu geograficznego?

Hierarchia krajów/regionów używana przez Traffic Manager można znaleźć [tutaj](traffic-manager-geographic-regions.md). Gdy ta strona jest aktualna wraz ze zmianami, można również programowo pobrać te same informacje przy użyciu [interfejsu API REST usługi Azure Traffic Manager](/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Jak Usługa Traffic Manager określa, skąd użytkownik wykonuje zapytania?

Traffic Manager sprawdza źródłowy adres IP zapytania (najprawdopodobniej jest to lokalny program rozpoznawania nazw DNS wykonujący zapytania w imieniu użytkownika) i używa wewnętrznej sieci IP do mapowania regionów w celu określenia lokalizacji. Ta mapa jest regularnie aktualizowana w celu uwzględnienia zmian w Internecie. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Czy jest gwarantowane, że Traffic Manager może prawidłowo określić dokładną lokalizację geograficzną użytkownika w każdym przypadku?

Nie, Traffic Manager nie może zagwarantować, że region geograficzny, który zostanie wywnioskowany ze źródłowego adresu IP zapytania DNS, będzie zawsze odpowiadał lokalizacji użytkownika z następujących powodów:

- Najpierw zgodnie z opisem w poprzednich często zadawanych pytaniach źródłowy adres IP jest używany przez program rozpoznawania nazw DNS w celu przeszukania w imieniu użytkownika. Lokalizacja geograficzna programu rozpoznawania nazw DNS jest dobrym serwerem proxy dla lokalizacji geograficznej użytkownika, ale może być również inna w zależności od wielkości usługi rozpoznawania nazw DNS i określonej usługi rozpoznawania nazw DNS, którą klient wybrał do użycia. Na przykład klient znajdujący się w Malezji może określić w ustawieniach urządzenia użycie usługi rozpoznawania nazw DNS, której serwer DNS w Singapurze może zostać pobrany do obsługi rozdzielczości zapytania dla tego użytkownika/urządzenia. W takim przypadku Traffic Manager może zobaczyć tylko adres IP programu rozpoznawania nazw, który odpowiada lokalizacji Singapuru. Zobacz również poprzednie często zadawane pytania dotyczące obsługi adresów podsieci klienta na tej stronie.

- Następnie Traffic Manager używa mapy wewnętrznej do przeprowadzenia translacji adresów IP do obszaru geograficznego. Mimo że ta mapa jest sprawdzana i aktualizowana w sposób ciągły, aby zwiększyć jej dokładność i obsłużyć rosnący charakter Internetu, istnieje możliwość, że nasze informacje nie są dokładną reprezentacją lokalizacji geograficznej wszystkich adresów IP.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Czy punkt końcowy musi być fizycznie zlokalizowany w tym samym regionie, w którym jest skonfigurowany do routingu geograficznego?

Nie, Lokalizacja punktu końcowego nie nakłada żadnych ograniczeń dotyczących tego, które regiony można zamapować na nie. Na przykład punkt końcowy w regionie US-Central platformy Azure może mieć do niego kierowany wszyscy użytkownicy z Indii.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Czy można przypisywać regiony geograficzne do punktów końcowych w profilu, który nie jest skonfigurowany do routingu geograficznego?

Tak, jeśli metoda routingu profilu nie jest geograficzna, można użyć [interfejsu API REST platformy Azure Traffic Manager](/rest/api/trafficmanager/) do przypisywania regionów geograficznych do punktów końcowych w tym profilu. W przypadku profilów typu routingu geograficznego ta konfiguracja jest ignorowana. Jeśli zmienisz taki profil na typ routingu geograficznego w późniejszym czasie, Traffic Manager mogą używać tych mapowań.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Dlaczego otrzymuję błąd podczas próby zmiany metody routingu istniejącego profilu na geograficzny?

Wszystkie punkty końcowe w profilu z routingiem geograficznym muszą mieć zamapowane co najmniej jeden region. Aby przekonwertować istniejący profil na typ routingu geograficznego, najpierw musisz skojarzyć regiony geograficzne ze wszystkimi punktami końcowymi przy użyciu [interfejsu API REST platformy Azure Traffic Manager](/rest/api/trafficmanager/) przed zmianą typu routingu na geograficzny. Jeśli używasz portalu, najpierw usuń punkty końcowe, Zmień metodę routingu profilu na geograficzną, a następnie Dodaj punkty końcowe wraz z ich mapowaniem regionu geograficznego.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Dlaczego zdecydowanie zaleca się, aby klienci utworzyli profile zagnieżdżone zamiast punktów końcowych w ramach profilu z włączonym routingiem geograficznym?

Region można przypisać tylko do jednego punktu końcowego w ramach profilu, jeśli jest on używany przy użyciu metody routingu geograficznego. Jeśli ten punkt końcowy nie jest zagnieżdżonym typem z dołączonym do niego profilem podrzędnym, jeśli ten punkt końcowy przestanie być w złej kondycji, Traffic Manager nadal wysyła ruch do niego, ponieważ alternatywa niewysyłania żadnego ruchu nie jest lepsza. Traffic Manager nie przejdzie w tryb failover do innego punktu końcowego, nawet jeśli przypisany region jest "nadrzędny" regionu przypisanego do punktu końcowego, który przeszedł w stan złej kondycji (na przykład jeśli punkt końcowy z regionem Hiszpania jest w złej kondycji) nie przejdzie w tryb failover do innego punktu końcowego, który ma przypisany region Europe. W tym celu należy się upewnić, że Traffic Manager uwzględnia granice geograficzne, które klient skonfigurował w swoich profilach. Aby skorzystać z przełączenia w tryb failover do innego punktu końcowego, gdy punkt końcowy jest w złej kondycji, zaleca się, aby regiony geograficzne były przypisywane do zagnieżdżonych profilów z wieloma punktami końcowymi, a nie z poszczególnych punktów końcowych. W ten sposób, jeśli punkt końcowy w zagnieżdżonym profilu podrzędnym ulegnie awarii, ruch może przechodzić w tryb failover do innego punktu końcowego w tym samym zagnieżdżonym profilu podrzędnym.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Czy istnieją jakieś ograniczenia dotyczące wersji interfejsu API, która obsługuje ten typ routingu?

Tak, tylko interfejs API w wersji 2017-03-01 i nowszej obsługuje typ routingu geograficznego. Wszystkie starsze wersje interfejsów API nie mogą być używane do tworzenia profilów typu routingu geograficznego lub przypisywania regionów geograficznych do punktów końcowych. Jeśli starsza wersja interfejsu API jest używana do pobierania profilów z subskrypcji platformy Azure, żaden profil typu routingu geograficznego nie jest zwracany. Ponadto w przypadku korzystania ze starszych wersji interfejsu API każdy zwrócony profil, który ma punkty końcowe z przypisaniem regionu geograficznego, nie ma pokazywanego przypisania regionu geograficznego.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Metoda routingu ruchu podsieci Traffic Manager

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Jakie są przypadki użycia, w których Routing podsieci jest przydatny?

Routing podsieci pozwala odróżnić środowisko użytkownika dla konkretnych zestawów użytkowników identyfikowane przez źródłowy adres IP żądań DNS. Przykładem będzie pokazywane inne treści, jeśli użytkownicy nawiązują połączenie z witryną internetową z CENTRALĄ firmowej. Innym z nich będzie ograniczać dostęp użytkowników z określonych usługodawców do punktów końcowych, które obsługują tylko połączenia IPv4, jeśli te usługodawcy mają wydajność podrzędną w przypadku użycia protokołu IPv6.
Kolejną przyczyną użycia metody routingu podsieci jest w połączeniu z innymi profilami w zagnieżdżonym zestawie profilów. Na przykład, jeśli chcesz używać metody routingu geograficznego dla użytkowników z ogrodzeniem geograficznym, ale dla określonego usługodawcy internetowego chcesz wykonać inną metodę routingu, możesz mieć profil z metodą routingu podsieci jako profil nadrzędny i przesłonić tego usługodawcę internetowego do korzystania z określonego profilu podrzędnego i posiadania standardowego profilu geograficznego dla wszystkich innych.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Jak Traffic Manager znać adres IP użytkownika końcowego?

Urządzenia użytkowników końcowych zazwyczaj używają programu rozpoznawania nazw DNS do przeszukiwania DNS w ich imieniu. Wychodzący adres IP takich resolverów jest Traffic Manager, który jest widoczny jako źródłowy adres IP. Ponadto Metoda routingu podsieci sprawdza także, czy istnieją informacje o rozszerzonych podsieciach klienta (ECS), które zostały przesłane do żądania. Jeśli są obecne informacje ECS, jest to adres używany do określenia routingu. W przypadku braku informacji ECS źródłowy adres IP zapytania jest używany do celów routingu.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Jak można określić adresy IP w przypadku używania routingu podsieci?

Adresy IP do skojarzenia z punktem końcowym można określić na dwa sposoby. Aby określić zakres (na przykład 1.2.3.4-5.6.7.8 lub 3.4.5.6-3.4.5.6), w pierwszej kolejności można użyć notacji z kropkami dziesiętnymi w formacie czterech kropek z adresami początkowymi i końcowymi. Następnie można użyć notacji CIDR, aby określić zakres (na przykład 1.2.3.0/24). Można określić wiele zakresów i można użyć obu typów notacji w zestawie zakresów. Obowiązuje kilka ograniczeń.

-    Nie można nakładać się na zakresy adresów, ponieważ każdy adres IP musi być zamapowany na tylko jeden punkt końcowy
-    Adres początkowy nie może być większy niż adres końcowy
-    W przypadku notacji CIDR adres IP przed "/" powinien być adresem początkowym tego zakresu (na przykład 1.2.3.0/24 jest prawidłowy, ale 1.2.3.4.4/24 jest nieprawidłowy)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Jak określić rezerwowy punkt końcowy w przypadku używania routingu podsieci?

W profilu z routingiem podsieci, jeśli masz punkt końcowy bez zmapowanych podsieci, wszystkie żądania, które nie są zgodne z innymi punktami końcowymi, będą kierowane do tego miejsca. Zdecydowanie zaleca się, aby w Twoim profilu był używany rezerwowy punkt końcowy, ponieważ Traffic Manager zwróci odpowiedź NXDOMAIN, jeśli żądanie się znajdzie, i nie jest zamapowana na żadne punkty końcowe lub jest zamapowana na punkt końcowy, ale ten punkt końcowy ma złą kondycję.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Co się stanie, jeśli punkt końcowy jest wyłączony w profilu typu routingu podsieci?

Jeśli istnieje punkt końcowy z wyłączonym profilem z routingiem podsieci, Traffic Manager będzie zachowywać się tak, jakby ten punkt końcowy i mapowania podsieci nie istnieją. Jeśli zostanie odebrane zapytanie, które byłoby dopasowane do mapowania adresów IP, a punkt końcowy jest wyłączony, Traffic Manager zwróci rezerwowy punkt końcowy (jeden bez mapowań) lub jeśli taki punkt końcowy nie istnieje, zwróci odpowiedź NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager wielowartościowa Metoda routingu ruchu

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Jakie są przypadki użycia, w których Routing z wieloma wartościami jest przydatny?

Routing z wieloma wartościami zwraca wiele prawidłowych punktów końcowych w pojedynczej odpowiedzi na zapytanie. Główną zaletą jest to, że jeśli punkt końcowy jest w złej kondycji, klient programu ma więcej opcji, aby ponowić próbę bez tworzenia innego wywołania DNS (które może zwracać tę samą wartość z nadrzędnej pamięci podręcznej). Ma to zastosowanie w przypadku aplikacji wrażliwych na dostępność, które chcą zminimalizować przestoje.
Innym zastosowaniem metody routingu z wieloma wartościami jest to, że punkt końcowy jest "dwuadresowy" do adresów IPv4 i IPv6 i chcesz nadać obiektowi wywołującemu obie opcje do wyboru, gdy inicjuje połączenie z punktem końcowym.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Ile punktów końcowych jest zwracanych w przypadku użycia routingu wielowartościowego?

Można określić maksymalną liczbę punktów końcowych do zwrócenia, a wiele z nich będzie zwracać nie więcej niż w przypadku odebrania zapytania. Maksymalna możliwa wartość tej konfiguracji to 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Czy w przypadku użycia routingu wielowartościowego otrzymasz ten sam zestaw punktów końcowych?

Nie możemy zagwarantować, że w każdym zapytaniu zostanie zwrócony ten sam zestaw punktów końcowych. Dotyczy to również faktu, że niektóre punkty końcowe mogą działać w złej kondycji, w którym punkt nie zostanie uwzględniony w odpowiedzi.

## <a name="real-user-measurements"></a>Pomiary rzeczywistego użytkownika

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Jakie korzyści wynikają z używania Pomiary rzeczywistego użytkownika?

W przypadku korzystania z metody routingu wydajności Traffic Manager wybiera najlepszy region platformy Azure dla użytkownika końcowego, aby mógł nawiązać połączenie, sprawdzając źródłową wartość IP i podsieć klienta mechanizmów EDNS (jeśli została przeniesiona) i sprawdzając ją względem analizy opóźnienia sieci obsługiwanej przez usługę. Pomiary rzeczywistego użytkownika rozszerza to na bazę użytkowników końcowych, ponieważ ich środowisko wpływa na tę tabelę opóźnień, a także zapewnia, że ta tabela odpowiednio obejmuje sieci użytkowników końcowych, z których użytkownicy końcowi łączą się z platformą Azure. Prowadzi to do zwiększonej dokładności routingu użytkownika końcowego.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Czy mogę używać Pomiary rzeczywistego użytkownika z regionami nienależącymi do platformy Azure?

Pomiary rzeczywistego użytkownika miary i raporty dotyczące tylko opóźnień, aby osiągnąć regiony platformy Azure. Jeśli używasz routingu opartego na wydajności dla punktów końcowych hostowanych w regionach spoza platformy Azure, możesz skorzystać z tej funkcji, wprowadzając zwiększone informacje o opóźnieniu dotyczące reprezentatywnego regionu platformy Azure, który został wybrany do skojarzenia z tym punktem końcowym.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Która metoda routingu przynosi korzyści z Pomiary rzeczywistego użytkownika?

Dodatkowe informacje uzyskane za pomocą Pomiary rzeczywistego użytkownika są stosowane tylko w przypadku profilów korzystających z metody routingu wydajności. Łącze Pomiary rzeczywistego użytkownika jest dostępne ze wszystkich profilów podczas wyświetlania go za pośrednictwem Azure Portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Czy muszę włączyć Pomiary rzeczywistego użytkownika każdy profil osobno?

Nie, wystarczy włączyć ją raz na subskrypcję, a wszystkie informacje o opóźnieniu mierzone i raportowane są dostępne dla wszystkich profilów.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Jak mogę wyłączyć Pomiary rzeczywistego użytkownika dla mojej subskrypcji?

Można zatrzymać naliczanie opłat związanych z Pomiary rzeczywistego użytkownika po zatrzymaniu zbierania i wysyłania pomiarów opóźnienia z aplikacji klienckiej. Na przykład podczas mierzenia kodu JavaScript osadzonego na stronach sieci Web można zatrzymać korzystanie z tej funkcji, usuwając kod JavaScript lub wyłączając jego wywołanie, gdy strona jest renderowana.

Możesz również wyłączyć Pomiary rzeczywistego użytkownika, usuwając klucz. Po usunięciu klucza wszystkie pomiary wysyłane do Traffic Manager z tym kluczem są odrzucane.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Czy mogę używać Pomiary rzeczywistego użytkownika z aplikacjami klienckimi innymi niż strony sieci Web?

Tak, Pomiary rzeczywistego użytkownika zaprojektowano w celu pozyskiwania danych zebranych przez innego typu klientów końcowych. Te często zadawane pytania zostaną zaktualizowane w miarę obsługi nowych typów aplikacji klienckich.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Ile pomiarów jest wykonywanych za każdym razem, gdy jest renderowana Strona sieci Web z włączonymi Pomiary rzeczywistego użytkownikaami?

Gdy Pomiary rzeczywistego użytkownika jest używany z pomiarem języka JavaScript dostarczone, każda strona renderuje wyniki w sześciu pomiarach. Następnie są one raportowane z powrotem do usługi Traffic Manager. Opłata jest naliczana za tę funkcję na podstawie liczby pomiarów raportowanych do Traffic Manager usługi. Na przykład jeśli użytkownik wychodzi poza stronę sieci Web, podczas gdy pomiary są wykonywane, ale przed zgłoszeniem, te pomiary nie są brane pod uwagę na potrzeby rozliczania.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Czy istnieje opóźnienie przed uruchomieniem skryptu Pomiary rzeczywistego użytkownika na mojej stronie sieci Web?

Nie, nie ma opóźnienia programowego przed wywołaniem skryptu.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Czy mogę używać Pomiary rzeczywistego użytkownika tylko z regionami platformy Azure, które mają być mierzone?

Nie, za każdym razem, gdy jest wywoływany, skrypt Pomiary rzeczywistego użytkownika mierzy zestaw sześciu regionów świadczenia usługi Azure określony przez usługę. Ten zestaw zmienia się między różnymi wywołaniami i gdy występuje duża liczba takich wywołań, pokrycie pomiaru obejmuje różne regiony platformy Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Czy mogę ograniczyć liczbę pomiarów wprowadzonych do określonego numeru?

Pomiar JavaScript jest osadzony w stronie sieci Web i masz pełną kontrolę nad rozpoczęciem i zatrzymywaniem korzystania z niego. Tak długo, jak Usługa Traffic Manager otrzymuje żądanie dotyczące listy regionów platformy Azure, które mają być mierzone, zostanie zwrócony zestaw regionów.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Czy mogę zobaczyć pomiary wykonywane przez aplikację kliencką w ramach Pomiary rzeczywistego użytkownika?

Ponieważ logika pomiaru jest uruchamiana z poziomu aplikacji klienckiej, masz pełną kontrolę nad tym, co się dzieje, w tym zobaczą pomiary opóźnienia. Traffic Manager nie zgłasza zagregowanego widoku pomiarów otrzymanych w ramach klucza połączonego z subskrypcją.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Czy mogę zmodyfikować skrypt pomiarowy dostarczony przez Traffic Manager?

Mimo że masz kontrolę nad tym, co jest osadzone na stronie sieci Web, zdecydowanie odradzamy wprowadzanie zmian do skryptu pomiarowego, aby upewnić się, że środki IT i poprawnie zgłaszają opóźnienia.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Czy będzie możliwe, aby inni mogli zobaczyć klucz, z którego korzystam z Pomiary rzeczywistego użytkownika?

Po osadzeniu skryptu pomiaru na stronie sieci Web będzie możliwe wyświetlenie skryptu i klucza Pomiary rzeczywistego użytkownika (RUM) przez inne osoby. Należy jednak pamiętać, że ten klucz różni się od identyfikatora subskrypcji i jest generowany przez Traffic Manager do użycia tylko do tego celu. Znajomość klucza RUM nie spowoduje naruszenia bezpieczeństwa konta platformy Azure.

### <a name="can-others-abuse-my-rum-key"></a>Czy inne osoby mogą używać klucza rumu?

Chociaż jest możliwe, że inne osoby mogą korzystać z klucza w celu wysyłania nieprawidłowych informacji do platformy Azure, to niektóre błędne pomiary nie zmienią trasy, ponieważ są brane pod uwagę wraz ze wszystkimi innymi pomiarami, które otrzymamy. Jeśli musisz zmienić klucze, możesz ponownie wygenerować klucz, w którym zostanie odrzucony stary klucz.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Czy muszę umieścić miarę JavaScript na wszystkich moich stronach sieci Web?

Pomiary rzeczywistego użytkownika zapewnia większą wartość w miarę wzrostu liczby pomiarów. Z tego względu należy podjąć decyzję, czy należy umieścić ją we wszystkich stronach sieci Web, czy na kilka wybranych. Zalecamy rozpoczęcie od umieszczenia go na najbardziej odwiedzonej stronie, w której użytkownik powinien pozostać na tej stronie pięć sekund lub dłużej.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Czy informacje o użytkownikach końcowych są identyfikowane przez Traffic Manager, jeśli używam Pomiary rzeczywistego użytkownika?

W przypadku użycia dostarczonego pomiaru języka JavaScript Traffic Manager będzie miał wgląd w adres IP klienta użytkownika końcowego i źródłowy adres IP lokalnego programu rozpoznawania nazw DNS, których używają. Traffic Manager używa adresu IP klienta tylko po jego obcięciu, aby nie mógł identyfikować określonego użytkownika końcowego, który wysłał pomiary.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Czy funkcja mierzenia Pomiary rzeczywistego użytkownika musi używać Traffic Manager do routingu?

Nie, nie trzeba używać Traffic Manager. Strona Routing Traffic Manager działa niezależnie od części pomiarowej rzeczywistego użytkownika i chociaż jest dobrym pomysłem, aby były one zarówno w tej samej właściwości sieci Web, jak i nie muszą być.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Czy muszę hostować każdą usługę w regionach platformy Azure, która ma być używana z Pomiary rzeczywistego użytkownika?

Nie musisz hostować żadnego składnika po stronie serwera na platformie Azure, aby Pomiary rzeczywistego użytkownika do pracy. Obraz o pojedynczym pikselach pobrany przez miarę JavaScript i usługa, która działa w różnych regionach świadczenia usługi Azure, jest hostowany i zarządzany przez platformę Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Czy moja przepustowość platformy Azure zwiększa się, gdy używam Pomiary rzeczywistego użytkownika?

Jak wspomniano w poprzedniej odpowiedzi, składniki po stronie serwera Pomiary rzeczywistego użytkownika są własnością i są zarządzane przez platformę Azure. Oznacza to, że użycie przepustowości platformy Azure nie zwiększy się, ponieważ używasz Pomiary rzeczywistego użytkownika. Nie obejmuje to żadnego użycia przepustowości poza obciążeniami platformy Azure. Minimalizujemy przepustowość używaną przez pobranie tylko jednego obrazu pikselowego w celu zmierzenia opóźnienia w regionie świadczenia usługi Azure. 

## <a name="traffic-view"></a>Widok ruchu

### <a name="what-does-traffic-view-do"></a>Co robią Widok ruchu?

Widok ruchu to funkcja Traffic Manager, która pomaga zrozumieć więcej informacji o użytkownikach i sposobie ich działania. Używa zapytań odebranych przez Traffic Manager i tabel analizy opóźnienia sieci, które obsługuje usługa, w celu zapewnienia następujących danych:

- Regiony, z których użytkownicy nawiązują połączenie z punktami końcowymi na platformie Azure.
- Liczba użytkowników łączących się z tych regionów.
- Regiony platformy Azure, do których są kierowane.
- Ich czas opóźnienia w tych regionach świadczenia usługi Azure.

Te informacje są dostępne do użycia w ramach nakładki mapy geograficznej i widoków tabelarycznych w portalu, a także dostępne jako pierwotne dane do pobrania.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Jak mogę skorzystać z Widok ruchu?

Widok ruchu zapewnia ogólny widok ruchu, który odbiera Traffic Manager profilów. W szczególności można go użyć, aby zrozumieć, w jaki sposób baza użytkownika nawiązuje połączenie z usługą i równie ważne. Możesz następnie użyć tych informacji, aby znaleźć obszary, w których należy skoncentrować się na przykład przez zwiększenie zasięgu platformy Azure do regionu, który może obsłużyć te użytkowników z mniejszym opóźnieniem. Innym wglądem, z którego można skorzystać, Widok ruchu jest wyświetlenie wzorców ruchu do różnych regionów, które z kolei mogą pomóc w podejmowaniu decyzji o zwiększeniu lub zmniejszeniu możliwości w tych regionach.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Jak różnią się Widok ruchu od metryk Traffic Manager dostępnych za pośrednictwem usługi Azure monitor?

Azure Monitor może służyć do zrozumienia na zagregowanym poziomie ruchu odbieranego przez profil i jego punkty końcowe. Umożliwia również śledzenie stanu kondycji punktów końcowych przez ujawnienie wyników kontroli kondycji. Jeśli musisz przekroczyć te i zrozumieć środowisko użytkownika końcowego łączące się z platformą Azure na poziomie regionalnym, Widok ruchu mogą być używane do osiągnięcia tego.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Czy Widok ruchu używać informacji o podsieci klienta mechanizmów EDNS?

Zapytania DNS obsługiwane przez platformę Azure Traffic Manager należy wziąć pod uwagę ECS informacji w celu zwiększenia dokładności routingu. Ale podczas tworzenia zestawu danych, który pokazuje, z których użytkowników nawiązuje połączenie, Widok ruchu używa tylko adresu IP programu rozpoznawania nazw DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Ile dni danych używa Widok ruchu?

Widok ruchu tworzy dane wyjściowe przez przetwarzanie danych z siedmiu dni poprzedzających dzień przed wyświetleniem go. Jest to przenoszone okno, a najnowsze dane będą używane przy każdym odwiedzeniu.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Jak Widok ruchu obsługiwać zewnętrzne punkty końcowe?

W przypadku korzystania z zewnętrznych punktów końcowych hostowanych poza regionami platformy Azure w profilu Traffic Manager można wybrać opcję mapowania do regionu platformy Azure, który jest serwerem proxy dla jego charakterystyki opóźnienia (jest to konieczne w przypadku używania metody routingu wydajności). Jeśli ma to mapowanie regionów platformy Azure, to metryki opóźnienia regionu platformy Azure będą używane podczas tworzenia Widok ruchu danych wyjściowych. Jeśli nie określono żadnego regionu platformy Azure, informacje o opóźnieniu będą puste w danych dla tych zewnętrznych punktów końcowych.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Czy muszę włączyć Widok ruchu dla każdego profilu w mojej subskrypcji?

W okresie zapoznawczym Widok ruchu była włączona na poziomie subskrypcji. W ramach ulepszeń wprowadzonych przed ogólną dostępnością można teraz włączyć Widok ruchu na poziomie profilu, co pozwala na bardziej szczegółowe włączenie tej funkcji. Domyślnie Widok ruchu zostanie wyłączona dla profilu.

>[!NOTE]
>Jeśli w trakcie okresu zapoznawczego włączono Widok ruchu na poziomie subskrypcji, musisz teraz ponownie włączyć go dla każdego profilu w ramach tej subskrypcji.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Jak mogę wyłączyć Widok ruchu?

Widok ruchu dla dowolnego profilu można wyłączyć przy użyciu portalu lub interfejsu API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Jak działa rozliczanie Widok ruchu?

Cennik Widok ruchu jest określany na podstawie liczby punktów danych użytych do utworzenia danych wyjściowych. Obecnie jedynym obsługiwanym typem danych jest zapytanie odbierane przez profil. Dodatkowo opłaty są naliczane tylko za przetwarzanie, które zostało wykonane po włączeniu Widok ruchu. Oznacza to, że jeśli włączysz Widok ruchu przez pewien czas w miesiącu i wyłączysz ją w inny sposób, tylko te punkty danych zostały przetworzone w ramach liczby włączonych funkcji do rozliczenia.

## <a name="traffic-manager-endpoints"></a>Punkty końcowe usługi Traffic Manager

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Czy można używać Traffic Manager z punktami końcowymi z wielu subskrypcji?

Korzystanie z punktów końcowych z wielu subskrypcji nie jest możliwe za pomocą usługi Azure Web Apps. Usługa Azure Web Apps wymaga, aby dowolna nazwa domeny niestandardowej użyta z Web Apps była używana tylko w ramach jednej subskrypcji. Nie można używać Web Apps z wielu subskrypcji o tej samej nazwie domeny.

W przypadku innych typów punktów końcowych można użyć Traffic Manager z punktami końcowymi z więcej niż jednej subskrypcji. W Menedżer zasobów punkty końcowe z dowolnych subskrypcji można dodać do Traffic Manager, o ile osoba konfigurująca profil Traffic Manager ma dostęp do odczytu do punktu końcowego. Uprawnienia te można przyznawać przy użyciu [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Punkty końcowe z innych subskrypcji można dodawać przy użyciu [Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Czy można użyć Traffic Manager z gniazdami "przemieszczania" usługi w chmurze?

Tak. Miejsca przejściowe usługi w chmurze można skonfigurować w Traffic Manager jako zewnętrzne punkty końcowe. Opłaty za sprawdzanie kondycji są nadal naliczane według stawki za Punkty końcowe platformy Azure.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Czy Traffic Manager obsługuje punkty końcowe protokołu IPv6?

Traffic Manager obecnie nie udostępnia serwerów nazw z adresami IPv6. Jednak Traffic Manager nadal mogą być używane przez klientów IPv6 łączących się z punktami końcowymi protokołu IPv6. Klient nie wysyła żądań DNS bezpośrednio do Traffic Manager. Zamiast tego klient używa cyklicznej usługi DNS. Klient korzystający z protokołu IPv6 wysyła żądania do cyklicznej usługi DNS za pośrednictwem protokołu IPv6. Następnie usługa cykliczna powinna mieć możliwość kontaktowania się z serwerami nazw Traffic Manager przy użyciu protokołu IPv4.

Traffic Manager reaguje na nazwę DNS lub adres IP punktu końcowego. Aby obsługiwać punkt końcowy IPv6, dostępne są dwie opcje. Możesz dodać punkt końcowy jako nazwę DNS, która ma skojarzony rekord AAAA, a Traffic Manager będzie sprawdzić kondycję tego punktu końcowego i zwrócić go jako typ rekordu CNAME w odpowiedzi na zapytanie. Możesz również dodać ten punkt końcowy bezpośrednio przy użyciu adresu IPv6, a Traffic Manager zwróci rekord typu AAAA w odpowiedzi na zapytanie.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Czy można użyć Traffic Manager z więcej niż jedną aplikacją sieci Web w tym samym regionie?

Zwykle Traffic Manager jest używany do kierowania ruchu do aplikacji wdrożonych w różnych regionach. Można go jednak również używać w przypadku, gdy aplikacja ma więcej niż jedno wdrożenie w tym samym regionie. Traffic Manager punkty końcowe platformy Azure nie zezwalają na dodanie do tego samego profilu Traffic Manager więcej niż jednego punktu końcowego aplikacji sieci Web z tego samego regionu platformy Azure.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Jak mogę przenieść punkty końcowe platformy Azure profilu Traffic Manager do innej grupy zasobów lub subskrypcji?

Punkty końcowe platformy Azure skojarzone z profilem Traffic Manager są śledzone przy użyciu ich identyfikatorów zasobów. Gdy zasób platformy Azure używany jako punkt końcowy (na przykład publiczny adres IP, klasyczna usługa w chmurze, WebApp lub inny profil Traffic Manager używany w sposób zagnieżdżony) jest przenoszony do innej grupy zasobów lub subskrypcji, jego identyfikator zasobu zmieni się. W tym scenariuszu należy obecnie zaktualizować profil Traffic Manager, usuwając najpierw, a następnie dodając punkty końcowe do profilu.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitorowanie punktu końcowego usługi Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Czy Traffic Manager jest odporny na awarie regionu platformy Azure?

Traffic Manager to kluczowy składnik dostarczania aplikacji o wysokiej dostępności na platformie Azure.
Aby zapewnić wysoką dostępność, Traffic Manager musi mieć wyjątkowo wysoki poziom dostępności i być odporny na awarie regionalne.

Po zaprojektowaniu składniki Traffic Manager są odporne na pełną awarię dowolnego regionu platformy Azure. Ta odporność dotyczy wszystkich składników Traffic Manager: serwerów nazw DNS, interfejsu API, warstwy magazynu i usługi monitorowania punktów końcowych.

W mało prawdopodobnym przypadku awarii całego regionu świadczenia usługi Azure oczekuje się, że Traffic Manager nadal działają normalnie. Aplikacje wdrożone w wielu regionach platformy Azure mogą polegać na Traffic Manager, aby skierować ruch do dostępnego wystąpienia aplikacji.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Jak wybór lokalizacji grupy zasobów ma wpływ na Traffic Manager?

Traffic Manager jest pojedynczą globalną usługą. Nie jest regionalna. Wybór lokalizacji grupy zasobów nie powoduje żadnych różnic Traffic Manager profilów wdrożonych w tej grupie zasobów.

Azure Resource Manager wymaga, aby wszystkie grupy zasobów określiły lokalizację, która określa domyślną lokalizację zasobów wdrożonych w danej grupie zasobów. Podczas tworzenia profilu Traffic Manager jest on tworzony w grupie zasobów. Wszystkie profile Traffic Manager używają **globalnej** jako lokalizacji, zastępując domyślną grupę zasobów.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Jak mogę określić aktualną kondycję każdego punktu końcowego?

Bieżący stan monitorowania każdego punktu końcowego, oprócz ogólnego profilu, jest wyświetlany w Azure Portal. Te informacje są również dostępne za pośrednictwem [interfejsu API REST](/rest/api/trafficmanager/)monitora ruchu, [poleceń cmdlet programu PowerShell](/powershell/module/az.trafficmanager)i [międzyplatformowego interfejsu wiersza polecenia platformy Azure](/cli/azure/install-classic-cli).

Możesz również użyć Azure Monitor do śledzenia kondycji punktów końcowych i wyświetlania wizualnej reprezentacji. Aby uzyskać więcej informacji na temat korzystania z Azure Monitor, zobacz [dokumentację dotyczącą monitorowania platformy Azure](../azure-monitor/data-platform.md).

### <a name="can-i-monitor-https-endpoints"></a>Czy mogę monitorować punkty końcowe HTTPS?

Tak. Traffic Manager obsługuje sondowanie za pośrednictwem protokołu HTTPS. Skonfiguruj **https** jako protokół w konfiguracji monitorowania.

Menedżer ruchu nie może zapewnić żadnej weryfikacji certyfikatu, w tym:

* Certyfikaty po stronie serwera nie są weryfikowane
* SNI certyfikaty po stronie serwera nie są sprawdzane
* Certyfikaty klienta nie są obsługiwane

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Czy podczas dodawania punktu końcowego używam adresu IP lub nazwy DNS?

Traffic Manager obsługuje dodawanie punktów końcowych przy użyciu trzech sposobów odwoływania się do nich — jako nazwę DNS jako adres IPv4 i jako adres IPv6. Jeśli punkt końcowy zostanie dodany jako adres IPv4 lub IPv6, odpowiedź na zapytanie będzie dotyczyć rekordu typu A lub AAAA. Jeśli punkt końcowy został dodany jako nazwa DNS, odpowiedź na zapytanie będzie mieć typ rekordu CNAME. Dodawanie punktów końcowych jako adresów IPv4 lub IPv6 jest dozwolone tylko wtedy, gdy punkt końcowy jest typu **zewnętrznego**.
Wszystkie metody routingu i ustawienia monitorowania są obsługiwane przez trzy typy adresów punktu końcowego.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Jakiego typu adresy IP można używać podczas dodawania punktu końcowego?

Traffic Manager pozwala określić punkty końcowe przy użyciu adresów IPv4 lub IPv6. Istnieje kilka ograniczeń, które są wymienione poniżej:

- Adresy odnoszące się do zarezerwowanych prywatnych przestrzeni adresowych IP są niedozwolone. Te adresy obejmują te, które zostały wywołane w dokumencie RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 i RFC 5771
- Adres nie może zawierać żadnych numerów portów (można określić porty, które mają być używane w ustawieniach konfiguracji profilu).
- Żadne dwa punkty końcowe w tym samym profilu nie mogą mieć tego samego docelowego adresu IP

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Czy można używać różnych typów adresowania punktów końcowych w ramach jednego profilu?

Nie, Traffic Manager nie zezwala na mieszanie typów adresowania punktów końcowych w ramach profilu, z wyjątkiem sytuacji, gdy profil ma typ routingu o wartości wielowartościowej, w którym można mieszać typy adresów IPv4 i IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Co się stanie, gdy typ rekordu zapytania przychodzącego różni się od typu rekordu skojarzonego z typem adresowania punktów końcowych?

Po odebraniu zapytania dotyczącego profilu Traffic Manager najpierw znaleźć punkt końcowy, który musi zostać zwrócony zgodnie z określoną metodą routingu, oraz stan kondycji punktów końcowych. Następnie szuka typu rekordu żądanego w zapytaniu przychodzącym i typu rekordu skojarzonego z punktem końcowym przed zwróceniem odpowiedzi na podstawie poniższej tabeli.

W przypadku profilów z dowolną metodą routingu inną niż wielowartościowy:

|Przychodzące żądanie zapytania|     Typ punktu końcowego|     Podano odpowiedź|
|--|--|--|
|WSZELKIE |    A/AAAA/CNAME |    Docelowy punkt końcowy| 
|A |    A/CNAME |    Docelowy punkt końcowy|
|A |    AAAA |    NoData |
|AAAA |    AAAA/CNAME |    Docelowy punkt końcowy|
|AAAA |    A |    NoData |
|CNAME |    CNAME |    Docelowy punkt końcowy|
|CNAME     |A/AAAA |    NoData |
|

W przypadku profilów z metodą routingu ustawioną na wartość z wieloma wartościami:

|Przychodzące żądanie zapytania|     Typ punktu końcowego |    Podano odpowiedź|
|--|--|--|
|WSZELKIE |    Mieszanie a i AAAA |    Docelowe punkty końcowe|
|A |    Mieszanie a i AAAA |    Tylko docelowe punkty końcowe typu A|
|AAAA    |Mieszanie a i AAAA|     Tylko docelowe punkty końcowe typu AAAA|
|CNAME |    Mieszanie a i AAAA |    NoData |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Czy mogę użyć profilu z adresami końcowymi IPv4/IPv6 w profilu zagnieżdżonym?

Tak, można z wyjątkiem, że profil typu wielowartościowego nie może być profilem nadrzędnym w zagnieżdżonym zestawie profilów.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Punkt końcowy aplikacji sieci Web został zatrzymany w profilu Traffic Manager, ale nie odbieram żadnego ruchu nawet po ponownym uruchomieniu. Jak można to naprawić?

Gdy punkt końcowy aplikacji sieci Web platformy Azure zostanie zatrzymany Traffic Manager zatrzymuje Sprawdzanie kondycji i ponownie uruchamia testy kondycji po wykryciu, że punkt końcowy został ponownie uruchomiony. Aby zapobiec tym opóźnieniu, należy wyłączyć i ponownie włączyć ten punkt końcowy w profilu Traffic Manager po ponownym uruchomieniu punktu końcowego.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Czy mogę używać Traffic Manager, nawet jeśli moja aplikacja nie obsługuje protokołu HTTP lub HTTPS?

Tak. Możesz określić TCP jako protokół monitorowania i Traffic Manager może zainicjować połączenie TCP i poczekać na odpowiedź z punktu końcowego. Jeśli punkt końcowy odpowie na żądanie połączenia z odpowiedzią na nawiązanie połączenia, w ramach przekroczenia limitu czasu ten punkt końcowy jest oznaczony jako w dobrej kondycji.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Jakie konkretne odpowiedzi są wymagane z punktu końcowego w przypadku korzystania z funkcji monitorowania protokołu TCP?

Gdy jest używane monitorowanie TCP, Traffic Manager uruchamia trzykrotne uzgadnianie TCP, wysyłając żądanie SYN do punktu końcowego na określonym porcie. Następnie czeka na odpowiedź SYN-ACK z punktu końcowego przez określony czas (określony w ustawieniach limitu czasu).

- Jeśli odebrano odpowiedź SYN-ACK w okresie limitu czasu określonym w ustawieniach monitorowania, ten punkt końcowy jest uznawany za prawidłowy. Wartość "FIN" lub "FIN-ACK" to oczekiwana odpowiedź z Traffic Manager, gdy regularnie kończy gniazdo.
- Jeśli odebrano odpowiedź SYN-ACK po upływie określonego limitu czasu, Traffic Manager będzie odpowiadać z RST, aby zresetować połączenie.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Jak szybko Traffic Manager przenieść moich użytkowników poza punkt końcowy w złej kondycji?

Traffic Manager udostępnia wiele ustawień, które mogą ułatwić sterowanie zachowaniem trybu failover profilu Traffic Manager w następujący sposób:

- można określić, że Traffic Manager sondować punkty końcowe częściej, ustawiając interwał sondowania o 10 sekund. Dzięki temu można wykryć dowolny punkt końcowy w złej kondycji, jak najszybciej. 
- Możesz określić czas oczekiwania przed upływem limitu czasu żądania sprawdzenia kondycji (minimalny limit czasu wynosi 5 sekund).
- Możesz określić liczbę błędów, które mogą wystąpić, zanim punkt końcowy zostanie oznaczony jako w złej kondycji. Ta wartość może być niska jako 0. w takim przypadku punkt końcowy jest oznaczony jako w złej kondycji, gdy tylko zakończy się niepowodzeniem pierwszego sprawdzania kondycji. Jednak użycie minimalnej wartości 0 w przypadku tolerowanej liczby błędów może prowadzić do wypróbowania punktów końcowych z powodu wszelkich przejściowych problemów, które mogą wystąpić podczas sondowania.
- Możesz określić czas wygaśnięcia (TTL) odpowiedzi systemu DNS na wartość równą 0. Oznacza to, że resolvery DNS nie mogą buforować odpowiedzi, a każde nowe zapytanie otrzymuje odpowiedź, która zawiera najnowsze informacje o kondycji, które ma Traffic Manager.

Korzystając z tych ustawień, Traffic Manager może zapewnić tryb failover w ciągu 10 sekund, gdy punkt końcowy jest w złej kondycji, a zapytanie DNS jest nawiązywane z odpowiednim profilem.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Jak można określić różne ustawienia monitorowania dla różnych punktów końcowych w profilu?

Ustawienia monitorowania Traffic Manager są na poziomie profilu. Jeśli konieczne jest użycie innego ustawienia monitorowania tylko dla jednego punktu końcowego, można to zrobić, korzystając z tego punktu końcowego jako [profilu zagnieżdżonego](traffic-manager-nested-profiles.md) , którego ustawienia monitorowania są inne niż w profilu nadrzędnym.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Jak mogę przypisać nagłówki HTTP do punktów końcowych Traffic Managerych kontroli kondycji?

Traffic Manager pozwala określić niestandardowe nagłówki w testach kondycji HTTP (S), które są inicjowane dla punktów końcowych. Jeśli chcesz określić niestandardowy nagłówek, możesz to zrobić na poziomie profilu (odpowiednie dla wszystkich punktów końcowych) lub określić na poziomie punktu końcowego. Jeśli nagłówek jest zdefiniowany na obu poziomach, to jeden z nich zostanie przesłonięty poziomu profilu.
Jeden typowy przypadek użycia dla tego Określa nagłówki hosta, aby żądania Traffic Manager mogły być prawidłowo kierowane do punktu końcowego hostowanego w środowisku wielodostępnym. Innym przypadkiem użycia jest zidentyfikowanie Traffic Manager żądań z dzienników żądań HTTP (S) punktu końcowego

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Z jakiego nagłówka hosta korzystają Sprawdzanie kondycji punktów końcowych?

Jeśli nie podano niestandardowego ustawienia nagłówka hosta, nagłówek hosta używany przez Traffic Manager jest nazwą DNS docelowego punktu końcowego skonfigurowanego w profilu, jeśli jest dostępny.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Jakie są adresy IP, z których pochodzą kontrole kondycji?

Kliknij [tutaj](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) , aby dowiedzieć się, jak pobrać listy adresów IP, z których mogą pochodzić Traffic Manager kontrole kondycji. Aby pobrać najnowszą listę, można użyć interfejsu API REST, wiersza polecenia platformy Azure lub Azure PowerShell. Przejrzyj wymienione adresy IP, aby upewnić się, że w punktach końcowych są dozwolone połączenia przychodzące z tych adresów, aby sprawdzić stan kondycji.

Przykład użycia Azure PowerShell:

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus
$result = $serviceTags.Values | Where-Object { $_.Name -eq "AzureTrafficManager" }
$result.Properties.AddressPrefixes
```

> [!NOTE]
> Publiczne adresy IP mogą ulec zmianie bez powiadomienia. Upewnij się, że pobieranie najnowszych informacji przy użyciu interfejsu API odnajdywania tagów usługi lub pliku JSON do pobrania.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Ile testów kondycji w punkcie końcowym można oczekiwać od Traffic Manager?

Liczba Traffic Manager kontroli kondycji docierających do punktu końcowego zależy od następujących czynników:

- wartość ustawiona dla interwału monitorowania (mniejszy interwał oznacza więcej żądań, które są wydawane w punkcie końcowym w danym okresie).
- Liczba lokalizacji, z których pochodzą kontrole kondycji (adresy IP, z których można oczekiwać, są wymienione w powyższych często zadawanych pytaniach).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Jak mogę otrzymywać powiadomienia, jeśli jeden z punktów końcowych ulegnie awarii?

Jedną z metryk dostarczonych przez Traffic Manager jest stan kondycji punktów końcowych w profilu. Można to zobaczyć jako zagregowane wszystkie punkty końcowe w profilu (na przykład 75% punktów końcowych jest w dobrej kondycji) lub, na poziomie na punkt końcowy. Metryki Traffic Manager są udostępniane za pomocą Azure Monitor i można korzystać z [funkcji alertów](../azure-monitor/alerts/alerts-metric.md) , aby otrzymywać powiadomienia o zmianie stanu kondycji punktu końcowego. Aby uzyskać więcej informacji, zobacz [Traffic Manager metryki i alerty](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager zagnieżdżonych profilów

### <a name="how-do-i-configure-nested-profiles"></a>Jak mogę skonfigurować profile zagnieżdżone?

Zagnieżdżone profile Traffic Manager można skonfigurować przy użyciu Azure Resource Manager i klasycznych interfejsów API REST platformy Azure, Azure PowerShell poleceń cmdlet i międzyplatformowych poleceń interfejsu wiersza polecenia platformy Azure. Są one również obsługiwane przez nowe Azure Portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Ile warstw zagnieżdżenia jest obsługiwana przez program Traffic Manager?

Profile można zagnieżdżać do 10 poziomów głębokości. "Pętle" są niedozwolone.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Czy można mieszać inne typy punktów końcowych z zagnieżdżonymi profilami podrzędnymi w tym samym profilu Traffic Manager?

Tak. Nie ma żadnych ograniczeń dotyczących sposobu łączenia punktów końcowych różnych typów w profilu.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Jak ma zastosowanie model rozliczeń dla zagnieżdżonych profilów?

Korzystanie z zagnieżdżonych profilów nie ma negatywnego wpływu na ceny.

Rozliczanie Traffic Manager ma dwa składniki: kontrole kondycji punktów końcowych i miliony zapytań DNS

* Kontrole kondycji punktów końcowych: nie ma opłat za profil podrzędny, gdy jest skonfigurowany jako punkt końcowy w profilu nadrzędnym. Monitorowanie punktów końcowych w profilu podrzędnym jest rozliczane w zwykły sposób.
* Zapytania DNS: poszczególne zapytania są zliczane tylko raz. Zapytanie względem profilu nadrzędnego zwracające punkt końcowy z profilu podrzędnego jest zliczane tylko względem profilu nadrzędnego.

Aby uzyskać szczegółowe informacje, zobacz [stronę z cennikiem Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Czy istnieje wpływ na wydajność profilów zagnieżdżonych?

Nie. W przypadku korzystania z profilów zagnieżdżonych nie ma wpływu na wydajność.

Serwery nazw Traffic Manager przechodzą wewnętrznie hierarchię profilu podczas przetwarzania każdego zapytania DNS. Zapytanie DNS do profilu nadrzędnego może odebrać odpowiedź DNS z punktem końcowym z profilu podrzędnego. Pojedynczy rekord CNAME jest używany niezależnie od tego, czy jest używany profil zagnieżdżony. Nie ma potrzeby tworzenia rekordu CNAME dla każdego profilu w hierarchii.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Jak Traffic Manager obliczać kondycję zagnieżdżonego punktu końcowego w profilu nadrzędnym?

Profil nadrzędny nie przeprowadza bezpośrednio kontroli kondycji elementu podrzędnego. W zamian kondycja punktów końcowych w profilu podrzędnym służy do obliczania ogólnej kondycji profilu podrzędnego. Te informacje są propagowane w hierarchii profilów zagnieżdżonych w celu określenia kondycji zagnieżdżonego punktu końcowego. Profil nadrzędny używa tej zagregowanej kondycji, aby określić, czy ruch może być kierowany do elementu podrzędnego.

W poniższej tabeli opisano zachowanie Traffic Manager Sprawdzanie kondycji dla zagnieżdżonego punktu końcowego.

| Stan monitora podrzędnego | Stan monitora nadrzędnego punktu końcowego | Uwagi |
| --- | --- | --- |
| Wyłączona. Profil podrzędny został wyłączony. |Zatrzymano |Stan nadrzędnego punktu końcowego jest zatrzymany, nie jest wyłączony. Stan wyłączony jest zarezerwowany dla wskazywania, że punkt końcowy został wyłączony w profilu nadrzędnym. |
| Pogorszenie. Co najmniej jeden punkt końcowy profilu podrzędnego jest w stanie obniżonej wydajności. |Online: liczba punktów końcowych w trybie online w profilu podrzędnym jest równa co najmniej wartości MinChildEndpoints.<BR>CheckingEndpoint: liczba punktów końcowych w trybie online i CheckingEndpoint w profilu podrzędnym jest równa co najmniej wartości MinChildEndpoints.<BR>Obniżona wydajność: w przeciwnym razie. |Ruch jest kierowany do punktu końcowego stanu CheckingEndpoint. Jeśli wartość MinChildEndpoints jest zbyt wysoka, punkt końcowy ma zawsze obniżony poziom. |
| Online. Co najmniej jeden punkt końcowy profilu podrzędnego jest stanem online. Brak punktu końcowego w stanie obniżonej wydajności. |Zobacz powyżej. | |
| CheckingEndpoints. Co najmniej jeden punkt końcowy profilu podrzędnego to "CheckingEndpoint". Żadne punkty końcowe nie są w trybie online ani nie zostały obniżone. |Jak wyżej. | |
| Nieaktywne. Wszystkie punkty końcowe profilu podrzędnego są wyłączone lub zatrzymane albo ten profil nie ma punktów końcowych. |Zatrzymano | |

## <a name="next-steps"></a>Następne kroki:

- Dowiedz się więcej na temat Traffic Manager [monitorowania punktów końcowych i automatycznego przełączania do trybu failover](../traffic-manager/traffic-manager-monitoring.md).
- Dowiedz się więcej o [metodach routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md)Traffic Manager.