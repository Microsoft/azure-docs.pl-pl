---
title: Narzędzie diagnostyczne i rozwiązujące
description: Dowiedz się, jak rozwiązać problemy z aplikacją w usłudze Azure App Service za pomocą narzędzia do diagnostyki i rozwiązywania problemów w witrynie Azure portal.
keywords: usługa aplikacji, usługa aplikacji platformy Azure, diagnostyka, pomoc techniczna, aplikacja internetowa, rozwiązywanie problemów, samopomoc
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: df8f68e47776f46ae2d1331f85bd76d6cfe17b80
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869940"
---
# <a name="azure-app-service-diagnostics-overview"></a>Omówienie diagnostyki usługi Azure App Service

Podczas uruchamiania aplikacji sieci web, chcesz być przygotowany na wszelkie problemy, które mogą wystąpić, z 500 błędów do użytkowników informujących, że witryna jest w dół. Diagnostyka usługi App Service to inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z aplikacją bez konieczności konfigurowania. Gdy napotkasz problemy z aplikacją, diagnostyka usługi App Service wskazuje, co jest nie tak, aby poprowadzić Cię do właściwych informacji, aby łatwiej i szybciej rozwiązać problem.

Mimo że to środowisko jest najbardziej przydatne, gdy masz problemy z aplikacją w ciągu ostatnich 24 godzin, wszystkie wykresy diagnostyczne są zawsze dostępne do analizy.

Diagnostyka usługi App Service działa nie tylko dla aplikacji w systemie Windows, ale także dla aplikacji w [systemie Linux/kontenery,](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) [środowisko usługi app service](https://docs.microsoft.com/azure/app-service/environment/intro)i usługi Azure [Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Otwórz diagnostykę usługi app service

Aby uzyskać dostęp do diagnostyki usługi App Service, przejdź do aplikacji sieci Web usługi App Service lub środowiska usługi app service w [witrynie Azure portal](https://portal.azure.com). W lewej nawigacji kliknij przycisk **Diagnozuj i rozwiązuj problemy**.

W przypadku funkcji platformy Azure przejdź do aplikacji funkcji, a w górnej części nawigacji kliknij **na platformie funkcje**i wybierz pozycję **Diagnozuj i rozwiązuj problemy** z sekcji **Zarządzanie zasobami.**

Na stronie głównej diagnostyki usługi app service możesz wybrać kategorię, która najlepiej opisuje problem z aplikacją przy użyciu słów kluczowych na każdym kafelku strony głównej. Ponadto na tej stronie można znaleźć **narzędzia diagnostyczne** dla aplikacji systemu Windows. Zobacz [Narzędzia diagnostyczne (tylko dla aplikacji systemu Windows).](#diagnostic-tools-only-for-windows-app)

![Strona główna](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Jeśli aplikacja jest w dół lub wykonywania powolny, można [zebrać śledzenia profilowania,](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) aby zidentyfikować główną przyczynę problemu. Profilowanie jest lekkie i jest przeznaczone do scenariuszy produkcyjnych.
>

## <a name="interactive-interface"></a>Interaktywny interfejs

Po wybraniu kategorii strony głównej, która najlepiej pasuje do problemu aplikacji, interaktywny interfejs diagnostyki usługi App Service, Genie, może poprowadzić Cię przez diagnozowanie i rozwiązywanie problemów z aplikacją. Możesz użyć skrótów kafelków dostarczonych przez firmę Genie, aby wyświetlić pełny raport diagnostyczny kategorii problemu, która Cię interesuje. Skróty kafelków zapewniają bezpośredni sposób uzyskiwania dostępu do metryk diagnostycznych.

![Skróty kafelków](./media/app-service-diagnostics/tile-shortcuts-2.png)

Po kliknięciu tych kafelków można wyświetlić listę tematów związanych z problemem opisanym na kafelku. Tematy te zawierają fragmenty istotnych informacji z pełnego raportu. Możesz kliknąć dowolny z tych tematów, aby dokładniej zbadać problemy. Możesz również kliknąć **wyświetl pełny raport,** aby zapoznać się ze wszystkimi tematami na jednej stronie.

![Tematy](./media/app-service-diagnostics/application-logs-insights-3.png)

![Zobacz pełny raport](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Raport diagnostyczny

Po wybraniu dalszego zbadania problemu, klikając temat, możesz wyświetlić więcej szczegółów na ten temat często uzupełnionych wykresami i markdowns. Raport diagnostyczny może być potężnym narzędziem do wskazywania problemu z aplikacją.

![Raport diagnostyczny](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Kontrola kondycji

Jeśli nie wiesz, co jest nie tak z aplikacją lub nie wiesz, od czego zacząć rozwiązywanie problemów, kontrola kondycji jest dobrym miejscem do rozpoczęcia. Sprawdzanie kondycji analizuje aplikacje, aby zapewnić szybki, interaktywny przegląd, który wskazuje, co jest zdrowe, a co nie, informując, gdzie szukać, aby zbadać problem. Jego inteligentny i interaktywny interfejs zapewnia wskazówki dotyczące procesu rozwiązywania problemów. Sprawdzanie kondycji jest zintegrowane z interfejsem Genie dla aplikacji systemu Windows i raportem diagnostycznym aplikacji sieci Web dla aplikacji systemu Linux.

### <a name="health-checkup-graphs"></a>Wykresy kontroli kondycji

Istnieją cztery różne wykresy w kontroli kondycji.

- **wniosków i błędów:** Wykres przedstawiający liczbę żądań złożonych w ciągu ostatnich 24 godzin wraz z błędami serwera HTTP.
- **wydajność aplikacji:** Wykres, który pokazuje czas odpowiedzi w ciągu ostatnich 24 godzin dla różnych grup percentylów.
- **Użycie procesora:** Wykres, który pokazuje ogólny procent użycia procesora CPU na wystąpienie w ciągu ostatnich 24 godzin.  
- **użycie pamięci:** Wykres, który pokazuje ogólny procent użycia pamięci fizycznej na wystąpienie w ciągu ostatnich 24 godzin.

![Kontrola kondycji](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Badanie problemów z kodem aplikacji (tylko dla aplikacji systemu Windows)

Ponieważ wiele problemów z aplikacją są związane z problemami w kodzie aplikacji, diagnostyki usługi App Service integruje się z [usługi Application Insights,](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) aby wyróżnić wyjątki i problemy z zależnościami, aby skorelować z wybranym przestojem. Usługa Application Insights musi być włączona oddzielnie.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Aby wyświetlić wyjątki i zależności usługi Application Insights, wybierz **skróty do** kafelków aplikacji sieci web w dół lub **skróty powolnego** kafelków aplikacji sieci Web.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Kroki rozwiązywania problemów (tylko dla aplikacji systemu Windows)

Jeśli problem zostanie wykryty z określoną kategorią problemu w ciągu ostatnich 24 godzin, można wyświetlić pełny raport diagnostyczny, a diagnostyka usługi App Service może monitować o wyświetlenie większej liczby porad dotyczących rozwiązywania problemów i następnych kroków, aby uzyskać bardziej z przewodnikiem.

![Wgląd w aplikacje i rozwiązywanie problemów oraz kolejne kroki](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Narzędzia diagnostyczne (tylko dla aplikacji systemu Windows)

Narzędzia diagnostyczne obejmują bardziej zaawansowane narzędzia diagnostyczne, które pomagają badać problemy z kodem aplikacji, powolność, parametry połączenia i inne. i proaktywne narzędzia, które pomagają ograniczyć problemy związane z użyciem procesora, żądaniami i pamięcią.

### <a name="proactive-cpu-monitoring"></a>Proaktywne monitorowanie procesora

Proaktywne monitorowanie procesora CPU zapewnia łatwy, proaktywny sposób podejmowania działań, gdy proces aplikacji lub dziecka dla aplikacji zużywa wysokie zasoby procesora. Można ustawić własne reguły progu procesora CPU tymczasowo ograniczyć problem z wysokim procesorem, dopóki nie zostanie znaleziona rzeczywista przyczyna nieoczekiwanego problemu. Aby uzyskać więcej informacji, zobacz [Ograniczanie problemów z procesorem przed ich wystąpieniem.](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)

![Proaktywne monitorowanie procesora](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Automatyczne gojenie i proaktywne autoleczenie

Automatyczne goleczenie jest akcja ograniczająca zagrożenie, którą można podjąć, gdy aplikacja ma nieoczekiwane zachowanie. Można ustawić własne reguły na podstawie liczby żądań, powolne żądanie, limit pamięci i kod stanu HTTP do wyzwalania akcji ograniczających zagrożenie. Użyj narzędzia, aby tymczasowo ograniczyć nieoczekiwane zachowanie, dopóki nie znajdziesz głównej przyczyny. Aby uzyskać więcej informacji, zobacz [Ogłaszanie nowego środowiska automatycznego gojenia w diagnostyce usługi aplikacji](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Automatyczne gojenie](./media/app-service-diagnostics/auto-healing-10.png)

Podobnie jak proaktywne monitorowanie procesora, proaktywne automatyczne gojenie jest rozwiązaniem "pod klucz" w celu ograniczenia nieoczekiwanego zachowania aplikacji. Proaktywne automatyczne gojenie uruchamia ponownie aplikację, gdy usługa App Service stwierdzi, że aplikacja jest w stanie nieodwracalnym. Aby uzyskać więcej informacji, zobacz [Wprowadzenie proaktywnego automatycznego leczenia](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Nawigator i analiza zmian (tylko dla aplikacji systemu Windows)

W dużym zespole z ciągłej integracji i gdzie aplikacja ma wiele zależności, może być trudne do określenia określonej zmiany, która powoduje zachowanie w złej kondycji. Nawigator pomaga uzyskać wgląd w topologię aplikacji, automatycznie renderując mapę zależności aplikacji i wszystkie zasoby w tej samej subskrypcji. Nawigator umożliwia wyświetlanie skonsolidowanej listy zmian wprowadzonych przez aplikację i jej zależności i zawęzić zmiany powodujące nieprawidłowe zachowanie. Dostęp do niego można uzyskać za pośrednictwem kafelka strony głównej **Navigator** i musi być włączony przed pierwszym użyciem. Aby uzyskać więcej informacji, zobacz Wgląd w zależności aplikacji za [pomocą aplikacji Navigator](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Domyślna strona nawigatora](./media/app-service-diagnostics/navigator-default-page-11.png)

![Widok różnicy](./media/app-service-diagnostics/diff-view-12.png)

Analiza zmian dla zmian w aplikacjach jest dostępna za pośrednictwem skrótów kafelków, **zmian aplikacji** i **awarii aplikacji** w dostępności **i wydajności,** dzięki czemu można jej używać jednocześnie z innymi metrykami. Przed użyciem funkcji należy ją najpierw włączyć. Aby uzyskać więcej informacji, zobacz [Ogłaszanie nowego środowiska analizy zmian w diagnostyce usługi app service](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Zamieść swoje pytania lub opinie na [UserVoice,](https://feedback.azure.com/forums/169385-web-apps) dodając "[Diag]" w tytule.
