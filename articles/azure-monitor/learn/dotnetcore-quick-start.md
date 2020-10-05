---
title: Szybki Start ASP.NET Core — Azure Monitor Application Insights
description: Zawiera instrukcje umożliwiające szybkie skonfigurowanie ASP.NET Core aplikacji sieci Web na potrzeby monitorowania za pomocą Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: a12fe837bfd9606d07c7be71851880d2cebb9d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055485"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Rozpoczynanie monitorowania aplikacji internetowej ASP.NET Core

Usługa Azure Application Insights umożliwia łatwe monitorowanie dostępności, wydajności i użycia aplikacji internetowej. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika. 

Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania zestawu SDK Application Insights do istniejącej ASP.NET Core aplikacji sieci Web. Aby dowiedzieć się więcej o konfigurowaniu Application Insights bez wyewidencjonowania tego [artykułu](../app/asp-net-core.md)przez program Visual Studio.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

- [Zainstaluj program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z następującymi obciążeniami:
  - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
  - Tworzenie aplikacji na platformie Azure
- [Instalowanie zestawu SDK programu .NET Core 2.0](https://dotnet.microsoft.com/download)
- Wymagana jest subskrypcja platformy Azure i istniejąca aplikacja internetowa .NET Core.

Jeśli nie masz aplikacji sieci Web ASP.NET Core, możesz skorzystać z naszego przewodnika krok po kroku, aby [utworzyć aplikację ASP.NET Core i dodać Application Insights.](../app/asp-net-core.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Usługa Application Insights umożliwia zbieranie danych telemetrycznych z dowolnej aplikacji połączonej z Internetem, niezależnie od tego, czy jest to aplikacja lokalna czy aplikacja w chmurze. Aby rozpocząć wyświetlanie tych danych, wykonaj poniższe czynności.

1. Wybierz pozycję **Utwórz zasób**  >  **Narzędzia deweloperskie**  >  **Application Insights**.

   > [!NOTE]
   >Jeśli tworzysz zasób Application Insights, możesz dowiedzieć się więcej, odwiedzając dokument [tworzenie Application Insights](../app/create-new-resource.md) .

    Zostanie wyświetlone okno konfiguracji. Wypełnij pola wejściowe, używając poniższej tabeli.

   | Ustawienia        |  Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów, w której mają być hostowane dane usługi App Insights. istnieje możliwość utworzenia nowej lub użycia istniejącej grupy zasobów. |
   | **Lokalizacja** | East US | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

2. Kliknij pozycję **Utwórz**.



## <a name="configure-app-insights-sdk"></a>Konfigurowanie zestawu SDK usługi App Insights

1. Otwórz **projekt** aplikacji internetowej ASP.NET Core w programie Visual Studio, kliknij prawym przyciskiem myszy nazwę aplikacji w **Eksploratorze rozwiązań**, a następnie wybierz pozycję **Dodaj** > **Telemetria usługi Application Insights**.

    ![Dodawanie telemetrii usługi Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Kliknij przycisk **wprowadzenie**

3. Wybierz swoje konto i subskrypcję, > wybierz **istniejący zasób** utworzony w Azure Portal > kliknij pozycję **zarejestruj**.

4. Wybierz pozycję **projekt**  >  **Zarządzanie**  >  **pakietami NuGet źródło pakietów: NuGet.org**  >  **zaktualizować** pakiety SDK Application Insights do najnowszej wersji stabilnej.

5. Wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** (Ctrl + F5), aby uruchomić aplikację

    ![Menu Przegląd usługi Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Dane zaczną pojawiać się w portalu po 3–5 minutach. W przypadku aplikacji testowej o małym natężeniu ruchu należy pamiętać, że większość metryk jest przechwytywana tylko wtedy, gdy istnieją aktywne żądania lub operacje.

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Ponownie otwórz stronę **przeglądu** Application Insights w Azure Portal, wybierając pozycję **Strona główna** i w obszarze ostatnie zasoby wybierz utworzony wcześniej zasób, aby wyświetlić szczegółowe informacje o aktualnie uruchomionej aplikacji.

   ![Menu Przegląd usługi Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Kliknij pozycję **Mapa aplikacji**, aby uzyskać wizualny układ relacji zależności między składnikami aplikacji. Każdy składnik przedstawia kluczowe wskaźniki wydajności, takie jak obciążenie, wydajność, błędy i alerty.

   ![Mapa aplikacji](./media/dotnetcore-quick-start/5appmap.png)

3. Kliknij ikonę **Analiza aplikacji** ![ Widok ikona mapy aplikacji ](./media/dotnetcore-quick-start/006.png) **w obszarze Analiza**. Spowoduje to otwarcie strony **Analiza usługi Application Insights**, która udostępnia zaawansowany język zapytań na potrzeby analizy wszystkich danych zbieranych przez usługę Application Insights. W tym przypadku jest generowane zapytanie, które renderuje liczbę żądań w formie wykresu. Możesz pisać własne zapytania do analizy innych danych.

   ![Wykres analizy żądań użytkowników w danym okresie](./media/dotnetcore-quick-start/6analytics.png)

4. Wróć do strony **Przegląd** i sprawdź pulpity nawigacyjne kluczowych wskaźników wydajności.  Ten pulpit nawigacyjny przedstawia dane statystyczne dotyczące kondycji aplikacji, w tym liczbę żądań przychodzących, czas trwania tych żądań i błędy. 

   ![Wykresy osi czasu przeglądu kondycji](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Po lewej stronie kliknij pozycję **metryki**. Użyj Eksploratora metryk do zbadania kondycji i wykorzystania zasobu. Możesz kliknąć pozycję **Dodaj nowy wykres**, aby utworzyć dodatkowe widoki niestandardowe, lub wybrać pozycję **Edytuj**, aby zmodyfikować istniejące typy wykresów, wysokość, paletę kolorów, grupowanie i metryki. Na przykład można utworzyć wykres, który wyświetla średni czas ładowania strony w przeglądarce, wybierając pozycję "czas ładowania strony w przeglądarce" z listy rozwijanej metryki i "Średnia" z agregacji. Aby dowiedzieć się więcej o usłudze Azure Eksplorator metryk odwiedź stronę Rozpoczynanie [pracy z usługą azure Eksplorator metryk](../platform/metrics-getting-started.md).

     ![Karta metryk: Średni wykres czasu ładowania strony przeglądarki](./media/dotnetcore-quick-start/8metrics.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Po zakończeniu testowania można usunąć grupę zasobów i wszystkie powiązane zasoby. W tym celu wykonaj poniższe kroki.

> [!NOTE]
> Jeśli użyto istniejącej grupy zasobów, poniższe instrukcje nie będą działać i konieczne będzie tylko usunięcie poszczególnych zasobów Application Insights. Należy pamiętać, że usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów należących do tej grupy.

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij pozycję **myResourceGroup**.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym pozycję Grupa **zasobów** , a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyszukiwanie i diagnozowanie wyjątków w czasie wykonywania](./tutorial-runtime-exceptions.md)

