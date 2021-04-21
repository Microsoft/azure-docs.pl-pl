---
title: Azure Traffic Manager — często zadawane pytania
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
ms.openlocfilehash: 708d63695cbba53578b13d1674b9aa99018bcae4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791125"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager często zadawane pytania

## <a name="traffic-manager-basics"></a>Traffic Manager podstawowe informacje

### <a name="what-ip-address-does-traffic-manager-use"></a>Jakiego adresu IP Traffic Manager używać?

Jak wyjaśniono [w Traffic Manager działa](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie dns. Wysyła odpowiedzi DNS, aby skierować klientów do odpowiedniego punktu końcowego usługi. Klienci łączą się następnie bezpośrednio z punktem końcowym usługi, a nie za pośrednictwem Traffic Manager.

W związku Traffic Manager nie zapewnia punktu końcowego ani adresu IP, z które klienci mogą się łączyć. Jeśli chcesz, aby statyczny adres IP usługi był skonfigurowany w usłudze, a nie w Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Jakie typy ruchu można rozsyłać przy użyciu Traffic Manager?
Jak wyjaśniono [w te Traffic Manager działa,](../traffic-manager/traffic-manager-how-it-works.md)punkt końcowy Traffic Manager może być dowolną usługą dostępną z Internetu hostowaną na platformie Azure lub poza platformą Azure. W związku Traffic Manager może przekierowyować ruch pochodzący z publicznej sieci Internet do zestawu punktów końcowych, które również mają połączenie z Internetem. Jeśli masz punkty końcowe, które znajdują się w sieci prywatnej (na przykład w wewnętrznej wersji programu [Azure Load Balancer](../load-balancer/components.md#frontend-ip-configurations)) lub mają użytkowników, którzy tworzą żądania DNS z takich sieci wewnętrznych, nie można użyć usługi Traffic Manager do rozsyłania tego ruchu.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Czy Traffic Manager sesje "sticky"?

Jak wyjaśniono [w Traffic Manager działa](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie dns. Używa odpowiedzi DNS do kierowania klientów do odpowiedniego punktu końcowego usługi. Klienci łączą się bezpośrednio z punktem końcowym usługi, a nie za pośrednictwem Traffic Manager. W związku Traffic Manager ruch HTTP między klientem a serwerem nie jest w związku z tym widzą.

Ponadto źródłowy adres IP zapytania DNS odebranego przez Traffic Manager należy do cyklicznej usługi DNS, a nie klienta. W związku Traffic Manager nie ma możliwości śledzenia poszczególnych klientów i nie można zaimplementować sesji "sticky". To ograniczenie jest typowe dla wszystkich systemów zarządzania ruchem opartych na systemie DNS i nie jest specyficzne dla Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Dlaczego podczas korzystania z protokołu HTTP występuje błąd Traffic Manager?

Jak wyjaśniono [w Traffic Manager działa](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie dns. Używa odpowiedzi DNS do kierowania klientów do odpowiedniego punktu końcowego usługi. Klienci łączą się następnie bezpośrednio z punktem końcowym usługi, a nie za pośrednictwem Traffic Manager. Traffic Manager nie widzi ruchu HTTP między klientem a serwerem. W związku z tym każdy wyświetlany błąd HTTP musi pochodzić z aplikacji. Aby klient łączył się z aplikacją, wszystkie kroki rozpoznawania nazw DNS zostały ukończone. Obejmuje to wszelkie interakcje Traffic Manager dotyczące przepływu ruchu aplikacji.

W związku z tym dalsze badanie powinno skupić się na aplikacji.

Nagłówek hosta HTTP wysyłany z przeglądarki klienta jest najbardziej powszechnym źródłem problemów. Upewnij się, że aplikacja jest skonfigurowana do akceptowania poprawnego nagłówka hosta dla nazwy domeny, która jest używasz. Aby uzyskać informacje o punktach końcowych korzystających z Azure App Service, zobacz konfigurowanie niestandardowej nazwy domeny dla aplikacji internetowej w u [Azure App Service użyciu Traffic Manager](../app-service/configure-domain-traffic-manager.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Jaki jest wpływ używania Traffic Manager?

Jak wyjaśniono [w Traffic Manager działa](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie dns. Ponieważ klienci łączą się bezpośrednio z punktami końcowymi usługi, nie ma to wpływu na wydajność podczas korzystania Traffic Manager po nawiązaniu połączenia.

Ponieważ Traffic Manager integruje się z aplikacjami na poziomie systemu DNS, wymaga on wstawienia dodatkowego wyszukiwania DNS do łańcucha rozpoznawania nazw DNS. Wpływ Traffic Manager na czas rozpoznawania nazw DNS jest minimalny. Traffic Manager używa globalnej sieci serwerów nazw i używa sieci [emisji](https://en.wikipedia.org/wiki/Anycast) dowolnej, aby upewnić się, że zapytania DNS są zawsze kierowane do najbliższego dostępnego serwera nazw. Ponadto buforowanie odpowiedzi DNS oznacza, że dodatkowe opóźnienie DNS związane z używaniem Traffic Manager ma zastosowanie tylko do ułamka sesji.

Metoda Performance kieruje ruch do najbliższego dostępnego punktu końcowego. Wynikiem netto jest to, że ogólny wpływ na wydajność skojarzony z tą metodą powinien być minimalny. Każdy wzrost opóźnienia DNS powinien zostać przesunięty przez mniejsze opóźnienie sieci do punktu końcowego.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Jakich protokołów aplikacji można używać z Traffic Manager?

Jak wyjaśniono [w Traffic Manager działa](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie dns. Po zakończeniu wyszukiwania DNS klienci łączą się bezpośrednio z punktem końcowym aplikacji, a nie za pośrednictwem Traffic Manager. W związku z tym połączenie może używać dowolnego protokołu aplikacji. W przypadku wybrania protokołu TCP jako protokołu monitorowania Traffic Manager monitorowania kondycji punktu końcowego można wykonać bez używania protokołów aplikacji. Jeśli zdecydujesz się zweryfikować kondycję przy użyciu protokołu aplikacji, punkt końcowy musi być w stanie odpowiadać na żądania HTTP lub HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Czy mogę używać Traffic Manager z nazwą domeny "naked"?

Tak. Aby dowiedzieć się, jak utworzyć rekord aliasu wierzchołka nazwy domeny w celu odwołania się do profilu Azure Traffic Manager, zobacz Configure an alias record to support apex domain names with Traffic Manager (Konfigurowanie rekordu [aliasu](../dns/tutorial-alias-tm.md)w celu obsługi nazw domen wierzchołkowych przy użyciu Traffic Manager ).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Czy Traffic Manager adres podsieci klienta podczas obsługi zapytań DNS? 

Tak, oprócz źródłowego adresu IP odbieranych zapytań DNS (który zazwyczaj jest adresem IP programu rozpoznawania nazw DNS) podczas wyszukiwania metod routingu geograficznego, wydajności i podsieci usługa Traffic Manager uwzględnia również adres podsieci klienta, jeśli jest uwzględniony w zapytaniu przez program rozpoznawania nazw, który wysyła żądanie w imieniu użytkownika końcowego.  
W szczególności [RFC 7871 —](https://tools.ietf.org/html/rfc7871) podsieć klienta w zapytaniach DNS, która zapewnia mechanizm rozszerzenia [dla systemu DNS (EDNS0),](https://tools.ietf.org/html/rfc2671) który może przekazywać adres podsieci klienta z rozpoznawania nazw, które go obsługują.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Co to jest czas wygaśnięcia systemu DNS i jak wpływa on na użytkowników?

Gdy zapytanie DNS trafia do Traffic Manager, ustawia wartość w odpowiedzi o nazwie time-to-live (TTL). Ta wartość, której jednostka jest w sekundach, wskazuje programom rozpoznawania nazw DNS podrzędne czas buforowania tej odpowiedzi. Chociaż program rozpoznawania nazw DNS nie ma gwarancji buforowania tego wyniku, buforowanie umożliwia im odpowiadanie na wszystkie kolejne zapytania z pamięci podręcznej zamiast Traffic Manager serwerów DNS. Ma to wpływ na odpowiedzi w następujący sposób:

- Wyższy czas wygaśnięcia zmniejsza liczbę zapytań, które trafiają na serwery DNS usługi Traffic Manager, co może zmniejszyć koszty dla klienta, ponieważ liczba obsługiwanych zapytań jest rozliczanym użyciem.
- Wyższy czas wygaśnięcia może potencjalnie skrócić czas wyszukiwania DNS.
- Wyższy czas wygaśnięcia oznacza również, że dane nie odzwierciedlają najnowszych informacji o kondycji uzyskanych Traffic Manager za pośrednictwem agentów sondowania.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Jak wysoki lub niski można ustawić czas wygaśnięcia dla Traffic Manager odpowiedzi?

Na poziomie profilu można ustawić czas wygaśnięcia dns na 0 sekund i do 2 147 483 647 sekund (maksymalny zakres zgodny ze specyfikacją [RFC-1035).](https://www.ietf.org/rfc/rfc1035.txt ) Czas wygaśnięcia 0 oznacza, że podrzędne rozpoznawania nazw DNS nie buforują odpowiedzi na zapytania i oczekuje się, że wszystkie zapytania dotrą do serwera Traffic Manager DNS do rozpoznawania.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Jak mogę zrozumieć ilość zapytań pochodzących z mojego profilu? 

Jedną z metryk dostarczonych przez Traffic Manager jest liczba zapytań, na które odpowiada profil. Te informacje można uzyskać na poziomie agregacji na poziomie profilu lub podzielić je dalej, aby zobaczyć ilość zapytań, w których zostały zwrócone określone punkty końcowe. Ponadto można skonfigurować alerty w celu powiadamiania użytkownika, jeśli liczba odpowiedzi na zapytanie przekroczy ustawione warunki. Aby uzyskać więcej informacji, [Traffic Manager metryki i alerty.](traffic-manager-metrics-alerts.md)

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager metody geograficznego routingu ruchu

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Jakie są przypadki użycia, w których routing geograficzny jest przydatny?

Typ routingu geograficznego może być używany w każdym scenariuszu, w którym klient platformy Azure musi rozróżnić swoich użytkowników na podstawie regionów geograficznych. Na przykład przy użyciu metody geograficznego routingu ruchu można zapewnić użytkownikom z określonych regionów środowisko użytkownika inne niż te z innych regionów. Innym przykładem jest zgodność z lokalnymi wymogami niezależności danych, które wymagają, aby użytkownicy z określonego regionu podlegali tylko punktom końcowym w tym regionie.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Jak mogę czy należy używać metody routingu wydajności czy metody routingu geograficznego?

Kluczową różnicą między tymi dwiema popularnymi metodami routingu jest to, że w przypadku metody routingu wydajności głównym celem jest wysłanie ruchu do punktu końcowego, który może zapewnić najmniejsze opóźnienie obiektowi wywołującego, natomiast w przypadku routingu geograficznego podstawowym celem jest wymuś ogrodzenie geograficzne dla wywołujących, aby można było celowo przekierować je do określonego punktu końcowego. Nakładanie się występuje, ponieważ istnieje korelacja między bliskością geograficzną i niższym opóźnieniem, chociaż nie zawsze jest to prawdziwe. Może to być punkt końcowy w innej lokalizacji geograficznej, który może zapewnić lepsze środowisko opóźnień dla wywołującego. W takim przypadku routing wydajności wyśle użytkownika do tego punktu końcowego, ale routing geograficzny zawsze wyśle go do punktu końcowego, który został zamapowany na jego region geograficzny. Aby to jeszcze bardziej wyjaśnić, rozważmy następujący przykład — w przypadku routingu geograficznego można utworzyć nietypowe mapowania, takie jak wysyłanie całego ruchu z Azji do punktów końcowych w USA i cały ruch w USA do punktów końcowych w Azji. W takim przypadku routing geograficzny celowo zrobi dokładnie to, do czego został skonfigurowany, a optymalizacja wydajności nie jest uwzględniana. 
>[!NOTE]
>W niektórych scenariuszach mogą być potrzebne możliwości routingu zarówno w zakresie wydajności, jak i routingu geograficznego. W przypadku tych scenariuszy profile zagnieżdżone mogą być doskonałym wyborem. Można na przykład skonfigurować profil nadrzędny z routingiem geograficznym, w którym wysyłasz cały ruch z usługi Ameryka Północna do profilu zagnieżdżonego, który ma punkty końcowe w USA, i użyć routingu wydajności do wysyłania tego ruchu do najlepszego punktu końcowego w tym zestawie. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Jakie regiony są obsługiwane przez usługi Traffic Manager routingu geograficznego?

Hierarchię kraju/regionu używaną przez Traffic Manager można znaleźć [tutaj.](traffic-manager-geographic-regions.md) Chociaż ta strona jest na bieżąco ze zmianami, możesz również programowo pobrać te same informacje przy użyciu interfejsu API REST usługi [Azure Traffic Manager .](/rest/api/trafficmanager/) 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Jak menedżer ruchu określa, skąd użytkownik odpytuje?

Traffic Manager wyszukuje źródłowy adres IP zapytania (najprawdopodobniej jest to lokalny program rozpoznawania nazw DNS wykonujący zapytanie w imieniu użytkownika) i używa wewnętrznego adresu IP do mapowania regionów w celu określenia lokalizacji. Ta mapa jest aktualizowana na bieżąco w celu uwzględnienia zmian w Internecie. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Czy w każdym Traffic Manager użytkownik może prawidłowo określić dokładną lokalizację geograficzną użytkownika?

Nie, Traffic Manager nie może zagwarantować, że region geograficzny, który wywnioskujemy na temat źródłowego adresu IP zapytania DNS, będzie zawsze odpowiadał lokalizacji użytkownika z następujących powodów:

- Najpierw, zgodnie z opisem w poprzedniej często zadawanych pytaniach, źródłowy adres IP, który widzimy, to adres rozpoznawania nazw DNS wykonujący wyszukiwania w imieniu użytkownika. Chociaż lokalizacja geograficzna rozpoznawania nazw DNS jest dobrym serwerem proxy dla lokalizacji geograficznej użytkownika, może być również inny w zależności od zasięgu usługi rozpoznawania nazw DNS i określonej usługi rozpoznawania nazw DNS wybranej przez klienta do użycia. Na przykład klient znajdujący się w Singapurze może określić w ustawieniach urządzenia usługę rozpoznawania nazw DNS, której serwer DNS w Singapurze może zostać wybrany do obsługi rozdzielczości zapytań dla tego użytkownika/urządzenia. W takim przypadku Traffic Manager tylko adres IP rozpoznawania nazw, który odpowiada lokalizacji Singapur. Zobacz też na tej stronie wcześniejsze często zadawane pytania dotyczące obsługi adresów podsieci klienta.

- Po drugie Traffic Manager używa mapy wewnętrznej do translacji adresu IP na region geograficzny. Chociaż ta mapa jest na bieżąco weryfikowana i aktualizowana w celu zwiększenia jej dokładności i uwzględnienia zmieniającego się charakteru Internetu, nadal istnieje możliwość, że nasze informacje nie są dokładną reprezentacją lokalizacji geograficznej wszystkich adresów IP.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Czy punkt końcowy musi być fizycznie zlokalizowany w tym samym regionie co skonfigurowany dla routingu geograficznego?

Nie, lokalizacja punktu końcowego nie nakłada żadnych ograniczeń na regiony, które mogą być na nie mapowane. Na przykład do punktu końcowego w US-Central platformy Azure mogą być kierowani wszyscy użytkownicy z Indiach.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Czy mogę przypisać regiony geograficzne do punktów końcowych w profilu, który nie jest skonfigurowany do routingu geograficznego?

Tak, jeśli metoda routingu profilu nie jest geograficzna, możesz użyć interfejsu API REST usługi [Azure Traffic Manager,](/rest/api/trafficmanager/) aby przypisać regiony geograficzne do punktów końcowych w tym profilu. W przypadku profilów routingu nie geograficznie ta konfiguracja jest ignorowana. Jeśli później zmienisz taki profil na typ routingu geograficznego, Traffic Manager użyć tych mapowań.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Dlaczego występuje błąd podczas próby zmiany metody routingu istniejącego profilu na Wartość geograficzna?

Wszystkie punkty końcowe w profilu z routingiem geograficznym muszą mieć zamapowany co najmniej jeden region. Aby przekonwertować istniejący profil na typ routingu geograficznego, należy najpierw skojarzyć regiony geograficzne ze wszystkimi punktami końcowymi przy użyciu interfejsu [API REST](/rest/api/trafficmanager/) usługi Azure Traffic Manager przed zmianą typu routingu na geograficzny. W przypadku korzystania z portalu najpierw usuń punkty końcowe, zmień metodę routingu profilu na geograficzną, a następnie dodaj punkty końcowe wraz z mapowaniem ich regionów geograficznych.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Dlaczego zdecydowanie zaleca się, aby klienci tworzyli zagnieżdżone profile zamiast punktów końcowych w profilu z włączonym routingiem geograficznym?

Region można przypisać tylko do jednego punktu końcowego w profilu, jeśli korzysta z metody routingu geograficznego. Jeśli ten punkt końcowy nie jest typem zagnieżdżony z dołączonym profilem podrzędnym, jeśli punkt końcowy będzie w złej kondycji, program Traffic Manager będzie nadal wysyłać do niego ruch, ponieważ alternatywa niesyłania żadnego ruchu nie jest lepsza. Traffic Manager nie przechodzi w tryb failover do innego punktu końcowego, nawet jeśli przypisany region jest "elementem nadrzędnym" regionu przypisanego do punktu końcowego w złej kondycji (na przykład jeśli punkt końcowy z regionem Hiszpania będzie w złej kondycji, nie przejdziemy w tryb failover do innego punktu końcowego, który ma przypisany region Europa). Ma to na celu zapewnienie, Traffic Manager z przestrzeganiem granic geograficznych, które klient sfigurł w swoim profilu. Aby skorzystać z zalet pracy w trybie pracy w przypadku złej kondycji punktu końcowego, zaleca się przypisanie regionów geograficznych do profilów zagnieżdżonych z wieloma punktami końcowymi w tym punkcie końcowym, a nie do poszczególnych punktów końcowych. W ten sposób, jeśli punkt końcowy w zagnieżdżonych profilach podrzędnych ulegnie awarii, ruch może przejść do trybu failover do innego punktu końcowego w tym samym zagnieżdżonych profilach podrzędnych.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Czy istnieją ograniczenia dotyczące wersji interfejsu API obsługującej ten typ routingu?

Tak, tylko interfejs API w wersji 2017-03-01 i nowszej obsługuje typ routingu geograficznego. Żadnych starszych wersji interfejsu API nie można używać do tworzenia profilów typu routingu geograficznego ani przypisywania regionów geograficznych do punktów końcowych. Jeśli starsza wersja interfejsu API jest używana do pobierania profilów z subskrypcji platformy Azure, żaden profil typu routingu geograficznego nie jest zwracany. Ponadto w przypadku korzystania ze starszych wersji interfejsu API żaden zwrócony profil, który ma punkty końcowe z przypisaniem regionu geograficznego, nie ma pokazanego przypisania regionu geograficznego.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager routingu ruchu w podsieci

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>W jakich przypadkach użycia routing podsieci jest przydatny?

Routing podsieci umożliwia odróżnienie obsługi dostarczania dla określonych zestawów użytkowników zidentyfikowanych przez źródłowy adres IP ich żądań DNS. Przykładem może być wyświetlanie innej zawartości, jeśli użytkownicy nabyli połączenie z witryną internetową z firmowej hq. Inną możliwością jest ograniczenie dostępu użytkowników z określonych internetowych adresów internetowych tylko do punktów końcowych, które obsługują tylko połączenia IPv4, jeśli są one podaną wydajnością w przypadku korzystania z protokołu IPv6.
Innym powodem, dla którego warto używać metody routingu Podsieć, jest połączenie z innymi profilami w zagnieżdżonych zestawach profilów. Jeśli na przykład chcesz użyć metody routingu geograficznego do geoprzesłonięcia użytkowników, ale dla określonego ispki chcesz wykonać inną metodę routingu, możesz mieć profil z metodą routingu Podsieć jako profilem nadrzędnym i przesłonić tego isP, aby użyć określonego profilu podrzędnego i mieć standardowy profil geograficzny dla wszystkich innych.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Jak Traffic Manager znać adres IP użytkownika końcowego?

Urządzenia użytkowników końcowych zwykle używają rozpoznawania nazw DNS do wyszukiwania DNS w ich imieniu. Wychodzący adres IP takich osób rozpoznawania nazw jest Traffic Manager jako źródłowy adres IP. Ponadto metoda routingu Subnet wyszukuje również informacje o rozszerzonej podsieci klienta (ECS) EDNS0 przekazane wraz z żądaniem. Jeśli są obecne informacje usługi ECS, jest to adres używany do określenia routingu. W przypadku braku informacji ECS źródłowy adres IP zapytania jest używany do celów routingu.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Jak określić adresy IP podczas korzystania z routingu podsieci?

Adresy IP do skojarzenia z punktem końcowym można określić na dwa sposoby. Najpierw można użyć notacji dziesiętnej dziesiętnej czworokąta z adresami rozpoczęcia i zakończenia, aby określić zakres (na przykład 1.2.3.4-5.6.7.8 lub 3.4.5.6-3.4.5.6). Po drugie można użyć notacji CIDR, aby określić zakres (na przykład 1.2.3.0/24). Można określić wiele zakresów i używać obu typów notacji w zestawie zakresów. Obowiązują pewne ograniczenia.

-    Zakresy adresów nie mogą się nakładać, ponieważ każdy adres IP musi być mapowany tylko na jeden punkt końcowy
-    Adres startowy nie może być więcej niż adres końcowy
-    W przypadku notacji CIDR adres IP przed "/" powinien być adresem startowym tego zakresu (na przykład 1.2.3.0/24 jest prawidłowy, ale 1.2.3.4.4/24 NIE jest prawidłowy)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Jak określić rezerwowy punkt końcowy podczas korzystania z routingu podsieci?

Jeśli w profilu z routingiem podsieci masz punkt końcowy bez zamapowanych na niego podsieci, wszystkie żądania, które nie są zgodne z innymi punktami końcowymi, zostaną przekierowane do tego punktu końcowego. Zdecydowanie zaleca się użycie takiego rezerwowego punktu końcowego w profilu, ponieważ program Traffic Manager zwróci odpowiedź NXDOMAIN, jeśli żądanie zostanie zwrócone i nie zostanie zamapowane na żadne punkty końcowe lub jeśli zostanie zamapowane na punkt końcowy, ale ten punkt końcowy jest w złej kondycji.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Co się stanie, jeśli punkt końcowy zostanie wyłączony w profilu typu routingu Podsieć?

W profilu z routingiem podsieci, jeśli masz punkt końcowy z wyłączonym punktem końcowym, usługa Traffic Manager będzie zachowywać się tak, jakby ten punkt końcowy i mapowania podsieci nie istniały. Jeśli zapytanie, które zostałoby dopasowane do mapowania adresów IP, zostanie odebrane i punkt końcowy zostanie wyłączony, program Traffic Manager zwróci rezerwowy punkt końcowy (taki bez mapowań) lub jeśli taki punkt końcowy nie istnieje, zwróci odpowiedź NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager routingu ruchu MultiValue

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Jakie są przypadki użycia, w których routing MultiValue jest przydatny?

Routing MultiValue zwraca wiele punktów końcowych w dobrej kondycji w jednej odpowiedzi na zapytanie. Główną zaletą jest to, że jeśli punkt końcowy jest w złej kondycji, klient ma więcej opcji ponawiania próby bez wykonania kolejnego wywołania DNS (które może zwrócić tę samą wartość z nadrzędnej pamięci podręcznej). Dotyczy to aplikacji wrażliwych na dostępność, które chcą zminimalizować przestoje.
Innym zastosowaniem metody routingu MultiValue jest to, że punkt końcowy jest "podwójnie macierzysty" dla adresów IPv4 i IPv6 i chcesz zapewnić wywołującym obie opcje do wyboru podczas inicjowania połączenia z punktem końcowym.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Ile punktów końcowych jest zwracanych w przypadku korzystania z routingu MultiValue?

Można określić maksymalną liczbę punktów końcowych do zwrócenia, a funkcja MultiValue zwróci nie więcej niż tyle punktów końcowych w dobrej kondycji po otrzymaniu zapytania. Maksymalna możliwa wartość dla tej konfiguracji wynosi 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Czy w przypadku korzystania z routingu MultiValue zostanie użyty ten sam zestaw punktów końcowych?

Nie możemy zagwarantować, że ten sam zestaw punktów końcowych zostanie zwrócony w każdym zapytaniu. Ma na to również wpływ fakt, że niektóre punkty końcowe mogą być w złej kondycji, w tym momencie nie zostaną uwzględnione w odpowiedzi

## <a name="real-user-measurements"></a>Pomiary rzeczywistego użytkownika

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Jakie są zalety korzystania z Pomiary rzeczywistego użytkownika?

Jeśli używasz metody routingu wydajności, usługa Traffic Manager wybiera najlepszy region platformy Azure dla użytkownika końcowego, z którym ma nawiązać połączenie, sprawdzając źródłowy adres IP i podsieć klienta usługi EDNS (jeśli została przekazana) i sprawdzając ją pod względem analizy opóźnień sieci, które obsługuje usługa. Pomiary rzeczywistego użytkownika to dla twojej bazy użytkowników końcowych, mając doświadczenie w pracy z tą tabelą opóźnień, a także zapewniając, że ta tabela odpowiednio obejmuje sieci użytkowników końcowych, z których użytkownicy końcowi łączą się z platformą Azure. Prowadzi to do zwiększenia dokładności routingu użytkownika końcowego.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Czy mogę używać Pomiary rzeczywistego użytkownika z regionami spoza platformy Azure?

Pomiary rzeczywistego użytkownika mierzy i raportuje tylko opóźnienie do osiągnięcia regionów świadczenia usługi Azure. Jeśli używasz routingu opartego na wydajności z punktami końcowymi hostowanych w regionach spoza platformy Azure, nadal możesz skorzystać z tej funkcji, mając zwiększone informacje o opóźnieniu dotyczące reprezentatywnego regionu świadczenia usługi Azure, który został wybrany do skojaarzynia z tym punktem końcowym.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Która metoda routingu korzysta z Pomiary rzeczywistego użytkownika?

Dodatkowe informacje uzyskane za pośrednictwem Pomiary rzeczywistego użytkownika mają zastosowanie tylko w przypadku profilów, które używają metody routingu wydajności. Link Pomiary rzeczywistego użytkownika jest dostępny ze wszystkich profilów podczas wyświetlania go za pośrednictwem Azure Portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Czy muszę włączyć oddzielne Pomiary rzeczywistego użytkownika każdego profilu?

Nie. Wystarczy włączyć ją raz dla subskrypcji, a wszystkie mierzone i zgłaszane informacje o opóźnieniu są dostępne dla wszystkich profilów.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Jak mogę wyłączyć Pomiary rzeczywistego użytkownika subskrypcji?

Możesz zatrzymać naliczanie opłat związanych z Pomiary rzeczywistego użytkownika po zatrzymaniu zbierania i wysyłania z powrotem pomiarów opóźnienia z aplikacji klienckiej. Na przykład podczas mierzenia kodu JavaScript osadzonego na stronach internetowych możesz przestać korzystać z tej funkcji, usuwając kod JavaScript lub wyłączając jego wywołania podczas renderowania strony.

Możesz również wyłączyć Pomiary rzeczywistego użytkownika, usuwając klucz. Po usunięciu klucza wszelkie miary wysyłane do Traffic Manager z tym kluczem są odrzucane.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Czy można używać Pomiary rzeczywistego użytkownika z aplikacjami klienckimi innymi niż strony internetowe?

Tak, Pomiary rzeczywistego użytkownika jest przeznaczony do pozyskania danych zbieranych za pośrednictwem różnych typów klientów użytkowników końcowych. Te często zadawane pytania będą aktualizowane w przypadku obsługi nowych typów aplikacji klienckich.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Ile pomiarów są dokonywane za każdym razem, Pomiary rzeczywistego użytkownika jest renderowana moja strona internetowa z włączoną obsługą usługi?

Jeśli Pomiary rzeczywistego użytkownika jest używany z dostarczonym kodem JavaScript pomiaru, renderowanie każdej strony powoduje po sześć pomiarów. Są one następnie zgłaszane z powrotem do Traffic Manager service. Opłaty za tę funkcję są naliczane na podstawie liczby pomiarów zgłoszonych do Traffic Manager usługi. Jeśli na przykład użytkownik przechodzi z twojej strony internetowej w czasie, gdy miary są podejmowane, ale przed ich raportem, te pomiary nie są brane pod uwagę na potrzeby rozliczeń.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Czy istnieje opóźnienie przed Pomiary rzeczywistego użytkownika skryptu na mojej stronie internetowej?

Nie, nie ma zaprogramowanych opóźnień przed wywołaniem skryptu.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Czy mogę używać Pomiary rzeczywistego użytkownika tylko z regionami świadczenia usługi Azure, które chcesz zmierzyć?

Nie, za każdym razem, gdy jest wywoływany, skrypt Pomiary rzeczywistego użytkownika mierzy zestaw sześciu regionów platformy Azure określonych przez usługę. Ten zestaw zmienia się między różnymi wywołaniami, a w przypadku dużej liczby takich wywołania pokrycie miar obejmuje różne regiony platformy Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Czy mogę ograniczyć liczbę pomiarów do określonej liczby?

Miara w języku JavaScript jest osadzona na stronie internetowej i masz pełną kontrolę nad tym, kiedy rozpocząć i przestać z niego korzystać. Tak długo, jak Traffic Manager odbiera żądanie dotyczące listy regionów platformy Azure, które mają być mierzone, zwracany jest zestaw regionów.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Czy mogę zobaczyć pomiary wykonane przez moją aplikację kliencyjną w ramach Pomiary rzeczywistego użytkownika?

Ponieważ logika miary jest uruchamiana z poziomu aplikacji klienckiej, masz pełną kontrolę nad tym, co się dzieje, w tym nad pomiarami opóźnień. Traffic Manager nie raportuje zagregowanego widoku miar odebranych w ramach klucza połączonego z Subskrypcją.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Czy mogę zmodyfikować skrypt pomiaru dostarczony przez Traffic Manager?

Gdy masz kontrolę nad tym, co jest osadzone na stronie internetowej, zdecydowanie odradzamy wprowadzenie jakichkolwiek zmian w skrypcie pomiaru, aby upewnić się, że prawidłowo mierzy i raportuje opóźnienia.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Czy będzie możliwe, aby inne osoby widziały klucz, który jest Pomiary rzeczywistego użytkownika?

Po osadzenie skryptu pomiaru na stronie internetowej będzie możliwe, aby inne osoby widziały ten skrypt i klucz Pomiary rzeczywistego użytkownika (JEGO). Należy jednak pamiętać, że ten klucz różni się od identyfikatora subskrypcji i jest generowany przez Traffic Manager do tego celu. Znajomość klucza SAFETY nie spowoduje naruszenia bezpieczeństwa konta platformy Azure.

### <a name="can-others-abuse-my-rum-key"></a>Czy inne osoby mogą zużyły mój klucz INIM?

Chociaż inne osoby mogą używać Twojego klucza do wysyłania nieprawidłowych informacji na platformę Azure, kilka nieprawidłowych pomiarów nie zmieni routingu, ponieważ jest on uwzględniany wraz ze wszystkimi innymi miarami, które otrzymujemy. Jeśli musisz zmienić klucze, możesz ponownie wygenerować klucz, w którym stary klucz zostanie odrzucony.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Czy muszę umieścić miarę w języku JavaScript na wszystkich stronach internetowych?

Pomiary rzeczywistego użytkownika dostarcza więcej wartości w miarę wzrostu liczby pomiarów. Jak już omówisz, to Ty musisz zdecydować, czy chcesz umieścić ją na wszystkich stronach internetowych, czy na kilku wybranych stronach. Zaleca się, aby zacząć od umieszczenia go na najczęściej odwiedzanych stronie, gdzie użytkownik powinien pozostać na tej stronie co najmniej pięć sekund.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Czy informacje o użytkownikach końcowych mogą być identyfikowane przez Traffic Manager, jeśli używam Pomiary rzeczywistego użytkownika?

W przypadku użycia podanego pomiaru w języku JavaScript usługa Traffic Manager będzie mieć wgląd w adres IP klienta użytkownika końcowego i źródłowy adres IP lokalnego programu rozpoznawania nazw DNS, który jest przez nich używany. Traffic Manager używa adresu IP klienta dopiero po obciętej, aby nie można było zidentyfikować określonego użytkownika końcowego, który wysłał miary.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Czy strona internetowa mierząc Pomiary rzeczywistego użytkownika musi używać Traffic Manager do routingu?

Nie, nie musi używać Traffic Manager. Strona routingu Traffic Manager działa niezależnie od części Pomiar rzeczywistego użytkownika i chociaż warto, aby były one w tej samej właściwości internetowej, nie muszą być.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Czy muszę hostować dowolną usługę w regionach platformy Azure, aby używać jej z Pomiary rzeczywistego użytkownika?

Nie, nie musisz hostować żadnego składnika po stronie serwera na platformie Azure, aby Pomiary rzeczywistego użytkownika działać. Obraz pojedynczego piksela pobrany przez kod JavaScript miary i usługę, w których jest on uruchomiony w różnych regionach świadczenia usługi Azure, jest hostowany i zarządzany przez platformę Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Czy użycie przepustowości platformy Azure zwiększy się, gdy użyję Pomiary rzeczywistego użytkownika?

Jak wspomniano w poprzedniej odpowiedzi, składniki usługi Pomiary rzeczywistego użytkownika są własnością platformy Azure i są przez nie zarządzane. Oznacza to, że użycie przepustowości platformy Azure nie zwiększy się, ponieważ Pomiary rzeczywistego użytkownika. Nie obejmuje to żadnego użycia przepustowości poza opłatami za platformę Azure. Minimalizujemy przepustowość używaną przez pobranie tylko jednego obrazu pikseli w celu pomiaru opóźnienia do regionu świadczenia usługi Azure. 

## <a name="traffic-view"></a>Widok ruchu

### <a name="what-does-traffic-view-do"></a>Co Widok ruchu zrobić?

Widok ruchu to funkcja funkcji Traffic Manager która pomaga lepiej zrozumieć użytkowników i sposób ich obsługi. Używa ona zapytań odebranych przez Traffic Manager i tabel analizy opóźnień sieci, które obsługuje usługa, aby zapewnić następujące informacje:

- Regiony, z których użytkownicy napisali połączenie z punktami końcowymi na platformie Azure.
- Liczba użytkowników łączących się z tych regionów.
- Regiony platformy Azure, do których są kierowane.
- Środowisko opóźnień w tych regionach świadczenia usługi Azure.

Te informacje są dostępne do pobrania za pośrednictwem nakładek mapy geograficznej i widoków tabelarowych w portalu.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Jak mogę skorzystać z używania Widok ruchu?

Widok ruchu ogólny widok ruchu obierania przez profile Traffic Manager danych. W szczególności można go użyć, aby zrozumieć, z którego miejsca łączy się baza użytkowników, i co równie ważne, jakie jest ich średnie opóźnienie. Dzięki tym informacjom można następnie znaleźć obszary, w których należy skoncentrować się, na przykład przez rozszerzenie zasięgu platformy Azure do regionu, który może obsługiwać tych użytkowników z niższym opóźnieniem. Innym szczegółowych informacji, które można uzyskać dzięki użyciu usługi Widok ruchu, jest zobaczenie wzorców ruchu do różnych regionów, co z kolei może pomóc w podejmowania decyzji dotyczących zwiększania lub zmniejszania liczby wynalazków w tych regionach.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Czym różni Widok ruchu od metryk Traffic Manager dostępnych za pośrednictwem usługi Azure Monitor?

Azure Monitor można użyć do zrozumienia na poziomie zagregowanym ruchu odbieranych przez twój profil i jego punkty końcowe. Umożliwia również śledzenie stanu kondycji punktów końcowych przez ujawnienie wyników kontroli kondycji. Jeśli musisz wyjść poza te możliwości i zrozumieć środowisko użytkownika końcowego łączące się z platformą Azure na poziomie regionalnym, możesz Widok ruchu to osiągnąć.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Czy Widok ruchu podsieci klienta usługi EDNS?

Zapytania DNS obsługiwane przez Azure Traffic Manager należy wziąć pod uwagę informacje USŁUGI ECS, aby zwiększyć dokładność routingu. Jednak podczas tworzenia zestawu danych, który pokazuje, skąd użytkownicy nawiązują połączenie, Widok ruchu korzysta tylko z adresu IP programu rozpoznawania nazw DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Z ilu dni są Widok ruchu danych?

Widok ruchu dane wyjściowe, przetwarzając dane z siedmiu dni poprzedzających dzień poprzedzający dzień przed ich wyświetleniem. Jest to okno ruchome, a podczas każdego odwiedzania będą używane najnowsze dane.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Jak Widok ruchu zewnętrzne punkty końcowe?

Jeśli używasz zewnętrznych punktów końcowych hostowanych poza regionami platformy Azure w profilu usługi Traffic Manager, możesz zdecydować się na jego mapowanie na region świadczenia usługi Azure, który jest serwerem proxy dla jego charakterystyk opóźnień (jest to w rzeczywistości potrzebne, jeśli używasz metody routingu wydajności). Jeśli ma mapowanie regionów platformy Azure, metryki opóźnienia regionu platformy Azure będą używane podczas tworzenia Widok ruchu danych wyjściowych. Jeśli nie określono regionu świadczenia usługi Azure, informacje o opóźnieniu będą puste w danych dla tych zewnętrznych punktów końcowych.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Czy muszę włączyć Widok ruchu dla każdego profilu w mojej subskrypcji?

W okresie zapoznawczym Widok ruchu na poziomie subskrypcji. W ramach ulepszeń, które wprowadzono przed ogólną dostępnością, można teraz włączyć usługę Widok ruchu na poziomie profilu, co pozwala na bardziej szczegółowe włączanie tej funkcji. Domyślnie Widok ruchu dla profilu.

>[!NOTE]
>Jeśli w Widok ruchu na poziomie subskrypcji w okresie zapoznawczym należy ponownie włączyć tę funkcję dla każdego profilu w ramach tej subskrypcji.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Jak mogę wyłączyć Widok ruchu?

Możesz wyłączyć dostęp do Widok ruchu dla dowolnego profilu przy użyciu portalu lub interfejsu API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Jak działa Widok ruchu rozliczeń?

Widok ruchu jest oparta na liczbie punktów danych użytych do utworzenia danych wyjściowych. Obecnie jedynym obsługiwanym typem danych są zapytania odbierane przez profil. Ponadto są naliczane tylko za przetwarzanie, które zostało wykonane po Widok ruchu włączone. Oznacza to, że jeśli włączysz funkcję Widok ruchu przez pewien okres w miesiącu i wyłączysz ją w innym czasie, tylko punkty danych przetworzone, gdy funkcja została włączona, będą wliczane do rachunku.

## <a name="traffic-manager-endpoints"></a>Punkty końcowe usługi Traffic Manager

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Czy mogę używać Traffic Manager z punktami końcowymi z wielu subskrypcji?

Używanie punktów końcowych z wielu subskrypcji nie jest możliwe w przypadku usługi Azure Web Apps. Usługa Azure Web Apps wymaga, aby każda niestandardowa nazwa domeny używana z usługą Web Apps używana tylko w ramach jednej subskrypcji. Nie jest możliwe używanie Web Apps z wielu subskrypcji o tej samej nazwie domeny.

W przypadku innych typów punktów końcowych można używać Traffic Manager z punktami końcowymi z więcej niż jednej subskrypcji. W Resource Manager punkty końcowe z dowolnej subskrypcji można dodać do usługi Traffic Manager, o ile osoba konfigurująca profil usługi Traffic Manager ma dostęp do odczytu do punktu końcowego. Te uprawnienia można przyznać przy użyciu kontroli dostępu opartej na rolach [(RBAC) platformy Azure.](../role-based-access-control/role-assignments-portal.md) Punkty końcowe z innych subskrypcji można dodawać przy użyciu [Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) lub interfejsu wiersza polecenia [platformy Azure.](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create)

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Czy mogę używać Traffic Manager z miejscami przejściowymi usługi w chmurze?

Tak. Miejsca "przejściowe" usługi w chmurze można skonfigurować w Traffic Manager jako zewnętrzne punkty końcowe. Opłaty za kontrole kondycji nadal są naliczane przy użyciu stawki za punkty końcowe platformy Azure.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Czy Traffic Manager punkty końcowe IPv6?

Traffic Manager obecnie nie zapewnia adresów IPv6 serwerów nazw. Jednak nadal Traffic Manager mogą być używane przez klientów IPv6 łączących się z punktami końcowymi IPv6. Klient nie wysyła żądań DNS bezpośrednio do Traffic Manager. Zamiast tego klient używa cyklicznej usługi DNS. Klient tylko IPv6 wysyła żądania do cyklicznej usługi DNS za pośrednictwem protokołu IPv6. Następnie usługa cykliczna powinna mieć możliwość kontaktowania się z serwerami nazw Traffic Manager przy użyciu protokołu IPv4.

Traffic Manager odpowiada przy użyciu nazwy DNS lub adresu IP punktu końcowego. Istnieją dwie opcje obsługi punktu końcowego IPv6. Punkt końcowy można dodać jako nazwę DNS ze skojarzonym rekordem AAAA, a usługa Traffic Manager będzie sprawdzać kondycję tego punktu końcowego i zwracać go jako typ rekordu CNAME w odpowiedzi na zapytanie. Możesz również dodać ten punkt końcowy bezpośrednio przy użyciu adresu IPv6, Traffic Manager zwróci rekord typu AAAA w odpowiedzi na zapytanie.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Czy mogę używać Traffic Manager z więcej niż jedną aplikacją internetową w tym samym regionie?

Zazwyczaj Traffic Manager jest używana do kierowania ruchu do aplikacji wdrożonych w różnych regionach. Można jej jednak również używać, gdy aplikacja ma więcej niż jedno wdrożenie w tym samym regionie. Punkt Traffic Manager platformy Azure nie zezwala na dodanie więcej niż jednego punktu końcowego aplikacji internetowej z tego samego regionu świadczenia usługi Azure do tego samego Traffic Manager końcowego.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Jak mogę przenieść punkty końcowe Traffic Manager platformy Azure do innej grupy zasobów lub subskrypcji?

Punkty końcowe platformy Azure skojarzone z profilem Traffic Manager są śledzone przy użyciu ich identyfikatorów zasobów. Gdy zasób platformy Azure używany jako punkt końcowy (na przykład publiczny adres IP, klasyczna usługa w chmurze, aplikacja internetowa lub inny profil Traffic Manager używany w sposób zagnieżdżony) jest przenoszony do innej grupy zasobów lub subskrypcji, jego identyfikator zasobu zmienia się. Obecnie w tym scenariuszu należy zaktualizować profil Traffic Manager najpierw usuwając, a następnie dodając punkty końcowe do profilu.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitorowanie punktu końcowego usługi Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Czy Traffic Manager na awarie regionów świadczenia usługi Azure?

Traffic Manager jest kluczowym składnikiem dostarczania aplikacji o wysokiej dostępnej wersji na platformie Azure.
Aby zapewnić wysoką dostępność, Traffic Manager muszą mieć wyjątkowo wysoki poziom dostępności i być odporne na awarie regionalne.

Z tego względu Traffic Manager są odporne na całkowite awarie dowolnego regionu świadczenia usługi Azure. Ta odporność ma zastosowanie do wszystkich Traffic Manager: serwerów nazw DNS, interfejsu API, warstwy magazynu i usługi monitorowania punktu końcowego.

W mało prawdopodobnym przypadku 3 Traffic Manager 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 0 Aplikacje wdrożone w wielu regionach świadczenia usługi Azure mogą polegać na Traffic Manager kierowania ruchu do dostępnego wystąpienia aplikacji.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Jak wybór lokalizacji grupy zasobów wpływa na Traffic Manager?

Traffic Manager to pojedyncza, globalna usługa. Nie jest to region. Wybór lokalizacji grupy zasobów nie ma znaczenia dla Traffic Manager wdrożonych w tej grupie zasobów.

Azure Resource Manager wymaga, aby wszystkie grupy zasobów określały lokalizację, która określa domyślną lokalizację dla zasobów wdrożonych w tej grupie zasobów. Podczas tworzenia profilu Traffic Manager jest on tworzony w grupie zasobów. Wszystkie Traffic Manager używają **globalnej** lokalizacji, zastępując domyślną grupę zasobów.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Jak mogę określić bieżącą kondycję każdego punktu końcowego?

Bieżący stan monitorowania każdego punktu końcowego, oprócz ogólnego profilu, jest wyświetlany w Azure Portal. Te informacje są również dostępne za pośrednictwem interfejsu [API REST](/rest/api/trafficmanager/)usługi Traffic Monitor, poleceń [cmdlet](/powershell/module/az.trafficmanager)programu PowerShell i [międzyplatformowego interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-classic-cli)

Możesz również użyć Azure Monitor, aby śledzić kondycję punktów końcowych i wyświetlić ich wizualną reprezentację. Aby uzyskać więcej informacji na temat korzystania z Azure Monitor, zobacz [dokumentację monitorowania platformy Azure.](../azure-monitor/data-platform.md)

### <a name="can-i-monitor-https-endpoints"></a>Czy mogę monitorować punkty końcowe HTTPS?

Tak. Traffic Manager obsługuje sondowanie za pośrednictwem protokołu HTTPS. Skonfiguruj **protokół HTTPS** jako protokół w konfiguracji monitorowania.

Traffic Manager nie może zapewnić weryfikacji certyfikatu, w tym:

* Certyfikaty po stronie serwera nie są weryfikowane
* Certyfikaty SNI po stronie serwera nie są weryfikowane
* Certyfikaty klienta nie są obsługiwane

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Czy podczas dodawania punktu końcowego używam adresu IP lub nazwy DNS?

Traffic Manager umożliwia dodawanie punktów końcowych przy użyciu trzech sposobów ich odwoływaniu się — jako nazwy DNS, jako adresu IPv4 i jako adresu IPv6. Jeśli punkt końcowy zostanie dodany jako adres IPv4 lub IPv6, odpowiedź na zapytanie będzie miała typ rekordu A lub AAAA. Jeśli punkt końcowy został dodany jako nazwa DNS, odpowiedź na zapytanie będzie typu rekord CNAME. Dodawanie punktów końcowych jako adresów IPv4 lub IPv6 jest dozwolone tylko wtedy, gdy punkt końcowy jest typu **Zewnętrzne.**
Wszystkie metody routingu i ustawienia monitorowania są obsługiwane przez trzy typy adresowania punktów końcowych.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Jakich typów adresów IP można używać podczas dodawania punktu końcowego?

Traffic Manager umożliwia określanie punktów końcowych przy użyciu adresów IPv4 lub IPv6. Poniżej przedstawiono kilka ograniczeń:

- Adresy odpowiadające zastrzeżonym prywatnym przestrzeniom adresów IP są niedozwolone. Adresy te obejmują te, które są wywoływane w dokumencie RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 i RFC 5771
- Adres nie może zawierać żadnych numerów portów (można określić porty, które mają być używane w ustawieniach konfiguracji profilu)
- Żaden z dwóch punktów końcowych w tym samym profilu nie może mieć tego samego docelowego adresu IP

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Czy mogę używać różnych typów adresowania punktu końcowego w ramach jednego profilu?

Nie, Traffic Manager nie pozwala na mieszanie typów adresowania punktów końcowych w profilu, z wyjątkiem przypadku profilu z typem routingu MultiValue, w którym można mieszać typy adresowania IPv4 i IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Co się stanie, gdy typ rekordu zapytania przychodzącego różni się od typu rekordu skojarzonego z typem adresowania punktów końcowych?

Po otrzymaniu zapytania względem profilu program Traffic Manager najpierw znajdzie punkt końcowy, który ma zostać zwrócony zgodnie z określoną metodą routingu i stanem kondycji punktów końcowych. Następnie przeszukuje typ rekordu żądany w zapytaniu przychodzącym i typ rekordu skojarzony z punktem końcowym przed zwróceniem odpowiedzi na podstawie poniższej tabeli.

W przypadku profilów z dowolną metodą routingu inną niż MultiValue:

|Żądanie zapytania przychodzącego|     Typ punktu końcowego|     Podano odpowiedź|
|--|--|--|
|WSZELKIE |    A / AAAA / CNAME |    Docelowy punkt końcowy| 
|A |    A / CNAME |    Docelowy punkt końcowy|
|A |    AAAA |    NODATA |
|AAAA |    AAAA / CNAME |    Docelowy punkt końcowy|
|AAAA |    A |    NODATA |
|CNAME |    CNAME |    Docelowy punkt końcowy|
|CNAME     |A/AAAA |    NODATA |
|

W przypadku profilów z metodą routingu ustawioną na MultiValue:

|Żądanie zapytania przychodzącego|     Typ punktu końcowego |    Podano odpowiedź|
|--|--|--|
|WSZELKIE |    Połączenie A i AAAA |    Docelowe punkty końcowe|
|A |    Połączenie A i AAAA |    Tylko docelowe punkty końcowe typu A|
|AAAA    |Połączenie A i AAAA|     Tylko docelowe punkty końcowe typu AAAA|
|CNAME |    Połączenie A i AAAA |    DANE NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Czy mogę użyć profilu z punktami końcowymi adresowanych przez protokół IPv4/IPv6 w profilu zagnieżdżonych?

Tak, można z wyjątkiem tego, że profil typu MultiValue nie może być profilem nadrzędnym w zagnieżdżonych zestawach profilów.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Zatrzymano punkt końcowy aplikacji internetowej w profilu usługi Traffic Manager, ale nie otrzymuję żadnego ruchu nawet po ponownym uruchomieniu. Jak mogę rozwiązać ten problem?

Po zatrzymaniu punktu końcowego aplikacji internetowej platformy Azure Traffic Manager sprawdzanie jego kondycji i ponowne uruchamianie kontroli kondycji dopiero po wykryciu ponownego uruchomienia punktu końcowego. Aby zapobiec takiemu opóźnieniu, wyłącz ten punkt końcowy w profilu Traffic Manager, a następnie włączyć go ponownie po ponownym uruchomieniu.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Czy mogę używać Traffic Manager nawet wtedy, gdy moja aplikacja nie obsługuje protokołu HTTP lub HTTPS?

Tak. Jako protokół monitorowania można określić protokół TCP, a Traffic Manager inicjować połączenie TCP i czekać na odpowiedź z punktu końcowego. Jeśli punkt końcowy odpowiada na żądanie połączenia odpowiedzią w celu nawiązania połączenia w ramach limitu czasu, ten punkt końcowy jest oznaczony jako w dobrej kondycji.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Jakie konkretne odpowiedzi są wymagane z punktu końcowego podczas korzystania z monitorowania protokołu TCP?

W przypadku korzystania z monitorowania protokołu TCP Traffic Manager trzykierunkowe uściślinie TCP przez wysłanie żądania SYN do punktu końcowego na określonym porcie. Następnie czeka na odpowiedź SYN-ACK z punktu końcowego przez okres czasu (określony w ustawieniach limitu czasu).

- Jeśli odpowiedź syn-ACK zostanie odebrana w przedziale czasu określonym w ustawieniach monitorowania, ten punkt końcowy jest uznawany za w dobrej kondycji. Fin lub FIN-ACK to oczekiwana odpowiedź z Traffic Manager, gdy regularnie kończy gniazdo.
- Jeśli odpowiedź SYN-ACK zostanie odebrana po upływie określonego limitu czasu, Traffic Manager odpowie za pomocą czasu RST w celu zresetowania połączenia.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Jak szybko Traffic Manager użytkowników z punktu końcowego w złej kondycji?

Traffic Manager udostępnia wiele ustawień, które mogą pomóc w kontrolowaniu zachowania trybu failover profilu Traffic Manager w następujący sposób:

- Można określić, że Traffic Manager sonduje punkty końcowe częściej, ustawiając interwał sondowania na 10 sekund. Dzięki temu można jak najszybciej wykryć każdy punkt końcowy w złej kondycji. 
- Można określić, jak długo trzeba czekać, zanim zostanie uchybiona wartość żądania kontroli kondycji (minimalna wartość przekierowy wynosi 5 sek.).
- Można określić liczbę błędów, które mogą wystąpić, zanim punkt końcowy zostanie oznaczony jako w złej kondycji. Ta wartość może być niska niż 0. W takim przypadku punkt końcowy jest oznaczany jako w złej kondycji natychmiast po niepomyślnej pierwszej kontroli kondycji. Jednak użycie minimalnej wartości 0 dla tolerowanej liczby błędów może prowadzić do wydziennia punktów końcowych z rotacji z powodu przejściowych problemów, które mogą wystąpić w czasie sondowania.
- Możesz określić czas wygaśnięcia (TTL) dla odpowiedzi DNS na wartość 0. Oznacza to, że program rozpoznawania nazw DNS nie może buforować odpowiedzi, a każde nowe zapytanie otrzymuje odpowiedź, która zawiera najbardziej aktualne informacje o kondycji Traffic Manager użytkownika.

Za pomocą tych ustawień usługa Traffic Manager tryb failover w czasie poniżej 10 sekund, gdy punkt końcowy będzie w złej kondycji i zostanie wykonane zapytanie DNS względem odpowiedniego profilu.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Jak określić różne ustawienia monitorowania dla różnych punktów końcowych w profilu?

Traffic Manager monitorowania są na poziomie profilu. Jeśli musisz użyć innego ustawienia monitorowania tylko dla jednego punktu końcowego, [](traffic-manager-nested-profiles.md) możesz to zrobić, ustawiając ten punkt końcowy jako profil zagnieżdżony, którego ustawienia monitorowania różnią się od ustawień profilu nadrzędnego.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Jak przypisać nagłówki HTTP do Traffic Manager kondycji do punktów końcowych?

Traffic Manager umożliwia określenie nagłówków niestandardowych w testach kondycji protokołu HTTP(S), które inicjuje do punktów końcowych. Jeśli chcesz określić nagłówek niestandardowy, możesz to zrobić na poziomie profilu (dotyczy to wszystkich punktów końcowych) lub określić go na poziomie punktu końcowego. Jeśli nagłówek jest zdefiniowany na obu poziomach, ten określony na poziomie punktu końcowego zastąpi pierwszy poziom profilu.
Jednym z typowych przypadków użycia w tym przypadku jest określanie nagłówków hosta, dzięki czemu żądania Traffic Manager mogą być prawidłowo kierowane do punktu końcowego hostowanej w środowisku wielodostępnym. Innym zastosowaniem tego jest identyfikowanie żądań Traffic Manager z dzienników żądań HTTP(S) punktu końcowego

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Jakiego nagłówka hosta używają kontrole kondycji punktu końcowego?

Jeśli nie podano niestandardowego ustawienia nagłówka hosta, nagłówek hosta używany przez usługę Traffic Manager jest nazwą DNS obiektu docelowego punktu końcowego skonfigurowanego w profilu, jeśli jest on dostępny.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Jakie są adresy IP, z których pochodzą kontrole kondycji?

Kliknij [tutaj,](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) aby dowiedzieć się, jak pobrać listy adresów IP, z których mogą pochodzić Traffic Manager kontroli kondycji. Możesz użyć interfejsu API REST, interfejsu wiersza polecenia platformy Azure Azure PowerShell, aby pobrać najnowszą listę. Przejrzyj wymienione adresy IP, aby upewnić się, że połączenia przychodzące z tych adresów IP mogą sprawdzać stan kondycji w punktach końcowych.

Przykład użycia Azure PowerShell:

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus
$result = $serviceTags.Values | Where-Object { $_.Name -eq "AzureTrafficManager" }
$result.Properties.AddressPrefixes
```

> [!NOTE]
> Publiczne adresy IP mogą ulec zmianie bez powiadomienia. Upewnij się, że pobrano najnowsze informacje przy użyciu interfejsu API odnajdywania tagów usługi lub pliku JSON do pobrania.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Ile kontroli kondycji do mojego punktu końcowego mogę oczekiwać od Traffic Manager?

Liczba testów kondycji Traffic Manager do punktu końcowego zależy od następujących czynników:

- wartość ustawiona dla interwału monitorowania (mniejszy interwał oznacza więcej żądań docelowych dla punktu końcowego w danym okresie).
- liczba lokalizacji, z których pochodzą kontrole kondycji (adresy IP, z których można oczekiwać tych kontroli, są wymienione w poprzednich często zadawanych pytaniach).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Jak uzyskać powiadomienie, jeśli jeden z punktów końcowych nie działa?

Jedną z metryk dostarczanych przez Traffic Manager jest stan kondycji punktów końcowych w profilu. Można to zobaczyć jako agregację wszystkich punktów końcowych w profilu (na przykład 75% punktów końcowych jest w dobrej kondycji) lub na poziomie punktu końcowego. Traffic Manager są udostępniane za pośrednictwem usługi Azure Monitor i możesz użyć jej funkcji [alertów,](../azure-monitor/alerts/alerts-metric.md) aby otrzymywać powiadomienia w przypadku zmiany stanu kondycji punktu końcowego. Aby uzyskać więcej informacji, zobacz [Traffic Manager metryki i alerty.](traffic-manager-metrics-alerts.md)  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager profilów zagnieżdżonych

### <a name="how-do-i-configure-nested-profiles"></a>Jak mogę skonfigurować profile zagnieżdżone?

Zagnieżdżone Traffic Manager można skonfigurować zarówno przy użyciu interfejsu Azure Resource Manager, jak i klasycznych interfejsów API REST platformy Azure, poleceń cmdlet usługi Azure PowerShell i międzyplatformowych poleceń interfejsu wiersza polecenia platformy Azure. Są one również obsługiwane za pośrednictwem nowej Azure Portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Ile warstw zagnieżdżania obsługuje traffic manger?

Profile można zagnieżdżać na głębokości do 10 poziomów. Pętle nie są dozwolone.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Czy można mieszać inne typy punktów końcowych z zagnieżdżone profile podrzędne w tym samym Traffic Manager profilu?

Tak. Nie ma żadnych ograniczeń dotyczących łączenia punktów końcowych różnych typów w profilu.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>W jaki sposób model rozliczeń ma zastosowanie do profilów zagnieżdżonych?

Używanie profilów zagnieżdżonych nie ma negatywnego wpływu na ceny.

Traffic Manager rozliczenia mają dwa składniki: kontrole kondycji punktu końcowego i miliony zapytań DNS

* Kontrole kondycji punktu końcowego: po skonfigurowaniu jako punktu końcowego w profilu nadrzędnym nie są naliczane opłaty za profil podrzędny. Monitorowanie punktów końcowych w profilu podrzędnym jest rozliczane w zwykły sposób.
* Zapytania DNS: każde zapytanie jest zliczane tylko raz. Zapytanie względem profilu nadrzędnego, które zwraca punkt końcowy z profilu podrzędnego, jest wliczane tylko do profilu nadrzędnego.

Aby uzyskać szczegółowe informacje, zobacz Traffic Manager [cennika.](https://azure.microsoft.com/pricing/details/traffic-manager/)

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Czy profile zagnieżdżone mają wpływ na wydajność?

Nie. Korzystanie z profilów zagnieżdżonych nie ma wpływu na wydajność.

Serwery Traffic Manager przechodzi wewnętrznie hierarchii profilów podczas przetwarzania każdego zapytania DNS. Zapytanie DNS do profilu nadrzędnego może odbierać odpowiedź DNS z punktem końcowym z profilu podrzędnego. Pojedynczy rekord CNAME jest używany niezależnie od tego, czy jest używany pojedynczy profil, czy profile zagnieżdżone. Nie ma potrzeby tworzenia rekordu CNAME dla każdego profilu w hierarchii.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Jak Traffic Manager oblicza kondycję zagnieżdżonych punktów końcowych w profilu nadrzędnym?

Profil nadrzędny nie wykonuje kontroli kondycji bezpośrednio na podrzędnym. Zamiast tego kondycja punktów końcowych profilu podrzędnego jest używana do obliczania ogólnej kondycji profilu podrzędnego. Te informacje są propagowane w górę hierarchii zagnieżdżonych profilów w celu określenia kondycji zagnieżdżonych punktów końcowych. Profil nadrzędny używa tej zagregowanej kondycji do określenia, czy ruch może być skierowany do elementu podrzędnego.

W poniższej tabeli opisano zachowanie Traffic Manager kondycji zagnieżdżonych punktów końcowych.

| Stan monitora profilu podrzędnego | Stan nadrzędnego monitora punktu końcowego | Uwagi |
| --- | --- | --- |
| Wyłączona. Profil podrzędny został wyłączony. |Zatrzymano |Stan nadrzędnego punktu końcowego to Zatrzymany, a nie Wyłączony. Stan Wyłączone jest zarezerwowany dla wskazania, że punkt końcowy został wyłączony w profilu nadrzędnym. |
| Zdegradowanych. Co najmniej jeden punkt końcowy profilu podrzędnego jest w stanie obniżonej wydajności. |Online: liczba punktów końcowych online w profilu podrzędnym jest co najmniej wartością MinChildEndpoints.<BR>CheckingEndpoint: liczba punktów końcowych Online i CheckingEndpoint w profilu podrzędnym jest co najmniej wartością MinChildEndpoints.<BR>Obniżona wydajność: w przeciwnym razie. |Ruch jest przekierowyny do punktu końcowego stanu CheckingEndpoint. Jeśli parametr MinChildEndpoints jest ustawiony zbyt wysoko, punkt końcowy zawsze ma obniżoną wydajność. |
| Online. Co najmniej jeden punkt końcowy profilu podrzędnego ma stan Online. Żaden punkt końcowy nie jest w stanie obniżonej wydajności. |Zobacz powyżej. | |
| SprawdzanieEndpoints. Co najmniej jeden punkt końcowy profilu podrzędnego to "CheckingEndpoint". Brak punktów końcowych "Online" lub "Obniżona wydajność" |Jak wyżej. | |
| Nieaktywne. Wszystkie punkty końcowe profilu podrzędnego są wyłączone lub zatrzymane lub ten profil nie ma punktów końcowych. |Zatrzymano | |

## <a name="next-steps"></a>Następne kroki:

- Dowiedz się więcej o monitorowaniu Traffic Manager [punktu końcowego i automatycznym trybie failover.](../traffic-manager/traffic-manager-monitoring.md)
- Dowiedz się więcej o Traffic Manager [routingu ruchu.](../traffic-manager/traffic-manager-routing-methods.md)