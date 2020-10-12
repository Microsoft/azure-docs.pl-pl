---
title: Monitorowanie aplikacji platformy Docker na platformie Azure Application Insights | Microsoft Docs
description: Liczniki wydajności platformy Docker, zdarzenia i wyjątki mogą być wyświetlane na Application Insights wraz z danymi telemetrycznymi z aplikacji kontenerowych.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 272b4e8af7b1ed3d01d8af0979b56954585f795d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90977579"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Monitorowanie aplikacji platformy Docker w Application Insights (przestarzałe)

> [!NOTE]
> To rozwiązanie jest przestarzałe. Aby dowiedzieć się więcej na temat naszych bieżących inwestycji w monitorowanie kontenerów, zalecamy wyewidencjonowanie [Azure monitor dla kontenerów](../insights/container-insights-overview.md).

Zdarzenia cyklu życia i liczniki wydajności z kontenerów [platformy Docker](https://www.docker.com/) mogą być przedstawiane na Application Insights. Zainstaluj [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) obrazu w kontenerze na hoście, a następnie wyświetli liczniki wydajności dla hosta, a także inne obrazy.

Dzięki platformie Docker aplikacje w lekkich kontenerach są dystrybuowane z użyciem wszystkich zależności. Zostaną one uruchomione na dowolnym komputerze hosta, na którym jest uruchomiony aparat platformy Docker.

Po uruchomieniu [obrazu Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) na hoście platformy Docker uzyskasz następujące korzyści:

* Dane telemetryczne cyklu życia dotyczące wszystkich kontenerów uruchomionych na hoście — Uruchom, Zatrzymaj i tak dalej.
* Liczniki wydajności dla wszystkich kontenerów. PROCESOR, pamięć, użycie sieci i nie tylko.
* Jeśli [zainstalowano Application INSIGHTS SDK dla języka Java](./java-get-started.md) w aplikacjach działających w kontenerach, wszystkie dane telemetryczne tych aplikacji będą miały dodatkowe właściwości identyfikujące kontener i maszynę hosta. Jeśli na przykład masz wystąpienia aplikacji uruchomionej na więcej niż jednym hoście, możesz łatwo odfiltrować dane telemetryczne aplikacji według hosta.

## <a name="set-up-your-application-insights-resource"></a>Skonfiguruj zasób Application Insights

1. Zaloguj się do [Microsoft Azure Portal](https://azure.com) i otwórz zasób Application Insights dla swojej aplikacji. lub [Utwórz nowy](./create-new-resource.md). 
   
    *Którego zasobu użyć?* Jeśli aplikacje, które są uruchomione na hoście, zostały opracowane przez kogoś innego, należy [utworzyć nowy zasób Application Insights](./create-new-resource.md). Jest to miejsce, w którym można wyświetlać i analizować dane telemetryczne. (Dla typu aplikacji wybierz pozycję "ogólne").
   
    Ale jeśli jesteś deweloperem aplikacji, mamy nadzieję, że [dodano Application Insights zestawu SDK](./java-get-started.md) do każdego z nich. Jeśli są one wszystkimi składnikami pojedynczej aplikacji biznesowej, można skonfigurować wszystkie te elementy do wysyłania telemetrii do jednego zasobu, a następnie użyć tego samego zasobu do wyświetlenia danych o cyklu Docker i wydajności. 
   
    Trzeci scenariusz polega na tym, że większość aplikacji jest opracowywanych, ale do wyświetlania telemetrii są używane oddzielne zasoby. W takim przypadku prawdopodobnie chcesz również utworzyć osobny zasób dla danych platformy Docker.

2. Kliknij listę rozwijaną **podstawowe** i skopiuj klucz Instrumentacji. Służy do informowania zestawu SDK, gdzie należy wysłać dane telemetryczne.

Zadbaj o to, aby to okno przeglądarki było przydatne, ponieważ wkrótce powrócisz do swojej telemetrii.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Uruchamianie monitora Application Insights na hoście

Teraz, gdy masz już miejsce na wyświetlenie telemetrii, możesz skonfigurować aplikację zawierającą kontenery, która będzie zbierać i wysyłać dane.

1. Połącz z hostem platformy Docker.
2. Edytuj klucz Instrumentacji w tym poleceniu, a następnie uruchom go:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Na hoście platformy Docker wymagany jest tylko jeden obraz Application Insights. Jeśli aplikacja jest wdrażana na wielu hostach platformy Docker, należy powtórzyć polecenie na każdym hoście.

## <a name="update-your-app"></a>Aktualizowanie aplikacji
Jeśli aplikacja jest Instrumentacją [Application INSIGHTS SDK dla języka Java](./java-get-started.md), Dodaj następujący wiersz do pliku ApplicationInsights.xml w projekcie, pod `<TelemetryInitializers>` elementem:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Spowoduje to dodanie informacji platformy Docker, takich jak kontener i identyfikator hosta, do każdego elementu telemetrii wysyłanego z aplikacji.

## <a name="view-your-telemetry"></a>Wyświetlanie telemetrii
Wróć do zasobu Application Insights w Azure Portal.

Kliknij kafelek Docker.

Wkrótce zobaczysz dane przychodzące z aplikacji platformy Docker, zwłaszcza jeśli masz inne kontenery uruchomione na aparacie platformy Docker.

### <a name="docker-container-events"></a>Zdarzenia kontenera platformy Docker
![Zrzut ekranu pokazuje Wybieranie wyszukiwania, okno wyników wyszukiwania diagnostycznego ze strzałką wskazującą ze zdarzenia niestandardowego do okna z danymi niestandardowymi.](./media/docker/13.png)

Aby zbadać poszczególne zdarzenia, kliknij przycisk [Wyszukaj](./diagnostic-search.md). Wyszukaj i Filtruj, aby znaleźć żądane zdarzenia. Kliknij dowolne zdarzenie, aby uzyskać więcej szczegółów.

### <a name="exceptions-by-container-name"></a>Wyjątki według nazwy kontenera
![Zrzut ekranu przedstawia stronę Eksplorator metryk z wybranym wykresem, a okno Szczegóły wykresu otwiera się po prawej stronie.](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Kontekst platformy Docker został dodany do telemetrii aplikacji
Dane telemetryczne żądania wysyłane z aplikacji z zestawem SDK AI są wzbogacane przy użyciu informacji kontekstu platformy Docker.

## <a name="q--a"></a>Pytania i odpowiedzi
*Co Application Insights Powiadom mnie, że nie mogę pobrać z platformy Docker?*

* Szczegółowy podział liczników wydajności według kontenera i obrazu.
* Integruj dane kontenera i aplikacji na jednym pulpicie nawigacyjnym.
* [Wyeksportuj dane telemetryczne](export-telemetry.md) w celu dalszej analizy do bazy danych, Power BI lub innego pulpitu nawigacyjnego.

*Jak mogę pobrać telemetrię z aplikacji?*

* Zainstaluj zestaw SDK Application Insights w aplikacji. Dowiedz się, jak: [aplikacje sieci Web Java](./java-get-started.md), [aplikacje sieci Web systemu Windows](./asp-net.md).

## <a name="video"></a>Wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Następne kroki

* [Application Insights dla języka Java](./java-get-started.md)
* [Usługa Application Insights dla środowiska Node.js](./nodejs.md)
* [Application Insights ASP.NET](./asp-net.md)

