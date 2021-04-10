---
title: Monitorowanie punktu końcowego usługi Azure Traffic Manager | Microsoft Docs
description: Ten artykuł pomoże Ci zrozumieć, w jaki sposób Traffic Manager używać monitorowania punktów końcowych i automatycznego trybu failover punktu końcowego, aby pomóc klientom platformy Azure wdrożyć aplikacje o wysokiej dostępności
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: 455eeb83ef5a9608c077de24b8d3d4722d26a822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742713"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorowanie punktu końcowego usługi Traffic Manager

Usługa Azure Traffic Manager obejmuje wbudowane monitorowanie punktów końcowych i automatyczne przełączanie do trybu failover punktu końcowego. Ta funkcja ułatwia dostarczanie aplikacji o wysokiej dostępności, które są odporne na awarie punktu końcowego, w tym awarie regionów platformy Azure.

## <a name="configure-endpoint-monitoring"></a>Konfigurowanie monitorowania punktów końcowych

Aby skonfigurować monitorowanie punktu końcowego, należy określić następujące ustawienia w profilu Traffic Manager:

* **Protokół**. Wybierz opcję HTTP, HTTPS lub TCP jako protokół używany przez Traffic Manager podczas sondowania punktu końcowego w celu sprawdzenia jego kondycji. Monitorowanie protokołu HTTPS nie sprawdza, czy certyfikat TLS/SSL jest prawidłowy — sprawdza tylko, czy certyfikat jest obecny.
* **Port**. Wybierz port używany dla żądania.
* **Ścieżka**. To ustawienie konfiguracji jest prawidłowe tylko dla protokołów HTTP i HTTPS, dla których jest wymagane ustawienie Path. Dostarczenie tego ustawienia dla protokołu monitorowania TCP spowoduje wystąpienie błędu. W przypadku protokołu HTTP i HTTPS podaj ścieżkę względną i nazwę strony sieci Web lub plik, do którego uzyskuje dostęp do monitorowania. Ukośnik (/) jest prawidłowym wpisem ścieżki względnej. Ta wartość oznacza, że plik znajduje się w katalogu głównym (domyślnie).
* **Niestandardowe ustawienia nagłówka**. To ustawienie konfiguracji ułatwia dodawanie określonych nagłówków HTTP do kontroli kondycji, które Traffic Manager wysyła do punktów końcowych w ramach profilu. Niestandardowe nagłówki można określić na poziomie profilu, który ma być stosowany dla wszystkich punktów końcowych w tym profilu i/lub na poziomie punktu końcowego, który ma zastosowanie tylko do tego punktu końcowego. Niestandardowych nagłówków można używać do sprawdzania kondycji punktów końcowych w środowisku wielodostępnym. Dzięki temu można prawidłowo skierować do lokalizacji docelowej, określając nagłówek hosta. Można również użyć tego ustawienia, dodając unikatowe nagłówki, które mogą służyć do identyfikowania Traffic Manager źródłowych żądań HTTP (S) i przetwarzają je inaczej. Można określić maksymalnie osiem nagłówków: par wartości rozdzielonych przecinkami. Na przykład "Header1: wartość1, header2: wartość2". 
* **Oczekiwane zakresy kodu stanu**. To ustawienie pozwala określić wiele zakresów kodu sukcesu w formacie 200-299, 301-301. Jeśli te kody stanu są odbierane jako odpowiedź z punktu końcowego po zakończeniu sprawdzania kondycji, Traffic Manager oznacza te punkty końcowe jako w dobrej kondycji. Można określić maksymalnie osiem zakresów kodu stanu. To ustawienie ma zastosowanie tylko do protokołu HTTP i HTTPS oraz do wszystkich punktów końcowych. To ustawienie jest na poziomie profilu Traffic Manager i domyślnie wartość 200 jest definiowana jako kod stanu sukcesu.
* **Interwał sondowania**. Ta wartość określa, jak często jest sprawdzany punkt końcowy dla jego kondycji z Traffic Manager agenta sondowania. W tym miejscu możesz określić dwie wartości: 30 sekund (normalna sonda) i 10 sekund (szybka sonda). Jeśli nie podano żadnych wartości, profil zostanie ustawiony na wartość domyślną 30 sekund. Odwiedź stronę z [cennikiem Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) , aby dowiedzieć się więcej o cenach szybkiego sondowania.
* **Tolerowana liczba niepowodzeń**. Ta wartość określa, ile błędów Agent Traffic Manager jest tolerowany przed oznaczeniem tego punktu końcowego jako złej kondycji. Jego wartość może namieścić się w zakresie od 0 do 9. Wartość 0 oznacza, że pojedynczy błąd monitorowania może spowodować, że punkt końcowy zostanie oznaczony jako w złej kondycji. Jeśli wartość nie jest określona, zostanie użyta wartość domyślna 3.
* **Limit czasu sondy**. Ta właściwość określa czas, przez jaki Agent sondowania usługi Traffic Manager powinien czekać przed uwzględnieniem sprawdzenia sondy kondycji w punkcie końcowym. Jeśli interwał sondowania jest ustawiony na 30 sekund, można ustawić wartość limitu czasu z przedziału od 5 do 10 sekund. Jeśli wartość nie jest określona, zostanie użyta wartość domyślna wynosząca 10 sekund. Jeśli interwał sondowania jest ustawiony na 10 sekund, można ustawić wartość limitu czasu z przedziału od 5 do 9 sekund. Jeśli wartość limitu czasu nie zostanie określona, zostanie użyta wartość domyślna wynosząca 9 sekund.

    ![Monitorowanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Ilustracja: Traffic Manager monitorowania punktu końcowego**

## <a name="how-endpoint-monitoring-works"></a>Jak działa monitorowanie punktów końcowych

Gdy protokół monitorowania jest ustawiony jako HTTP lub HTTPS, Traffic Manager Agent sondowania wysyła żądanie GET do punktu końcowego przy użyciu protokołu, portu i ścieżki względnej. Punkt końcowy jest uznawany za prawidłowy, jeśli Agent sondowania odbiera odpowiedź 200-OK lub wszystkie odpowiedzi skonfigurowane w zakresie **oczekiwanych \* zakresów kodu stanu**. Jeśli odpowiedź ma inną wartość lub nie otrzymano odpowiedzi w ramach przedziału czasu, Traffic Managera próbka agenta sondowania zgodnie z tolerowaną liczbą błędów. Nie wykonano żadnych ponownych prób, jeśli to ustawienie ma wartość 0. Punkt końcowy jest oznaczony jako w złej kondycji, jeśli liczba kolejnych niepowodzeń jest wyższa niż tolerowana liczba błędów.

Gdy protokół monitorowania to TCP, Agent Traffic Manager sondowania tworzy żądanie połączenia TCP przy użyciu określonego portu. Jeśli punkt końcowy odpowiada na żądanie z odpowiedzią na nawiązanie połączenia, sprawdzanie kondycji zostanie oznaczone jako powodzenie. Agent Traffic Manager sondowania resetuje połączenie TCP. W przypadku, gdy odpowiedź ma inną wartość lub nie otrzymano odpowiedzi w ramach przedziału czasu, Traffic Managera próbka agenta sondowania zgodnie z tolerowaną liczbą błędów. Nie wykonano żadnych ponownych prób, jeśli to ustawienie ma wartość 0. Jeśli liczba kolejnych niepowodzeń jest większa niż akceptowalna liczba błędów, oznacza to, że punkt końcowy jest oznaczony jako w złej kondycji.

We wszystkich przypadkach Traffic Manager sondy z wielu lokalizacji. Kolejne błędy określają, co się dzieje w poszczególnych regionach. Dlatego punkty końcowe otrzymują sondy kondycji z Traffic Manager z wyższą częstotliwością niż ustawienie używane dla interwału sondowania.

>[!NOTE]
>W przypadku protokołu monitorowania HTTP lub HTTPS typowym sposobem na stronie punktu końcowego jest zaimplementowanie strony niestandardowej w aplikacji — na przykład/Health.aspx. Przy użyciu tej ścieżki do monitorowania można wykonywać testy specyficzne dla aplikacji, takie jak sprawdzanie liczników wydajności lub sprawdzanie dostępności bazy danych. Na podstawie tych niestandardowych kontroli strona zwraca odpowiedni kod stanu HTTP.

Wszystkie punkty końcowe w ustawieniach monitorowania udziału profilu Traffic Manager. Jeśli konieczne jest użycie różnych ustawień monitorowania dla różnych punktów końcowych, można utworzyć [zagnieżdżone profile Traffic Manager](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stan punktu końcowego i profilu

Profile i punkty końcowe Traffic Manager można włączać i wyłączać. Jednak zmiana stanu punktu końcowego może być również spowodowana przez Traffic Manager zautomatyzowane ustawienia i procesy.

### <a name="endpoint-status"></a>Stan punktu końcowego

Można włączać lub wyłączać konkretny punkt końcowy. Nie dotyczy to usługi źródłowej, która może nadal być w dobrej kondycji. Zmiana stanu punktu końcowego steruje dostępnością punktu końcowego w profilu Traffic Manager. Gdy stan punktu końcowego jest wyłączony, Traffic Manager nie sprawdza jego kondycji, a punkt końcowy nie jest uwzględniony w odpowiedzi DNS.

### <a name="profile-status"></a>Stan profilu

Korzystając z ustawienia stan profilu, można włączyć lub wyłączyć konkretny profil. Gdy stan punktu końcowego ma wpływ na pojedynczy punkt końcowy, stan profilu ma wpływ na cały profil, w tym wszystkie punkty końcowe. Po wyłączeniu profilu punkty końcowe nie są sprawdzane pod kątem kondycji, a żadne punkty końcowe nie są uwzględniane w odpowiedzi DNS. Kod odpowiedzi [NXDOMAIN](https://tools.ietf.org/html/rfc2308) jest zwracany dla zapytania DNS.

### <a name="endpoint-monitor-status"></a>Stan monitora punktu końcowego

Stan monitora punktu końcowego jest wartością wygenerowaną przez Traffic Manager, która pokazuje stan punktu końcowego. Tego ustawienia nie można zmienić ręcznie. Stan monitora punktu końcowego to kombinacja wyników monitorowania punktów końcowych i skonfigurowanego stanu punktu końcowego. Możliwe wartości stanu monitora punktu końcowego przedstawiono w poniższej tabeli:

| Stan profilu | Stan punktu końcowego | Stan monitora punktu końcowego | Uwagi |
| --- | --- | --- | --- |
| Disabled |Enabled (Włączony) |Nieaktywne |Profil został wyłączony. Mimo że stan punktu końcowego jest włączony, stan profilu (wyłączony) ma pierwszeństwo. Punkty końcowe w wyłączonych profilach nie są monitorowane. Kod odpowiedzi NXDOMAIN jest zwracany dla zapytania DNS. |
| &lt;dowolne&gt; |Disabled |Disabled |Punkt końcowy został wyłączony. Wyłączone punkty końcowe nie są monitorowane. Punkt końcowy nie jest uwzględniony w odpowiedziach DNS, ponieważ nie odbiera on ruchu. |
| Enabled (Włączony) |Enabled (Włączony) |Tryb online |Punkt końcowy jest monitorowany i jest w dobrej kondycji. Jest on uwzględniony w odpowiedziach DNS i może odbierać ruch. |
| Enabled (Włączony) |Enabled (Włączony) |Obniżona wydajność |Sprawdzanie kondycji monitorowania punktu końcowego kończy się niepowodzeniem. Punkt końcowy nie jest uwzględniony w odpowiedziach DNS i nie odbiera ruchu. <br>Wyjątek to jeśli wszystkie punkty końcowe mają obniżoną wydajność. W takim przypadku wszystkie są uznawane za zwracane w odpowiedzi na zapytanie.</br>|
| Enabled (Włączony) |Enabled (Włączony) |CheckingEndpoint |Punkt końcowy jest monitorowany, ale wyniki pierwszej sondy nie zostały jeszcze odebrane. CheckingEndpoint jest stanem tymczasowym, który zwykle występuje natychmiast po dodaniu lub włączeniu punktu końcowego w profilu. Punkt końcowy w tym stanie jest dołączony do odpowiedzi DNS i może odbierać ruch. |
| Enabled (Włączony) |Enabled (Włączony) |Zatrzymano |Aplikacja sieci Web, do której wskazuje punkt końcowy, nie jest uruchomiona. Sprawdź ustawienia aplikacji sieci Web. Ten stan może również wystąpić, jeśli punkt końcowy jest typu zagnieżdżonego punktu końcowego, a profil podrzędny zostanie wyłączony lub nieaktywny. <br>Punkt końcowy ze stanem zatrzymanym nie jest monitorowany. Nie jest on uwzględniony w odpowiedziach DNS i nie odbiera ruchu. Wyjątek to jeśli wszystkie punkty końcowe mają obniżoną wydajność. W takim przypadku wszystkie są uznawane za zwracane w odpowiedzi na zapytanie.</br>|

Aby uzyskać szczegółowe informacje o sposobie obliczania stanu monitora punktu końcowego dla zagnieżdżonych punktów końcowych, zobacz [profile Traffic Manager zagnieżdżonych](traffic-manager-nested-profiles.md).

>[!NOTE]
> Stan monitorowania zatrzymanego punktu końcowego może wystąpić na App Service, jeśli aplikacja sieci Web nie działa w warstwie Standardowa lub wyższej. Aby uzyskać więcej informacji, zobacz [Traffic Manager integrację z App Service](../app-service/web-sites-traffic-manager.md).

### <a name="profile-monitor-status"></a>Stan monitora profilu

Stan monitora profilu jest kombinacją skonfigurowanego stanu profilu oraz wartości stanu monitorowania punktu końcowego dla wszystkich punktów końcowych. Możliwe wartości są opisane w poniższej tabeli:

| Stan profilu (zgodnie z konfiguracją) | Stan monitora punktu końcowego | Stan monitora profilu | Uwagi |
| --- | --- | --- | --- |
| Disabled |&lt;dowolny &gt; lub profil bez zdefiniowanych punktów końcowych. |Disabled |Profil został wyłączony. |
| Enabled (Włączony) |Stan co najmniej jednego punktu końcowego ma obniżoną wydajność. |Obniżona wydajność |Przejrzyj wartości stanu poszczególnych punktów końcowych, aby określić, które punkty końcowe wymagają dalszej uwagi. |
| Enabled (Włączony) |Stan co najmniej jednego punktu końcowego jest w trybie online. Żadne punkty końcowe nie mają stanu obniżonej wydajności. |Tryb online |Usługa akceptuje ruch. Nie są wymagane żadne dalsze działania. |
| Enabled (Włączony) |Stan co najmniej jednego punktu końcowego to CheckingEndpoint. Żadne punkty końcowe nie są w trybie online ani nie są w stanie obniżenia wydajności. |CheckingEndpoints |Ten stan przejścia występuje, gdy profil zostanie utworzony lub włączony. Kondycja punktu końcowego jest sprawdzana po raz pierwszy. |
| Enabled (Włączony) |Stany wszystkich punktów końcowych w profilu są wyłączone lub zatrzymane albo profil nie ma zdefiniowanych punktów końcowych. |Nieaktywne |Punkty końcowe nie są aktywne, ale profil jest nadal włączony. |

## <a name="endpoint-failover-and-recovery"></a>Tryb failover i odzyskiwanie punktu końcowego

Traffic Manager okresowo sprawdzać kondycję każdego punktu końcowego, łącznie z punktami końcowymi w złej kondycji. Traffic Manager wykrywa, kiedy punkt końcowy jest w dobrej kondycji i przywraca go z powrotem do obrotu.

Stan punktu końcowego jest nieprawidłowy, gdy wystąpi dowolne z następujących zdarzeń:

- Jeśli protokół monitorowania to HTTP lub HTTPS:
    - Odpowiedź innego typu niż 200 lub odpowiedź, która nie zawiera zakresu stanu określonego w ustawieniu **przedziały oczekiwanych zakresów kodu stanu** . (W tym inny kod 2xx lub przekierowanie 301/302).
- Jeśli protokół monitorowania to TCP: 
    - Odpowiedź inna niż ACK lub SYN-ACK zostaje odebrana w odpowiedzi na żądanie SYN wysłane przez Traffic Manager w celu podjęcia próby ustanowienia połączenia.
- Limit czasu. 
- Wszystkie inne problemy z połączeniem, które nie są dostępne w punkcie końcowym, są niedostępne.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z błędami, zobacz [Rozwiązywanie problemów ze stanem obniżonej wydajności na platformie Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Oś czasu na poniższej ilustracji zawiera szczegółowy opis procesu monitorowania Traffic Manager punkt końcowy o następujących ustawieniach:

* Protokół monitorowania to HTTP.
* Interwał sondowania wynosi 30 sekund.
* Liczba tolerowanych błędów wynosi 3.
* Wartość limitu czasu to 10 sekund.
* Czas wygaśnięcia systemu DNS wynosi 30 sekund.

![Traffic Manager trybu failover punktu końcowego i sekwencji powrotu po awarii](./media/traffic-manager-monitoring/timeline.png)

**Rysunek: przejście do trybu failover i sekwencji odzyskiwania punktu końcowego w usłudze Traffic Manager**

1. **Pobierz**. W każdym punkcie końcowym system monitorowania Traffic Manager wykonuje żądanie GET w ścieżce określonej w ustawieniach monitorowania.
2. **200 OK lub niestandardowy zakres kodu określony Traffic Manager ustawieniach monitorowania profilu**. System monitorujący oczekuje protokołu HTTP 200 OK lub kodu stanu w zakresie określonym w ustawieniach monitorowania do zwrócenia w ciągu 10 sekund. Po otrzymaniu tej odpowiedzi rozpoznaje ona, że usługa jest dostępna.
3. **30 sekund między sprawdzeniami**. Sprawdzanie kondycji punktu końcowego jest powtarzane co 30 sekund.
4. **Usługa jest niedostępna**. Usługa jest niedostępna. Traffic Manager nie będzie wiadomo do momentu następnego sprawdzenia kondycji.
5. **Próbuje uzyskać dostęp do ścieżki monitorowania**. System monitorowania wykonuje żądanie GET, ale nie otrzymuje odpowiedzi z upływem limitu czasu wynoszącego 10 sekund. Następnie próbuje jeszcze trzy razy, co 30 sekund. Po pomyślnym wykonaniu jednej z tych prób zostanie zresetowana liczba prób.
6. **Stan ustawiony na obniżony**. Po czwartym kolejnej awarii system monitorujący oznacza niedostępny stan punktu końcowego jako obniżony.
7. **Ruch jest przekierowywany do innych punktów końcowych**. Traffic Manager serwery nazw DNS są aktualizowane i Traffic Manager nie będą już zwracały punktu końcowego w odpowiedzi na zapytania DNS. Nowe połączenia są kierowane do innych, dostępnych punktów końcowych. Jednak poprzednie odpowiedzi DNS, które obejmują ten punkt końcowy, nadal mogą być buforowane przez cykliczne serwery DNS i klientów DNS. Klienci nadal używają punktu końcowego do momentu wygaśnięcia pamięci podręcznej DNS. Po wygaśnięciu pamięci podręcznej DNS klienci składają nowe zapytania DNS i są kierowani do różnych punktów końcowych. Czas trwania pamięci podręcznej jest kontrolowany przez ustawienie czasu wygaśnięcia w profilu Traffic Manager, na przykład 30 sekund.
8. **Sprawdzanie kondycji jest kontynuowane**. Traffic Manager kontynuuje sprawdzanie kondycji punktu końcowego, gdy ma stan obniżonej sprawności. Traffic Manager wykrywa, gdy punkt końcowy powróci do kondycji.
9. **Usługa wraca do trybu online**. Usługa zostanie udostępniona. Punkt końcowy utrzymuje stan obniżonej sprawności w Traffic Manager do momentu, gdy system monitorowania zakończy kolejne Sprawdzenie kondycji.
10. **Ruch do wznawiania usługi**. Traffic Manager wysyła żądanie GET i odbiera odpowiedź o stanie 200 OK. Usługa powróciła do stanu dobrej kondycji. Serwery nazw Traffic Manager są aktualizowane i zaczynają w odpowiedzi DNS nazwę DNS usługi. Ruch wraca do punktu końcowego jako buforowane odpowiedzi DNS, które zwracają inne punkty końcowe, tracą ważność, a w przypadku zakończenia istniejących połączeń z innymi punktami końcowymi.

    > [!NOTE]
    > Ponieważ Traffic Manager działa na poziomie systemu DNS, nie może mieć wpływu na istniejące połączenia z żadnym punktem końcowym. Gdy kieruje ruch między punktami końcowymi (zmieniono ustawienia profilu lub podczas przełączania do trybu failover lub powrotu po awarii), Traffic Manager kieruje nowe połączenia do dostępnych punktów końcowych. Jednak inne punkty końcowe mogą nadal odbierać ruch przez istniejące połączenia do momentu zakończenia tych sesji. Aby umożliwić wyopróżnianie ruchu z istniejących połączeń, aplikacje powinny ograniczyć czas trwania sesji używany z każdym punktem końcowym.

## <a name="traffic-routing-methods"></a>Metody routingu ruchu

Gdy punkt końcowy ma stan obniżonej wydajności, nie jest już zwracany w odpowiedzi na zapytania DNS. Zamiast tego należy wybrać i zwrócić alternatywny punkt końcowy. Metoda routingu ruchu skonfigurowana w profilu określa, w jaki sposób alternatywny punkt końcowy jest wybierany.

* **Priorytet**. Punkty końcowe tworzą listę z priorytetami. Pierwszy dostępny punkt końcowy na liście zawsze jest zwracany. Jeśli stan punktu końcowego ma obniżoną wydajność, zwracany jest następny dostępny punkt końcowy.
* **Ważone**. Wszystkie dostępne punkty końcowe są wybierane losowo na podstawie przypisanych wag i wag innych dostępnych punktów końcowych.
* **Wydajność**. Zwracany jest punkt końcowy znajdujący się najbliżej użytkownika końcowego. Jeśli ten punkt końcowy jest niedostępny, Traffic Manager przenosi ruch do punktów końcowych w następnym najbliższym regionie świadczenia usługi Azure. Można skonfigurować alternatywne plany trybu failover dla ruchu sieciowego — Routing przy użyciu [zagnieżdżonych profilów Traffic Manager](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geograficzny**. Zwracany jest punkt końcowy mapowany do lokalizacji geograficznej na podstawie adresu IP żądania zapytania. Jeśli ten punkt końcowy jest niedostępny, nie zostanie wybrany inny punkt końcowy do przełączenia w tryb failover, ponieważ lokalizacja geograficzna może zostać zmapowana tylko do jednego punktu końcowego w profilu. (Szczegółowe informacje znajdują się w [sekcji często zadawane pytania](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Najlepszym rozwiązaniem w przypadku korzystania z routingu geograficznego jest użycie przez klientów zagnieżdżonych profilów Traffic Manager z więcej niż jednym punktem końcowym jako punktami końcowymi profilu.
* Z **wieloma wartościami** Zwrócono wiele punktów końcowych mapowanych na adresy IPv4/IPv6. Po odebraniu zapytania dla tego profilu odpowiednie punkty końcowe są zwracane na podstawie **maksymalnej liczby rekordów w** podanej wartości odpowiedzi. Domyślna liczba odpowiedzi to dwa punkty końcowe.
* **Podsieć** Zwracany jest punkt końcowy mapowany na zestaw zakresów adresów IP. Gdy żądanie jest odbierane z tego adresu IP, zwracany jest punkt końcowy, który jest mapowany dla tego adresu IP. 

Aby uzyskać więcej informacji, zobacz [Traffic Manager metod routingu ruchu sieciowego](traffic-manager-routing-methods.md).

> [!NOTE]
> Jeden wyjątek dla normalnego zachowania routingu ruchu występuje, gdy wszystkie kwalifikujące się punkty końcowe mają stan obniżonej sprawności. Traffic Manager podejmuje próbę "najlepsze wysiłki" i *reaguje tak, jakby wszystkie punkty końcowe stanu obniżonej kondycji rzeczywiście były w stanie online*. Takie zachowanie jest preferowane z alternatywą, która nie zwraca żadnego punktu końcowego w odpowiedzi DNS. Wyłączone lub zatrzymane punkty końcowe nie są monitorowane, dlatego nie są uważane za kwalifikujące się do ruchu.
>
> Ten stan jest często spowodowany przez nieprawidłową konfigurację usługi, na przykład:
>
> * Lista kontroli dostępu [ACL] blokująca Traffic Manager kontrole kondycji.
> * Nieprawidłowa konfiguracja portu lub protokołu monitorowania w profilu Menedżera ruchu.
>
> Wynika to z tego, że jeśli Traffic Manager Sprawdzanie kondycji nie jest prawidłowo skonfigurowane, może się pojawić na podstawie routingu ruchu, *ponieważ Traffic Manager działa* prawidłowo. Jednak w tym przypadku nie można przełączyć do trybu failover punktu końcowego, który ma wpływ na ogólną dostępność aplikacji. Ważne jest, aby sprawdzić, czy profil zawiera stan online, a nie stan obniżonej sprawności. Stan online wskazuje, że sprawdzanie kondycji Traffic Manager działa zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z błędami sprawdzania kondycji, zobacz [Rozwiązywanie problemów z obniżoną wydajnością w usłudze Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Często zadawane pytania

* [Czy Traffic Manager jest odporny na awarie regionu platformy Azure?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [Jak wybór lokalizacji grupy zasobów ma wpływ na Traffic Manager?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Jak mogę określić aktualną kondycję każdego punktu końcowego?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [Czy mogę monitorować punkty końcowe HTTPS?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [Czy podczas dodawania punktu końcowego używam adresu IP lub nazwy DNS?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Jakiego typu adresy IP można używać podczas dodawania punktu końcowego?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Czy można używać różnych typów adresowania punktów końcowych w ramach jednego profilu?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Co się stanie, gdy typ rekordu zapytania przychodzącego różni się od typu rekordu skojarzonego z typem adresowania punktów końcowych?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Czy mogę użyć profilu z adresami końcowymi IPv4/IPv6 w profilu zagnieżdżonym?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Punkt końcowy aplikacji sieci Web został zatrzymany w profilu Traffic Manager, ale nie odbieram żadnego ruchu nawet po ponownym uruchomieniu. Jak można to naprawić?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Czy mogę używać Traffic Manager, nawet jeśli moja aplikacja nie obsługuje protokołu HTTP lub HTTPS?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Jakie konkretne odpowiedzi są wymagane z punktu końcowego w przypadku korzystania z funkcji monitorowania protokołu TCP?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Jak szybko Traffic Manager przenieść moich użytkowników poza punkt końcowy w złej kondycji?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Jak można określić różne ustawienia monitorowania dla różnych punktów końcowych w profilu?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Jak mogę przypisać nagłówki HTTP do punktów końcowych Traffic Managerych kontroli kondycji?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Z jakiego nagłówka hosta korzystają Sprawdzanie kondycji punktów końcowych?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [Jakie są adresy IP, z których pochodzą kontrole kondycji?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Ile testów kondycji w punkcie końcowym można oczekiwać od Traffic Manager?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Jak mogę otrzymywać powiadomienia, jeśli jeden z punktów końcowych ulegnie awarii?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, [jak działa Traffic Manager](traffic-manager-how-it-works.md)

Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez Traffic Manager

Dowiedz się, jak [utworzyć profil Traffic Manager](traffic-manager-manage-profiles.md)

[Rozwiązywanie problemów ze stanem obniżonej wydajności](traffic-manager-troubleshooting-degraded.md) w punkcie końcowym Traffic Manager