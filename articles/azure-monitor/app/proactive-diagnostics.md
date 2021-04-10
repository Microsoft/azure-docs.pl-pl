---
title: Inteligentne wykrywanie na platformie Azure Application Insights | Microsoft Docs
description: Application Insights przeprowadza automatyczne głębokiej analizy danych telemetrycznych aplikacji i ostrzega o potencjalnych problemach.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 974795e853416ff7a4c051530b050fc663c9423d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585660"
---
# <a name="smart-detection-in-application-insights"></a>Wykrywanie inteligentne w usłudze Application Insights
 Inteligentne wykrywanie automatycznie ostrzega o potencjalnych problemach z wydajnością i anomaliach niepowodzeń w aplikacji sieci Web. Wykonuje proaktywnie analizę danych telemetrycznych wysyłanych przez aplikację do [Application Insights](./app-insights-overview.md). W przypadku nagłego wzrostu współczynnika błędów lub nietypowych wzorców wydajności klienta lub serwera zostanie wyświetlony alert. Ta funkcja nie wymaga konfiguracji. Działa, gdy aplikacja wysyła wystarczającą ilość danych telemetrycznych.

Możesz uzyskać dostęp do wykrycia wystawionego przez Inteligentne wykrywanie zarówno z otrzymywanych wiadomości e-mail, jak i z bloku inteligentnego wykrywania.

## <a name="review-your-smart-detections"></a>Przejrzyj Inteligentne wykrywanie
Wykrywanie wykrywania można przeprowadzić na dwa sposoby:

* **Otrzymasz wiadomość e-mail** od Application Insights. Oto typowy przykład:
  
    ![Alert e-mail](./media/proactive-diagnostics/03.png)
  
    Kliknij przycisk Big (duży), aby otworzyć więcej szczegółów w portalu.
* **Blok inteligentnego wykrywania** w Application Insights. Wybierz pozycję **Inteligentne wykrywanie** w menu **Zbadaj** , aby wyświetlić listę ostatnich wykryć.

![Wyświetlanie ostatnich wykryć](./media/proactive-diagnostics/04.png)

Wybierz wykrywanie, aby wyświetlić jego szczegóły.

## <a name="what-problems-are-detected"></a>Jakie problemy są wykrywane?
Inteligentne wykrywanie wykrywa różne problemy, takie jak:

* [Anomalie wykrywania inteligentnego](./proactive-failure-diagnostics.md). Używamy uczenia maszynowego, aby ustawić oczekiwaną liczbę żądań zakończonych niepowodzeniem dla aplikacji, skorelowanie z obciążeniem i innymi czynnikami. Jeśli współczynnik błędów wykracza poza oczekiwaną kopertę, wyślemy alert.
* [Wykrywanie inteligentne — anomalie wydajności](./proactive-performance-diagnostics.md). Otrzymuję powiadomienia, jeśli czas odpowiedzi operacji lub czasu trwania zależności jest wolniejszy w porównaniu do historycznej linii bazowej lub w przypadku zidentyfikowania nietypowego wzorca w czasie odpowiedzi lub w czasie ładowania strony.   
* Ogólne degradacje i problemy, takie jak [spadek wydajności](./proactive-trace-severity.md), [przeciek pamięci](./proactive-potential-memory-leak.md), [nietypowy wzrost ilości](./proactive-exception-volume.md) i [antywzorce zabezpieczeń](./proactive-application-security-detection-pack.md).

(Linki pomocy w poszczególnych powiadomieniach przeprowadzą Cię do odpowiednich artykułów).

## <a name="smart-detection-email-notifications"></a>Powiadomienia e-mail dotyczące wykrywania inteligentnego

Wszystkie reguły inteligentnego wykrywania, z wyjątkiem reguł oznaczonych jako _wersja zapoznawcza_, są domyślnie skonfigurowane do wysyłania powiadomień e-mail po znalezieniu wykrycia.

Konfigurowanie powiadomień e-mail dla konkretnej reguły wykrywania inteligentnego można wykonać, otwierając blok **Ustawienia** inteligentnego wykrywania i wybierając regułę, co spowoduje otwarcie bloku **Edytuj regułę** .

Alternatywnie można zmienić konfigurację przy użyciu szablonów Azure Resource Manager. Aby uzyskać więcej informacji, [Zobacz temat zarządzanie Application Insights regułami wykrywania automatycznego przy użyciu szablonów Azure Resource Manager](./proactive-arm-config.md) .

## <a name="video"></a>Wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Następne kroki
Te narzędzia diagnostyczne ułatwiają inspekcję danych telemetrycznych z aplikacji:

* [Eksplorator metryk](../essentials/metrics-charts.md)
* [Eksplorator wyszukiwania](./diagnostic-search.md)
* [Analiza — zaawansowany język zapytań](../logs/log-analytics-tutorial.md)

Inteligentne wykrywanie jest całkowicie automatyczne. Ale być może chcesz skonfigurować więcej alertów?

* [Ręcznie skonfigurowane alerty metryk](../alerts/alerts-log.md)
* [Testy dostępności sieci Web](./monitor-web-app-availability.md)