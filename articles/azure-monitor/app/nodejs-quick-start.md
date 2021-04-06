---
title: 'Szybki Start: monitorowanie Node.js przy użyciu Azure Monitor Application Insights'
description: Zawiera instrukcje umożliwiające szybkie skonfigurowanie Node.js aplikacji sieci Web na potrzeby monitorowania za pomocą Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 75ac0cb2616eb86026218d2971ad192c1a557e5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100628964"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Szybki Start: Rozpoczynanie monitorowania Node.js aplikacji sieci Web za pomocą platformy Azure Application Insights

W tym przewodniku szybki start dodasz zestaw SDK Application Insights w wersji 0,22 dla Node.js do istniejącej aplikacji sieci Web Node.js.

Usługa Azure Application Insights umożliwia łatwe monitorowanie dostępności, wydajności i użycia aplikacji internetowej. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika. Zestaw SDK w wersji 0.20 lub nowszej umożliwia monitorowanie popularnych pakietów innych firm, w tym MongoDB, MySQL i Redis.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Działająca aplikacja Node.js.

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Application Insights może zbierać dane telemetryczne z dowolnej aplikacji połączonej z Internetem, niezależnie od tego, czy działa ona lokalnie, czy w chmurze. Aby rozpocząć wyświetlanie tych danych, wykonaj poniższe czynności.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Utwórz zasób**  >  **Narzędzia deweloperskie**  >  **Application Insights**.

   ![Dodawanie zasobu usługi Azure Application Insights](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Jeśli tworzysz zasób Application Insights, możesz dowiedzieć się więcej, odwiedzając dokument [tworzenie Application Insights](../app/create-new-resource.md) .

   Zostanie wyświetlona strona konfiguracji; Skorzystaj z poniższej tabeli, aby wypełnić pola wejściowe. 

    | Ustawienia        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów do hostowania danych AppInsights. istnieje możliwość utworzenia nowej lub użycia istniejącej grupy zasobów. |
   | **Lokalizacja** | East US | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

3. Wybierz przycisk **Utwórz**.

## <a name="configure-appinsights-sdk"></a>Konfigurowanie zestawu SDK AppInsights

1. Wybierz pozycję **Przegląd** i skopiuj **klucz Instrumentacji** aplikacji.

   ![Wyświetlanie klucza Instrumentacji Application Insights](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Dodaj zestaw SDK usługi Application Insights dla środowiska Node.js do aplikacji. W folderze głównym aplikacji uruchom polecenie:

   ```bash
   npm install applicationinsights --save
   ```

3. Edytuj pierwszy plik *. js* aplikacji i Dodaj dwa wiersze poniżej do najwyższej części skryptu. Jeśli używasz [ aplikacji szybkiego startuNode.js](../../app-service/quickstart-nodejs.md), zmodyfikuj plik *index.js* . Zamień `<instrumentation_key>` na klucz Instrumentacji usługi Application Insight. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Uruchom ponownie aplikację.

> [!NOTE]
> Dane zaczną pojawiać się w portalu po 3–5 minutach. W przypadku aplikacji testowej o małym natężeniu ruchu należy pamiętać, że większość metryk jest przechwytywana tylko wtedy, gdy istnieją aktywne żądania lub operacje.

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Możesz teraz ponownie otworzyć stronę **Przegląd** usługi Application Insights w witrynie Azure Portal, na której pobrano klucz instrumentacji, w celu wyświetlenia szczegółowych informacji o obecnie uruchomionej aplikacji.

   ![Menu przegląd Application Insights](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Wybierz pozycję **Mapa aplikacji** , aby uzyskać wizualny układ relacji zależności między składnikami aplikacji. Każdy składnik przedstawia kluczowe wskaźniki wydajności, takie jak obciążenie, wydajność, błędy i alerty.

   ![Application Insights mapowanie aplikacji](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Wybierz ikonę **Analiza aplikacji** ![ Widok ikon mapy aplikacji ](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **w obszarze Analiza**.  Ta akcja powoduje otwarcie **Application Insights Analytics**, który oferuje bogaty język zapytań umożliwiający analizowanie wszystkich danych zebranych przez Application Insights. W tym przypadku jest generowane zapytanie, które renderuje liczbę żądań w formie wykresu. Możesz pisać własne zapytania do analizy innych danych.

   ![Wykresy analityczne Application Insights](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Wróć do strony **Przegląd** i sprawdź grafy kluczowych wskaźników wydajności.  Ten pulpit nawigacyjny przedstawia dane statystyczne dotyczące kondycji aplikacji, w tym liczbę żądań przychodzących, czas trwania tych żądań i błędy.

   ![Wykresy osi czasu przegląd kondycji Application Insights](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   Aby włączyć wykres **Wyświetlenie strony — czas ładowania** z danymi **telemetrycznymi po stronie klienta**, dodaj ten skrypt na każdej stronie, którą chcesz śledzić:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Po lewej stronie wybierz pozycję **Metryki**. Użyj Eksploratora metryk do zbadania kondycji i wykorzystania zasobu. Możesz wybrać opcję **Dodaj nowy wykres** , aby utworzyć dodatkowe widoki niestandardowe lub wybrać pozycję **Edytuj** , aby zmodyfikować istniejące typy wykresów, wysokość, paletę kolorów, grupowania i metryki. Na przykład można utworzyć wykres, który wyświetla średni czas ładowania strony w przeglądarce, wybierając pozycję "czas ładowania strony w przeglądarce" z listy rozwijanej metryki i "Średnia" z agregacji. Aby dowiedzieć się więcej o usłudze Azure Eksplorator metryk, odwiedź stronę [wprowadzenie do usługi azure Eksplorator metryk](../essentials/metrics-getting-started.md).

   ![Wykres metryk serwera Application Insights](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Aby dowiedzieć się więcej na temat monitorowania Node.js, zapoznaj się z [dodatkową dokumentacją Node.js AppInsights](../app/nodejs.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu testowania można usunąć grupę zasobów i wszystkie powiązane zasoby. W tym celu wykonaj poniższe kroki.

> [!NOTE]
> Jeśli użyto istniejącej grupy zasobów, poniższe instrukcje nie będą działać i konieczne będzie tylko usunięcie poszczególnych zasobów Application Insights. Należy pamiętać, że usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów underyling, które są członkami tej grupy.

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz pozycję **myResourceGroup**.
2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym pozycję Grupa **zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyszukiwanie i diagnozowanie problemów z wydajnością](../logs/log-query-overview.md)

