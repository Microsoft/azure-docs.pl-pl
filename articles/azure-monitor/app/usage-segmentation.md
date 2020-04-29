---
title: Analiza użytkowników, sesji i zdarzeń w usłudze Azure Application Insights
description: Analiza demograficzna użytkowników aplikacji sieci Web.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 56059304026b060e2215ce73e0e94e3200573a14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670988"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Analiza użytkowników, sesji i zdarzeń w Application Insights

Dowiedz się, w jaki sposób użytkownicy korzystają z aplikacji sieci Web, jakie są najbardziej interesujące strony, w których znajdują się Twoje osoby, oraz jakich przeglądarek i systemów operacyjnych używają. Analizuj dane telemetryczne biznesowe i użycia przy użyciu [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Zrzut ekranu przedstawiający Application Insights użytkowników](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Wprowadzenie

Jeśli nie widzisz jeszcze danych w blokach użytkownicy, sesje lub zdarzenia w portalu Application Insights, [Dowiedz się, jak rozpocząć pracę z narzędziami użycia](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Narzędzie do segmentacji użytkowników, sesji i zdarzeń

Trzy z nich używają tego samego narzędzia do wycinania i zbierania danych telemetrycznych z aplikacji internetowej z trzech perspektyw. Filtrując i dzieląc dane, możesz uzyskać wgląd w szczegółowe informacje o względnym użyciu różnych stron i funkcji.

* **Narzędzie użytkownicy**: ile osób użyło Twojej aplikacji i jej funkcji.  Użytkownicy są zliczane przy użyciu identyfikatorów anonimowych przechowywanych w plikach cookie przeglądarki. Jedna osoba korzystająca z różnych przeglądarek lub maszyn będzie liczyć jako więcej niż jednego użytkownika.
* **Narzędzie sesje**: ile sesji działania użytkownika obejmowało niektóre strony i funkcje aplikacji. Sesja jest liczona po połowie godziny nieaktywności użytkownika lub po 24 godzinach ciągłego użycia.
* **Narzędzie zdarzenia**: jak często są używane pewne strony i funkcje aplikacji. Widok strony jest liczony, gdy przeglądarka ładuje stronę z aplikacji, pod warunkiem, że zostały [one Instrumentacją](../../azure-monitor/app/javascript.md). 

    Zdarzenie niestandardowe reprezentuje jedno wystąpienie coś w aplikacji, często interakcję użytkownika, jak kliknięcie przycisku lub zakończenie pewnego zadania. Wstaw kod w aplikacji, aby [generować niestandardowe zdarzenia](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Wykonywanie zapytań dla określonych użytkowników

Poznaj różne grupy użytkowników, dostosowując opcje zapytania w górnej części narzędzia Użytkownicy:

* Pokaż: Wybierz kohorta użytkowników do przeanalizowania.
* Kto użył: wybierz niestandardowe zdarzenia i wyświetlenia stron.
* W trakcie: Wybierz zakres czasu.
* Według: Wybierz sposób tworzenia pakietów danych przez okres lub inną właściwość, taką jak Browser lub miasto.
* Podziel przez: wybierz właściwość, według której chcesz podzielić lub segmentować dane. 
* Dodaj filtry: Ogranicz zapytania do określonych użytkowników, sesji lub zdarzeń na podstawie ich właściwości, takich jak Browser lub miasto. 
 
## <a name="saving-and-sharing-reports"></a>Zapisywanie i udostępnianie raportów 
Możesz zapisywać raporty użytkowników, prywatnie, bezpośrednio w sekcji Moje raporty lub udostępnione wszystkim osobom z dostępem do tego zasobu Application Insights w sekcji Raporty udostępnione.

Aby udostępnić łącze do raportu użytkowników, sesji lub zdarzeń; Kliknij przycisk **Udostępnij** na pasku narzędzi, a następnie skopiuj link.

Aby udostępnić kopię danych w raporcie użytkownicy, sesje lub zdarzenia; Kliknij przycisk **Udostępnij** na pasku narzędzi, a następnie kliknij **ikonę słowa** , aby utworzyć dokument programu Word zawierający dane. Lub kliknij **ikonę słowa** powyżej wykresu głównego.

## <a name="meet-your-users"></a>Poznaj użytkowników

Sekcja **zapoznaj się z użytkownikami** zawiera informacje o pięciu przykładowych użytkownikach pasujących do bieżącego zapytania. Biorąc pod uwagę i eksplorowanie zachowań osób, oprócz agregacji, program może zapewnić wgląd w sposób, w jaki ludzie rzeczywiście korzystają z Twojej aplikacji.

## <a name="next-steps"></a>Następne kroki

- Aby włączyć środowiska użycia, Rozpocznij wysyłanie [zdarzeń niestandardowych](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetleń stron](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli masz już wysłane zdarzenia niestandardowe lub widoki stron, zapoznaj się z narzędziami użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](usage-retention.md)
    - [Przepływy użytkowników](usage-flows.md)
    - [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
    - [Dodawanie kontekstu użytkownika](usage-send-user-context.md)
