---
title: Wykrywanie inteligentne — anomalie wydajności | Microsoft Docs
description: Application Insights przeprowadza inteligentnej analizy telemetrii aplikacji i ostrzega o potencjalnych problemach. Ta funkcja nie wymaga instalacji.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: a055a323001e0291d3cb5c1716e640b3c8b21dbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573738"
---
# <a name="smart-detection---performance-anomalies"></a>Wykrywanie inteligentne — anomalie wydajności

[Application Insights](./app-insights-overview.md) automatycznie analizuje wydajność aplikacji sieci Web i może ostrzec o potencjalnych problemach. Może się to zdarzyć z powodu odebrania jednego z naszych powiadomień dotyczących wykrywania inteligentnego.

Ta funkcja nie wymaga żadnych specjalnych ustawień, innych niż Konfigurowanie aplikacji do Application Insights dla [obsługiwanego języka](./platforms.md). Jest ona aktywna, gdy aplikacja generuje wystarczającą ilość danych telemetrycznych.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Kiedy otrzymuję powiadomienie o wykryciu Smart Detect?

Application Insights wykrył, że wydajność aplikacji uległa obniżeniu na jeden z następujących sposobów:

* **Obniżenie czasu odpowiedzi** — aplikacja rozpoczęła reagowanie na żądania wolniej niż używane do. Ta zmiana może być na przykład szybka, ponieważ w ostatnim wdrożeniu wystąpił regresja. Lub może to być stopniowe, może to być spowodowane przeciekiem pamięci. 
* **Pogorszenie czasu trwania zależności** — aplikacja wykonuje wywołania do interfejsu API REST, bazy danych lub innej zależności. Zależność reaguje wolniej niż jej użycie.
* **Wzorzec powolnej wydajności** — prawdopodobnie aplikacja ma problem z wydajnością, który ma wpływ tylko na niektóre żądania. Na przykład strony są ładowane wolniej w jednym typie przeglądarki niż inne. lub żądania są udostępniane wolniej z jednego określonego serwera. Obecnie nasze algorytmy wyglądają na czasy ładowania stron, czasy odpowiedzi na żądania i czasy odpowiedzi dla zależności.  

Inteligentne wykrywanie wymaga co najmniej 8 dni telemetrii w woluminie, aby można było ustalić linię bazową normalnej wydajności. W związku z tym po uruchomieniu aplikacji przez ten okres każdy znaczący problem spowoduje powiadomienie.


## <a name="does-my-app-definitely-have-a-problem"></a>Czy moja aplikacja ma w nieskończoność problem?

Nie, powiadomienie nie oznacza, że w aplikacji występuje problem. To po prostu sugestia dotyczącą czegoś, czemu możesz chcieć przyjrzeć się bliżej.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?

Powiadomienia obejmują informacje diagnostyczne. Oto przykład:


![Oto przykład wykrywania pogorszenia czasu odpowiedzi serwera](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Klasyfikacja**. Powiadomienie przedstawia liczbę użytkowników lub liczbę operacji, których dotyczy. Może to pomóc w przypisaniu priorytetu do problemu.
2. **Zakres**. Czy problem wpływa na cały ruch, czy tylko niektóre strony? Czy jest ograniczone do określonych przeglądarek lub lokalizacji? Te informacje można uzyskać na podstawie powiadomienia.
3. **Diagnozowanie**. Często informacje diagnostyczne w powiadomieniu zasugerują charakter problemu. Na przykład jeśli czas odpowiedzi jest wolniejszy, gdy liczba żądań jest wysoka, sugeruje serwer lub zależności, które są przeciążone. 

    W przeciwnym razie Otwórz blok wydajność w Application Insights. W tym miejscu znajdują się dane [profilera](profiler.md) . Jeśli zostały zgłoszone wyjątki, można również wypróbować [debuger migawek](./snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Powiadomienia inteligentnego wykrywania są domyślnie włączone i wysyłane do tych, którzy mają [kontrolę](../../role-based-access-control/built-in-roles.md#monitoring-reader) dostępu [współautora i monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor) do subskrypcji, w której znajduje się zasób Application Insights. Aby to zmienić, kliknij pozycję **Konfiguruj** w powiadomieniu e-mail lub Otwórz ustawienia inteligentnego wykrywania w Application Insights. 
  
  ![Ustawienia inteligentnego wykrywania](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Aby zrezygnować z otrzymywania powiadomień e-mail, możesz użyć linku **anulowania subskrypcji** w wiadomości e-mail dotyczącej wykrywania inteligentnego.

Wiadomości e-mail dotyczące anomalii dotyczących wydajności wykrywania inteligentnego są ograniczone do jednej wiadomości e-mail dziennie na Application Insights zasób. Wiadomość e-mail zostanie wysłana tylko wtedy, gdy w tym dniu wykryjesz co najmniej jeden nowy problem. Nie zostaną powtórzone żadne komunikaty. 

## <a name="faq"></a>Często zadawane pytania

* *Tak więc personel firmy Microsoft szuka moich danych?*
  * Nie. Usługa jest całkowicie automatyczna. Tylko otrzymujesz powiadomienia. Twoje dane są [prywatne](./data-retention-privacy.md).
* *Czy analizujesz wszystkie dane zbierane przez Application Insights?*
  * Jeszcze nie. Obecnie analizujemy czas odpowiedzi na żądanie, czas odpowiedzi zależności i czas ładowania strony. Analiza dodatkowych metryk znajduje się w naszej zaległości.

* Dla jakich typów aplikacji jest to działanie?
  * Te degradacje są wykrywane w każdej aplikacji, która generuje odpowiednie dane telemetryczne. Jeśli zainstalowano Application Insights w aplikacji sieci Web, żądania i zależności są automatycznie śledzone. Jednak w usługach zaplecza lub innych aplikacjach, jeśli wstawiono wywołania [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) lub [TrackDependency](./api-custom-events-metrics.md#trackdependency), Inteligentne wykrywanie będzie działało w ten sam sposób.

* *Czy mogę utworzyć własne reguły wykrywania anomalii lub dostosować istniejące reguły?*

  * Jeszcze nie, ale możesz:
    * [Skonfiguruj alerty](../alerts/alerts-log.md) informujące o przekroczeniu progu przez metrykę.
    * [Wyeksportuj dane telemetryczne](./export-telemetry.md) do [bazy danych](./code-sample-export-sql-stream-analytics.md) lub [do Power BI](./export-power-bi.md), gdzie możesz ją przeanalizować.
* *Jak często przeprowadzana jest analiza?*

  * Przeprowadzamy analizę codziennie na danych telemetrycznych od poprzedniego dnia (pełny dzień w strefie czasowej UTC).
* *Czy te zastępują [alerty metryk](../alerts/alerts-log.md)?*
  * Nie.  Nie zobowiązujemy się do wykrywania wszystkich zachowań, które mogą być rozważane nietypowe.


* *Czy jeśli nie chcę niczego w odpowiedzi na powiadomienie, otrzymam przypomnienie?*
  * Nie. otrzymasz komunikat o każdym problemie tylko raz. Jeśli problem będzie się powtarzać, zostanie zaktualizowany w bloku Smart Detection.
* *Wiadomość e-mail została utracona. Gdzie mogę znaleźć powiadomienia w portalu?*
  * W Application Insights przegląd aplikacji kliknij kafelek **wykrywanie inteligentne** . Będzie można znaleźć wszystkie powiadomienia z powrotem do 90 dni.

## <a name="how-can-i-improve-performance"></a>Jak można poprawić wydajność?
Powolne i nieudane odpowiedzi to jeden z największych frustrations dla użytkowników witryny sieci Web, jak wiadomo, że masz doświadczenie. Dlatego ważne jest, aby rozwiązać problemy.

### <a name="triage"></a>Klasyfikacja
Po pierwsze czy ma to znaczenie? Jeśli strona jest zawsze powolnie załadowana, ale tylko 1% użytkowników witryny musi być na nim przyjrzeć, być może masz ważniejsze rzeczy. Z drugiej strony, jeśli tylko 1% użytkowników otworzy go, ale zgłasza wyjątki za każdym razem, co może być cennym badaniem.

Użyj instrukcji wpływającej (wpływ na użytkowników lub% ruchu) jako przewodnika ogólnego, ale pamiętaj, że nie jest to cały wątek. Zbierz inne dowody do potwierdzenia.

Należy wziąć pod uwagę parametry problemu. Jeśli jest zależna od lokalizacji geograficznej, skonfiguruj [testy dostępności](./monitor-web-app-availability.md) włącznie z tym regionem: w tym obszarze mogą znajdować się problemy z siecią.

### <a name="diagnose-slow-page-loads"></a>Diagnozuj wolne ładowanie stron
Gdzie jest problem? Czy serwer jest powolnie reagować, czy jest to strona bardzo długa, czy w przeglądarce trzeba wykonać dużą pracę, aby ją wyświetlić?

Otwórz blok metryki przeglądarki. Segmentowy widok czasu ładowania strony w przeglądarce pokazuje, gdzie odbywa się czas. 

* Jeśli **czas żądania wysłania** jest wysoki, serwer reaguje powoli lub żądanie jest wpisem z dużą ilością danych. Sprawdź [metryki wydajności](./performance-counters.md) , aby zbadać czasy odpowiedzi.
* Skonfiguruj [Śledzenie zależności](./asp-net-dependencies.md) , aby zobaczyć, czy spowolnienie jest spowodowane usługami zewnętrznymi czy bazą danych.
* Jeśli **otrzymujesz odpowiedź** , Twoja strona i jej zależne części — JavaScript, CSS, obrazy i tak dalej (ale dane ładowane asynchronicznie) są długie. Skonfiguruj [Test dostępności](./monitor-web-app-availability.md)i pamiętaj, aby ustawić opcję ładowania części zależnych. Po otrzymaniu pewnych wyników Otwórz szczegóły wyniku i rozwiń go, aby zobaczyć czasy ładowania różnych plików.
* Wysoka **godzina przetwarzania przez klienta** sugeruje, że skrypty działają wolno. Jeśli powód nie jest oczywisty, rozważ dodanie pewnego kodu chronometrażu i wysłanie czasu w wywołaniach trackMetric.

### <a name="improve-slow-pages"></a>Popraw wolne strony
W sieci Web jest dostępna pełna informacja o ulepszaniu odpowiedzi serwera i czasów ładowania stron, dlatego nie będziemy próbować powtórzyć ich w tym miejscu. Oto kilka porad, które prawdopodobnie już wiesz, po prostu zapoznaj się z nim:

* Wolne ładowanie z powodu dużych plików: Załaduj skrypty i inne części asynchronicznie. Użyj grupowania skryptów. Podziel stronę główną na widżety, które ładują dane oddzielnie. Nie wysyłaj zwykłego pliku HTML dla długich tabel: Użyj skryptu, aby zażądać danych jako JSON lub innego formatu kompaktowego, a następnie wypełnij tabelę. Istnieją wspaniałe platformy, które pomogą Ci. (Pociąga to za sobą również duże skrypty).
* Wolne zależności serwera: należy wziąć pod uwagę lokalizację geograficzną składników. Jeśli na przykład używasz platformy Azure, upewnij się, że serwer sieci Web i baza danych znajdują się w tym samym regionie. Czy zapytania są pobierają więcej informacji niż są one potrzebne? Czy chcesz buforować lub wsadowo pomoc?
* Problemy z pojemnością: Sprawdź metryki serwera dotyczące czasów odpowiedzi i liczby żądań. Jeśli czas odpowiedzi jest nieproporcjonalny z szczytami w licznikach żądań, prawdopodobnie serwery są rozciągane.


## <a name="server-response-time-degradation"></a>Obniżenie czasu odpowiedzi serwera

Powiadomienie o obniżeniu czasu odpowiedzi informuje o tym, że:

* Czas odpowiedzi w porównaniu do normalnego czasu odpowiedzi dla tej operacji.
* Liczba użytkowników, których to dotyczy.
* Średni czas odpowiedzi i 90-krotny czas odpowiedzi dla tej operacji w dniu wykrywania i 7 dni przed. 
* Liczba żądań operacji w dniu wykrycia i 7 dni przed.
* Korelacja między obniżeniem w tej operacji a obniżeniem wydajności w powiązanych zależnościach. 
* Linki ułatwiające zdiagnozowanie problemu.
  * Ślady profilera ułatwiające wyświetlenie czasu trwania operacji (link jest dostępny, jeśli przykłady śledzenia profilera zostały zebrane dla tej operacji w okresie wykrywania). 
  * Raporty dotyczące wydajności w Eksploratorze metryk, w którym można wydzielić i indeksować zakres czasu/filtry dla tej operacji.
  * Wyszukaj to wywołanie, aby wyświetlić określone właściwości wywołania.
  * Raporty o błędach — w przypadku zliczenia > 1 oznacza to, że wystąpiły błędy w tej operacji, które mogły przyczynić się do obniżenia wydajności.

## <a name="dependency-duration-degradation"></a>Pogorszenie czasu trwania zależności

Nowoczesne aplikacje bardziej i bardziej przyjmują podejście do projektowania mikrousług, które w wielu przypadkach prowadzi do dużej niezawodności usług zewnętrznych. Na przykład, jeśli aplikacja korzysta z pewnej platformy danych, a nawet jeśli utworzysz własną usługę bot, prawdopodobnie przekażesz go do pewnego dostawcy usług poznawczej, aby umożliwić botówom współdziałanie w bardziej ludzki sposób i niektórym usługom magazynu danych Bot do ściągania odpowiedzi.  

Przykładowe powiadomienie o obniżeniu zależności:

![Oto przykład wykrywania pogorszenia czasu trwania zależności](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Zwróć uwagę, że:

* Czas trwania w porównaniu do normalnego czasu odpowiedzi dla tej operacji
* Liczba użytkowników, których to dotyczy
* Średni czas trwania i 90-krotny czas trwania dla tej zależności w dniu wykrywania i 7 dni przed
* Liczba wywołań zależności w dniu wykrycia i 7 dni przed
* Linki ułatwiające zdiagnozowanie problemu
  * Raporty dotyczące wydajności w Eksploratorze metryk dla tej zależności
  * Wyszukaj te wywołania zależności, aby wyświetlić właściwości wywołań
  * Raporty o błędach — w przypadku liczby > 1 oznacza to, że w okresie wykrywania Wystąpił błąd wywołania zależności, które mogły przyczynić się do obniżenia czasu trwania. 
  * Otwórz analizę z zapytaniami, które obliczają ten czas trwania zależności i liczbę  

## <a name="smart-detection-of-slow-performing-patterns"></a>Inteligentne wykrywanie wolnych wzorców wykonywania 

Application Insights odnajduje problemy z wydajnością, które mogą mieć wpływ tylko na niektóre części użytkowników, lub mieć wpływ tylko na użytkowników w niektórych przypadkach. Na przykład powiadomienia o ładowaniu stron są wolniejsze w jednym typie przeglądarki niż w przypadku innych typów przeglądarek, lub jeśli żądania są przekazywane wolniej od określonego serwera. Może również wykrywać problemy związane z połączeniami właściwości, takimi jak wolne ładowanie stron w jednym obszarze geograficznym w przypadku klientów korzystających z określonego systemu operacyjnego.  

Anomalie takie jak te są bardzo trudne do wykrycia, sprawdzając dane, ale są one bardziej typowe, niż można myśleć. Często są one używane tylko wtedy, gdy klient zauważa. Przez ten czas jest zbyt późno: zaatakowany użytkownicy już przełączają się do Twoich konkurentów.

Obecnie nasze algorytmy wyglądają na czasy ładowania stron, żądania czasów odpowiedzi na serwerze i czasy odpowiedzi dla zależności.  

Nie musisz ustawiać żadnych progów ani konfigurować reguł. Algorytmy uczenia maszynowego i wyszukiwania danych są używane do wykrywania nietypowych wzorców.

![Na stronie alertu e-mail kliknij link, aby otworzyć raport diagnostyczny na platformie Azure](./media/proactive-performance-diagnostics/03.png)

* **Gdy** pokazuje czas wykrycia problemu.
* **Opis:**

  * Wykryto problem;
  * Charakterystyki zestawu zdarzeń, które znaleźliśmy, wykryto zachowanie problemu.
* Tabela porównuje źle wykonywany zestaw z średnim zachowaniem wszystkich innych zdarzeń.

Kliknij linki, aby otworzyć Eksploratora metryk i wyszukać odpowiednie raporty, filtrować według czasu i właściwości powolnego zestawu.

Zmodyfikuj zakres czasu i filtry, aby poznać dane telemetryczne.

## <a name="next-steps"></a>Następne kroki
Te narzędzia diagnostyczne ułatwiają inspekcję danych telemetrycznych z aplikacji:

* [Profiler](profiler.md) 
* [Debuger migawek](./snapshot-debugger.md)
* [Analiza](../logs/log-analytics-tutorial.md)
* [Inteligentna diagnostyka analityczna](../logs/log-query-overview.md)

Inteligentne wykrywanie są całkowicie automatyczne. Ale być może chcesz skonfigurować więcej alertów?

* [Ręcznie skonfigurowane alerty metryk](../alerts/alerts-log.md)
* [Testy dostępności sieci Web](./monitor-web-app-availability.md)