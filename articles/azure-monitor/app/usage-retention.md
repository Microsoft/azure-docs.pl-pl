---
title: Analizowanie przechowywania użytkowników aplikacji sieci Web za pomocą usługi Azure Application Insights
description: Ile użytkowników wraca do swojej aplikacji?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 45f3a7152add8d64d961feb055413760df01eb94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025615"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analiza przechowywania użytkowników dla aplikacji sieci Web za pomocą Application Insights

Funkcja przechowywania w [usłudze Azure Application Insights](./app-insights-overview.md) pomaga analizować liczbę użytkowników, którzy zwracają do aplikacji, oraz częstotliwość wykonywania określonych zadań lub osiągania celów. Na przykład po uruchomieniu witryny gry można porównać liczby użytkowników, którzy powracają do lokacji po utracie gry z liczbą zwracaną po wygraniu. Ta wiedza może pomóc w ulepszaniu środowiska użytkownika i strategii biznesowej.

## <a name="get-started"></a>Rozpoczęcie pracy

Jeśli nie widzisz jeszcze danych w narzędziu do przechowywania w portalu Application Insights, [Dowiedz się, jak rozpocząć pracę z narzędziami użycia](usage-overview.md).

## <a name="the-retention-tool"></a>Narzędzie przechowywania

![Narzędzie utrzymywania](./media/usage-retention/retention.png)

1. Na pasku narzędzi użytkownicy mogą tworzyć nowe raporty dotyczące przechowywania, otwierać istniejące raporty dotyczące przechowywania, zapisywać bieżący raport o przechowywaniu lub zapisywać jako, przywracać zmiany wprowadzone w zapisanych raportach, odświeżać dane w raporcie, udostępniać raporty pocztą e-mail lub łącze bezpośrednie i uzyskiwać dostęp do strony dokumentacji. 
2. Domyślnie przechowywanie pokazuje wszystkich użytkowników, którzy zostali dołączeni i pozostali w danym okresie. Możesz wybrać inną kombinację zdarzeń, aby zawęzić koncentrację do określonych działań użytkownika.
3. Dodaj co najmniej jeden filtr dla właściwości. Na przykład możesz skupić się na użytkownikach w określonym kraju lub regionie. Po ustawieniu filtrów kliknij przycisk **Aktualizuj** . 
4. Ogólny wykres przechowywania przedstawia podsumowanie przechowywania użytkowników w wybranym okresie. 
5. Siatka pokazuje liczbę użytkowników zachowywanych zgodnie z konstruktorem zapytań w #2. Każdy wiersz reprezentuje kohorta użytkowników, którzy wykonali każde zdarzenie w pokazanym okresie. Każda komórka w wierszu pokazuje, ile z kohorta zwraca co najmniej raz w późniejszym czasie. Niektórzy użytkownicy mogą wrócić w ciągu kilku okresów. 
6. Karty Insights pokazują pięć najważniejszych zdarzeń inicjujących i pięć najważniejszych zdarzeń, które umożliwiają użytkownikom lepsze zrozumienie ich raportu przechowywania. 

![Przesuwanie kursora myszy](./media/usage-retention/hover.png)

Użytkownicy mogą przemieszczać się nad komórkami w narzędziu do przechowywania, aby uzyskać dostęp do przycisku analizy i etykietek narzędzi objaśniających znaczenie komórki. Przycisk analiza umożliwia użytkownikom narzędzia do analizy z wstępnie wypełnionym zapytaniem, które generuje użytkowników z komórki. 

## <a name="use-business-events-to-track-retention&quot;></a>Śledzenie przechowywania przy użyciu zdarzeń służbowych

Aby uzyskać najbardziej przydatną analizę przechowywania, należy mierzyć zdarzenia reprezentujące znaczące działania biznesowe. 

Na przykład wielu użytkowników może otworzyć stronę w aplikacji bez odtwarzania widocznej gry. Śledzenie tylko wyświetleń stron spowodowałoby niedokładne oszacowanie liczby osób, które zwracają grę, gdy wcześniej cieszą się. Aby uzyskać jasne zdjęcie dla zwracanych odtwarzaczy, aplikacja powinna wysyłać niestandardowe zdarzenie, gdy użytkownik rzeczywiście się odtwarza.  

Dobrym sposobem jest kod zdarzeń niestandardowych, które reprezentują kluczowe akcje biznesowe i służą do analizy przechowywania. Aby przechwycić wynik gry, należy napisać wiersz kodu w celu wysłania niestandardowego zdarzenia do Application Insights. Jeśli napiszesz go w kodzie strony sieci Web lub w Node.JS, będzie to wyglądać następująco:

```JavaScript
    appinsights.trackEvent(&quot;won game");
```

Lub w kodzie serwera ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Dowiedz się więcej na temat pisania zdarzeń niestandardowych](./api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Następne kroki
- Aby włączyć środowiska użycia, Rozpocznij wysyłanie [zdarzeń niestandardowych](./api-custom-events-metrics.md#trackevent) lub [wyświetleń stron](./api-custom-events-metrics.md#page-views).
- Jeśli masz już wysłane zdarzenia niestandardowe lub widoki stron, zapoznaj się z narzędziami użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Użytkownicy, sesje, zdarzenia](usage-segmentation.md)
    - [Lejki](usage-funnels.md)
    - [Przepływy użytkownika](usage-flows.md)
    - [Skoroszyty](../visualize/workbooks-overview.md)
    - [Dodawanie kontekstu użytkownika](./usage-overview.md)