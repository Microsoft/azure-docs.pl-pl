---
title: Przeprowadzanie analizy głównej przyczyny po wystąpieniu alertu — Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak przeprowadzać analizę głównej przyczyny po wystąpieniu alertu przy użyciu usługi Azure Time Series Insights.
author: Philmea
ms.author: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: c3cb940583677d813873c07fbfa679fdcc1dff59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77565472"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Samouczek: przeprowadzanie analizy głównej przyczyny po wystąpieniu alertu

W tym samouczku pokazano, jak diagnozować główną przyczynę alertu za pomocą akceleratora rozwiązania do monitorowania zdalnego. Na pulpicie nawigacyjnym rozwiązania do monitorowania zdalnego zobaczysz, że został wyzwolony alert, a następnie zbadasz główną przyczynę za pomocą eksploratora usługi Azure Time Series Insights.

Ten samouczek korzysta z dwóch urządzeń symulowanych: ciężarówek dostawczych. Wysyłają one dane telemetryczne obejmujące lokalizację, wysokość nad poziomem morza, prędkość i temperaturę ładunku. Ciężarówki są zarządzane przez organizację Contoso i są połączone z akceleratorem rozwiązania do monitorowania zdalnego. Jako operator firmy Contoso musisz zrozumieć, dlaczego jedna z ciężarówek (delivery-truck-02) zarejestrowała alert niskiej temperatury.

W tym samouczku zostały wykonane następujące czynności:

>[!div class="checklist"]
> * Filtrowanie urządzeń na pulpicie nawigacyjnym
> * Wyświetlanie danych telemetrycznych w czasie rzeczywistym
> * Eksplorowanie danych w eksploratorze usługi Time Series Insights
> * Przeprowadzanie analizy głównej przyczyny
> * Tworzenie nowej reguły na podstawie zdobytych informacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Wybieranie urządzenia do wyświetlenia

Aby wybrać połączone urządzenia, które mają być wyświetlane na stronie **Pulpit nawigacyjny**, użyj filtrów. Aby wyświetlić tylko urządzenia typu **Truck** (Ciężarówka), wybierz wbudowany filtr **Trucks** na liście rozwijanej filtrów:

[![Filtrowanie ciężarówek na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Po zastosowaniu filtrowania na mapie oraz w panelu telemetrii na stronie **Pulpit nawigacyjny** są wyświetlane tylko te urządzenia, które odpowiadają warunkom filtrowania. Widać, że do akceleratora rozwiązania są podłączone dwie ciężarówki, w tym ciężarówka **truck-02**.

## <a name="view-real-time-telemetry"></a>Wyświetlanie danych telemetrycznych w czasie rzeczywistym

Akcelerator rozwiązań pokazuje dane telemetryczne w czasie rzeczywistym na wykresie na stronie **Pulpit nawigacyjny**. Domyślnie wykres pokazuje dane telemetryczne wysokości nad poziomem morza, która zmienia się wraz z upływem czasu:

[![Wykres telemetrii na wysokości ciężarówki](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Aby wyświetlić dane telemetryczne dotyczące temperatury dla ciężarówek, kliknij pozycję **Temperature** (Temperatura) na **panelu danych telemetrycznych**. Widać, jak zmieniała się temperatura obydwu ciężarówek w ciągu ostatnich 15 minut. W okienku alertów widać również, że alert dotyczący niskiej temperatury został wyzwolony dla ciężarówki delivery-truck-02.

[![Pulpit nawigacyjny RM z niskim alertem tymczasowym](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Eksplorowanie danych

Aby zidentyfikować przyczynę alarmu dotyczącego niskiej temperatury, otwórz dane telemetryczne ciężarówki dostawczej w eksploratorze usługi Time Series Insights. Kliknij dowolny link **Explore in Time Series Insights** (Eksploruj w usłudze Time Series Insights) na pulpicie nawigacyjnym:

[![Pulpit nawigacyjny RM z wyróżnionymi linkami TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Po uruchomieniu eksploratora zobaczysz wszystkie swoje urządzenia na liście:

[![Widok początkowy Eksploratora TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Przefiltruj urządzenia, wpisując ciąg **delivery-truck** w polu filtru, i wybierz wartość **temperature** (temperatura) w polu **Measure** (Miara) w panelu po lewej stronie:

[![Temperatura ciężarówki Eksploratora TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Zostanie wyświetlony ten sam widok, który był wyświetlany na pulpicie nawigacyjnym zdalnego monitorowania. Ponadto teraz można przybliżyć horyzont czasowy, w którym alert został wyzwolony:

[![Powiększenie Eksploratora TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Możesz również dodać inne strumienie danych telemetrycznych pochodzące z ciężarówek. Kliknij przycisk **Add** (Dodaj) w lewym górnym rogu. Zostanie wyświetlone nowe okienko:

[![Eksplorator TSI z nowym okienkiem](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

W nowym okienku zmień nazwę nowej etykiety na **Devices** (Urządzenia), aby była zgodna z poprzednią. Wybierz pozycję **altitude** (wysokość) w polu **Measure** (Miara) i wartość **iothub-connection-device-id** w polu **Split By** (Podziel według), aby dodać dane telemetryczne wysokości do widoku:

[![Eksplorator TSI z temperaturą i wysokością](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Diagnozowanie alertu

Po wyświetleniu strumieni w bieżącym widoku widać, że profile wysokości dla obydwu ciężarówek się różnią. Ponadto spadek temperatury dla ciężarówki **delivery-truck-02** ma miejsce, gdy ciężarówka osiąga dużą wysokość nad poziomem morza. Ta informacja jest dla Ciebie zaskakująca, ponieważ ciężarówki miały jechać tą samą trasą.

Aby potwierdzić swoje podejrzenia, że ciężarówki pojechały różnymi trasami, dodaj kolejne okienko do panelu bocznego, używając przycisku **Add** (Dodaj). W nowym okienku zmień nazwę nowej etykiety na **Devices** (Urządzenia), aby była zgodna z poprzednią. Wybierz pozycję **longitude** (długość geograficzna) w polu **Measure** (Miara) i wartość **iothub-connection-device-id** w polu **Split By** (Podziel według), aby dodać dane telemetryczne długości geograficznej do widoku. Możesz zauważyć, że ciężarówki faktycznie pojechały innymi trasami, patrząc na różnicę między strumieniami **długości geograficznej**:

[![Eksplorator TSI z temperaturą, wysokością i długością geograficzną](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Tworzenie nowej reguły

Trasy ciężarówek są przeważnie optymalizowane z wyprzedzeniem, okazuje się jednak, że ruch na drogach, pogoda i inne nieprzewidziane zdarzenia mogą powodować opóźnienia i pozostawiać podejmowane w ostatniej chwili decyzje dotyczące trasy w rękach kierowców, którzy podejmują je w oparciu o swoje doświadczenie i ocenę sytuacji. Jednak ze względu na to, że temperatura produktów znajdujących się wewnątrz pojazdu ma kluczowe znaczenie, należy utworzyć dodatkową regułę w rozwiązaniu do monitorowania zdalnego. Ta reguła zapewnia otrzymanie ostrzeżenia, jeśli średnia wysokość w okresie 1 minuty przekroczy 350 stóp (ok. 107 metrów):

[![Zestaw kart reguły zdalnego monitorowania — reguła wysokości](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Aby dowiedzieć się, jak tworzyć i edytować reguły, zapoznaj się z poprzednim samouczkiem na temat [wykrywania problemów z urządzeniami](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak diagnozować główną przyczynę alertu za pomocą eksploratora usługi Time Series Insights i akceleratora rozwiązania do monitorowania zdalnego. Aby dowiedzieć się, w jaki sposób korzystać z akceleratora rozwiązań w celu identyfikowania i rozwiązywania problemów z urządzeniami połączonymi, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Korzystanie z alertów urządzenia do identyfikowania i rozwiązywania problemów z urządzeniami połączonymi z rozwiązaniem do monitorowania](iot-accelerators-remote-monitoring-maintain.md)
