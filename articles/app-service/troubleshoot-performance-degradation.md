---
title: Rozwiązywanie problemów z obniżeniem wydajności
description: Dowiedz się, jak rozwiązywać problemy związane z wydajnością aplikacji w Azure App Service, w tym monitorowanie zachowań aplikacji, zbieranie danych i zmniejszanie problemu.
tags: top-support-issue
keywords: wydajność aplikacji sieci Web, powolna aplikacja, powolna aplikacja
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: e048e4c17092c71a5adaf29f342546b9258837c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100586232"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Rozwiązywanie problemów z wydajnością wolniejszych aplikacji w Azure App Service
Ten artykuł pomaga rozwiązywać problemy związane z wydajnością aplikacji w [Azure App Service](./overview.md).

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure w [witrynie MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie możesz także zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję **Uzyskaj pomoc techniczną**.

## <a name="symptom"></a>Objaw
Podczas przeglądania aplikacji strony ładują się powoli i czasami przekraczają limit czasu.

## <a name="cause"></a>Przyczyna
Ten problem często jest spowodowany problemami na poziomie aplikacji, takimi jak:

* żądania sieci trwają długo
* kod aplikacji lub zapytania bazy danych są niewydajne
* Aplikacja korzystająca z dużej ilości pamięci/procesora
* awaria aplikacji z powodu wyjątku

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Rozwiązywanie problemów można podzielić na trzy różne zadania, w kolejności sekwencyjnej:

1. [Obserwuj i monitoruj zachowanie aplikacji](#observe)
2. [Zbieranie danych](#collect)
3. [Eliminowanie problemu](#mitigate)

[App Service](overview.md) oferuje różne opcje w każdym kroku.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Obserwuj i monitoruj zachowanie aplikacji
#### <a name="track-service-health"></a>Śledzenie kondycji usługi
Microsoft Azure jest w trakcie każdej przerwy w świadczeniu usług lub obniżenia wydajności. Kondycję usługi można śledzić w [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [śledzenie kondycji usługi](../service-health/service-notifications.md).

#### <a name="monitor-your-app"></a>Monitorowanie aplikacji
Ta opcja umożliwia dowiedzieć się, czy aplikacja ma jakiekolwiek problemy. W bloku aplikacji kliknij kafelek **żądania i błędy** . Blok **Metryka** przedstawia wszystkie metryki, które można dodać.

Niektóre metryki, które mogą być monitorowane dla aplikacji, to

* Średni zestaw roboczy pamięci
* Średni czas odpowiedzi
* Czas procesora CPU
* Zestaw roboczy pamięci
* Żądania

![Monitorowanie wydajności aplikacji](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Aby uzyskać więcej informacji, zobacz:

* [Monitorowanie aplikacji w Azure App Service](web-sites-monitor.md)
* [Otrzymywanie powiadomień o alertach](../azure-monitor/alerts/alerts-overview.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorowanie stanu punktu końcowego sieci Web
Jeśli aplikacja jest uruchamiana w warstwie cenowej **standardowa** , App Service pozwala monitorować dwa punkty końcowe z trzech lokalizacji geograficznych.

Monitorowanie punktu końcowego służy do konfigurowania testów sieci Web z lokalizacji rozproszonych geograficznie, które testują czas odpowiedzi i przestoje adresów URL sieci Web. Test wykonuje operację HTTP GET w adresie URL sieci Web, aby określić czas odpowiedzi i przestoje z każdej lokalizacji. Każda skonfigurowana lokalizacja uruchamia test co pięć minut.

Czas przestoju jest monitorowany przy użyciu kodów odpowiedzi HTTP, a czas odpowiedzi jest mierzony w milisekundach. Test monitorowania kończy się niepowodzeniem, jeśli kod odpowiedzi HTTP jest większy lub równy 400 lub jeśli odpowiedź trwa dłużej niż 30 sekund. Punkt końcowy jest uznawany za dostępny, jeśli jego testy monitorowania zakończyły się powodzeniem ze wszystkich określonych lokalizacji.

Aby je skonfigurować, zobacz [monitorowanie aplikacji w Azure App Service](web-sites-monitor.md).

Zobacz też temat [monitorowanie witryn sieci Web systemu Azure w górę i monitorowania punktów końcowych — z Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) do wideo na potrzeby monitorowania punktów końcowych.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorowanie wydajności aplikacji przy użyciu rozszerzeń
Możesz również monitorować wydajność aplikacji przy użyciu *rozszerzenia lokacji*.

Każda aplikacja App Service udostępnia rozszerzalny punkt końcowy zarządzania, który umożliwia korzystanie z zaawansowanego zestawu narzędzi wdrożonych jako rozszerzenia lokacji. Rozszerzenia obejmują: 

- Edytory kodu źródłowego, takie jak [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Narzędzia do zarządzania dla podłączonych zasobów, takie jak baza danych MySQL połączona z aplikacją.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) to rozszerzenie witryny do monitorowania wydajności, które jest również dostępne. Aby użyć Application Insights, należy ponownie skompilować kod przy użyciu zestawu SDK. Można także zainstalować rozszerzenie, które zapewnia dostęp do dodatkowych danych. Zestaw SDK umożliwia pisanie kodu do monitorowania użycia i wydajności aplikacji w bardziej szczegółowy sposób. Aby uzyskać więcej informacji, zobacz [monitorowanie wydajności w aplikacjach sieci Web](../azure-monitor/app/app-insights-overview.md).

<a name="collect"></a>

### <a name="2-collect-data"></a>2. Zbierz dane
App Service udostępnia funkcje diagnostyczne do rejestrowania informacji z serwera sieci Web i aplikacji sieci Web. Informacje są rozdzielone na diagnostykę serwera sieci Web i diagnostykę aplikacji.

#### <a name="enable-web-server-diagnostics"></a>Włącz diagnostykę serwera sieci Web
Można włączać lub wyłączać następujące rodzaje dzienników:

* **Szczegóły rejestrowania błędów** — szczegółowe informacje o błędach dla kodów stanu HTTP wskazujących niepowodzenie (kod stanu 400 lub nowszy). Może to zawierać informacje, które mogą pomóc w ustaleniu, dlaczego serwer zwrócił kod błędu.
* **Śledzenie nieudanych żądań** — szczegółowe informacje o żądaniach zakończonych niepowodzeniem, w tym śledzenia składników usług IIS używanych do przetwarzania żądania oraz czasu wykonywanego w każdym składniku. Może to być przydatne, jeśli próbujesz zwiększyć wydajność aplikacji lub odizolować, co jest przyczyną określonego błędu HTTP.
* **Rejestrowanie serwera sieci Web** — informacje o transakcjach http przy użyciu rozszerzonego formatu W3C plików dziennika. Jest to przydatne podczas ustalania ogólnych metryk aplikacji, takich jak Liczba obsłużonych żądań lub ile żądań pochodzi z określonego adresu IP.

#### <a name="enable-application-diagnostics"></a>Włącz diagnostykę aplikacji
Istnieje kilka opcji zbierania danych wydajności aplikacji z App Service, profilowania aplikacji na żywo z programu Visual Studio lub modyfikowania kodu aplikacji, aby rejestrować więcej informacji i śladów. Możesz wybrać opcje w zależności od tego, jaki jest dostęp do aplikacji i co zaobserwowano w narzędziach do monitorowania.

##### <a name="use-application-insights-profiler"></a>Użyj Application Insights Profiler
Możesz włączyć Application Insights Profiler, aby rozpocząć przechwytywanie szczegółowych danych śledzenia wydajności. Można uzyskać dostęp do śladów przechwyconych do pięciu dni temu, gdy konieczne jest zbadanie problemów w przeszłości. Możesz wybrać tę opcję, o ile masz dostęp do zasobu Application Insights aplikacji w Azure Portal.

Application Insights Profiler zawiera statystyki dotyczące czasu odpowiedzi dla każdego wywołania sieci Web i śladów wskazujących, który wiersz kodu spowodował spowolnienie odpowiedzi. Czasami aplikacja App Service działa wolno, ponieważ określony kod nie jest w pełni przygotowany. Przykłady obejmują sekwencyjny kod, który można uruchamiać równolegle i niepożądanymi rywalizacjami o blokadę bazy danych. Usunięcie tych wąskich gardeł w kodzie zwiększa wydajność aplikacji, ale trudno jest wykryć, nie konfigurując rozbudowanych śladów i dzienników. Ślady zbierane przez Application Insights Profiler pomagają identyfikować wiersze kodu, które spowalniają działanie aplikacji, i przezwyciężyć to wyzwanie dla App Service aplikacji.

 Aby uzyskać więcej informacji, zobacz [profilowanie aktywnych aplikacji w Azure App Service z Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Korzystanie z profilowania zdalnego
W Azure App Service aplikacje sieci Web, aplikacje interfejsu API, zaplecza mobilne i Zadania WebJob można zdalnie przetworzyć. Wybierz tę opcję, jeśli masz dostęp do zasobu aplikacji i wiesz, jak odtworzyć problem, lub jeśli wiesz dokładnie, ile czasu występuje problem z wydajnością.

Profilowanie zdalne jest przydatne, jeśli użycie procesora CPU przez proces jest wysokie, a proces działa wolniej niż oczekiwano lub opóźnienie żądań HTTP jest wyższe niż normalne, można zdalnie profilować proces i pobrać stosy wywołań próbkowania procesora, aby przeanalizować aktywność procesu i ścieżki do kodu.

Aby uzyskać więcej informacji, zobacz [Obsługa profilowania zdalnego w Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Ręczne konfigurowanie śladów diagnostycznych
Jeśli masz dostęp do kodu źródłowego aplikacji sieci Web, Diagnostyka aplikacji umożliwia przechwytywanie informacji generowanych przez aplikację sieci Web. Aplikacje ASP.NET mogą używać `System.Diagnostics.Trace` klasy do rejestrowania informacji w dzienniku diagnostyki aplikacji. Należy jednak zmienić kod i ponownie wdrożyć aplikację. Ta metoda jest zalecana, jeśli aplikacja działa w środowisku testowym.

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania aplikacji do rejestrowania, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji w Azure App Service](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Korzystanie z narzędzia diagnostycznego
App Service zapewnia inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z aplikacją bez konieczności konfigurowania. Gdy wystąpią problemy z aplikacją, narzędzie diagnostyczne wskaże, co się nie stało, aby poprowadzić Cię do właściwych informacji, aby łatwiej i szybko rozwiązywać problemy oraz rozwiązać ten problem.

Aby uzyskać dostęp do diagnostyki App Service, przejdź do aplikacji App Service lub App Service Environment w [Azure Portal](https://portal.azure.com). W lewym okienku nawigacji kliknij pozycję **Diagnozuj i rozwiąż problemy**.

#### <a name="use-the-kudu-debug-console"></a>Korzystanie z konsoli debugowania kudu
App Service obejmuje konsolę debugowania, której można użyć do debugowania, eksplorowania, przekazywania plików, a także punktów końcowych JSON do uzyskiwania informacji o środowisku. Ta konsola jest nazywana *konsolą kudu* lub *pulpitem nawigacyjnym SCM* dla aplikacji.

Możesz uzyskać dostęp do tego pulpitu nawigacyjnego, przechodząc do linku **https:// &lt; nazwę aplikacji>. SCM.azurewebsites.NET/**.

Dostępne są następujące elementy kudu:

* Ustawienia środowiska dla aplikacji
* strumień dziennika
* zrzut diagnostyczny
* Konsola debugowania, w której można uruchamiać polecenia cmdlet programu PowerShell i podstawowe polecenia DOS.

Kolejną przydatną funkcją kudu jest to, że w przypadku, gdy aplikacja zgłasza wyjątki pierwszej szansy, można użyć kudu i narzędzi SysInternals ProcDump, aby utworzyć zrzuty pamięci. Zrzuty pamięci są migawkami procesu i często mogą pomóc w rozwiązywaniu bardziej skomplikowanych problemów z aplikacją.

Aby uzyskać więcej informacji na temat funkcji dostępnych w programie kudu, zobacz [temat narzędzia DevOps platformy Azure, których należy wiedzieć](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. Zmniejsz problem
#### <a name="scale-the-app"></a>Skalowanie aplikacji
W Azure App Service, aby zwiększyć wydajność i przepływność, można dostosować skalę, w której jest uruchamiana aplikacja. Skalowanie aplikacji w górę obejmuje dwie powiązane akcje: zmiana planu App Service na wyższą warstwę cenową oraz skonfigurowanie niektórych ustawień po przełączeniu do wyższej warstwy cenowej.

Aby uzyskać więcej informacji na temat skalowania, zobacz [skalowanie aplikacji w Azure App Service](manage-scale-up.md).

Ponadto możesz uruchomić aplikację w więcej niż jednym wystąpieniu. Skalowanie w górę nie tylko zapewnia większą możliwość przetwarzania, ale również zapewnia pewną odporność na uszkodzenia. Jeśli proces ulegnie awarii na jednym wystąpieniu, inne wystąpienia nadal będą obsługiwały żądania.

Skalowanie można ustawić jako ręczne lub automatyczne.

#### <a name="use-autoheal"></a>Użyj autozabliźnione
Autozabliźnione odtwarza proces roboczy aplikacji w oparciu o wybrane ustawienia (na przykład zmiany konfiguracji, żądania, limity pamięci lub czas wymagany do wykonania żądania). W większości przypadków odtwarzanie procesu jest najszybszym sposobem na odzyskanie sprawności po wystąpieniu problemu. Mimo że zawsze możesz ponownie uruchomić aplikację bezpośrednio w Azure Portal, autozabliźnione robi automatycznie. Wszystko, co musisz zrobić, to dodanie wyzwalaczy w web.config głównym aplikacji. Te ustawienia będą działały w taki sam sposób, nawet jeśli aplikacja nie jest aplikacją platformy .NET.

Aby uzyskać więcej informacji, zobacz [Funkcja autonaprawy witryn sieci Web systemu Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Uruchom ponownie aplikację
Ponowne uruchomienie jest często najprostszym sposobem na odzyskanie po jednorazowych problemach. Na [Azure Portal](https://portal.azure.com/)w bloku aplikacji są dostępne opcje zatrzymania lub ponownego uruchomienia aplikacji.

 ![Uruchom ponownie aplikację, aby rozwiązać problemy z wydajnością](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Możesz również zarządzać aplikacją przy użyciu Azure PowerShell. Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).