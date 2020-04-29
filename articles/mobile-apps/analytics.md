---
title: Informacje dotyczące użycia aplikacji mobilnych i zachowania użytkowników w usługach Visual Studio App Center i Azure
description: Dowiedz się więcej na temat usług takich jak App Center, które ułatwiają podejmowanie szybkich decyzji firmowych przez zrozumienie, jak użytkownicy korzystają z aplikacji mobilnej.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241080"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analizowanie i zrozumienie użycia aplikacji mobilnych
Jak dobrze rozumiesz, jak użytkownicy korzystają z aplikacji? Ile aktywnych użytkowników ma Twoja aplikacja i jak zmienia się użycie z upływem czasu? Jakie funkcje są używane i które z nich korzystają? Gdzie są oparte te użytkownicy? Ilu użytkowników korzysta z najnowszej wersji aplikacji? Wszystkie te pytania są ważne, aby zrozumieć, jak przekształcić swoją aplikację w pomyślną działalność. Aby odpowiedzieć na te rodzaje pytań dotyczących analizy użycia, należy zebrać dane użycia z aplikacji.

Im więcej informacji znajdziesz w danych, tym więcej można znaleźć sposoby ulepszania aplikacji i zapewnić zadowolenie użytkowników. Ważne jest, aby użyć danych w celu uzyskania szczegółowych informacji i upewnienia się, że użytkownicy zadbają.

## <a name="importance-of-analytics"></a>Znaczenie analiz
- Zapoznaj się z użytkownikami, sposobami ich współpracy z aplikacją i Dowiedz się, w jaki sposób można dostosować swoją aplikację i zapewnić doskonałe środowisko rozwoju firmy.
- Śledź metryki użycia, aby podejmować świadome decyzje dotyczące wprowadzania na rynek aplikacji i lepszego udostępniania swoich klientów.
- Zmierz wydajność aplikacji.
- Dowiedz się, które części wartości i wydajności dysku aplikacji.
- Uzyskuj szczegółowe informacje oparte na danych w kwestiach dotyczących zmian i przechowywania.

Użyj następujących usług, aby włączyć funkcję analizy aplikacji mobilnych.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/) pozwala rozwijać odbiorców, koncentrując się na ważnych kwestiach. Oferuje głębokie raportowanie i szczegółowe informacje o sesjach użytkowników, najważniejszych urządzeniach, wersjach systemu operacyjnego i analizie behawioralnej. Łatwo twórz niestandardowe zdarzenia, aby śledzić wszystko dzięki rozbudowanej analizie aplikacji.

   **Najważniejsze funkcje**
   - Śledź wzorce użycia, wdrażanie użytkowników i inne metryki zaangażowania bezpłatnie.
   - Zidentyfikuj trendy, zachowanie użytkowników i zaangażowanie za poorednictwem zdarzeń niestandardowych.
   - Uzyskaj wbudowane metryki i szczegółowe informacje dotyczące użycia aplikacji (codziennie, co tydzień, co miesiąc), sesji, właściwości urządzenia i demograficznych użytkowników na jednym pulpicie nawigacyjnym.
   - Ciągle Eksportuj wszystkie dane analizy App Center na platformę Azure, aby uzyskać nieograniczony czas przechowywania. App Center Analytics obsługuje Eksportowanie do usługi Azure Blob Storage i Azure Application Insights.
   - Integruj się z usługą Azure Application Insights, aby uzyskać jeszcze dokładniejsze informacje, takie jak przechowywanie, analizowanie lejka i kohorty.
   - Skorzystaj z jednowierszowej integracji z zestawem SDK, aby rozpocząć pracę w ciągu kilku minut.
   - Uzyskaj wsparcie dla platformy dla systemów iOS, Android, macOS, systemu tvOS, Xamarin, w systemach natywnych, Unity i Cordova.

   **Dokumentacja**
   - [Zarejestruj się w usłudze App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Wprowadzenie do usługi App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor zawiera [Application Insights](/azure/azure-monitor/app/app-insights-overview), które udostępnia narzędzia do zbierania i analizowania danych telemetrycznych w celu zmaksymalizowania wydajności i monitorowania aplikacji mobilnej. Korzystając z Application Insights, możesz skorzystać z usługi App Center Analytics, aby skonfigurować Eksportowanie do Application Insights. Application Insights może wysyłać zapytania, segmentować, filtrować i analizować dane telemetryczne zdarzeń niestandardowych z aplikacji poza narzędzia analityczne, które App Center zapewnia.

**Najważniejsze funkcje**
   - Wykonaj zapytanie względem danych telemetrycznych zdarzeń niestandardowych.
   - Filtrowanie danych telemetrycznych zdarzeń z zaawansowanymi możliwościami segmentacji.
   - Analizuj wzorce konwersji, przechowywania i nawigacji w aplikacji. Możesz użyć:
     - Lejki do analizowania i monitorowania szybkości konwersji.
     - Przechowywanie, aby analizować, jak dobrze aplikacja zachowuje użytkowników w czasie.
     - Skoroszyty do łączenia wizualizacji i tekstu w raport umożliwiający udostępnianie.
     - Kohorty nazwy i zapisu określonych grup użytkowników lub wydarzeń, aby można było łatwo odwoływać się do innych narzędzi analitycznych.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com/)
- [Analizuj swoją aplikację mobilną przy użyciu App Center i Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Korzystanie z App Center analizy z Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Usługa Azure PlayFab](https://playfab.com/) oferuje kompletną platformę zaplecza z usługami gier, analizą w czasie rzeczywistym i LiveOps, które są potrzebne do tworzenia światowej klasy gier połączonych z chmurą. Te usługi zmniejszają bariery do uruchomienia dla deweloperów gier. Oferują one zarówno duże, jak i małe Studios ekonomiczne rozwiązania programistyczne, które są skalowane w swoich grach. Usługi mogą pomóc Studios zaangażowanie, zachowanie i zarabiaj graczy. Dzięki PlayFab deweloperzy mogą używać inteligentnej chmury do kompilowania i obsługi gier, analizowania danych gier i ulepszania ogólnych środowisk gier.

**Najważniejsze funkcje**
   - Monitoruj pulpity nawigacyjne w czasie rzeczywistym.
   - Oceń wydajność gry, korzystając z najważniejszych metryk.
   - Zapoznaj się z podsumowaniem codziennych i miesięcznych wydajności gry za poorednictwem raportów generowanych automatycznie. Raporty można wyświetlać w Menedżerze gier i pobierać je do Twojej skrzynki odbiorczej codziennie.
   - Użyj testowania A/B, aby uruchomić eksperymenty i określić optymalne ustawienie dla określonej zmiennej.
   - Użyj segmentacji dla graczy, aby zdefiniować zautomatyzowane grupy graczy.
    
**Dokumentacja**
- [Portal PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analiza](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Przewodniki Szybki start](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
