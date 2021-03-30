---
title: Narzędzie do diagnostyki i rozwiązywania
description: Dowiedz się, jak rozwiązywać problemy z aplikacją w Azure App Service przy użyciu narzędzia diagnostyki i rozwiązywania w Azure Portal.
keywords: App Service, Azure App Service, Diagnostyka, pomoc techniczna, aplikacja sieci Web, rozwiązywanie problemów, Samopomocowa
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: d58341979b0bbe0699a5ca293b20394c43cde1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962812"
---
# <a name="azure-app-service-diagnostics-overview"></a>Omówienie diagnostyki Azure App Service

Gdy uruchamiasz aplikację sieci Web, chcesz przygotować się do wszelkich problemów, które mogą wystąpić, od 500 błędów do użytkowników, informując o tym, że witryna nie działa. App Service Diagnostics to inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z aplikacją bez konieczności konfigurowania. Gdy uruchamiasz problemy z aplikacją, App Service Diagnostics wskazuje, co jest niewłaściwe, aby poprowadzić Cię do odpowiednich informacji, aby łatwiej i szybko rozwiązywać problemy.

Chociaż to środowisko jest najbardziej przydatne, gdy występują problemy z aplikacją w ciągu ostatnich 24 godzin, wszystkie wykresy diagnostyczne są zawsze dostępne do przeanalizowania.

App Service Diagnostics działa nie tylko dla aplikacji w systemie Windows, ale również aplikacje w systemie [Linux/kontenery](./overview.md#app-service-on-linux), [App Service Environment](./environment/intro.md)i [Azure Functions](../azure-functions/functions-overview.md).

## <a name="open-app-service-diagnostics"></a>Otwórz diagnostykę App Service

Aby uzyskać dostęp do diagnostyki App Service, przejdź do aplikacji internetowej App Service lub App Service Environment w [Azure Portal](https://portal.azure.com). W lewym okienku nawigacji kliknij pozycję **Diagnozuj i rozwiąż problemy**.

W przypadku Azure Functions przejdź do aplikacji funkcji, a następnie w górnej części nawigacyjnej kliknij pozycję **funkcje platformy**, a następnie wybierz pozycję **Diagnozuj i rozwiąż problemy** w sekcji **Zarządzanie zasobami** .

Na stronie głównej diagnostyki App Service możesz wybrać kategorię, która najlepiej opisuje problem z aplikacją przy użyciu słów kluczowych w każdym fragmencie strony głównej. Ta strona służy również do znajdowania **Narzędzia diagnostyczne** aplikacji systemu Windows. Zobacz [Narzędzia diagnostyczne (tylko dla aplikacji systemu Windows)](#diagnostic-tools-only-for-windows-app).

![Strona główna](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Jeśli aplikacja nie działa lub działa wolno, można [zebrać ślad profilowania](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) , aby zidentyfikować główną przyczynę problemu. Profilowanie jest jasne i przeznaczone do scenariuszy produkcyjnych.
>

## <a name="interactive-interface"></a>Interfejs interaktywny

Po wybraniu kategorii głównej najlepiej dopasowanej do problemu z aplikacją App Service Diagnostyka "Interactive Interface Genie" może przeprowadzić Cię przez diagnozowanie i rozwiązywanie problemów z aplikacją. Aby wyświetlić pełny raport diagnostyczny interesującej kategorii problemu, można użyć skrótów, które są udostępniane przez Genie. Skróty kafelków zapewniają bezpośredni sposób uzyskiwania dostępu do metryk diagnostycznych.

![Skróty kafelków](./media/app-service-diagnostics/tile-shortcuts-2.png)

Po kliknięciu tych kafelków zobaczysz listę tematów związanych z problemem opisanym w kafelku. Te tematy zawierają fragmenty istotnych informacji z pełnego raportu. Możesz kliknąć dowolny z tych tematów, aby dokładniej zbadać problemy. Ponadto można kliknąć pozycję **Wyświetl pełny raport** , aby poznać wszystkie tematy na jednej stronie.

![Tematy](./media/app-service-diagnostics/application-logs-insights-3.png)

![Wyświetl pełny raport](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Raport diagnostyczny

Po wybraniu opcji dalszej analizy problemu przez kliknięcie tematu można wyświetlić więcej szczegółów dotyczących tematu często uzupełnione o wykresy i przestawki. Raport diagnostyczny może być zaawansowanym narzędziem do lokalizowania problemu z aplikacją.

![Raport diagnostyczny](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Health Checkup

Jeśli nie wiesz, co się stało z Twoją aplikacją lub nie wiesz, gdzie rozpocząć rozwiązywanie problemów, to dobrym miejscem do uruchomienia programu Health Checkup. Program Health Checkup analizuje aplikacje, aby zapewnić szybkie i interaktywne Omówienie, które wskazuje na to, co jest zdrowe i co jest niewłaściwe, co pozwala sprawdzić, gdzie warto zbadać problem. Interfejs inteligentny i interaktywny zapewnia wskazówki w procesie rozwiązywania problemów. Program Health Checkup jest zintegrowany z Genie środowisko dla aplikacji systemu Windows i raportów diagnostycznych usługi Web App w dół dla aplikacji Linux.

### <a name="health-checkup-graphs"></a>Wykresy w programie Health Checkup

W programie Health Checkup znajdują się cztery różne wykresy.

- **żądania i błędy:** Wykres przedstawiający liczbę żądań wykonanych w ciągu ostatnich 24 godzin wraz z błędami serwera HTTP.
- **wydajność aplikacji:** Wykres przedstawiający czas odpowiedzi w ciągu ostatnich 24 godzin dla różnych grup percentylu.
- **Użycie procesora CPU:** Wykres przedstawiający całkowity procent użycia procesora CPU na wystąpienie w ciągu ostatnich 24 godzin.  
- **użycie pamięci:** Wykres przedstawiający całkowity procent użycia pamięci fizycznej na wystąpienie w ciągu ostatnich 24 godzin.

![Health Checkup](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Zbadaj problemy związane z kodem aplikacji (tylko w przypadku aplikacji systemu Windows)

Ponieważ wiele problemów z aplikacjami jest związanych z problemami w kodzie aplikacji, App Service Diagnostics integruje się z [Application Insights](../azure-monitor/app/app-insights-overview.md) , aby wyróżnić wyjątki i problemy zależności w celu skorelowania z wybranym przestojem. Application Insights musi być włączona osobno.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Aby wyświetlić Application Insights wyjątki i zależności, wybierz pozycję **aplikacja sieci Web aplikacje** lub wolne kafelki **aplikacji sieci Web** .

### <a name="troubleshooting-steps-only-for-windows-app"></a>Kroki rozwiązywania problemów (tylko w przypadku aplikacji systemu Windows)

Jeśli w ciągu ostatnich 24 godzin wykryto problem z określoną kategorią problemu, można wyświetlić pełny raport diagnostyczny, a Diagnostyka App Service może monitować o wyświetlenie dalszych porad dotyczących rozwiązywania problemów oraz następnych kroków w celu uzyskania większej funkcjonalności.

![Application Insights i rozwiązywanie problemów oraz następne kroki](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Narzędzia diagnostyczne (tylko dla aplikacji systemu Windows)

Narzędzia diagnostyczne zawierają bardziej zaawansowane narzędzia diagnostyczne, które ułatwiają badanie problemów z kodem aplikacji, powolnej, parametrów połączenia i nie tylko. i aktywne narzędzia, które pomagają ograniczyć problemy związane z użyciem procesora CPU, żądaniami i pamięcią.

### <a name="proactive-cpu-monitoring"></a>Aktywne monitorowanie procesora

Aktywne monitorowanie procesora CPU zapewnia łatwy i aktywny sposób podejmowania akcji, gdy aplikacja lub proces podrzędny aplikacji zużywa duże zasoby procesora CPU. Można ustawić własne reguły progu procesora CPU, aby tymczasowo wyeliminować problemy związane z dużym procesorem CPU do momentu znalezienia rzeczywistej przyczyny nieoczekiwanego problemu. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z procesorami przed ich wdrożeniem](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Aktywne monitorowanie procesora](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Funkcja autonaprawy i proaktywna funkcja autonaprawy

Funkcja autonaprawy jest akcją zaradczą, którą można wykonać, gdy aplikacja ma nieoczekiwane zachowanie. Możesz ustawić własne reguły na podstawie liczby żądań, wolnego żądania, limitu pamięci i kodu stanu HTTP, aby wyzwolić akcje zaradcze. Użyj narzędzia, aby tymczasowo ograniczyć nieoczekiwane zachowanie do momentu znalezienia głównej przyczyny problemu. Aby uzyskać więcej informacji, zobacz temat [ogłaszanie nowego środowiska autonaprawy w diagnostyce usługi App Service](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Funkcja autonaprawy](./media/app-service-diagnostics/auto-healing-10.png)

Podobnie jak w przypadku aktywnego monitorowania procesora, proaktywne Samonaprawianie to rozwiązanie, które pozwala ograniczyć nieoczekiwane zachowanie aplikacji. Proaktywna funkcja automatycznego naprawiania ponownie uruchamia aplikację, gdy App Service ustali, że aplikacja jest w stanie nieodwracalnym. Aby uzyskać więcej informacji, zobacz [wprowadzenie do proaktywnego Autokorekty](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Nawigator i Analiza zmian (tylko dla aplikacji systemu Windows)

W dużym zespole z ciągłą integracją i w przypadku, gdy aplikacja ma wiele zależności, może być trudne do lokalizowania konkretnej zmiany powodującej zachowanie złej kondycji. Nawigator pomaga uzyskać wgląd w topologię aplikacji przez automatyczne renderowanie mapy zależności aplikacji i wszystkich zasobów w tej samej subskrypcji. Nawigator pozwala wyświetlić skonsolidowaną listę zmian dokonanych przez aplikację i jej zależności oraz zawęzić zmiany powodujące zachowanie w złej kondycji. Dostęp do niego można uzyskać za pomocą **nawigatora** kafelków strony głównej i musi on zostać włączony przed użyciem go po raz pierwszy. Aby uzyskać więcej informacji, zobacz [uzyskiwanie wglądu w zależności aplikacji za pomocą nawigatora](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Domyślna strona nawigatora](./media/app-service-diagnostics/navigator-default-page-11.png)

![Widok różnic](./media/app-service-diagnostics/diff-view-12.png)

Dostęp do analiz zmian aplikacji można uzyskać za pomocą skrótów kafelków, **zmian aplikacji** i **awarii aplikacji** w celu zapewnienia **dostępności i wydajności** , dzięki czemu można używać jej jednocześnie z innymi metrykami. Przed użyciem tej funkcji należy ją najpierw włączyć. Aby uzyskać więcej informacji, zobacz temat [ogłaszanie nowego środowiska analizy zmian w programie App Service Diagnostics](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Opublikuj swoje pytania lub opinie w witrynie [UserVoice](https://feedback.azure.com/forums/169385-web-apps) , dodając w tytule "[diag]".