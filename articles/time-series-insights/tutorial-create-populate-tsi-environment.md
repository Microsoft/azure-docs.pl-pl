---
title: 'Samouczek: Tworzenie środowiska — Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak utworzyć środowisko Azure Time Series Insights wypełniane danymi z symulowanych urządzeń.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: ea57a623546df99408d488da4522411f55ab0dd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094365"
---
# <a name="tutorial-create-an-azure-time-series-insights-gen1-environment"></a>Samouczek: Tworzenie środowiska Azure Time Series Insights Gen1

> [!CAUTION]
> To jest artykuł Gen1.

Ten samouczek przeprowadzi Cię przez proces tworzenia środowiska Azure Time Series Insights, które jest wypełniane danymi z symulowanych urządzeń. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz środowisko Azure Time Series Insights.
> * Utwórz rozwiązanie do symulacji urządzeń, które zawiera Centrum IoT.
> * Podłącz środowisko Azure Time Series Insights do centrum IoT Hub.
> * Uruchom symulację urządzenia, aby przesłać strumieniowo dane do środowiska Azure Time Series Insightsowego.
> * Sprawdź symulowane dane telemetryczne.

> [!IMPORTANT]
> Utwórz konto [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto logowania platformy Azure musi być również członkiem roli **właściciela** subskrypcji. Aby uzyskać więcej informacji, przeczytaj temat [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Przejrzyj wideo

Dowiedz się, jak generować dane i rozpoczynać pracę z Azure Time Series Insights przy użyciu akceleratora rozwiązań usługi Azure IoT.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Omówienie

Środowisko Azure Time Series Insights polega na zbieraniu i przechowywaniu danych urządzeń. Po zapisaniu można używać [eksploratora Azure Time Series Insights](time-series-quickstart.md) i [interfejsu API zapytania Azure Time Series Insights](/rest/api/time-series-insights/gen1-query-api) do wykonywania zapytań i analizowania danych.

Azure IoT Hub to źródło zdarzeń używane przez wszystkie urządzenia (symulowane lub fizyczne) w samouczku umożliwiające bezpieczne łączenie i przesyłanie danych do chmury platformy Azure.

Ten samouczek używa również [akceleratora rozwiązania IoT](https://www.azureiotsolutions.com) do generowania i przesyłania strumieniowego przykładowych danych telemetrycznych do IoT Hub.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com) zapewniają wstępnie skonfigurowane rozwiązania klasy korporacyjnej, których można użyć do przyspieszenia tworzenia niestandardowych rozwiązań IoT.

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

Najpierw utwórz rozwiązanie do symulacji urządzenia, które generuje dane testowe, aby wypełnić środowisko Azure Time Series Insights.

1. W osobnym oknie lub karcie przejdź do [azureiotsolutions.com](https://www.azureiotsolutions.com). Zaloguj się przy użyciu tego samego konta subskrypcji platformy Azure i wybierz akcelerator **symulacji urządzenia** .

   [![Uruchom akcelerator symulacji urządzeń](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Wybierz pozycję **Wypróbuj teraz**. Następnie wprowadź wymagane parametry na stronie **Tworzenie rozwiązania symulacji urządzenia** .

   Parametr|Opis
   ---|---
   **Nazwa wdrożenia** | Ta unikatowa wartość jest używana do tworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów.
   **Subskrypcja platformy Azure** | Określ tę samą subskrypcję, która została użyta do utworzenia środowiska Azure Time Series Insights w poprzedniej sekcji.
   **Opcje wdrożenia** | Wybierz pozycję **Udostępnij nowe IoT Hub** , aby utworzyć nowe centrum IoT Hub specyficzne dla tego samouczka.
   **Lokalizacja platformy Azure** | Określ ten sam region, który został użyty do utworzenia środowiska Azure Time Series Insights w poprzedniej sekcji.

   Po zakończeniu wybierz pozycję **Utwórz** , aby udostępnić zasoby platformy Azure rozwiązania. Ukończenie tego procesu może potrwać do 20 minut.

   [![Inicjuj rozwiązanie do symulacji urządzeń](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Po zakończeniu aprowizacji zostaną wyświetlone dwie aktualizacje z informacją o tym, że stan wdrożenia został przeniesiony z **aprowizacji** na **gotowe**.

   >[!IMPORTANT]
   > Nie wprowadzaj jeszcze akceleratora rozwiązania. Pozostaw Tę stronę sieci Web otwartą, ponieważ wrócisz do niej później.

   [![Zakończono inicjowanie rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Teraz Sprawdź nowo utworzone zasoby w Azure Portal. Na stronie **grupy zasobów** Zwróć uwagę, że nowa grupa zasobów została utworzona przy użyciu **nazwy rozwiązania** podanej w ostatnim kroku. Zanotuj zasoby, które zostały utworzone dla symulacji urządzenia.

   [![Zasoby symulacji urządzenia](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Tworzenie środowiska

Następnie Utwórz środowisko Azure Time Series Insights w ramach subskrypcji platformy Azure.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta subskrypcji platformy Azure.
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**.
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**.

   [![Wybierz zasób środowiska Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Na stronie **środowisko Time Series Insights** wprowadź wymagane parametry.

   Parametr|Opis
   ---|---
   **Nazwa środowiska** | Wybierz unikatową nazwę środowiska Azure Time Series Insights. Nazwy są używane przez Eksploratora Azure Time Series Insights i [interfejsy API zapytań](/rest/api/time-series-insights/gen1-query).
   **Subskrypcja** | Subskrypcje są kontenerami zasobów platformy Azure. Wybierz subskrypcję, aby utworzyć środowisko Azure Time Series Insights.
   **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Wybierz istniejącą grupę zasobów lub Utwórz nową dla zasobu środowiska Azure Time Series Insights.
   **Lokalizacja** | Wybierz region centrum danych dla środowiska Azure Time Series Insightsowego. Aby uniknąć dodatkowego opóźnienia, Utwórz środowisko Azure Time Series Insights w tym samym regionie, w którym są inne zasoby IoT.
   **Warstwa** | Wybierz potrzebną przepustowość. Wybierz pozycję **S1**.
   **Pojemność** | Pojemność to mnożnik stosowany do szybkości transferu danych przychodzących i pojemności magazynu skojarzonej z wybraną jednostką SKU. Można zmienić pojemność po utworzeniu. Wybierz pojemność **1**.

   Po zakończeniu wybierz pozycję **Dalej: Źródło zdarzenia** , aby przejść do następnego kroku.

   [![Tworzenie zasobu środowiska Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Teraz podłącz środowisko Azure Time Series Insights do centrum IoT Hub utworzonego przez Akcelerator rozwiązania. Ustaw **opcję Wybierz centrum** `Select existing` . Następnie wybierz pozycję IoT Hub utworzoną przez Akcelerator rozwiązania, gdy zostanie ustawiona **nazwa IoT Hub**.

   [![Połącz środowisko Azure Time Series Insights z utworzonym Centrum IoT Hub](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Na koniec wybierz pozycję **Przegląd + Utwórz**.

1. Sprawdź panel **powiadomień** , aby monitorować ukończenie wdrożenia.

   [![Wdrożenie środowiska Azure Time Series Insights powiodło się](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Uruchamianie symulacji urządzenia

Teraz, gdy wdrożenie i początkowa konfiguracja zostały ukończone, Wypełnij środowisko Azure Time Series Insights przy użyciu przykładowych danych z [symulowanych urządzeń utworzonych przez akcelerator](#create-a-device-simulation).

Wraz z Centrum IoT jest generowana Azure App Service aplikacja sieci Web służąca do tworzenia i przesyłania danych telemetrycznych urządzenia symulowanego.

1. Wróć do swojego [Pulpitu nawigacyjnego akceleratorów rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard). Zaloguj się ponownie, w razie potrzeby, przy użyciu tego samego konta platformy Azure, którego używasz w tym samouczku. Wybierz swoje urządzenie, a następnie **Przejdź do akceleratora rozwiązania** , aby uruchomić wdrożone rozwiązanie.

   [![Pulpit nawigacyjny akceleratorów rozwiązań](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Aplikacja sieci Web symulacji urządzenia rozpoczyna się od wyświetlenia monitu o przyznanie aplikacji sieci Web **i zalogowanie się i odczytanie** uprawnienia do profilu. To uprawnienie umożliwia aplikacji pobranie informacji o profilu użytkownika niezbędnych do obsługi aplikacji.

   [![Zgoda na aplikację sieci Web symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Wybierz pozycję **+ Nowa symulacja**. Po załadowaniu strony **konfiguracji symulacji** wprowadź wymagane parametry.

   Parametr|Opis
   ---|---
   **Docelowa usługa IoT Hub** | Wybierz opcję **Użyj wstępnie zainicjowanej IoT Hub**.
   **Model urządzenia** | Wybierz pozycję **Chiller** (Chłodnia).
   **Liczba urządzeń**  | Wprowadź `10` **wartość** poniżej.
   **Częstotliwość telemetrii** | Wprowadź `10` sekundy.
   **Czas trwania symulacji** | Wybierz pozycję **Zakończ w:** i wprowadź `5` minuty.

   Gdy skończysz, wybierz pozycję **Rozpocznij symulację**. Symulacja jest uruchamiana co 5 minut. Generuje ona dane z symulowanych urządzeń 1 000 co 10 sekund.

   [![Konfiguracja symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Podczas wykonywania symulacji należy zauważyć, że **całkowita liczba komunikatów** i **komunikatów na sekundę** jest aktualizowana, co około 10 sekund. Symulacja zostaje zakończona po około 5 minutach i wróci do **konfiguracji symulacji**.

   [![Przebieg symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Sprawdzanie danych telemetrycznych

W tej ostatniej sekcji można sprawdzić, czy dane telemetryczne zostały wygenerowane i zapisane w środowisku Azure Time Series Insights. Aby sprawdzić dane, należy użyć Eksploratora Azure Time Series Insights, który służy do wykonywania zapytań i analizowania danych telemetrycznych.

1. Wróć do strony **Przegląd** grupy zasobów środowiska Azure Time Series Insights. Wybierz środowisko Azure Time Series Insights.

   [![Środowisko i Grupa zasobów środowiska Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na stronie **Przegląd** środowiska Azure Time Series Insights wybierz **adres URL Eksploratora Time Series Insights** , aby otworzyć Eksploratora Azure Time Series Insights.

   [![Eksplorator usługi Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Eksplorator Azure Time Series Insights ładuje i uwierzytelnia się przy użyciu konta Azure Portal. Początkowo zostanie wyświetlony obszar wykresu, w którym zapełnił się środowisko Azure Time Series Insights wraz z symulowanymi danymi telemetrycznymi. Aby odfiltrować węższy zakres czasu, zaznacz listę rozwijaną w lewym górnym rogu. Wprowadź wystarczająco duży zakres czasu, aby przekroczyć czas trwania symulacji urządzenia. Następnie wybierz opcję wyszukiwania Lupa.

   [![Filtr zakresu czasu Eksploratora Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zawężanie zakresu czasu umożliwia wykresowi powiększanie do odrębnych obciążeń transferu danych do centrum IoT Hub i środowiska Azure Time Series Insights. Zwróć uwagę na tekst **przesyłania strumieniowego** w prawym górnym rogu, który pokazuje łączną liczbę znalezionych zdarzeń. Możesz również przeciągnąć suwak **rozmiar interwału** , aby kontrolować stopień szczegółowości wykresu.

   [![Widok przefiltrowany zakresu czasu Eksploratora Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Na koniec można również kliknąć region w celu odfiltrowania zakresu. Następnie kliknij prawym przyciskiem myszy i użyj polecenia **Eksploruj zdarzenia** , aby wyświetlić szczegóły zdarzenia w widoku **zdarzenia** tabelaryczne.

   [![Przefiltrowany widok i zdarzenia przedziału czasu Eksploratora Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym samouczku przedstawiono kilka uruchomionych usług platformy Azure, które obsługują rozwiązanie do Azure Time Series Insights środowiska i symulacji urządzeń. Aby je usunąć, przejdź z powrotem do Azure Portal.

Z menu po lewej stronie w Azure Portal:

1. Wybierz ikonę **grupy zasobów** . Następnie wybierz grupę zasobów utworzoną dla środowiska Azure Time Series Insights. W górnej części strony wybierz pozycję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

1. Wybierz ikonę **grupy zasobów** . Następnie wybierz grupę zasobów, która została utworzona przez Akcelerator rozwiązania do symulacji urządzeń. W górnej części strony wybierz pozycję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz środowisko Azure Time Series Insights.
> * Utwórz rozwiązanie do symulacji urządzeń, które zawiera Centrum IoT.
> * Podłącz środowisko Azure Time Series Insights do centrum IoT Hub.
> * Uruchom symulację urządzenia, aby przesłać strumieniowo dane do środowiska Azure Time Series Insightsowego.
> * Sprawdź symulowane dane telemetryczne.

Teraz, gdy wiesz już, jak utworzyć własne środowisko Azure Time Series Insights, Dowiedz się, jak stworzyć aplikację sieci Web, która wykorzystuje dane ze środowiska Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Przeczytaj przykłady wizualizacji zestawu SDK klienta hostowanego](https://tsiclientsample.azurewebsites.net/)