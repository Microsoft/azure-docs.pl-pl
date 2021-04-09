---
title: Monitorowanie wydajności aplikacji sieci Web — Application Insights platformy Azure
description: Jak Application Insights pasuje do cyklu devOps
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: bc897cc0259894964e0b6164219b1e5459e824b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579569"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Szczegółowa diagnostyka dla aplikacji internetowych i usług dzięki usłudze Application Insights
## <a name="why-do-i-need-application-insights"></a>Dlaczego potrzebuję Application Insights?
Application Insights monitoruje uruchomioną aplikację sieci Web. Zawiera informacje o błędach i problemach z wydajnością oraz ułatwia analizowanie sposobu korzystania z aplikacji przez klientów. Działa w przypadku aplikacji uruchamianych na wielu platformach (ASP.NET, Java EE, Node.js,...) i jest hostowana w chmurze lub lokalnie. 

![Aspekty złożoności dostarczania aplikacji sieci Web](./media/devops/010.png)

Jest to niezbędne do monitorowania nowoczesnej aplikacji w trakcie jej działania. Co najważniejsze, chcesz wykryć błędy przed większością klientów. Warto również wykrywać i rozwiązywać problemy z wydajnością, które w nieprzerwaniu mogą spowalniać pracę lub powodować pewne niedogodności z użytkownikami. A gdy system spełnia Twoje oczekiwania, chcesz wiedzieć, co robią Użytkownicy: czy korzysta z najnowszej funkcji? Czy są one pomyślne?

Nowoczesne aplikacje sieci Web są opracowywane w cyklu ciągłego dostarczania: wydanie nowej funkcji lub ulepszeń; Obserwuj, jak dobrze działa dla użytkowników; Zaplanuj następny przyrost rozwoju na podstawie tej wiedzy. Kluczową częścią tego cyklu jest faza obserwacji. Application Insights udostępnia narzędzia do monitorowania aplikacji sieci Web pod kątem wydajności i użycia.

Najważniejszym aspektem tego procesu jest Diagnostyka i diagnostyka. Jeśli aplikacja się nie powiedzie, firma zostanie utracona. Główną rolą środowiska monitorowania jest w związku z tym wykrycie niezawodnych błędów, natychmiastowe powiadomienie i zaprezentowanie informacji niezbędnych do zdiagnozowania problemu. Jest to dokładnie Application Insights.

### <a name="where-do-bugs-come-from"></a>Skąd pochodzą usterki?
Błędy w systemach sieci Web zwykle powstają w przypadku problemów z konfiguracją lub nieprawidłowych interakcji między ich wieloma składnikami. W związku z tym pierwsze zadanie podczas działania zdarzenia na żywo ma na celu zidentyfikowanie locus problemu: który składnik lub relacja jest przyczyną?

Niektóre z tych stanów w kolorze szarym mogą zapamiętać prostsze ERA, w których program komputerowy działał na jednym komputerze. Deweloperzy przetestują ją dokładnie przed wysłaniem. po jej wysłaniu, rzadko zobaczysz lub postanowisz o tym ponownie. Użytkownicy będą musieli dysponować niektórymi usterkami przez wiele lat. 

Rzeczy są teraz bardzo inne. Aplikacja ma mnóstwo na różnych urządzeniach i może być trudne do zagwarantowania dokładnego zachowania na każdym z nich. Hostowanie aplikacji w chmurze oznacza, że usterki można poprawić szybko, ale oznacza to również ciągłą konkurencję i oczekiwanie na nowe funkcje w częstych odstępach czasu. 

W tych warunkach jedynym sposobem, aby zachować kontrolę nad liczbą usterek, jest automatyczne testowanie jednostkowe. Ręczne ponowne przetestowanie wszystkich wszystkich dostaw może być niemożliwe. Test jednostkowy jest teraz częścią commonplace procesu kompilacji. Narzędzia takie jak pomoc Xamarin Test Cloud, zapewniając automatyczne testowanie interfejsu użytkownika w wielu wersjach przeglądarki. Te systemy testowania umożliwiają nam nadzieję, że liczba błędów znalezionych w aplikacji może być ograniczona do minimum.

Typowe aplikacje sieci Web mają wiele składników na żywo. Oprócz klienta programu (w przeglądarce lub aplikacji urządzenia) i serwera sieci Web, prawdopodobnie jest istotne przetwarzanie zaplecza. Być może zaplecze jest potok komponentów lub luźna Kolekcja elementów współpracy. Wiele z nich nie będzie w Twoim formancie — są to usługi zewnętrzne, z których zależy.

W takich konfiguracjach może być trudne i nieekonomiczne do testowania w przypadku każdego możliwego trybu awarii, innego niż w przypadku samego systemu na żywo. 

### <a name="questions-"></a>Pytania...
Niektóre pytania zadawane podczas opracowywania systemu internetowego:

* Czy moja aplikacja uległa awarii? 
* Co dokładnie zaszło? — Jeśli żądanie zakończyło się niepowodzeniem, chcę wiedzieć, jak to się stało. Potrzebujemy śladu zdarzeń...
* Czy moja aplikacja jest wystarczająco szybko dostępna? Jak długo trwa reagowanie na typowe żądania?
* Czy serwer może obsłużyć obciążenie? Kiedy rośnie częstotliwość żądań, czy czas odpowiedzi jest stały?
* Jak są moje zależności — interfejsy API REST, bazy danych i inne składniki, które wywołuje moja aplikacja. W szczególności jeśli system działa wolno, czy jest moim składnikiem, czy mogę otrzymywać wolne odpowiedzi od kogoś innego?
* Czy moja aplikacja jest w górę lub w dół? Czy widzisz je na całym świecie? Powiadom mnie, jeśli zatrzyma....
* Co to jest główna przyczyna? Czy wystąpił błąd w składniku lub zależności? Czy jest to problem z komunikacją?
* Ilu użytkowników dotyczy problem? Jeśli mam problemy, które należy rozwiązać, co jest najważniejszym elementem?

## <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
![Podstawowy przepływ pracy Application Insights](./media/devops/020.png)

1. Application Insights instrumentację aplikacji i wysyła do niej telemetrię, gdy aplikacja jest uruchomiona. Do aplikacji można skompilować zestaw SDK Application Insights lub można zastosować instrumentację w czasie wykonywania. Dawna Metoda jest bardziej elastyczna, ponieważ możesz dodać własną telemetrię do zwykłych modułów.
2. Dane telemetryczne są wysyłane do portalu Application Insights, w którym są przechowywane i przetwarzane. (Chociaż Application Insights jest hostowana w Microsoft Azure, może monitorować wszystkie aplikacje sieci Web — nie tylko aplikacje platformy Azure).
3. Dane telemetryczne są prezentowane w formie wykresów i tabel zdarzeń.

Istnieją dwa główne typy danych telemetrycznych: wystąpienia zagregowane i nieprzetworzone. 

* Dane wystąpienia obejmują na przykład raport żądania, który został odebrany przez aplikację internetową. Szczegóły żądania można znaleźć i sprawdzić za pomocą narzędzia Search w portalu Application Insights. Wystąpienie będzie zawierać dane, takie jak czas odpowiedzi aplikacji na żądanie, a także żądany adres URL, przybliżona lokalizacja klienta i inne dane.
* Zagregowane dane obejmują liczbę zdarzeń na jednostkę czasu, dzięki czemu można porównać szybkość żądań z czasem odpowiedzi. Zawiera również średnie metryki, takie jak czasy odpowiedzi na żądania.

Główne kategorie danych to:

* Żądania do aplikacji (zazwyczaj żądania HTTP), z danymi na adres URL, czas odpowiedzi i powodzenie lub niepowodzenie.
* Zależności — wywołania REST i SQL wykonywane przez aplikację, również z identyfikatorami URI, czasy odpowiedzi i sukcesem
* Wyjątki, w tym ślady stosu.
* Dane widoku strony, które pochodzą z przeglądarek użytkowników.
* Takie metryki jak liczniki wydajności, a także metryki, które można napisać samodzielnie. 
* Zdarzenia niestandardowe, których można użyć do śledzenia zdarzeń firmy
* Ślady dzienników używane do debugowania.

## <a name="case-study-real-madrid-fc"></a>Analiza przypadku: Real Madryt F.C.
Usługa sieci Web [rzeczywistego klubu](https://www.realmadrid.com/) w witrynie Madryt, która obsługuje około 450 000 000 wentylatorów na całym świecie. Wentylatory uzyskują dostęp do tych danych za pomocą przeglądarek sieci Web i aplikacji mobilnych klubu. Wentylatory nie mogą zaistnieć tylko bilety, ale również uzyskiwać dostęp do informacji i klipów wideo dotyczących wyników, graczy i nadchodzących gier. Umożliwiają one wyszukiwanie filtrów, takich jak liczba zamierzonych celów. Istnieją także linki do mediów społecznościowych. Środowisko użytkownika jest wysoce spersonalizowane i zaprojektowano jako dwukierunkową komunikację z wentylatorami.

Rozwiązanie [to system usług i aplikacji na Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Skalowalność to kluczowy wymóg: ruch jest zmienny i może dotrzeć do bardzo dużych woluminów podczas i wokół dopasowań.

W przypadku rzeczywistego Madryta należy koniecznie monitorować wydajność systemu. Usługa Azure Application Insights zapewnia kompleksowy wgląd w system, zapewniając niezawodny i wysoki poziom usług. 

Klub zawiera również szczegółowe informacje o jego wentylatorach: w których są (tylko 3% w Hiszpanii), jakie są interesy uczestników, wyniki historyczne i nadchodzące gry oraz jak reagują na wyniki.

Większość tych danych telemetrii jest automatycznie zbieranych bez dodanych kodów, które upraszczają rozwiązanie i zmniejszają złożoność operacyjną.  W przypadku rzeczywistego Madryta w każdym miesiącu Application Insights się z 3 800 000 000 punktów telemetrii.

Real Madryt używa modułu Power BI, aby wyświetlić dane telemetryczne.

![Widok Power BI Application Insights telemetrii](./media/devops/080.png)

## <a name="smart-detection"></a>Inteligentne wykrywanie
[Proaktywna Diagnostyka](./proactive-diagnostics.md) to najnowsza funkcja. Bez żadnej specjalnej konfiguracji przez użytkownika Application Insights automatycznie wykrywać i ostrzegać o nietypowym zwiększeniu szybkości niepowodzeń w aplikacji. Jest to wystarczająco proste, aby ignorować tło sporadycznych błędów, a także zwiększać się proporcjonalnie do wzrostu żądań. Jeśli na przykład wystąpi awaria w jednej z usług, z której korzystasz, lub jeśli nowo wdrożona kompilacja nie działa prawidłowo, zobaczysz ją zaraz po wyświetleniu wiadomości e-mail. (I istnieją elementy webhook, aby można było wyzwolić inne aplikacje).

Innym aspektem tej funkcji jest wykonywanie codziennej analizy danych telemetrycznych, szukając nietypowych wzorców wydajności, które trudno wykryć. Na przykład może to zmniejszyć wydajność skojarzoną z określonym obszarem geograficznym lub z określoną wersją przeglądarki.

W obu przypadkach alert nie tylko informuje o znalezionych objawach, ale również udostępnia dane, które są potrzebne do zdiagnozowania problemu, na przykład odpowiednie raporty dotyczące wyjątków.

![Wiadomość e-mail z aktywnej diagnostyki](./media/devops/030.png)

Samtec klienta: "podczas ostatniej funkcji uruchomienie produkcyjne znaleziono nieskalowanej bazy danych, która zwiększa limity zasobów i powoduje przekroczenie limitów czasu. Alerty dotyczące usługi Active Detection zostały zrealizowane w miarę segregowania problemu i niemal w czasie rzeczywistym. Ten alert połączony z alertami na platformie Azure pomaga nam prawie natychmiast rozwiązać ten problem. Łączny czas przestoju < 10 minut ".

## <a name="live-metrics-stream"></a>Transmisja strumieniowa metryk na żywo
Wdrożenie najnowszej kompilacji może być doświadczeniem. Jeśli wystąpią jakieś problemy, warto wiedzieć o nich od razu, aby w razie potrzeby można było wykonać kopię zapasową. Live Metrics Stream zapewnia kluczowe metryki z opóźnieniem około jednej sekundy.

![Metryki na żywo](./media/devops/0040.png)

Umożliwia natychmiastowe sprawdzenie przykładu wszelkich błędów lub wyjątków.

![Zdarzenia błędów na żywo](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Mapa aplikacji
Mapa aplikacji automatycznie odnajduje topologię aplikacji, na podstawie jej informacji o wydajności, co pozwala na łatwe identyfikowanie wąskich gardeł wydajności i problemów przepływów w środowisku rozproszonym. Umożliwia odnajdywanie zależności aplikacji w usługach platformy Azure. Problem można klasyfikacja, opisując, czy jest powiązany z kodem, czy z pojedynczym miejscem przechodzenia do szczegółów związanych z diagnostyką. Na przykład aplikacja może się nie powieść z powodu obniżenia wydajności w warstwie SQL. Za pomocą mapy aplikacji możesz ją natychmiast zobaczyć, a następnie przejść do szczegółów środowiska SQL Index Advisor lub zapytania szczegółowego.

![Mapa aplikacji](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Analiza Application Insights
Dzięki [analizie](../logs/log-query-overview.md)można pisać dowolne zapytania w zaawansowanym języku podobnym do bazy danych SQL.  Diagnozowanie całego stosu aplikacji jest proste, ponieważ różne perspektywy są połączone i można zadać odpowiednie pytania, aby skorelować wydajność usługi z użyciem metryk firmy i środowiska klienta. 

Możesz wykonywać zapytania dotyczące wszystkich wystąpień telemetrii i danych nieprzetworzonych metryk przechowywanych w portalu. Język zawiera filtrowanie, łączenie, agregację i inne operacje. Można obliczyć pola i przeprowadzać analizę statystyczną. Istnieją zarówno wizualizacje tabelaryczne, jak i graficzne.

![Wykres kwerend i wyników analizy](./media/devops/0025.png)

Na przykład można łatwo:

* Poznanie danych wydajności żądań aplikacji przez warstwy klientów, aby zrozumieć ich środowisko pracy.
* Wyszukiwanie określonych kodów błędów lub niestandardowych nazw zdarzeń podczas badania witryny na żywo.
* Przejdź do szczegółów użycia aplikacji dla konkretnych klientów, aby zrozumieć, jak są uzyskiwane i wdrażane funkcje.
* Śledzenie sesji i czasów odpowiedzi dla określonych użytkowników w celu umożliwienia pracownikom pomocy technicznej i działu operacyjnego zapewnienia błyskawicznej obsługi klienta.
* Należy określić często używane funkcje aplikacji do odpowiedzi na pytania dotyczące priorytetyzacji funkcji.

DNN klienta: "Application Insights podał nam brak części równania, aby można było łączyć, sortować, wysyłać zapytania i filtrować dane zgodnie z wymaganiami. Umożliwienie naszym zespołowi używania własnych ingenuity i środowiska do znajdowania danych za pomocą zaawansowanego języka zapytań umożliwiło nam znalezienie szczegółowych informacji i rozwiązywanie problemów, które nie były jeszcze znane. Wiele interesujących odpowiedzi pochodzi z pytań, które zaczynają się od *"zastanawiam się, jeśli...".*

## <a name="development-tools-integration"></a>Integracja narzędzi programistycznych
### <a name="configuring-application-insights"></a>Konfigurowanie Application Insights
Programy Visual Studio i zaćmienie mają narzędzia do konfigurowania poprawnych pakietów SDK dla opracowywanego projektu. Istnieje polecenie menu umożliwiające dodanie Application Insights.

Jeśli zamierzasz używać struktury rejestrowania śledzenia, takiej jak narzędzia log4n, NLog lub system. Diagnostics. Trace, uzyskasz możliwość wysyłania dzienników do Application Insights wraz z innymi danymi telemetrycznymi, aby można było łatwo skorelować ślady z żądaniami, wywołaniami zależności i wyjątkami.

### <a name="search-telemetry-in-visual-studio"></a>Wyszukaj dane telemetryczne w programie Visual Studio
Podczas tworzenia i debugowania funkcji można wyświetlać i przeszukiwać dane telemetryczne bezpośrednio w programie Visual Studio, korzystając z tych samych funkcji wyszukiwania jak w portalu internetowym.

A kiedy Application Insights rejestruje wyjątek, można wyświetlić punkt danych w programie Visual Studio i przejść bezpośrednio do odpowiedniego kodu.

![Wyszukiwanie w programie Visual Studio](./media/devops/060.png)

Podczas debugowania można zachować dane telemetryczne na komputerze deweloperskim, wyświetlając go w programie Visual Studio, ale bez wysyłania go do portalu. Ta opcja lokalna pozwala uniknąć mieszania debugowania z danymi telemetrycznymi produkcyjnymi.

### <a name="work-items"></a>Elementy robocze
Gdy zostanie zgłoszony alert, Application Insights może automatycznie utworzyć element roboczy w systemie śledzenia pracy.

## <a name="but-what-about"></a>Ale co z...?
* [Prywatność i magazyn](./data-retention-privacy.md) — Twoje dane telemetryczne są przechowywane na bezpiecznych serwerach platformy Azure.
* Wydajność — wpływ jest bardzo niski. Dane telemetryczne są przetwarzane wsadowo.
* [Cennik](./pricing.md) — możesz zacząć korzystać bezpłatnie i kontynuować pracę w trakcie korzystania z niskiego wolumenu.


## <a name="video"></a>Wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Następne kroki
Wprowadzenie do Application Insights jest proste. Główne opcje są następujące:

* [Serwery IIS](./monitor-performance-live-website-now.md), a także dla [Azure App Service](./app-insights-overview.md).
* Instrumentacja projektu podczas opracowywania. Można to zrobić dla aplikacji [ASP.NET](./asp-net.md) lub [Java](./java-get-started.md) , a także [Node.js](./nodejs.md) i hosta [innych typów](./platforms.md). 
* Instrumentacja [dowolnej strony sieci Web](./javascript.md) przez dodanie krótkiego fragmentu kodu.

