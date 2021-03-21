---
title: Azure Traffic Manager — metody routingu ruchu
description: Ten artykuł ułatwia zapoznanie się z różnymi metodami routingu ruchu używanymi przez Traffic Manager
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: eeebded128f636ecba2e4e0dab1bc2f0632aaa6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730976"
---
# <a name="traffic-manager-routing-methods"></a>Metody routingu w usłudze Traffic Manager

Usługa Azure Traffic Manager obsługuje sześć metod routingu ruchu sieciowego w celu określenia sposobu kierowania ruchu w sieci do różnych punktów końcowych usługi. Dla dowolnego profilu Traffic Manager stosuje metodę routingu ruchu skojarzoną z nią do każdego odebranego zapytania DNS. Metoda routingu ruchu określa, który punkt końcowy jest zwracany w odpowiedzi DNS.

W Traffic Manager są dostępne następujące metody routingu ruchu:

* **[Priorytet](#priority-traffic-routing-method):** wybierz pozycję Routing **priorytetu** , jeśli chcesz mieć podstawowy punkt końcowy usługi dla całego ruchu. Można podać wiele punktów końcowych kopii zapasowych na wypadek, gdy podstawowy lub jeden z punktów końcowych kopii zapasowych jest niedostępny.
* **[Ważone](#weighted):** wybierz opcję Routing **ważony** , gdy chcesz rozpowszechnić ruch w zestawie punktów końcowych na podstawie ich wagi. Ustaw wagę tak samo, aby równomiernie rozłożyć we wszystkich punktach końcowych.
* **[Wydajność](#performance):** wybierz opcję Routing **wydajności** , gdy punkty końcowe znajdują się w różnych lokalizacjach geograficznych, i chcesz, aby użytkownicy końcowi korzystali z punktu końcowego "najbliższy" dla najmniejszego opóźnienia sieci.
* **[Geograficzna](#geographic):** wybierz opcję Routing **geograficzny** , aby skierować użytkowników do określonych punktów końcowych (platformy Azure, zewnętrznych lub zagnieżdżonych) w zależności od tego, gdzie ich zapytania DNS pochodzą z geograficznie. Dzięki tej metodzie routingu można być zgodna z scenariuszami, takimi jak mandaty dotyczące danych, lokalizacja zawartości & środowiska użytkownika i pomiar ruchu z różnych regionów.
* **Z [wieloma wartościami](#multivalue):** **Wybierz opcję** wielu dla profilów Traffic Manager, które mogą mieć tylko adresy IPv4/IPv6 jako punkty końcowe. Po odebraniu zapytania dla tego profilu są zwracane wszystkie zdrowe punkty końcowe.
* **[Podsieć](#subnet):** wybierz metodę routingu ruch **podsieci** , aby zmapować zestawy adresów IP użytkowników końcowych do określonego punktu końcowego. Po odebraniu żądania punkt końcowy zostanie przypisany do źródłowego adresu IP tego żądania. 


Wszystkie profile Traffic Manager mają monitorowanie kondycji i automatyczną pracę w trybie failover dla punktów końcowych. Aby uzyskać więcej informacji, zobacz [Traffic Manager monitorowania punktów końcowych](traffic-manager-monitoring.md). W ramach profilu Traffic Manager można skonfigurować tylko jedną metodę routingu ruchu jednocześnie. W dowolnym momencie możesz wybrać inną metodę routingu ruchu dla profilu. Zmiany zostaną zastosowane w ciągu jednej minuty bez przestojów. Metody routingu ruchu można łączyć za pomocą zagnieżdżonych profilów Traffic Manager. Zagnieżdżanie profilów umożliwia zaawansowane konfiguracje routingu ruchu, które spełniają potrzeby większych i złożonych aplikacji. Aby uzyskać więcej informacji, zobacz [profile Traffic Manager zagnieżdżonych](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Priorytetowy ruch-Metoda routingu

Często organizacja chce zapewnić niezawodność swoich usług. W tym celu wdrażają co najmniej jedną usługę kopii zapasowych na wypadek, gdyby podstawowa. Metoda routingu ruchu "Priority" pozwala klientom platformy Azure łatwo zaimplementować ten wzorzec trybu failover.

![Usługa Azure Traffic Manager "Priority" — Metoda routingu](media/traffic-manager-routing-methods/priority.png)

Profil usługi Traffic Manager zawiera listę punktów końcowych usług z różnymi priorytetami. Domyślnie usługa Traffic Manager kieruje cały ruch do podstawowego punktu końcowego (punktu końcowego o najwyższym priorytecie). Jeśli podstawowy punkt końcowy jest niedostępny, usługa Traffic Manager kieruje ruch do drugiego punktu końcowego. W sytuacji, gdy podstawowy i pomocniczy punkt końcowy nie są dostępne, ruch przechodzi do trzeciego i tak dalej. Dostępność punktu końcowego zależy od skonfigurowanego stanu (włączonego lub wyłączonego) oraz od bieżącego monitorowania.

### <a name="configuring-endpoints"></a>Konfigurowanie punktów końcowych

Za pomocą Azure Resource Manager można jawnie skonfigurować priorytet punktu końcowego przy użyciu właściwości "Priority" dla każdego punktu końcowego. Ta właściwość jest wartością z przedziału od 1 do 1000. Niższa wartość reprezentuje wyższy priorytet. Punkty końcowe nie mogą udostępniać wartości priorytetu. Ustawienie właściwości jest opcjonalne. W przypadku pominięcia jest używany domyślny priorytet oparty na kolejności punktów końcowych.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Ważony ruch sieciowy — Metoda routingu
"Ważona" Metoda routingu ruchu umożliwia równomierne dystrybuowanie ruchu lub użycie wstępnie zdefiniowanej wagi.

![Platforma Azure Traffic Manager "ważona" — Metoda routingu ruchu](media/traffic-manager-routing-methods/weighted.png)

W przypadku metody routingu ruchu ważonego należy przypisać wagę do każdego punktu końcowego w konfiguracji profilu Traffic Manager. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr jest opcjonalny. W przypadku pominięcia menedżerowie ruchu używają domyślnej wagi "1". Im wyższa waga, tym wyższy priorytet.

Dla każdego odebranego zapytania DNS usługa Traffic Manager losowo wybiera dostępny punkt końcowy. Prawdopodobieństwo wybrania punktu końcowego zależy od wagi, jaka została przypisana do tego punktu. Użycie tej samej wagi we wszystkich punktach końcowych skutkuje równym rozkładem ruchu. Użycie wyższych lub niższych wag w określonych punktach końcowych powoduje, że te punkty końcowe są zwracane częściej lub rzadziej w odpowiedziach DNS.

Metoda ważona umożliwia kilka przydatnych scenariuszy:

* Stopniowe Uaktualnianie aplikacji: nadanej wartości procentowej ruchu kierowanego do nowego punktu końcowego i stopniowe zwiększenie ruchu w czasie do 100%.
* Migrowanie aplikacji na platformę Azure: Utwórz profil z platformą Azure i zewnętrznymi punktami końcowymi. Dostosuj wagę punktów końcowych, aby preferować nowe punkty końcowe.
* Przenoszenie w chmurze w celu zwiększenia pojemności: szybkie rozszerzanie lokalnego wdrożenia do chmury przez umieszczenie go za profilem Traffic Manager. Jeśli potrzebujesz dodatkowej pojemności w chmurze, możesz dodać lub włączyć więcej punktów końcowych i określić, jaka część ruchu przechodzi do każdego punktu końcowego.

Wagi można skonfigurować przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.

Należy pamiętać, że odpowiedzi DNS są przechowywane w pamięci podręcznej przez klientów. Są one również buforowane przez cykliczne serwery DNS używane przez klientów do rozpoznawania nazw DNS. Ta pamięć podręczna może mieć wpływ na ważone rozkłady ruchu. Gdy liczba klientów i cykliczne serwery DNS są duże, dystrybucja ruchu działa zgodnie z oczekiwaniami. Jeśli jednak liczba klientów lub rekursywny serwer DNS jest mała, buforowanie może znacząco pochylić rozkład ruchu.

Typowe przypadki użycia obejmują:

* Środowiska deweloperskie i testowe
* Komunikacja między aplikacjami
* Aplikacje przeznaczone do użycia w wąskim znaczeniu użytkownika, które współużytkują wspólną infrastrukturę DNS rekursywnie (na przykład pracownicy firmy łączący się za pomocą serwera proxy)

Te efekty buforowania DNS są wspólne dla wszystkich systemów routingu ruchu opartych na systemie DNS, a nie tylko na platformie Azure Traffic Manager. W niektórych przypadkach jawne czyszczenie pamięci podręcznej DNS może stanowić obejście problemu. Jeśli to nie zadziała, alternatywna metoda routingu ruchu może być bardziej odpowiednia.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Ruch wydajności — Metoda routingu

Wdrożenie punktów końcowych w co najmniej dwóch lokalizacjach na całym świecie może zwiększyć czas odpowiedzi aplikacji. Za pomocą metody routingu ruchu "Performance" można skierować ruch do lokalizacji, która jest najbliżej.

![Usługa Azure Traffic Manager "wydajność" — Metoda routingu](media/traffic-manager-routing-methods/performance.png)

Punkt końcowy "najbliższy" nie musi być najbliższy, mierzony przez odległość geograficzną. Zamiast tego Metoda routingu ruchu "Performance" określa najbliższy punkt końcowy przez pomiar opóźnienia sieci. Traffic Manager utrzymuje tabelę opóźnienia internetowego do śledzenia czasu rundy między zakresami adresów IP a każdym centrum danych platformy Azure.

Traffic Manager wyszukuje źródłowy adres IP przychodzącego żądania DNS w tabeli opóźnienia Internetu. Traffic Manager następnie wybiera dostępny punkt końcowy w centrum danych platformy Azure o najniższym opóźnieniu dla tego zakresu adresów IP. Następnie Traffic Manager zwraca ten punkt końcowy w odpowiedzi DNS.

Zgodnie z opisem w temacie [jak działa Traffic Manager](traffic-manager-how-it-works.md), Traffic Manager nie odbiera zapytań DNS bezpośrednio od klientów. Zamiast tego zapytania DNS pochodzą z cyklicznej usługi DNS, z której korzystają klienci. W związku z tym adres IP używany do określenia punktu końcowego "najbliższy" nie jest adresem IP klienta, ale jest adresem IP cyklicznej usługi DNS. Ten adres IP jest dobrym serwerem proxy dla klienta.


Traffic Manager regularnie aktualizuje tabelę opóźnienia Internetu w celu uwzględnienia zmian w globalnej sieci Internet i nowych regionach świadczenia usługi Azure. Jednak wydajność aplikacji różni się w zależności od zmian w czasie rzeczywistym w ramach obciążenia przez Internet. Ruch związany z wydajnością — Routing nie monitoruje obciążenia w danym punkcie końcowym usługi. Jeśli punkt końcowy będzie niedostępny, Traffic Manager nie będzie uwzględniać go w odpowiedziach zapytań DNS.


Punkty do uwagi:

* Jeśli Twój profil zawiera wiele punktów końcowych w tym samym regionie świadczenia usługi Azure, Traffic Manager dystrybuuje ruch równomiernie przez dostępne punkty końcowe w tym regionie. Jeśli wolisz inny rozkład ruchu w obrębie regionu, możesz użyć [zagnieżdżonych profilów Traffic Manager](traffic-manager-nested-profiles.md).
* Jeśli wszystkie włączone punkty końcowe w najbliższym regionie platformy Azure mają obniżoną wydajność, Traffic Manager przenosi ruch do punktów końcowych w następnym najbliższym regionie świadczenia usługi Azure. Jeśli chcesz zdefiniować preferowaną sekwencję trybu failover, użyj [zagnieżdżonych profilów Traffic Manager](traffic-manager-nested-profiles.md).
* W przypadku używania metody routingu ruchu o wydajności z zewnętrznymi punktami końcowymi lub zagnieżdżonych punktów końcowych należy określić lokalizację tych punktów końcowych. Wybierz region platformy Azure znajdujący się najbliżej wdrożenia. Te lokalizacje są wartościami obsługiwanymi przez tabelę opóźnienia Internetu.
* Algorytm wybierający punkt końcowy jest deterministyczny. Powtarzające się zapytania DNS z tego samego klienta są kierowane do tego samego punktu końcowego. Zazwyczaj klienci korzystają z różnych rekursywnych serwerów DNS podczas podróży. Klient może być kierowany do innego punktu końcowego. W przypadku routingu mogą być również narażone aktualizacje tabeli opóźnienia Internetu. Dlatego metoda routingu ruchu w zakresie wydajności nie gwarantuje, że klient jest zawsze kierowany do tego samego punktu końcowego.
* Po zmianie tabeli opóźnienia internetowego można zauważyć, że niektórzy klienci są kierowani do innego punktu końcowego. Ta zmiana routingu jest bardziej precyzyjna w oparciu o bieżące dane opóźnienia. Te aktualizacje są niezbędne do zachowania dokładności routingu ruchu sieciowego w miarę ciągłego rozwoju Internetu.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Ruch geograficzny — Metoda routingu

Profile Traffic Manager można skonfigurować tak, aby korzystały z metody routingu geograficznego, dzięki czemu użytkownicy będą kierowani do określonych punktów końcowych (Azure, External lub Nested) na podstawie lokalizacji geograficznej, z której pochodzą zapytanie DNS. Dzięki tej metodzie routingu umożliwia ona zgodność z mandatami związanymi z suwerennością danych, lokalizowaniem zawartości & środowiska użytkownika i mierzeniem ruchu z różnych regionów.
Jeśli profil jest skonfigurowany pod kątem routingu geograficznego, każdy punkt końcowy skojarzony z tym profilem musi mieć przypisane do niego zestaw regionów geograficznych. Region geograficzny może mieć następujące poziomy szczegółowości 
- World — dowolny region
- Grupowanie regionalne — na przykład Afryka, Bliski Wschód, Australia/Pacyfik itp. 
- Kraj/region — na przykład Irlandia, Peru, Hongkong SAR itp. 
- Województwo — na przykład stan USA-Kalifornia, Australia-Queensland, Canada-Alberta itd. (Uwaga: ten poziom szczegółowości jest obsługiwany tylko w przypadku Stanów/prowincji w Australii, Kanadzie i USA).

Po przypisaniu regionu lub zestawu regionów do punktu końcowego wszystkie żądania z tych regionów są kierowane tylko do tego punktu końcowego. Traffic Manager używa źródłowego adresu IP zapytania DNS, aby określić region, z którego użytkownik wykonuje zapytanie. Często można je znaleźć jako adres IP lokalnego programu rozpoznawania nazw DNS wykonującego zapytanie dla użytkownika.  

![Azure Traffic Manager "geograficzny" — Metoda routingu](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager odczytuje źródłowy adres IP zapytania DNS i decyduje, z którego regionu geograficznego pochodzi. Następnie sprawdza, czy istnieje punkt końcowy, do którego jest mapowany ten region geograficzny. Ta funkcja wyszukiwania zaczyna się na najniższym poziomie szczegółowości (stan/region, w którym jest obsługiwany, inne na poziomie kraju/regionu) i przechodzi do najwyższego poziomu, który jest **światem**. Pierwsze dopasowanie znalezione przy użyciu tego przechodzenia jest wybierane jako punkt końcowy do zwrócenia w odpowiedzi na zapytanie. W przypadku dopasowywania z typem zagnieżdżonym punkt końcowy w tym profilu podrzędnym jest zwracany na podstawie jego metody routingu. Następujące punkty mają zastosowanie do tego zachowania:

- Region geograficzny można zamapować tylko na jeden punkt końcowy w profilu Traffic Manager, gdy typem routingu jest Routing geograficzny. To ograniczenie gwarantuje, że Routing użytkowników jest deterministyczny, a klienci mogą włączać scenariusze, które wymagają jednoznacznej granicy geograficznej.
- Jeśli region użytkownika znajduje się na liście dwóch różnych mapowań geograficznych punktów końcowych, Traffic Manager wybiera punkt końcowy o najniższym poziomie szczegółowości. Traffic Manager nie będzie uwzględniać żądań routingu z tego regionu do innego punktu końcowego. Rozważmy na przykład profil typu routingu geograficznego z dwoma punktami końcowymi — Endpoint1 i Endpoint2. Endpoint1 jest skonfigurowany do odbierania ruchu z Irlandii, a Endpoint2 jest skonfigurowany do odbierania ruchu z Europy. Jeśli żądanie pochodzi z Irlandii, zawsze jest kierowane do Endpoint1.
- Ponieważ region można zamapować tylko do jednego punktu końcowego, Traffic Manager zwraca odpowiedź, czy punkt końcowy jest w dobrej kondycji.

    >[!IMPORTANT]
    >Zdecydowanie zaleca się, aby klienci korzystający z metody routingu geograficznego skojarzyć ją z punktami końcowymi typu zagnieżdżonego, które mają profile podrzędne zawierające co najmniej dwa punkty końcowe w ramach każdego z nich.
- Jeśli zostanie znaleziony odpowiednik punktu końcowego i ten punkt końcowy jest w stanie **zatrzymania** , Traffic Manager zwraca odpowiedź NoData. W takim przypadku żadne dalsze wyszukiwania nie są większe w hierarchii regionów geograficznych. To zachowanie jest również stosowane dla zagnieżdżonych typów punktów końcowych, gdy profil podrzędny jest w stanie **zatrzymania** lub **wyłączenia** .
- Jeśli punkt końcowy wyświetla stan **wyłączony** , nie zostanie uwzględniony w procesie dopasowywania regionu. To zachowanie jest również stosowane dla zagnieżdżonych typów punktów końcowych, gdy punkt końcowy jest w stanie **wyłączenia** .
- Jeśli zapytanie pochodzi z regionu geograficznego, który nie ma mapowania w tym profilu, Traffic Manager zwraca odpowiedź NoData. Dlatego zdecydowanie zaleca się używanie routingu geograficznego z jednym punktem końcowym. W idealnym przypadku Typ zagnieżdżony z co najmniej dwoma punktami końcowymi w profilu podrzędnym, **z przypisanym** do niego regionem. Ta konfiguracja zapewnia również, że wszystkie adresy IP, które nie są mapowane do regionu, są obsługiwane.

Zgodnie z opisem w temacie [jak działa Traffic Manager](traffic-manager-how-it-works.md), Traffic Manager nie odbiera zapytań DNS bezpośrednio od klientów. Zapytania DNS pochodzą z cyklicznej usługi DNS, z której korzystają klienci. Dlatego adres IP używany do określenia regionu nie jest adresem IP klienta, ale raczej adresem IP cyklicznej usługi DNS. Ten adres IP jest dobrym serwerem proxy dla klienta.

### <a name="faqs"></a>Często zadawane pytania

* [Jakie są przypadki użycia, w których funkcja routingu geograficznego jest przydatna?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Jak mogę zdecydować, czy należy użyć metody routingu wydajności czy metody routingu geograficznego?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Jakie regiony są obsługiwane przez Traffic Manager na potrzeby routingu geograficznego?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Jak Usługa Traffic Manager określa, skąd użytkownik wykonuje zapytania?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Czy jest gwarantowane, że Traffic Manager może prawidłowo określić dokładną lokalizację geograficzną użytkownika w każdym przypadku?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Czy punkt końcowy musi znajdować się fizycznie w tym samym regionie, w którym jest skonfigurowany do routingu geograficznego?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Czy można przypisywać regiony geograficzne do punktów końcowych w profilu, który nie jest skonfigurowany do routingu geograficznego?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Dlaczego otrzymuję błąd podczas próby zmiany metody routingu istniejącego profilu na geograficzny?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Dlaczego zdecydowanie zaleca się, aby klienci utworzyli profile zagnieżdżone zamiast punktów końcowych w ramach profilu z włączonym routingiem geograficznym?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Czy istnieją jakieś ograniczenia dotyczące wersji interfejsu API, która obsługuje ten typ routingu?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Wielowartościowy ruch-Metoda routingu
Metoda **routingu ruchu wielowartościowego** umożliwia uzyskanie wielu prawidłowych punktów końcowych w pojedynczej odpowiedzi na zapytanie DNS. Ta konfiguracja umożliwia wywołującemu podejmowanie ponownych prób po stronie klienta z innymi punktami końcowymi w przypadku, gdy zwrócony punkt końcowy nie odpowiada. Ten wzorzec pozwala zwiększyć dostępność usługi i ograniczyć opóźnienie związane z uzyskiwaniem punktu końcowego w dobrej kondycji przez nowe zapytanie DNS. Metoda routingu z wieloma wartościami działa tylko wtedy, gdy wszystkie punkty końcowe typu "External" i są określone jako adresy IPv4 lub IPv6. Po odebraniu zapytania dla tego profilu są zwracane wszystkie prawidłowe punkty końcowe i podlegają konfigurowalnej maksymalnej liczbie zwracanych wartości.

### <a name="faqs"></a>Często zadawane pytania

* [Jakie są przypadki użycia, w których Routing z wieloma wartościami jest przydatny?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Ile punktów końcowych jest zwracanych w przypadku użycia routingu wielowartościowego?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Czy w przypadku użycia routingu wielowartościowego otrzymasz ten sam zestaw punktów końcowych?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Ruch podsieci — Metoda routingu
Metoda routingu ruchu **podsieci** umożliwia mapowanie zestawu zakresów adresów IP użytkowników końcowych na określone punkty końcowe w profilu. Jeśli Traffic Manager odbiera zapytanie DNS dla tego profilu, sprawdzi źródłowy adres IP tego żądania. Następnie określi punkt końcowy, do którego jest zamapowany, i zwróci ten punkt końcowy w odpowiedzi na zapytanie. W większości przypadków źródłowy adres IP jest programem rozpoznawania nazw DNS używanym przez wywołującego.

Adres IP, który ma być mapowany do punktu końcowego, można określić jako zakresy CIDR (na przykład 1.2.3.0/24) lub jako zakres adresów (na przykład 1.2.3.4-5.6.7.8). Zakresy adresów IP skojarzone z punktem końcowym muszą być unikatowe w ramach tego profilu. Zakres adresów nie może pokrywać się z zestawem adresów IP innego punktu końcowego w tym samym profilu.
W przypadku zdefiniowania punktu końcowego bez zakresu adresów, który działa jako rezerwowe i pobiera ruch z pozostałych podsieci. Jeśli żaden rezerwowy punkt końcowy nie jest uwzględniony, Traffic Manager wysyła odpowiedź NoData dla wszelkich niezdefiniowanych zakresów. Zdecydowanie zaleca się zdefiniowanie rezerwowego punktu końcowego, aby upewnić się, że wszystkie możliwe zakresy adresów IP są określone w punktach końcowych.

Routing podsieci może służyć do dostarczania innego środowiska dla użytkowników nawiązujących połączenie z określoną przestrzenią adresów IP. Można na przykład wykonać kierowanie wszystkich żądań z biura firmy do innego punktu końcowego. Ta metoda routingu jest szczególnie przydatna, jeśli próbujesz przetestować tylko wewnętrzną wersję aplikacji. Inny scenariusz polega na udostępnianiu określonego środowiska użytkownikom łączącym się za pośrednictwem danego usługodawcy internetowego (gdy na przykład chcesz blokować użytkowników tego usługodawcy).

### <a name="faqs"></a>Często zadawane pytania

* [Jakie są przypadki użycia, w których Routing podsieci jest przydatny?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Jak Traffic Manager znać adres IP użytkownika końcowego?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Jak można określić adresy IP w przypadku używania routingu podsieci?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Jak określić rezerwowy punkt końcowy w przypadku używania routingu podsieci?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Co się stanie, jeśli punkt końcowy jest wyłączony w profilu typu routingu podsieci?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak opracowywać aplikacje o wysokiej dostępności przy użyciu funkcji [monitorowania punktów końcowych Traffic Manager](traffic-manager-monitoring.md)