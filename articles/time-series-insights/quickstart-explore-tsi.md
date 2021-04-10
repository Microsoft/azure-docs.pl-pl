---
title: 'Przewodnik Szybki Start: Eksplorowanie środowiska demonstracyjnego Gen2 — Azure Time Series Insights Gen2 | Microsoft Docs'
description: Eksplorowanie najważniejszych funkcji środowiska demonstracyjnego Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 03/01/2021
ms.openlocfilehash: 25340af243217313ce6d3f7205c102d4bcd350b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698114"
---
# <a name="quickstart-explore-the-azure-time-series-insights-gen2-demo-environment"></a>Szybki start: Eksplorowanie środowiska demonstracyjnego usługi Azure Time Series Insights Gen2

Ten przewodnik Szybki Start umożliwia rozpoczęcie pracy z Azure Time Series Insights środowiskiem Gen2. W bezpłatnej wersji demonstracyjnej zawarto podstawowe funkcje, które zostały dodane do Azure Time Series Insights Gen2.

Środowisko demonstracyjne Azure Time Series Insights Gen2 zawiera firmę firmy Contoso, która działa w dwóch farmach z wiatru. Każda Farma ma 10 turbin. Każda turbina jest wyposażona w 20 czujników raportujących dane co minutę do usługi Azure IoT Hub. Czujniki zbierają informacje o warunkach pogodowych, skoku bloku i położeniu Yaw. Rejestrowane są również informacje na temat wydajności generatora, zachowania skrzyni biegów i monitorów bezpieczeństwa.

W tym przewodniku szybki start dowiesz się, jak za pomocą usługi Azure Time Series Insights Gen2 uzyskać szczegółowe informacje umożliwiające podejmowanie działań w danych firmy Contoso. Przeprowadzamy również krótką analizę głównych przyczyn w celu lepszego przewidywania błędów krytycznych i przeprowadzenia konserwacji.

> [!IMPORTANT]
> Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)   , jeśli go nie masz.

## <a name="explore-the-azure-time-series-insights-gen2-explorer-in-a-demo-environment"></a>Eksplorowanie Azure Time Series Insights Explorer Gen2 w środowisku demonstracyjnym

Eksplorator Azure Time Series Insights Gen2 demonstruje dane historyczne i analizę głównych przyczyn. Aby rozpocząć:

1. Przejdź do środowiska [demonstracyjnego farmy wiatrów firmy Contoso](https://insights.timeseries.azure.com/preview/samples) .  

1. Jeśli zostanie wyświetlony monit, zaloguj się do Azure Time Series Insights Gen2 Explorer za pomocą poświadczeń konta platformy Azure.

## <a name="work-with-historical-data"></a>Pracuj z danymi historycznymi

1. Wybierz **hierarchię contoso Windfarm**.

   [![Hierarchia contoso WindFarm](media/quickstart-explore/quick-start-contoso-1.png)](media/quickstart-explore/quick-start-contoso-1.png#lightbox)

1. W firmie **contoso 1** zapoznaj się z tematem wiatr turbin **W7**.

   [![W7 w firmie Contoso 1](media/quickstart-explore/quick-start-contoso-2.png)](media/quickstart-explore/quick-start-contoso-2.png#lightbox)

   1. Zmień zakres widoku na **1/1/17 20:00:00.00 do 3/10/17 20:00:00.00 (UTC)**.

      [![Widok zakresu](media/quickstart-explore/range-setting-1.png)](media/quickstart-explore/range-setting-1.png#lightbox)

      [![Ustawienie widoku zakresu](media/quickstart-explore/range-setting-2.png)](media/quickstart-explore/range-setting-2.png#lightbox)

   1. Dostosuj widok zakresu przy użyciu powiększenia **(+)** i out **(-)** i przesuwania paska suwaka.

      [![Dostosuj widok zakresu](media/quickstart-explore/view-range-setting.png)](media/quickstart-explore/view-range-setting.png#lightbox)

   1. Aby wybrać czujnik, wybierz pozycję **contoso roślina 1**  >  **W7**  >  **Generator system**  >  **GeneratorSpeed**. Następnie przejrzyj wyświetlane wartości.

      [![Szybkość generatora](media/quickstart-explore/quick-start-generator-speed-1.png)](media/quickstart-explore/quick-start-generator-speed-1.png#lightbox)

1. Niedawno firma Contoso wykryła pożar w turbinie **W7**. Opinie różnią się w zależności od tego, co spowodowało pożar. W Azure Time Series Insights Gen2 jest wyświetlany czujnik alertów o zapłonie, który został aktywowany podczas pożaru.

   1. Zmień zakres widoku na **3/9/17 20:00:00.00 do 3/10/17 20:00:00.00 (UTC)**.
   1. Wybierz pozycję FireAlert **systemu bezpieczeństwa**  >  .

      [![Firma Contoso znalazła ogień w programie wiatr turbin W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Zapoznaj się z innymi zdarzeniami w czasie działania, aby zrozumieć, co się stało. Wykorzystanie ropy naftowej i aktywne ostrzeżenia, które zostały wprowadzone tuż przed pożarem.

   1. Wybierz pozycję HydraulicOilPressure **System (gęstość**)  >  .
   1. Wybierz pozycję ActiveWarning **System (gęstość**)  >  .

      [![Przeglądanie innych zdarzeń w tym samym czasie](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Czujniki ropy naftowej i aktywne ostrzeżenia są napowietrzane bezpośrednio przed pożarem. Rozwiń wyświetlaną serię czasową, aby przejrzeć inne oznaki, które były oczywiste, co prowadzi do pożaru. Obie czujniki zmieniają się w miarę upływu czasu. Wahania wskazują, że wzorzec trwały i worrisome.

    * Zmień zakres widoku na **2/24/17 20:00:00.00 do 3/10/17 20:00:00.00 (UTC)**.

      [![Również przeskoki czujników ropy naftowej i aktywnych ostrzeżeń](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Badanie dwóch lat danych historycznych ujawnia inne zdarzenie pożaru, które miało takie same wahania czujnika.

    * Zmień zakres widoku na **1/1/16 na 12/31/17** (wszystkie dane).

      [![Wyszukaj wzorce historyczne](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Za pomocą Azure Time Series Insights Gen2 i danych telemetrycznych, wykryliśmy długoterminowy trend ukryty w danych historycznych. Dzięki tym nowym szczegółowym informacjom możemy:

* Wyjaśnij, co się stało.
* Rozwiąż problem.
* Umieść lepsze systemy powiadomień o alertach.

## <a name="root-cause-analysis"></a>Analiza głównej przyczyny

1. Niektóre scenariusze wymagają zaawansowanej analizy, aby odkryć wskazówki dotyczące danych. Wybierz Windmill **W6** w dniu **6/25**.

    1. Zmień zakres widoku na **6/1/17 20:00:00.00 do 7/1/17 20:00:00.00 (UTC)**.
    1. Wybierz **contoso roślina 1**  >  **W6**  >  **Safety system**  >  **VoltageActuatorSwitchWarning**.

       [![Zmień zakres widoku i wybierz pozycję W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Ostrzeżenie wskazuje na problem z napięciem z generatora. Ogólne dane wyjściowe mocy generatora są w normalnych parametrach w bieżącym interwale. Przez zwiększenie naszego interwału okazuje się, że kolejny wzór zostanie spowodowany. Inicjał jest oczywisty.

    1. Usuń czujnik **VoltageActuatorSwitchWarning** .
    1. Wybierz pozycję **Generator systemu**  >  **ActivePower**.
    1. Zmień interwał na **3D**.

       [![Zmień interwał na 3D](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Zwiększając zakres czasu, możemy określić, czy problem został zatrzymany, czy nadal.

    * Zwiększ przedział czasu do 60 dni.

      [![Zwiększ przedział czasu do 60 dni](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Inne punkty danych czujników można dodać, aby zapewnić większy kontekst. Im więcej czujników jest wyświetlanych, tym pełniejsze zrozumienie problemu jest. Upuśćmy znacznik, aby wyświetlić rzeczywiste wartości.

    1. Wybierz pozycję **system generatora**, a następnie wybierz pozycję trzy czujniki: **GridVoltagePhase1**, **GridVoltagePhase2** i **GridVoltagePhase3**.
    1. Ustaw znacznik na ostatnim punkcie danych w widocznym obszarze.

       [![Upuść znacznik](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Dwie czujniki napięcia działają w sposób porównywalny i w ramach zwykłych parametrów. Wygląda na to, że czujnik **GridVoltagePhase3** jest przyczyna.

1. Po dodaniu wysoce kontekstowych danych, lista rozwijana fazy 3 pojawia się jeszcze bardziej, aby rozwiązać ten problem. Teraz mamy dobry lider w zakresie przyczyny ostrzeżenia. Jesteśmy gotowi do rozpatrzenia problemu z naszym zespołem obsługi.  

    * Zmień widok tak, aby wszystkie czujniki **systemu generatora** były nakładane na tę samą skalę wykresu.

      [![Zmień widok tak, aby obejmował wszystko](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Wszystko jest gotowe do utworzenia własnego środowiska Azure Time Series Insights Gen2. Aby rozpocząć:

> [!div class="nextstepaction"]
> [Planowanie środowiska Azure Time Series Insights Gen2](./how-to-plan-your-environment.md)

Dowiedz się, jak korzystać z demonstracji i jej funkcji:

> [!div class="nextstepaction"]
> [Eksplorator Azure Time Series Insights Gen2](./concepts-ux-panels.md)
