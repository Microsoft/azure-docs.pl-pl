---
title: Inteligentne wykrywanie w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Usługa Application Insights przeprowadza automatyczną głęboką analizę danych telemetrycznych aplikacji i ostrzega przed potencjalnymi problemami.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: ff9f88e1d2e643d04c4417283420217e7d496caf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536798"
---
# <a name="smart-detection-in-application-insights"></a>Wykrywanie inteligentne w usłudze Application Insights
 Inteligentne wykrywanie automatycznie ostrzega o potencjalnych problemach z wydajnością i anomaliach awarii w aplikacji sieci web. Przeprowadza proaktywną analizę danych telemetrycznych wysyłanych przez aplikację do [usługi Application Insights.](../../azure-monitor/app/app-insights-overview.md) Jeśli występuje nagły wzrost szybkości awarii lub nieprawidłowe wzorce wydajności klienta lub serwera, otrzymasz alert. Ta funkcja nie wymaga konfiguracji. Działa, jeśli aplikacja wysyła wystarczającą ilość danych telemetrycznych.

Dostęp do wykrywania wydanych przez inteligentne wykrywanie można uzyskać zarówno z otrzymywanych wiadomości e-mail, jak i z bloku Inteligentne wykrywanie.

## <a name="review-your-smart-detections"></a>Przejrzyj swoje inteligentne wykrywanie
Wykrywanie można wykrywać na dwa sposoby:

* **Otrzymasz wiadomość e-mail** z usługi Application Insights. Oto typowy przykład:
  
    ![Alert e-mail](./media/proactive-diagnostics/03.png)
  
    Kliknij duży przycisk, aby otworzyć więcej szczegółów w portalu.
* **Blok inteligentnego wykrywania** w usłudze Application Insights. Wybierz **opcję Inteligentne wykrywanie** w menu **Badaj,** aby wyświetlić listę ostatnich wykrywania.

![Wyświetlanie ostatnich wykrywania](./media/proactive-diagnostics/04.png)

Wybierz wykrywanie, aby wyświetlić jego szczegóły.

## <a name="what-problems-are-detected"></a>Jakie problemy są wykrywane?
Inteligentne wykrywanie wykrywa i powiadamia o różnych problemach, takich jak:

* [Inteligentne wykrywanie - Anomalie awarii](../../azure-monitor/app/proactive-failure-diagnostics.md). Używamy uczenia maszynowego, aby ustawić oczekiwaną szybkość żądań nie powiodło się dla aplikacji, korelując z obciążeniem i innymi czynnikami. Jeśli wskaźnik awarii wykracza poza oczekiwaną kopertę, wysyłamy alert.
* [Inteligentne wykrywanie - Anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md). Otrzymasz powiadomienia, jeśli czas odpowiedzi operacji lub czas trwania zależności spowalnia w porównaniu do historycznej linii bazowej lub jeśli identyfikujemy nietypowy wzorzec w czasie odpowiedzi lub czasie ładowania strony.   
* Ogólne degradacje i problemy, takie jak [degradacja śledzenia,](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity) [przeciek pamięci,](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak) [Nieprawidłowy wzrost głośności wyjątków](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) i [anty-wzorce zabezpieczeń.](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack)

(Łącza pomocy w każdym powiadomieniu zadają Ci odpowiednie artykuły).

## <a name="smart-detection-email-notifications"></a>Powiadomienia e-mail z wykrywaniem inteligentnym

Wszystkie reguły inteligentnego wykrywania, z wyjątkiem reguł oznaczonych jako _podgląd,_ są domyślnie skonfigurowane do wysyłania powiadomień e-mail po wykryciu wykrycia.

Konfigurowanie powiadomień e-mail dla określonej reguły inteligentnego wykrywania można wykonać, otwierając blok **Inteligentne ustawienia** wykrywania i wybierając regułę, która otworzy blok **Reguła edycji.**

Alternatywnie można zmienić konfigurację przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, [zobacz Zarządzanie regułami wykrywania inteligentnego usługi Application Insights przy użyciu szablonów usługi Azure Resource Manager.](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)

## <a name="video"></a>Film wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Następne kroki
Te narzędzia diagnostyczne ułatwiają sprawdzanie danych telemetrycznych z aplikacji:

* [Eksplorator metryki](../../azure-monitor/platform/metrics-charts.md)
* [Eksplorator wyszukiwania](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - potężny język zapytań](../../azure-monitor/log-query/get-started-portal.md)

Inteligentne wykrywanie jest całkowicie automatyczne. Ale może chcesz skonfigurować kilka alertów więcej?

* [Ręcznie skonfigurowane alerty metryk](../../azure-monitor/app/alerts.md)
* [Testy dostępności sieci Web](../../azure-monitor/app/monitor-web-app-availability.md) 

