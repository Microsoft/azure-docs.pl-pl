---
title: Inteligentne wykrywanie - anomalie wydajności | Dokumenty firmy Microsoft
description: Usługa Application Insights przeprowadza inteligentną analizę telemetrii aplikacji i ostrzega przed potencjalnymi problemami. Ta funkcja nie wymaga konfiguracji.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: 6c5b19c7e03993ef973cd708ed7a6fe89feb01a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687692"
---
# <a name="smart-detection---performance-anomalies"></a>Inteligentne wykrywanie — anomalie wydajności

[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) automatycznie analizuje wydajność aplikacji sieci web i może ostrzegać o potencjalnych problemach. Być może czytasz to, ponieważ otrzymałeś jedno z naszych powiadomień o wykrywaniu z inteligentnego.

Ta funkcja nie wymaga specjalnej konfiguracji, innej niż konfigurowanie aplikacji do wglądu w aplikacje (na [ASP.NET,](../../azure-monitor/app/asp-net.md) [Java](../../azure-monitor/app/java-get-started.md)lub [Node.js](../../azure-monitor/app/nodejs.md)i w [kodzie strony sieci Web](../../azure-monitor/app/javascript.md)). Jest aktywny, gdy aplikacja generuje wystarczającą ilość danych telemetrycznych.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Kiedy otrzymam powiadomienie o wykrywaniu z inteligentnego wykrywania?

Usługa Application Insights wykryła, że wydajność aplikacji uległa pogorszeniu na jeden z następujących sposobów:

* **Degradacja czasu odpowiedzi** — aplikacja zaczęła odpowiadać na żądania wolniej niż kiedyś. Zmiana może być szybkie, na przykład, ponieważ nie było regresji w najnowszym wdrożeniu. Lub może to być stopniowe, może spowodowane przez wyciek pamięci. 
* **Degradacja czasu trwania zależności** — aplikacja wywołuje interfejs API REST, bazę danych lub inną zależność. Zależność odpowiada wolniej niż kiedyś.
* **Powolny wzorzec wydajności** — aplikacja wydaje się mieć problem z wydajnością, który ma wpływ tylko na niektóre żądania. Na przykład strony ładują się wolniej w jednym typie przeglądarki niż inne; lub żądania są obsługiwane wolniej z jednego konkretnego serwera. Obecnie nasze algorytmy patrzą na czasy wczytywanie strony, czasy odpowiedzi żądania i czasy odpowiedzi zależności.  

Inteligentne wykrywanie wymaga co najmniej 8 dni danych telemetrycznych na woluminie wykonalnym w celu ustanowienia linii bazowej normalnej wydajności. Tak więc po uruchomieniu aplikacji przez ten okres, każdy istotny problem spowoduje powiadomienie.


## <a name="does-my-app-definitely-have-a-problem"></a>Czy moja aplikacja na pewno ma problem?

Nie, powiadomienie nie oznacza, że aplikacja na pewno ma problem. To po prostu sugestia dotyczącą czegoś, czemu możesz chcieć przyjrzeć się bliżej.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?

Powiadomienia zawierają informacje diagnostyczne. Oto przykład:


![Oto przykład wykrywania degradacji czasu reakcji serwera](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Segregowanie**. Powiadomienie pokazuje, ilu użytkowników lub ile operacji dotyczy. Może to pomóc w przypisaniu priorytetu problemu.
2. **Zakres**. Czy problem dotyczy całego ruchu, czy tylko niektórych stron? Czy jest to ograniczone do określonych przeglądarek lub lokalizacji? Informacje te można uzyskać z powiadomienia.
3. **Diagnozowanie**. Często informacje diagnostyczne w powiadomieniu sugerują charakter problemu. Na przykład jeśli czas odpowiedzi spowalnia, gdy szybkość żądania jest wysoka, co sugeruje, że serwer lub zależności są przeciążone. 

    W przeciwnym razie otwórz bloku wydajności w usłudze Application Insights. Znajdziesz tam dane [Profilera.](profiler.md) Jeśli zostaną wyświetleń wyjątki, można również wypróbować [debuger migawki](../../azure-monitor/app/snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Powiadomienia wykrywania inteligentnego są domyślnie włączone i wysyłane do tych, którzy mają dostęp [czytnika monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) i [współautora monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) do subskrypcji, w której znajduje się zasób usługi Application Insights. Aby to zmienić, kliknij przycisk **Konfiguruj** w powiadomieniu e-mail lub otwórz ustawienia inteligentnego wykrywania w usłudze Application Insights. 
  
  ![Ustawienia inteligentnego wykrywania](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Możesz użyć linku **rezygnacji** z subskrypcji w wiadomości e-mail z inteligentnym wykrywaniem, aby przestać otrzymywać powiadomienia e-mail.

Wiadomości e-mail dotyczące anomalii wydajności inteligentnych wykrywania są ograniczone do jednej wiadomości e-mail dziennie na zasób usługi Application Insights. Wiadomość e-mail zostanie wysłana tylko wtedy, gdy w tym dniu wykryto co najmniej jeden nowy problem. Nie dostaniesz powtórzeń żadnej wiadomości. 

## <a name="faq"></a>Często zadawane pytania

* *Tak, pracownicy firmy Microsoft spojrzeć na moje dane?*
  * Nie. Usługa jest całkowicie automatyczna. Tylko Ty otrzymujesz powiadomienia. Twoje dane są [prywatne](../../azure-monitor/app/data-retention-privacy.md).
* *Czy analizujesz wszystkie dane zebrane przez usługa Application Insights?*
  * Obecnie nie. Obecnie analizujemy czas odpowiedzi żądania, czas odpowiedzi zależności i czas ładowania strony. Analiza dodatkowych metryk jest na naszych zaległości oczekuje.

* Do jakich typów aplikacji to działa?
  * Te degradacje są wykrywane w dowolnej aplikacji, która generuje odpowiednie dane telemetryczne. Jeśli aplikacja została zainstalowana w aplikacji sieci Web, żądania i zależności są automatycznie śledzone. Ale w usługach wewnętrznej bazy danych lub innych aplikacjach, jeśli wstawiłeś wywołania [trackrequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) lub [TrackDependency,](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)inteligentne wykrywanie będzie działać w ten sam sposób.

* *Czy mogę utworzyć własne reguły wykrywania anomalii lub dostosować istniejące reguły?*

  * Jeszcze nie, ale możesz:
    * [Skonfiguruj alerty informujące](../../azure-monitor/app/alerts.md) o przekroczeniu progu przez metrykę.
    * [Eksportuj dane telemetryczne](../../azure-monitor/app/export-telemetry.md) do [bazy danych](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) lub do usługi [Power BI,](../../azure-monitor/app/export-power-bi.md )w której można je analizować samodzielnie.
* *Jak często przeprowadzana jest analiza?*

  * Możemy uruchomić analizę codziennie na dane telemetryczne z poprzedniego dnia (cały dzień w strefie czasowej UTC).
* *Czy to zastępuje [alerty metryczne?](../../azure-monitor/app/alerts.md)*
  * Nie.  Nie zobowiązujemy się do wykrywania każdego zachowania, które można uznać za nieprawidłowe.


* *Jeśli nie zrobię nic w odpowiedzi na powiadomienie, czy otrzymam przypomnienie?*
  * Nie, otrzymasz komunikat o każdym problemie tylko raz. Jeśli problem będzie się powtarzał, zostanie on zaktualizowany w bloku źródła inteligentnego wykrywania.
* *Zgubiłem wiadomość e-mail. Gdzie mogę znaleźć powiadomienia w portalu?*
  * W przeglądzie aplikacji w aplikacji **Smart Detection** aplikacja zawiera szczegółowe informacje o aplikacji. Tam możesz znaleźć wszystkie powiadomienia do 90 dni wstecz.

## <a name="how-can-i-improve-performance"></a>Jak mogę poprawić wydajność?
Powolne i nieudane odpowiedzi są jedną z największych frustracji użytkowników witryny, jak wiesz z własnego doświadczenia. Tak więc, ważne jest, aby rozwiązać problemy.

### <a name="triage"></a>Segregowanie
Po pierwsze, czy to ma znaczenie? Jeśli strona jest zawsze powolna, aby załadować, ale tylko 1% użytkowników witryny kiedykolwiek trzeba spojrzeć na to, może masz ważniejsze rzeczy do przemyślenia. Z drugiej strony, jeśli tylko 1% użytkowników go otworzyć, ale zgłasza wyjątki za każdym razem, że może być warte zbadania.

Użyj instrukcji wpływu (użytkowników, których dotyczy problem lub % ruchu) jako ogólnego przewodnika, ale pamiętaj, że nie jest to cała historia. Zbierz inne dowody, aby potwierdzić.

Należy wziąć pod uwagę parametry problemu. Jeśli jest zależna od lokalizacji geograficznej, skonfiguruj [testy dostępności,](../../azure-monitor/app/monitor-web-app-availability.md) w tym ten region: mogą po prostu wystąpić problemy z siecią w tym obszarze.

### <a name="diagnose-slow-page-loads"></a>Diagnozowanie powolnych wczytywek stron
Gdzie jest problem? Czy serwer jest powolny, aby odpowiedzieć, jest strona bardzo długa, czy przeglądarka musi zrobić dużo pracy, aby go wyświetlić?

Otwórz blok metryki Przeglądarki. Podzielony na segmenty wyświetlanie czasu ładowania strony przeglądarki pokazuje, dokąd zmierza czas. 

* Jeśli **czas wysyłania żądania** jest wysoki, serwer odpowiada powoli lub żądanie jest wpis z dużą ilością danych. Przyjrzyj się [metryk wydajności,](../../azure-monitor/app/web-monitor-performance.md#metrics) aby zbadać czasy odpowiedzi.
* Skonfiguruj [śledzenie zależności,](../../azure-monitor/app/asp-net-dependencies.md) aby zobaczyć, czy powolność jest spowodowana usługami zewnętrznymi lub bazą danych.
* Jeśli **odpowiedź odbierana** jest dominująca, strona i jej zależne części - JavaScript, CSS, obrazy i tak dalej (ale nie asynchronicznie załadowane dane) są długie. Skonfiguruj [test dostępności](../../azure-monitor/app/monitor-web-app-availability.md)i upewnij się, że ustawisz opcję ładowania części zależnych od obciążenia. Po otrzymaniu pewnych wyników otwórz szczegóły wyniku i rozwiń go, aby zobaczyć czasy ładowania różnych plików.
* Wysoki **czas przetwarzania klienta** sugeruje, że skrypty działają powoli. Jeśli przyczyna nie jest oczywista, należy rozważyć dodanie kodu chronometrażu i wysłać godziny w trackMetric wywołań.

### <a name="improve-slow-pages"></a>Poprawa powolnych stron
Istnieje sieć pełna porad na temat poprawy odpowiedzi serwera i czasu ładowania strony, więc nie będziemy próbować powtórzyć tego wszystkiego tutaj. Oto kilka wskazówek, które prawdopodobnie już wiesz o, tylko po to, aby ci myśleć:

* Powolne ładowanie z powodu dużych plików: Załaduj skrypty i inne części asynchronicznie. Użyj tworzenia pakietów skryptów. Podziel stronę główną na widżety, które ładują swoje dane oddzielnie. Nie wysyłaj zwykłego starego kodu HTML dla długich tabel: użyj skryptu, aby zażądać danych jako JSON lub innego kompaktowego formatu, a następnie wypełnij tabelę na miejscu. Istnieją świetne ramy, aby pomóc w tym wszystkim. (Oczywiście pociągają one za sobą również duże skrypty).
* Powolne zależności serwera: należy wziąć pod uwagę lokalizacje geograficzne składników. Na przykład jeśli używasz platformy Azure, upewnij się, że serwer sieci web i bazy danych znajdują się w tym samym regionie. Czy kwerendy pobierają więcej informacji, niż potrzebują? Czy buforowanie lub wsadowanie pomoże?
* Problemy z pojemnością: spójrz na metryki serwera czasu odpowiedzi i liczby żądań. Jeśli czas odpowiedzi jest nieproporcjonalnie nieproporcjonalny w przypadku szczytów w liczbie żądań, prawdopodobnie serwery są rozciągnięte.


## <a name="server-response-time-degradation"></a>Degradacja czasu odpowiedzi serwera

Powiadomienie o pogorszeniu czasu reakcji informuje:

* Czas odpowiedzi w porównaniu do normalnego czasu odpowiedzi dla tej operacji.
* Ilu użytkowników dotyczy.
* Średni czas reakcji i 90-ty percentylowy czas odpowiedzi dla tej operacji w dniu wykrycia i 7 dni wcześniej. 
* Liczba żądań tej operacji w dniu wykrycia i 7 dni wcześniej.
* Korelacja między degradacji w tej operacji i degradacji w zależności powiązanych. 
* Łącza ułatwiające zdiagnozowanie problemu.
  * Profilera ślady, aby ułatwić wyświetlanie czasu operacji jest spędzony (łącze jest dostępne, jeśli profileer przykłady śledzenia zostały zebrane dla tej operacji w okresie wykrywania). 
  * Raporty wydajności w Eksploratorze metryki, gdzie można pokroić i kości zakres czasu /filtry dla tej operacji.
  * Wyszukaj to wywołanie, aby wyświetlić określone właściwości wywołania.
  * Raporty o błędach — jeśli liczba > 1 oznacza to, że wystąpiły błędy w tej operacji, które mogły przyczynić się do obniżenia wydajności.

## <a name="dependency-duration-degradation"></a>Degradacja czasu trwania zależności

Nowoczesne aplikacje coraz częściej przyjmują podejście do projektowania mikro usług, co w wielu przypadkach prowadzi do dużej niezawodności usług zewnętrznych. Na przykład jeśli aplikacja opiera się na niektórych platform danych lub nawet jeśli zbudujesz własną usługę bot prawdopodobnie przekaźnik u jakiegoś dostawcy usług poznawczych, aby umożliwić botom interakcję w bardziej ludzki sposób i niektóre usługi magazynu danych dla bota, aby wyciągnąć odpowiedzi z.  

Przykładowe powiadomienie o degradacji zależności:

![Oto przykład wykrywania degradacji czasu trwania zależności](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Należy zauważyć, że informuje:

* Czas trwania w porównaniu z normalnym czasem reakcji dla tej operacji
* Ilu użytkowników jest dotkniętych
* Średni czas trwania i 90-ty percentyl dla tej zależności w dniu wykrycia i 7 dni przed
* Liczba wywołań zależności w dniu wykrycia i 7 dni przed
* Łącza ułatwiające zdiagnozowanie problemu
  * Raporty wydajności w Eksploratorze metryk dla tej zależności
  * Wyszukaj te wywołania zależności, aby wyświetlić właściwości połączeń
  * Raporty o błędach — jeśli liczba > 1 oznacza to, że w okresie wykrywania wystąpiły nieudane wywołania zależności, które mogły przyczynić się do degradacji czasu trwania. 
  * Otwórz Analytics z kwerendami, które obliczają ten czas trwania zależności i zliczają  

## <a name="smart-detection-of-slow-performing-patterns"></a>Inteligentne wykrywanie wzorców o powolnym wykonywaniu 

Usługa Application Insights wyszukuje problemy z wydajnością, które mogą mieć wpływ tylko na część użytkowników lub tylko w niektórych przypadkach. Na przykład powiadomienie o wczytyniu stron jest wolniejsze w jednym typie przeglądarki niż w innych typach przeglądarek lub jeśli żądania są obsługiwane wolniej z określonego serwera. Można również wykryć problemy związane z kombinacjami właściwości, takie jak powolne ładowanie strony w jednym obszarze geograficznym dla klientów korzystających z określonego systemu operacyjnego.  

Anomalie takie jak te są bardzo trudne do wykrycia tylko przez inspekcję danych, ale są bardziej powszechne, niż mogłoby się wydawać. Często pojawiają się one tylko wtedy, gdy klienci skarżą się. W tym czasie jest już za późno: użytkownicy, których to dotyczy, już przechodzą na twoich konkurentów!

Obecnie nasze algorytmy patrzą na czasy ładowania strony, czasy odpowiedzi żądania na serwerze i czasy odpowiedzi zależności.  

Nie trzeba ustawiać żadnych progów ani konfigurować reguł. Algorytmy uczenia maszynowego i eksploracji danych są używane do wykrywania nieprawidłowych wzorców.

![W alertie e-mail kliknij łącze, aby otworzyć raport diagnostyczny na platformie Azure](./media/proactive-performance-diagnostics/03.png)

* **Po** wyświetleniu czasu wykrycia problemu.
* **Co** opisuje:

  * Wykryto problem;
  * Charakterystyka zestawu zdarzeń, które znaleźliśmy wyświetlane zachowanie problemu.
* Tabela porównuje zestaw o słabych wynikach ze średnim zachowaniem wszystkich innych zdarzeń.

Kliknij łącza, aby otworzyć Eksplorator metryki i Wyszukaj odpowiednie raporty, filtrowane według czasu i właściwości zestawu o powolnym wykonywaniu.

Zmodyfikuj zakres czasu i filtry, aby eksplorować dane telemetryczne.

## <a name="next-steps"></a>Następne kroki
Te narzędzia diagnostyczne ułatwiają sprawdzanie danych telemetrycznych z aplikacji:

* [Profiler](profiler.md) 
* [Debuger migawki](../../azure-monitor/app/snapshot-debugger.md)
* [Analiza](../../azure-monitor/log-query/get-started-portal.md)
* [Inteligentna diagnostyka analityczna](../../azure-monitor/app/analytics.md)

Inteligentne wykrywanie jest całkowicie automatyczne. Ale może chcesz skonfigurować kilka alertów więcej?

* [Ręcznie skonfigurowane alerty metryk](../../azure-monitor/app/alerts.md)
* [Testy dostępności sieci Web](../../azure-monitor/app/monitor-web-app-availability.md)
