---
title: Odporność na awarie dzięki monitorowaniu i analizie przy użyciu Azure AD B2C | Microsoft Docs
description: Odporność na awarie dzięki monitorowaniu i analizie przy użyciu Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90b2cd4521613a7b449598f0d097a7ec1c2958c6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724546"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Odporność dzięki monitorowaniu i analizie

Monitorowanie maksymalizuje dostępność i wydajność aplikacji i usług. Oferuje kompleksowe rozwiązanie do zbierania, analizowania i działania na telemetrii z infrastruktury i aplikacji. Alerty z wyprzedzeniem powiadamiają Cię o wykrytych problemach z usługą lub aplikacjami. Umożliwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników końcowych usługi. [Usługa Azure AD log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) ułatwia analizowanie, wyszukiwanie dzienników inspekcji i dzienników logowania oraz tworzenie widoków niestandardowych.

## <a name="monitor-and-get-notified-through-alerts"></a>Monitoruj alerty i otrzymuj powiadomienia

Monitorowanie systemu i infrastruktury ma kluczowe znaczenie dla zapewnienia ogólnej kondycji usług. Zaczyna się od definicji metryk biznesowych, takich jak nowe przybycie użytkownika, stawki uwierzytelniania użytkownika końcowego i konwersja. Skonfiguruj takie wskaźniki do monitorowania. Jeśli planujesz nadchodzące przeskoki ze względu na ruch promocyjny lub świąteczny, Popraw szacunki dotyczące zdarzeń i odpowiadających im wzorców dla metryk firmy. Po zdarzeniu Wróć do poprzedniego testu porównawczego.

Podobnie, aby wykryć awarie lub zakłócenia wydajności, skonfigurowanie dobra linia bazowa, a następnie zdefiniowanie alertów jest niezbędną metodą szybkiego reagowania na pojawiające się problemy.

![Obraz przedstawia składniki monitorowania i analizy](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Jak zaimplementować monitorowanie i alerty

- **Monitorowanie**: Użyj [Azure monitor](../../active-directory-b2c/azure-monitor.md) do ciągłego monitorowania kondycji na podstawie kluczowych celów poziomu usług (SLO) i otrzymuj powiadomienia przy każdej zmianie krytycznej. Zacznij od zidentyfikowania zasad Azure AD B2C lub aplikacji jako krytycznego składnika firmy, którego Kondycja ma być monitorowana w celu utrzymania celu SLO. Zidentyfikuj kluczowe wskaźniki, które są wyrównane z SLO.
Na przykład Śledź następujące metryki, ponieważ nagłe porzucenie w jednej z nich spowoduje utratę firmy.

  - **Łączna liczba żądań**: całkowita wartość "n" liczby żądań wysłanych do zasad Azure AD B2C.

  - **Częstotliwość powodzeń (%)**: pomyślne żądania/łączna liczba żądań.

  Dostęp do [kluczowych wskaźników](../../active-directory-b2c/view-audit-logs.md) w usłudze [Application Insights](../../active-directory-b2c/analytics-with-application-insights.md) , w których są przechowywane Azure AD B2C dzienniki oparte na zasadach, dzienniki [inspekcji](../../active-directory-b2c/analytics-with-application-insights.md)i dzienniki logowania.  

   - **Wizualizacje**: używanie pulpitów nawigacyjnych kompilacji usługi log Analytics do wizualnego monitorowania kluczowych wskaźników.

   - **Bieżący okres**: Utwórz wykresy czasowe, aby wyświetlić zmiany w łącznej liczbie żądań i współczynniku sukcesu (%) w bieżącym okresie, na przykład bieżący tydzień.

   - **Poprzedni okres**: Utwórz wykresy danych czasowych, aby wyświetlić zmiany w łącznej liczbie żądań i współczynniku sukcesu (%) dla celów referencyjnych, na przykład w ubiegłym tygodniu, w ciągu pewnego poprzedniego okresu.

- **Alertowanie**: za pomocą usługi log Analytics Zdefiniuj [alerty](../../azure-monitor/platform/alerts-log.md) wyzwalane w przypadku nagłych zmian w kluczowych wskaźnikach. Te zmiany mogą mieć negatywny wpływ na SLO. Alerty korzystają z różnych form metod powiadomień, w tym wiadomości e-mail, wiadomości SMS i elementów webhook. Zacznij od zdefiniowania kryterium, które działa jako próg, względem którego zostanie wyzwolony alert. Na przykład:
  - Alert z nieoczekiwanym porzuceniem w łącznej liczbie żądań: Wyzwól alert, gdy łączna liczba porzucanych żądań zostanie nieoczekiwana. Na przykład jeśli w łącznej liczbie żądań w porównaniu z poprzednim okresem występuje 25%, Zgłoś alert.  
  - Alert dotyczący znaczącego spadku w współczynniku sukcesu (%): Wyzwól alert, gdy Częstotliwość powodzeń dla wybranych zasad znacznie spadnie.
  - Po odebraniu alertu Rozwiąż problem przy użyciu rozszerzenia [log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), [Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md)i [vs Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) Azure AD B2C. Po rozwiązaniu problemu i wdrożeniu zaktualizowanej aplikacji lub zasad kontynuuje monitorowanie kluczowych wskaźników do momentu powrotu z powrotem do normalnego zakresu.

- **Alerty usługi**: Użyj [alertów poziomu usługi Azure AD B2C](../../service-health/service-health-overview.md) , aby otrzymywać powiadomienia o problemach z usługami, planowanej konserwacji, poradniku kondycji i poradniku zabezpieczeń.

- **Raportowanie**: [korzystając z usługi log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), twórz raporty, które ułatwiają zapoznanie się z informacjami o użytkownikach, wyzwaniami technicznymi i możliwościami wzrostu.
  - **Pulpit nawigacyjny kondycji**: Tworzenie [niestandardowych pulpitów nawigacyjnych przy użyciu funkcji pulpitu nawigacyjnego platformy Azure](../../azure-monitor/learn/tutorial-app-dashboards.md) , która obsługuje dodawanie wykresów przy użyciu zapytań log Analytics. Na przykład Zidentyfikuj wzorzec udanych i niepowodzeń logowania, przyczyny niepowodzenia i dane telemetryczne dotyczące urządzeń używanych do żądania.
  - **Porzuć Azure AD B2C podróże**: Użyj [skoroszytu](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) , aby śledzić listę porzuconych Azure AD B2C podróży, w których użytkownik rozpoczął proces logowania lub rejestracji, ale nigdy go nie zakończył. Zawiera szczegółowe informacje na temat identyfikatora zasad i podziału kroków wykonywanych przez użytkownika przed opuszczeniem podróży.
  - **Azure AD B2C skoroszyty monitorowania**: Użyj [skoroszytów monitorowania](https://github.com/azure-ad-b2c/siem), w tym Azure AD B2C pulpitu nawigacyjnego, operacji uwierzytelniania wieloskładnikowego (MFA), raportu dostępu warunkowego i dzienników wyszukiwania według korelacji, aby uzyskać lepszy wgląd w kondycję środowiska Azure AD B2C.
  
## <a name="next-steps"></a>Następne kroki

- [Zasoby dotyczące odporności dla deweloperów Azure AD B2C](resilience-b2c.md)
  - [Odporne środowisko użytkownika końcowego](resilient-end-user-experience.md)
  - [Odporne interfejsy z procesami zewnętrznymi](resilient-external-processes.md)
  - [Odporność na najlepsze rozwiązania dla deweloperów](resilience-b2c-developer-best-practices.md)
- [Tworzenie odporności w infrastrukturze uwierzytelniania](resilience-in-infrastructure.md)
- [Zwiększanie odporności uwierzytelniania i autoryzacji w aplikacjach](resilience-app-development-overview.md)
