---
title: Co to jest usługa Azure Application Insights? | Microsoft Docs
description: Zarządzanie wydajnością aplikacji i śledzenie użycia działającej aplikacji internetowej.  Wykrywanie, klasyfikacja i diagnozowanie problemów, rozumienie, jak użytkownicy używają Twojej aplikacji.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: f00eea37c43fba10e0a89498dd81366630852db7
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371385"
---
# <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
Application Insights, funkcja [Azure monitor](../overview.md), to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów i informatyków DevOps. Służy do monitorowania aplikacji na żywo. Automatycznie wykryje anomalie wydajności i oferuje zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie, jakie użytkownicy faktycznie są w tej aplikacji.  Usługa ta pomaga w ciągłym doskonaleniu wydajności i użyteczności tworzonych rozwiązań. Działa w przypadku aplikacji na różnych platformach, w tym .NET, Node.js, Java i Python hostowanych lokalnie, hybrydowych lub dowolnej chmurze publicznej. Integruje się ona z procesem DevOps i ma punkty połączenia z szeroką gamą narzędzi programistycznych. Może monitorować i analizować dane telemetryczne z aplikacji mobilnych dzięki integracji z usługą Visual Studio App Center.

## <a name="how-does-application-insights-work"></a>Jak działa usługa Application Insights?
Zainstalujesz niewielki pakiet Instrumentacji (SDK) w aplikacji lub Włącz Application Insights przy użyciu agenta Application Insights, jeśli jest [obsługiwany](./platforms.md). Instrumentacja monitoruje aplikację i kieruje dane telemetryczne do zasobu usługi Azure Application Insights przy użyciu unikatowego identyfikatora GUID, który odwołuje się jako klucz Instrumentacji.

Instrumentacja może obejmować nie tylko aplikację usługi sieci Web, ale również wszystkie składniki tła i skrypty JavaScript na samych stronach sieci Web. Aplikacja i jej składniki mogą działać w dowolnym miejscu — nie musi być hostowana na platformie Azure.

![Instrumentacja usługi Application Insights w Twojej aplikacji wysyła dane telemetryczne do zasobu usługi Application Insights.](./media/app-insights-overview/diagram.png)

Ponadto możesz ściągnąć dane telemetryczne ze środowisk hostów, takich jak liczniki wydajności, diagnostyka platformy Azure lub dzienniki platformy Docker. Możesz też skonfigurować testy internetowe, które okresowo wysyłają syntetyczne żądania do usługi internetowej.

Wszystkie te strumienie telemetrii są zintegrowane z Azure Monitor. W Azure Portal można zastosować zaawansowane narzędzia analityczne i wyszukiwania do danych pierwotnych.

### <a name="whats-the-overhead"></a>Co to jest narzut?
Wpływ na wydajność aplikacji jest mały. Wywołania śledzenia nie powodują blokowania oraz są łączone w partie i wysłane w oddzielnym wątku.

## <a name="what-does-application-insights-monitor"></a>Co monitoruje usługa Application Insights?

Usługa Application Insights jest przeznaczona dla zespołu deweloperów po to, aby pomóc zrozumieć, jak działa Twoja aplikacja i jak jest używana. Monitoruje ona:

* **Liczby żądań, czasy reakcji i współczynniki błędów** — dowiedz się, które strony są najbardziej popularne, o jakiej porze dnia i gdzie są Twoi użytkownicy. Zobacz, które strony działają najlepiej. Jeśli Twoje czasy odpowiedzi i częstotliwości awarii są duże, gdy jest więcej żądań, być może masz problem z zasobami. 
* **Współczynniki zależności, czasy reakcji i współczynniki błędów** — dowiedz się, czy usługi zewnętrzne nie spowalniają pracy.
* **Wyjątki** — analizowanie zagregowanych danych statystycznych lub wybieranie określonych wystąpień i przechodzenie do szczegółów śladu stosu i powiązanych żądań. Są zgłaszane zarówno wyjątki serwera, jak i przeglądarki.
* **Wydajność ładowania i wyświetleń stron** — zgłoszona przez przeglądarki użytkowników.
* **Wywołania AJAX** ze stron sieci Web — liczba, czasy reakcji i współczynniki błędów.
* **Liczby użytkowników i sesji**.
* **Liczniki wydajności** z serwerów systemu Windows lub Linux, takie jak użycie procesora CPU, pamięci i sieci. 
* **Diagnostyka hosta** z platformy Docker lub Azure. 
* **Diagnostyczne dzienniki śledzenia** z Twojej aplikacji — dzięki temu możesz skorelować zdarzenia śledzenia z żądaniami.
* **Niestandardowe zdarzenia i metryki**, które samodzielnie zapisujesz w kodzie klienta lub serwera, do śledzenia zdarzeń biznesowych, takich jak sprzedane towary lub wygrane gry.

## <a name="where-do-i-see-my-telemetry"></a>Gdzie mogę zobaczyć swoją telemetrię?

Istnieje wiele sposobów eksploracji danych. Zapoznaj się z następującymi artykułami:

|  |  |
| --- | --- |
| [**Inteligentne wykrywanie i ręczne alerty**](./proactive-diagnostics.md)<br/>Skonfiguruj automatyczne alerty, które dostosowują się do zwykłych wzorców danych telemetrycznych i wyzwalaczy, gdy istnieje coś poza zwykłym wzorcem. Możesz również [ustawić alerty](../platform/alerts-log.md) dla konkretnych poziomów metryk niestandardowych lub standardowych. |![Przykładowy alert](./media/app-insights-overview/alerts-tn.png) |
| [**Mapa aplikacji**](./app-map.md)<br/>Poznaj składniki aplikacji z kluczowymi metrykami i alertami. |![Mapa aplikacji](./media/app-insights-overview/appmap-tn.png)  |
| [**Profiler**](./profiler.md)<br/>Inspekcja profilów wykonania próbek żądań. |![Przechwytywanie ekranu pokazuje profile wykonywania próbkowanych żądań.](./media/app-insights-overview/profiler.png) |
| [**Analiza użycia**](./usage-overview.md)<br/>Analiza segmentacji i utrzymywania użytkowników.|![Narzędzie utrzymywania](./media/app-insights-overview/retention.png) |
| [**Wyszukiwanie diagnostyczne danych dla wystąpienia**](./diagnostic-search.md)<br/>Wyszukiwanie i filtrowanie zdarzeń, takich jak żądania, wyjątki, wywołania zależności, dzienniki śledzenia i wyświetlenia stron.  |![Wyszukiwanie telemetrii](./media/app-insights-overview/search-tn.png) |
| [**Eksplorator metryk dla danych zagregowanych**](../platform/metrics-charts.md)<br/>Eksploruj, filtruj i segmentuj zagregowane dane, takie jak liczby żądań, błędów i wyjątków, czasy reakcji, czasy ładowania stron. |![Metryki](./media/app-insights-overview/metrics-tn.png) |
| [**Pulpity nawigacyjne**](./overview-dashboard.md)<br/>Połącz dane z wielu zasobów i udostępnij innym osobom. Opcja ta doskonale nadaje się dla aplikacji wieloskładnikowych i ciągłego wyświetlania w pomieszczeniu zespołu. |![Przykładowy pulpit nawigacyjny](./media/app-insights-overview/dashboard-tn.png) |
| [**Transmisja strumieniowa metryk na żywo**](./live-stream.md)<br/>Podczas wdrażania nowej kompilacji obejrzyj te wskaźniki wydajności prawie w czasie rzeczywistym, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. |![Przykładowe metryki na żywo](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analiza**](../log-query/log-query-overview.md)<br/>Odpowiedz na trudne pytania dotyczące wydajności i użycia Twojej aplikacji za pomocą tego zaawansowanego języka zapytań. |![Przykładowa analiza](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>Zobacz dane dotyczące wydajności w kodzie. Przejdź do kodu ze śladów stosu.|![Zrzut ekranu przedstawia szczegóły wyjątku w programie Visual Studio i przykład przechodzenie do kodu ze śladów stosu.](./media/app-insights-overview/visual-studio-tn.png) |
| [**Debuger migawek**](./snapshot-debugger.md)<br/>Debuguje próbki migawek pobrane z operacji na żywo, używając wartości parametrów.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>Integruje metryki użycia z innymi analizami biznesowymi.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**Interfejs API REST**](https://dev.applicationinsights.io/)<br/>Napisz kod, aby uruchamiać zapytania dla swoich metryk i danych pierwotnych.| ![Interfejs API REST](./media/app-insights-overview/rest-tn.png) |
| [**Eksport ciągły**](./export-telemetry.md)<br/>Zbiorczy eksport danych pierwotnych do magazynu zaraz po ich odebraniu. |![Eksportowanie](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Jak mogę używać usługi Application Insights?

### <a name="monitor"></a>Monitorowanie
Zainstaluj usługę Application Insights w swojej aplikacji, skonfiguruj [testy sieci Web dostępności](./monitor-web-app-availability.md) oraz:

* Sprawdź domyślny [pulpit nawigacyjny aplikacji](./overview-dashboard.md) dla pokoju zespołu, aby śledzić obciążenie, czas odpowiedzi oraz wydajność zależności, obciążeń stron i wywołań AJAX.
* Wykryj, jakie żądania są najwolniejsze i najczęściej kończą się niepowodzeniem.
* Obejrzyj [transmisję strumieniową na żywo](./live-stream.md) podczas wdrażania nowej wersji, aby od razu dowiedzieć się o wszelkich pogorszeniach.

### <a name="detect-diagnose"></a>Wykrywanie, diagnozowanie
Po odebraniu alertu lub odnalezieniu problemu:

* Oceń, na ilu użytkowników ma to wpływ.
* Skoreluj błędy z wyjątkami, wywołaniami zależności i śladami.
* Sprawdź profilera, migawki, zrzuty stosu i dzienniki śledzenia.

### <a name="build-measure-learn"></a>Kompilowanie, mierzenie, poznawanie
[Zmierz efektywność](./usage-overview.md) każdej nowej funkcji, która zostanie wdrożona.

* Zaplanuj pomiar sposobu używania nowych funkcji środowiska użytkownika lub biznesowych przez klientów.
* Zapisz niestandardową telemetrię do kodu.
* Oprzyj następny cyklu rozwoju na twardych dowodach ze swojej telemetrii.

## <a name="get-started"></a>Rozpoczęcie pracy
Usługa Application Insights jest jedną z wielu usług hostowanych na platformie Microsoft Azure, a dane telemetryczne są przesyłane do analizy i prezentacji. Dlatego przed wykonaniem jakichkolwiek innych czynności będzie potrzebna subskrypcja platformy [Microsoft Azure](https://azure.com). Rejestracja jest bezpłatna, a jeśli wybierzesz podstawowy [plan cenowy](https://azure.microsoft.com/pricing/details/application-insights/) usługi Application Insights, nie będzie opłat do chwili, gdy aplikacja rozrośnie się na tyle, aby mieć znaczne wykorzystanie. Jeśli Twoja organizacja ma już subskrypcję, może ona dodać do niej Twoje konto Microsoft.

Istnieje kilka sposobów, aby rozpocząć. Zacznij od tego, co najlepiej sprawdza się w Twoim przypadku. Resztę możesz dodać później.

* **W czasie wykonywania: wdróż instrumentację aplikacji internetowej na serwerze.** Idealne rozwiązanie w przypadku aplikacji, które zostały już wdrożone. Pozwala uniknąć wszelkich aktualizacji kodu.
  * [**Aplikacje ASP.NET lub ASP.NET Core hostowane na platformie Azure Web Apps**](./azure-web-apps.md)
  * [**ASP.NET aplikacje hostowane w usługach IIS na maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych platformy Azure**](./azure-vm-vmss-apps.md)
  * [**ASP.NET aplikacje hostowane w lokalnych maszynach wirtualnych usług IIS**](./monitor-performance-live-website-now.md)
* **W czasie tworzenia: dodaj usługę Application Insights do swojego kodu.** Pozwala dostosować zbieranie danych telemetrycznych i wysyłać dodatkowe dane telemetryczne.
  * [Aplikacje ASP.NET](./asp-net.md)
  * [ASP.NET Core aplikacji](./asp-net-core.md)
  * [Aplikacje konsolowe platformy .NET](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [Inne platformy](./platforms.md)
* **[Instrumentacja stron internetowych](./javascript.md)** dla widoku strony, wywołań AJAX i innej telemetrii po stronie klienta.
* **[Analizowanie użycia aplikacji mobilnej](../learn/mobile-center-quickstart.md)** dzięki integracji z usługą Visual Studio App Center.
* **[Testy dostępności](./monitor-web-app-availability.md)** — regularnie używaj polecenia ping dla witryny sieci Web z naszych serwerów.

## <a name="next-steps"></a>Następne kroki
Rozpocznij pracę w czasie wykonywania za pomocą rozwiązań:

* [Maszyna wirtualna platformy Azure i zestaw skalowania maszyn wirtualnych platformy Azure — aplikacje hostowane](./azure-vm-vmss-apps.md)
* [Serwer usług IIS](./monitor-performance-live-website-now.md)
* [Aplikacje internetowe platformy Azure](./azure-web-apps.md)

Rozpocznij pracę w czasie programowania za pomocą rozwiązań:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript](./javascript.md)


## <a name="support-and-feedback"></a>Pomoc techniczna i opinie
* Pytania i problemy:
  * [Rozwiązywanie problemów][qna]
  * [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-monitor.html)
  * [Witryna StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Twoje sugestie:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Blog usługi Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../learn/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md

