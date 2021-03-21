---
title: Raporty platformy Azure z przodu Standard/Premium (wersja zapoznawcza)
description: W tym artykule wyjaśniono, w jaki sposób raportowanie działa w ramach zewnętrznych drzwi platformy Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101100268"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Raporty platformy Azure z przodu Standard/Premium (wersja zapoznawcza)

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Raporty z usługi Azure preports Standard/Premium umożliwiają wbudowaną i zaawansowaną pracę z założeniami, w jaki sposób działa usługa Azure front-drzwi wraz ze skojarzonymi metrykami zapory aplikacji sieci Web. Możesz również skorzystać z dzienników dostępu, aby przeprowadzić dalsze Rozwiązywanie problemów i debugowanie. Raporty analizy z przodu platformy Azure obejmują raporty o ruchu i raporty o zabezpieczeniach.

| Raporty | Szczegóły |
|---------|---------|
| Przegląd kluczowych metryk | Przedstawia ogólne dane wysyłane z krawędzi czołowych drzwi platformy Azure do klientów<br/>-Szczytowa przepustowość<br/>-Żądania <br/>-Współczynnik trafień w pamięci podręcznej<br/> — Łączne opóźnienie<br/>-5XX współczynnik błędów |
| Ruch przychodzący przez domenę | -Zawiera przegląd wszystkich domen w profilu<br/>-Podział danych przesłanych z AFD Edge do klienta<br/>— Łączna liczba żądań<br/>-3XX/4XX/5XX kod odpowiedzi według domen |
| Ruch według lokalizacji | -Wyświetla widok mapy żądań i użycia według najważniejszych krajów<br/>Widok trendu najważniejszych krajów |
| Użycie | -Wyświetla transfer danych z zewnętrznej krawędzi drzwi platformy Azure do klientów<br/>-Transfer danych poza początkową do AFD Edge<br/>-Przepustowość od AFD Edge do klientów<br/>-Przepustowość od początku do AFD krawędzi<br/>-Żądania<br/>— Łączne opóźnienie<br/>-Trend liczby żądań według kodu stanu HTTP |
| Buforowanie | -Pokazuje Współczynnik trafień pamięci podręcznej według liczby żądań<br/>-Widok trendu żądań trafień i chybień |
| Górny adres URL | -Pokazuje liczbę żądań <br/>— Transferowane dane <br/>-Współczynnik trafień w pamięci podręcznej <br/>-Dystrybucja kodów stanu odpowiedzi dla najbardziej żądanych zasobów 50. |
| Najpopularniejszy odwołujący | -Pokazuje liczbę żądań <br/>— Transferowane dane <br/>-Współczynnik trafień w pamięci podręcznej <br/>-Dystrybucja kodów stanu odpowiedzi dla głównych odsprzedawców 50, które generują ruch. |
| Najlepszy agent użytkownika | -Pokazuje liczbę żądań <br/>— Transferowane dane <br/>-Współczynnik trafień w pamięci podręcznej <br/>-Dystrybucja kodów stanu odpowiedzi dla najważniejszych agentów użytkowników 50, którzy zostali zaproszeni do zażądania zawartości. |

| Raporty dotyczące zabezpieczeń | Szczegóły |
|---------|---------|
| Przegląd kluczowych metryk | -Pokazuje dopasowane reguły WAF<br/>-Dopasowane reguły OWASP<br/>-Dopasowane reguły BOT<br/>-Dopasowane reguły niestandardowe |
| Metryki według wymiarów | -Podział dopasowanych reguł WAF według akcji<br/>-Wykres pierścieniowy zdarzeń według typu i zdarzenia zestawu reguł według grupy reguł<br/>— Przerwij listę najważniejszych zdarzeń według identyfikatora reguły, kraju, adresu IP, adresu URL i agenta użytkownika  |

> [!NOTE]
> Raporty zabezpieczeń są dostępne tylko w przypadku platformy Azure z pakietem Enterprise Premium.

Większość raportów jest oparta na dziennikach dostępu i jest oferowana bezpłatnie klientom w ramach zewnętrznych drzwi platformy Azure. Aby wyświetlić te raporty, klient nie musi włączać dzienników dostępu ani wykonywać żadnych czynności konfiguracyjnych. Raporty są dostępne za poorednictwem portalu i interfejsu API. Pobieranie woluminów CSV jest również obsługiwane. 

Raporty obsługują wszystkie wybrane zakresy dat z poprzednich 90 dni. Punkty danych co 5 minut, co godzinę lub codziennie, na podstawie wybranego zakresu dat. Zwykle można wyświetlać dane z opóźnieniem w ciągu godziny i czasami z opóźnieniem do kilku godzin. 

## <a name="access-reports-using-the-azure-portal"></a>Uzyskiwanie dostępu do raportów przy użyciu Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz profil Standard/Premium na platformie Azure.

1. W okienku nawigacji wybierz pozycję **raporty lub zabezpieczenia** w obszarze *Analiza*.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Zrzut ekranu przedstawiający stronę docelową raportów":::

1. Istnieje siedem kart dla różnych wymiarów, wybierz wymiar zainteresowania.

   * Ruch przychodzący przez domenę
   * Użycie 
   * Ruch według lokalizacji
   * Pamięć podręczna
   * Górny adres URL
   * Najpopularniejszy odwołujący
   * Najlepszy agent użytkownika

1. Po wybraniu wymiaru można wybrać różne filtry.
  
    1. **Pokaż dane dla** — wybierz zakres dat, dla którego chcesz wyświetlić ruch według domeny. Dostępne są następujące zakresy:
        
        * Ostatnie 24 godziny
        * Ostatnie 7 dni
        * Ostatnie 30 dni
        * Ostatnie 90 dni
        * Ten miesiąc
        * Ostatni miesiąc
        * Data niestandardowa

         Domyślnie dane są wyświetlane w ciągu ostatnich siedmiu dni. W przypadku kart z wykresami liniowymi stopień szczegółowości danych przechodzi do zakresów dat wybranych jako zachowanie domyślne. 
    
        * 5 minut — jeden punkt danych co 5 minut w przypadku zakresów dat mniejszych lub równych 24 godzin.
        * Według godziny — jedno dane co godzinę dla zakresów dat z przedziału od 24 do 30 dni
        * Według dnia — jedno dane dziennie dla zakresów dat, które są większe niż 30 dni.

        Możesz zawsze używać agregacji, aby zmienić domyślny stopień szczegółowości agregacji. Uwaga: 5 minut nie działa z zakresem danych dłuższym niż 14 dni. 

    1. **Lokalizacja** — wybierz jedną lub wiele lokalizacji klienta według kraju. Kraje są pogrupowane w sześć regionów: Ameryka Północna, Azji, Europie, Afryki, Oceanii i Ameryki Południowej. Zapoznaj się z [mapowaniem regionu/kraju](https://en.wikipedia.org/wiki/Subregion). Domyślnie są wybierane wszystkie kraje.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Zrzut ekranu przedstawiający raporty dotyczące wymiaru lokalizacji.":::
   
    1. **Protokół** — wybierz opcję http lub https, aby wyświetlić dane o ruchu.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Zrzut ekranu przedstawiający raporty o wymiarze protokołu.":::
    
    1. **Domeny** — wybierz jedną lub wiele punktów końcowych lub domen niestandardowych. Domyślnie zaznaczone są wszystkie punkty końcowe i domeny niestandardowe. 
    
        * Jeśli usuniesz punkt końcowy lub domenę niestandardową w jednym profilu, a następnie utworzysz ponownie ten sam punkt końcowy lub domenę w innym profilu. Punkt końcowy będzie traktowany jako drugi punkt końcowy.  
        * Jeśli przeglądasz raporty według domeny niestandardowej — usuwasz jedną domenę niestandardową i powiążesz ją z innym punktem końcowym. Będą one traktowane jako jedna domena niestandardowa. Jeśli widok według punktu końcowego — będą one traktowane jako osobne elementy. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Zrzut ekranu przedstawiający raporty dla wymiaru domeny.":::

1. Jeśli chcesz wyeksportować dane do pliku CSV, wybierz link *Pobierz wolumin CSV* na wybranej karcie.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Zrzut ekranu przedstawiający plik CSV pobierania raportów.":::

### <a name="key-metrics-for-all-reports"></a>Kluczowe metryki dla wszystkich raportów

| Metric | Opis |
|---------|---------|
| Przesłane dane | Przedstawia dane przesyłane z AFD Edge punktów obecności do klienta dla wybranego przedziału czasu, lokalizacji klienta, domen i protokołów. |
| Przepustowość szczytowa | Użycie szczytowej przepustowości w bitach na sekundę od punktów obecności zewnętrznych drzwi platformy Azure do klienta dla wybranego przedziału czasu, lokalizacji klienta, domen i protokołów. | 
| Łączna liczba żądań | Liczba żądań, które AFD krawędź pop odpowiedziała na klienta dla wybranego przedziału czasu, lokalizacji klienta, domen i protokołów. |
| Współczynnik trafień w pamięci podręcznej | Wartość procentowa wszystkich żądań buforowanych w pamięci podręcznej, dla których AFD jest obsługiwana zawartość z pamięci podręcznej krawędzi dla wybranego przedziału czasu, lokalizacji klienta, domen i protokołów. |
| Współczynnik błędów 5XX | Procent żądań, dla których kod stanu HTTP dla klienta był 5XX dla wybranego przedziału czasu, lokalizacji klienta, domen i protokołów. |
| Łączne opóźnienie | Średnie opóźnienie wszystkich żądań dla wybranego przedziału czasu, lokalizacje klientów, domeny i protokoły. Opóźnienie dla każdego żądania jest mierzone jako łączny czas, w którym żądanie klienta zostaje odebrane przez program Azure front-drzwi do momentu wysłania ostatniego bajtu odpowiedzi z usług frontonu platformy Azure do klienta. |

## <a name="traffic-by-domain"></a>Ruch przychodzący przez domenę

Ruch przez domenę zapewnia widok siatki wszystkich domen w ramach tego profilu platformy Azure. Ten raport umożliwia wyświetlenie: 
* Żądania
* Dane przesyłane z usług frontonu platformy Azure do klienta
* Żądania z kodem stanu (3XX, 4Xx i 5XX) dla każdej domeny

Domeny obejmują punkt końcowy i domeny niestandardowe, jak wyjaśniono w sesji uzyskiwania dostępu do raportu.  

Możesz przejść do innych kart, aby dokładniej zbadać lub wyświetlić dziennik dostępu, aby uzyskać więcej informacji, jeśli znajdziesz metryki poniżej oczekiwań. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Zrzut ekranu strony docelowej dla raportów":::


## <a name="usage"></a>Użycie

Ten raport przedstawia trendy kodu stanu ruchu i odpowiedzi według różnych wymiarów, w tym:

* Dane przesyłane z brzegu do klienta i od początku do krawędzi na wykresie liniowym. 

* Dane przesyłane z programu Edge do klienta według protokołu na wykresie liniowym. 

* Liczba żądań od brzegu do klientów na wykresie liniowym.  

* Liczba żądań od brzegu do klientów przez protokół, HTTP i HTTPS, na wykresie liniowym. 

* Przepustowość od brzegu do klienta na wykresie liniowym. 

* Całkowite opóźnienie, które mierzy łączny czas odpowiedzi od żądania klienta odebranego przez tylne drzwi do momentu wysłania ostatniego bajtu z przodu do klienta.

* Liczba żądań od brzegu do klientów według kodu stanu HTTP, na wykresie liniowym. Każde żądanie generuje kod stanu HTTP. Kod stanu HTTP pojawia się w HTTPStatusCode w dzienniku nieprzetworzonym. Kod stanu opisuje sposób obsługi żądania przez usługę CDN Edge. Na przykład kod stanu 2xx wskazuje, że żądanie zostało pomyślnie obsłużone dla klienta. Kod stanu 4xx wskazuje, że wystąpił błąd. Aby uzyskać więcej informacji na temat kodów stanu HTTP, zobacz listę kodów stanu HTTP. 

* Liczba żądań od brzegu do klientów według kodu stanu HTTP. Procent żądań według kodu stanu HTTP między wszystkimi żądaniami w siatce. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Zrzut ekranu przedstawiający raporty według użycia" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Ruch według lokalizacji

Ten raport przedstawia pierwsze 50 lokalizacji według kraju osób odwiedzających, którzy uzyskują dostęp do zasobów. Raport zawiera również informacje o podziale metryk według kraju i zawiera ogólny widok krajów, w których generowany jest najwięcej ruchu. Na koniec widzisz, który kraj ma większy współczynnik trafień pamięci podręcznej lub kody błędów 4XX/5XX.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Zrzut ekranu przedstawiający raporty według lokalizacji" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

W raportach znajdują się następujące elementy:

* Ogólnoświatowy widok mapy najpopularniejszych krajów 50 przez dane przesłane lub wybrane żądania.
* Widok trendu wykresu liniowego z pięciu najważniejszych krajów według danych, które zostały przesłane i wybrane żądania. 
* Siatka najważniejszych krajów z odpowiednimi danymi, które zostały przeniesione z AFD do klientów, dane przetransferowane% wszystkich krajów, żądań, żądań% między wszystkimi krajami, współczynnik trafień pamięci podręcznej, kod odpowiedzi 4XX i kod odpowiedzi 5XX.

## <a name="caching"></a>Buforowanie

Raporty buforowanie zawiera widok wykresu trafień/chybień w pamięci podręcznej oraz współczynnik trafień pamięci podręcznej na podstawie żądań. Te kluczowe metryki wyjaśniają, jak Usługa CDN jest buforowana zawartość od momentu najszybszej wydajności z trafień pamięci podręcznej Szybkość dostarczania danych można zoptymalizować przez zminimalizowanie chybień w pamięci podręcznej. Ten raport zawiera:

* Trend liczby trafień i chybień w pamięci podręcznej (wykres liniowy).

* Współczynnik trafień pamięci podręcznej na wykresie liniowym.

Trafienia w pamięci podręcznej/Chybienia opisują trafienia w pamięci podręcznej numeru żądania oraz Chybienia w pamięci podręcznej żądań klientów

* Trafienia: żądania klientów, które są obsługiwane bezpośrednio z serwerów brzegowych programu Azure CDN. Odwołuje się do tych żądań, których wartości CacheStatus w dziennikach nieprzetworzonych są TRAFIeń, PARTIAL_HIT lub zdalnych TRAFIeń. 

* Chybień: żądania klientów, które są obsługiwane przez Azure CDN serwery graniczne pobierają zawartość z lokalizacji źródłowej. Odwołuje się do tych żądań, których wartości pola CacheStatus w dziennikach nieprzetworzonych są POMINIĘTe. 

**Współczynnik trafień pamięci podręcznej** opisuje wartość procentową buforowanych żądań, które są obsługiwane bezpośrednio z krawędzi. Wzór trafień pamięci podręcznej to: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` . 

Ten raport uwzględnia scenariusze buforowania i żądania, które spełniają następujące wymagania, są uwzględniane w obliczeniach. 

* Żądana zawartość została zbuforowana w punkcie POP najbliżej osoby żądającej lub osłony pochodzenia. 

* Częściowo buforowana zawartość dla fragmentu obiektu.

Wyklucza to wszystkie z następujących przypadków: 

* Żądania, które zostały odrzucone z powodu zestawu reguł.

* Żądania zawierające zestaw zgodnych reguł, które zostały ustawione na wyłączone pamięci podręcznej. 

* Żądania, które są blokowane przez WAF. 

* Nagłówki odpowiedzi pochodzenia wskazują, że nie powinny być buforowane. Na przykład funkcja Cache-Control: Private, Cache-Control: Brak pamięci podręcznej lub pragma: nagłówki Brak pamięci podręcznej uniemożliwią buforowanie elementu zawartości. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Zrzut ekranu przedstawiający raporty dotyczące buforowania.":::

## <a name="top-urls"></a>Najważniejsze adresy URL

Najważniejsze adresy URL umożliwiają wyświetlanie ilości ruchu ponoszonego przez konkretny punkt końcowy lub domenę niestandardową. W ciągu ostatnich 90 dni zobaczysz dane dotyczące najbardziej żądanych zasobów 50 w dowolnym okresie. Zostaną wyświetlone Popularne adresy URL z poniższymi wartościami. Użytkownik może sortować adresy URL według liczby żądań, procentu żądania, przetransferowanych danych i% przetransferowanych danych. Wszystkie metryki są agregowane według godziny i mogą się różnić w zależności od wybranego przedziału czasu. Adres URL odnosi się do wartości RequestUri w dzienniku dostępu. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Zrzut ekranu przedstawiający raporty dla górnego adresu URL.":::

* Adres URL, odnosi się do pełnej ścieżki żądanego elementu zawartości w formacie `http(s)://contoso.com/index.html/images/example.jpg` . 
* Liczba żądań. 
* % Żądania łącznej liczby żądań obsłużonych przez platformę Azure front-drzwi. 
* Przesyłane dane. 
* Procent przesłanych danych. 
* Współczynnik trafień pamięci podręcznej%
* Żądania z kodem odpowiedzi jako 4XX
* Żądania z kodem odpowiedzi jako 5XX

> [!NOTE]
> Najważniejsze adresy URL mogą ulec zmianie z upływem czasu i aby uzyskać dokładną listę najważniejszych adresów URL 50, usługa Azure Front-drzwiczki zlicza wszystkie żądania adresów URL według godziny i utrzymuje bieżącą sumę w ciągu dnia. Adresy URL u dołu adresów URL 500 mogą wzrosnąć lub opuścić listę w ciągu dnia, więc łączna liczba tych adresów URL zbliża się.  
>
> Górne adresy URL 50 mogą wzrosnąć i znajdować się na liście, ale rzadko znikają z listy, dzięki czemu liczby dla najpopularniejszych adresów URL są zwykle niezawodne. Gdy adres URL zostanie wysunięty poza listę i ponownie przekroczy dzień, liczba żądań w okresie, gdy ich brak z listy jest szacowana na podstawie numeru żądania adresu URL, który pojawia się w tym okresie.  
>
> Ta sama logika dotyczy najwyższego agenta użytkownika. 

## <a name="top-referrers"></a>Najpopularniejsze odwołujące

Najpopularniejsze odsprzedawców umożliwiają klientom wyświetlanie górnego odwołującego 50, który pochodzi z większości żądań do zawartości w konkretnym punkcie końcowym lub w domenie niestandardowej. Dane można wyświetlić w dowolnym okresie w ciągu ostatnich 90 dni. Odwołujący wskazuje adres URL, z którego zostało wygenerowane żądanie. Odwołujący mogą pochodzić z aparatu wyszukiwania lub innych witryn sieci Web. Jeśli użytkownik wpisze adres URL (na przykład http (s)://contoso.com/index.html) bezpośrednio w wierszu adresu przeglądarki, odwołujący dla żądanego elementu to "Empty". Najpopularniejsze raporty odwołujące zawierają następujące wartości. Można sortować według liczby żądań, procentu żądania, przetransferowanych danych i% przetransferowanych danych. Wszystkie metryki są agregowane według godziny i mogą się różnić w zależności od wybranego przedziału czasu. 

* Odwołujący, wartość obiektu odwołującego w dziennikach nieprzetworzonych 
* Liczba żądań 
* Żądanie% łącznej liczby żądań obsłużonych przez Azure CDN w wybranym okresie. 
* Przesłane dane 
* Przetransferowane dane% 
* Współczynnik trafień pamięci podręcznej%
* Żądania z kodem odpowiedzi jako 4XX
* Żądania z kodem odpowiedzi jako 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Zrzut ekranu przedstawiający raporty dla górnego odłożonego.":::

## <a name="top-user-agent"></a>Najlepszy agent użytkownika

Ten raport umożliwia wyświetlenie widoku graficznego i statystyki najważniejszych agentów użytkownika 50, które były używane do żądania zawartości. Na przykład
* Mozilla/5.0 (Windows NT 10,0; — 
* AppleWebKit/537.36 (KHTML, like Gecko) 
* Chrome/86.0.4240.75 
* Przeglądarka Safari/537.36.  

Siatka przedstawia liczbę żądań, procent żądań, przesłanych danych i przesłanych danych, współczynnik trafień pamięci podręcznej (%), żądania z kodem odpowiedzi jako 4XX oraz żądania z kodem odpowiedzi jako 5XX. Agent użytkownika odwołuje się do wartości UserAgent w dziennikach dostępu.

## <a name="security-report"></a>Raport o zabezpieczeniach

Ten raport pozwala uzyskać widok graficzny i statystyka wzorców WAF według różnych wymiarów.

| Wymiary | Opis |
|---------|---------|
| Przegląd metryk — dopasowane reguły WAF | Żądania zgodne z niestandardowymi regułami WAF, zarządzanymi regułami WAF i menedżerem bot. |
| Informacje o metrykach — zablokowane żądania | Procent żądań, które są blokowane przez reguły WAF między wszystkimi żądaniami, które pasują do reguł WAF. |
| Przegląd metryk — dopasowane reguły zarządzane | Cztery wykresy liniowe — trend dla żądań, które są blokowane, Rejestruj, Zezwalaj i przekierowania. |
| Przegląd metryk — dopasowana reguła niestandardowa | Żądania zgodne z niestandardowymi regułami WAF. |
| Przegląd metryk — dopasowana reguła bot | Żądania zgodne z bot Manager. |
| Trend żądania WAF według akcji | Cztery wykresy liniowe — trend dla żądań, które są blokowane, Rejestruj, Zezwalaj i przekierowania. |
| Zdarzenia według typu reguły | Wykres pierścieniowy rozkładu żądań WAF według typu reguły, np. bot, reguł niestandardowych i zarządzanych reguł. |
| Zdarzenia według grupy reguł | Wykres pierścieniowy rozkładu żądań WAF według grupy reguł. |
| Żądania według akcji | Tabela żądań według akcji, w kolejności malejącej. |
| Żądania według najważniejszych identyfikatorów reguł | Tabela żądań według najpopularniejszych identyfikatorów reguł 50 w kolejności malejącej. |
| Żądania według najważniejszych krajów |  Tabela żądań według pierwszych 50 krajów w kolejności malejącej. |
| Żądania według najpopularniejszych adresów IP klienta |  Tabela żądań według najpopularniejszych adresów IP 50 w kolejności malejącej. |
| Żądania według górnego adresu URL żądania |  Tabela żądań według najpopularniejszych adresów URL 50 w kolejności malejącej. |
| Żądania według największych nazw hostów | Tabela żądań według najwyższej nazwy hosta 50 w kolejności malejącej. |
| Żądania według najlepszych agentów użytkowników | Tabela żądań według pierwszych 50 agentów użytkowników w kolejności malejącej. |

## <a name="cvs-format"></a>Format CVS

Pliki CSV można pobrać dla różnych kart w raportach. W tej sekcji opisano wartości w każdym pliku CSV.

### <a name="general-information-about-the-cvs-report"></a>Ogólne informacje na temat raportu CVS

Każdy raport CSV zawiera pewne ogólne informacje, a informacje są dostępne we wszystkich plikach CSV. ze zmiennymi w oparciu o pobrany raport. 


| Wartość | Opis |
|---------|---------|
| Raport | Nazwa raportu. | 
| Domains | Lista punktów końcowych lub domen niestandardowych raportu. |
| StartDateUTC | Początek zakresu dat, dla którego Wygenerowano raport w uniwersalnym czasie koordynowanym (UTC) |
| EndDateUTC | Koniec zakresu dat, dla którego Wygenerowano raport w uniwersalnym czasie koordynowanym (UTC) |
| GeneratedTimeUTC | Data i godzina wygenerowania raportu w uniwersalnym czasie koordynowanym (UTC) |
| Lokalizacja | Lista krajów, w których pochodzą żądania klienta. Domyślnie wartość jest równa. Nie dotyczy raportu zabezpieczeń. |
| Protokół | Protokół żądania, HTTP lub HTTPs. Nie dotyczy najpopularniejszych adresów URL i ruchu przez agenta użytkownika w raportach i raportu o zabezpieczeniach. |
| Agregacja | Stopień szczegółowości agregacji danych w każdym wierszu, co 5 minut, co godzinę i codziennie. Nie dotyczy ruchu przez domenę, górny adres URL i ruch przez agenta użytkownika w raportach i zabezpieczeniach. |

### <a name="data-in-traffic-by-domain"></a>Dane w ruchu według domeny

* Domena 
* Łączna liczba żądań 
* Współczynnik trafień w pamięci podręcznej 
* Żądania 3XX 
* Żądania 4XX 
* Żądania 5XX 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Dane w ruchu według lokalizacji

* Lokalizacja
* TotalRequests
* Żądając
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Dane w użyciu

W tym pliku CSV istnieją trzy raporty. Jeden dla protokołu HTTP, jeden dla protokołu HTTPS i jeden dla kodu stanu HTTP. 

Raporty dla protokołów HTTP i HTTPs mają ten sam zestaw danych. 

* Godzina 
* Protokół 
* Przetransferowane (bajty) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Raport dotyczący kodu stanu HTTP. 

* Godzina 
* Przetransferowane (bajty) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Dane w pamięci podręcznej 

* Godzina
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Dane w górnym adresie URL 

* Adres URL 
* TotalRequests 
* Żądając 
* Przetransferowane (bajty) 
* Procent przetransferowanych 

### <a name="data-in-user-agent"></a>Dane w agencie użytkownika 

* UserAgent 
* TotalRequests 
* Żądając 
* Przetransferowane (bajty) 
* Procent przetransferowanych 

### <a name="security-report"></a>Raport o zabezpieczeniach 

Istnieje siedem tabel z tymi samymi polami.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

Siedem tabel dotyczy czasu, identyfikatora reguły, kraju, adresu IP, adresu URL, nazwy hosta, agenta użytkownika. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [metrykach monitorowania w czasie rzeczywistym z platformy Azure w warstwie Standardowa/Premium](how-to-monitor-metrics.md).
