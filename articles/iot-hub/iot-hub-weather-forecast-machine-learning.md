---
title: Prognoza pogody przy użyciu Azure Machine Learning Studio (klasycznej) z danymi IoT Hub danych
description: Użyj Azure Machine Learning Studio (klasycznej), aby przewidzieć prawdopodobieństwo opadu na podstawie danych temperatury i wilgotności zbieranych z czujnika przez centrum IoT.
author: robinsh
manager: philmea
keywords: uczenie maszynowe prognozy pogody
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 455d78ed21403952046448dd4447b5ec54f77c00
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566983"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Prognoza pogody korzystająca z danych z czujników z centrum IoT w Azure Machine Learning Studio (wersja klasyczna)

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Uczenie maszynowe to technika nauki o danych, która pomaga komputerom uczyć się na istniejących danych w celu przewidywania przyszłych zachowań, wyników i trendów. Azure Machine Learning Studio (klasyczne) to usługa analizy predykcyjnej w chmurze, która umożliwia szybkie tworzenie i wdrażanie modeli predykcyjnych jako rozwiązań analitycznych. Z tego artykułu dowiesz się, jak używać usługi Azure Machine Learning Studio (klasycznej) do prognozowania pogody (prawdopodobieństwo opadu) przy użyciu danych temperatury i wilgotności z centrum Azure IoT Hub. Prawdopodobieństwo opadu to dane wyjściowe przygotowanego modelu przewidywania pogody. Model jest oparty na danych historycznych, aby prognozować prawdopodobieństwo opadu na podstawie temperatury i wilgotności.

## <a name="prerequisites"></a>Wymagania wstępne

- Ukończ [samouczek dotyczący symulatora online urządzenia Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jeden z samouczków urządzenia. Na przykład możesz przejść do urządzenia [Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) z node.jslub do jednego z przewodników Szybki start [Wysyłanie](quickstart-send-telemetry-dotnet.md) telemetrii. Te artykuły obejmują następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Centrum Azure IoT Hub w ramach Twojej subskrypcji.
  - Aplikacja kliency, która wysyła komunikaty do centrum Azure IoT Hub.
- Konto [Azure Machine Learning Studio (klasyczne).](https://studio.azureml.net/)
- Preferowane [jest konto](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts)usługi Azure Storage , konto ogólnego przeznaczenia w wersji **2,** ale będzie również działać każde konto usługi Azure Storage, które obsługuje usługę Azure Blob Storage.

> [!Note]
> W tym artykule używane są Azure Stream Analytics i kilka innych płatnych usług. Dodatkowe opłaty są naliczane w przypadku Azure Stream Analytics, gdy dane muszą być przesyłane między regionami świadczenia usługi Azure. Z tego powodu warto upewnić się, że konto grupy zasobów, usługi IoT Hub i usługi Azure Storage , a także obszar roboczy programu Machine Learning Studio (klasyczny) i zadanie usługi Azure Stream Analytics dodane w dalszej części tego samouczka , znajdują się w tym samym regionie świadczenia usługi Azure. Regionalną pomoc techniczną dla programu Azure Machine Learning Studio (wersja klasyczna) i innych usług platformy Azure możesz sprawdzić na stronie dostępność produktów platformy [Azure według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Wdrażanie modelu przewidywania pogody jako usługi internetowej

W tej sekcji pobierzemy model przewidywania pogody z Azure AI Library. Następnie do modelu dodasz moduł skryptu R w celu oczyszczenia danych temperatury i wilgotności. Na koniec wdrożysz model jako predykcyjną usługę internetową.

### <a name="get-the-weather-prediction-model"></a>Uzyskiwanie modelu przewidywania pogody

W tej sekcji pobierzemy model przewidywania pogody z Azure AI Gallery i otworzymy go w programie Azure Machine Learning Studio (wersja klasyczna).

1. Przejdź do [strony modelu przewidywania pogody](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Otwórz stronę modelu przewidywania pogody w Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Wybierz **pozycję Otwórz w programie Studio (wersja klasyczna),** aby otworzyć model w programie Microsoft Azure Machine Learning Studio (wersja klasyczna). Wybierz region w pobliżu centrum IoT i prawidłowy obszar roboczy w oknie podręcznym **Kopiowanie eksperymentu z** galerii.

   ![Otwieranie modelu przewidywania pogody w programie Azure Machine Learning Studio (wersja klasyczna)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Dodawanie modułu skryptu R w celu oczyszczenia danych temperatury i wilgotności

Aby model zachowywał się prawidłowo, dane temperatury i wilgotności muszą być konwertowane na dane liczbowe. W tej sekcji do modelu przewidywania pogody dodasz moduł skryptu R, który usuwa wszystkie wiersze z wartościami danych temperatury lub wilgotności, których nie można przekonwertować na wartości liczbowe.

1. Po lewej stronie okna aplikacji Azure Machine Learning Studio (wersja klasyczna) wybierz strzałkę, aby rozwinąć panel narzędzi. Wprowadź tekst "Execute" (Wykonaj) w polu wyszukiwania. Wybierz moduł **Execute R Script (Wykonaj skrypt R).**

   ![Wybieranie modułu Execute R Script (Wykonaj skrypt R)](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Przeciągnij moduł **Execute R Script (Wykonywanie skryptu R)** w pobliżu modułu Clean Missing Data (Czyszczenie **brakujących** danych) i istniejącego modułu Execute R Script (Wykonywanie **skryptu R)** na diagramie. Usuń połączenie między modułami **Clean Missing Data** (Czyszczenie brakujących danych) i Execute R Script (Wykonywanie skryptu **R),** a następnie połącz dane wejściowe i wyjściowe nowego modułu, jak pokazano poniżej.

   ![Dodawanie modułu Execute R Script (Wykonywanie skryptu R)](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Wybierz nowy moduł **Execute R Script (Wykonywanie skryptu R),** aby otworzyć okno właściwości. Skopiuj i wklej następujący kod w **polu Skrypt R.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Po zakończeniu okno właściwości powinno wyglądać podobnie do następującego:

   ![Dodawanie kodu do modułu Execute R Script (Wykonywanie skryptu R)](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Wdrażanie predykcyjnej usługi internetowej

W tej sekcji zweryfikujesz model, skonfigurujemy predykcyjną usługę internetową na podstawie modelu, a następnie wdrożymy usługę internetową.

1. Wybierz **pozycję Uruchom,** aby zweryfikować kroki w modelu. Ukończenie tego kroku może potrwać kilka minut.

   ![Uruchamianie eksperymentu w celu zweryfikowania kroków](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Wybierz pozycję SET UP WEB SERVICE Predictive Web Service **(SKONFIGURUJ PREDYKCYJNĄ**  >  **usługę internetową).** Zostanie otwarty diagram eksperymentu predykcyjnego.

   ![Wdrażanie modelu przewidywania pogody w programie Azure Machine Learning Studio (wersja klasyczna)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Na diagramie eksperymentu predykcyjnego usuń połączenie między modułem **wejściowym** usługi internetowej i modułem **Select Columns in Dataset** (Wybieranie kolumn w zestawie danych) u góry. Następnie przeciągnij moduł **wejściowy usługi internetowej** w pobliżu modułu Score Model **(Ocena modelu)** i połącz go w pokazany sposób:

   ![Łączenie dwóch modułów w programie Azure Machine Learning Studio (wersja klasyczna)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Wybierz **pozycję RUN** (URUCHOM), aby zweryfikować kroki w modelu.

1. Wybierz **pozycję DEPLOY WEB SERVICE (WD** WDRAŻAJ USŁUGĘ INTERNETOWĄ), aby wdrożyć model jako usługę internetową.

1. Na pulpicie nawigacyjnym modelu pobierz skoroszyt programu **Excel 2010 lub starszego** dla **żądania/odpowiedzi.**

   > [!Note]
   > Upewnij się, że pobrano skoroszyt programu **Excel 2010** lub starszego, nawet jeśli na komputerze jest uruchomiona nowsza wersja programu Excel.

   ![Pobieranie programu Excel dla punktu końcowego REQUEST RESPONSE](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Otwórz skoroszyt programu Excel, zanotuj adres **URL USŁUGI INTERNETOWEJ** i KLUCZ **DOSTĘPU.**

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, konfigurowanie i uruchamianie Stream Analytics zadań

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [Azure Portal](https://portal.azure.com/)wybierz pozycję **Utwórz zasób.** W polu Wyszukiwania wpisz "zadanie usługi Stream Analytics", a następnie wybierz **pozycję Stream Analytics zadania** z listy rozwijanej wyników. Po Stream Analytics **zadania wybierz** pozycję **Utwórz.**
1. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Subskrypcja:** wybierz subskrypcję, jeśli różni się ona od domyślnej.

   **Grupa zasobów:** użyj tej samej grupy zasobów, która jest używana przez centrum IoT Hub.

   **Lokalizacja:** użyj tej samej lokalizacji co grupa zasobów.

   Pozostaw wartości domyślne we wszystkich pozostałych polach.

   ![Tworzenie zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Wybierz przycisk **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz Stream Analytics zadania.
1. W obszarze **Topologia zadania** wybierz pozycję **Dane wejściowe**.
1. W **okienku Dane wejściowe** wybierz pozycję **Dodaj wejście strumienia**, a następnie wybierz pozycję **IoT Hub** z listy rozwijanej. W **okienku Nowe** dane wejściowe wybierz pozycję **Wybierz IoT Hub subskrypcji** i wprowadź następujące informacje:

   **Alias danych wejściowych:** unikatowy alias danych wejściowych.

   **Subskrypcja:** wybierz subskrypcję, jeśli różni się ona od domyślnej.

   **IoT Hub:** Wybierz centrum IoT Hub ze swojej subskrypcji.

   **Nazwa zasad dostępu współdzielonych:** Wybierz  **usługę**. (Możesz również użyć funkcji **iothubowner).**

   **Grupa konsumentów:** wybierz utworzoną grupę odbiorców.

   Pozostaw wartości domyślne we wszystkich pozostałych polach.

   ![Dodawanie danych wejściowych do zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Wybierz pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Dane wyjściowe**.
1. W **okienku Dane wyjściowe** wybierz pozycję **Dodaj,** a następnie wybierz pozycję **Blob Storage/Data Lake Storage** z listy rozwijanej. W **okienku Nowe dane** wyjściowe wybierz pozycję **Wybierz magazyn ze** swoich subskrypcji i wprowadź następujące informacje:

   **Alias wyjściowy**: unikatowy alias danych wyjściowych.

   **Subskrypcja:** wybierz swoją subskrypcję, jeśli jest inna niż domyślna.

   **Konto magazynu:** konto magazynu dla magazynu obiektów blob. Możesz utworzyć konto magazynu lub użyć istniejącego.

   **Kontener:** kontener, w którym jest zapisywany obiekt blob. Możesz utworzyć kontener lub użyć istniejącego.

   **Format serializacji zdarzeń:** wybierz **opcję CSV**.

   ![Dodawanie danych wyjściowych do zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Wybierz pozycję **Zapisz**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Dodaj funkcję do zadania Stream Analytics, aby wywołać wdrożoną usługę internetową

1. W **obszarze Topologia zadania** wybierz pozycję **Funkcje**.
1. W **okienku Funkcje** wybierz pozycję **Dodaj,** a następnie wybierz pozycję **Azure ML Studio** z listy rozwijanej. (Upewnij się, że **wybierasz usługę Azure ML Studio,** a nie **Usługę Azure ML).** W **okienku Nowa funkcja** wybierz ręcznie **Azure Machine Learning podaj ustawienia** funkcji i wprowadź następujące informacje:

   **Alias funkcji:** wprowadź `machinelearning` .

   **Adres URL:** wprowadź adres URL USŁUGI INTERNETOWEJ zanotowyny ze skoroszytu programu Excel.

   **Klucz:** wprowadź klucz dostępu zanotowyny ze skoroszytu programu Excel.

   ![Dodawanie funkcji do zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. Wybierz pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Zapytanie**.
1. Zastąp istniejący kod poniższym kodem:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.

   Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.

1. Wybierz **pozycję Zapisz zapytanie.**

> [!Note]
> W przypadku wybrania **zapytania testowego** zostanie wyświetlony następujący komunikat: Testowanie zapytań za pomocą Machine Learning nie jest obsługiwane. Zmodyfikuj zapytanie i spróbuj ponownie. Możesz bezpiecznie zignorować ten komunikat i wybrać przycisk **OK,** aby zamknąć okno komunikatu. Pamiętaj, aby zapisać zapytanie przed przystąpieniem do następnej sekcji.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W Stream Analytics wybierz pozycję **Przegląd** w okienku po lewej stronie. Następnie wybierz **pozycję Rozpocznij**  >    >  **teraz.** Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania usługi Stream Analytics](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Użyj Eksplorator usługi Microsoft Azure Storage, aby wyświetlić prognozę pogody

Uruchom aplikację kliencową, aby rozpocząć zbieranie i wysyłanie danych temperatury i wilgotności do centrum IoT. Dla każdego komunikatu, który otrzymuje centrum IoT, zadanie Stream Analytics wywołuje usługę internetową prognozowania pogody, aby uzyskać prawdopodobieństwo opadu. Wynik jest następnie zapisywany w usłudze Azure Blob Storage. Eksplorator usługi Azure Storage to narzędzie, za pomocą których można wyświetlić wynik.

1. [Pobierz i zainstaluj Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/).
1. Otwórz Eksplorator usługi Azure Storage.
1. Zaloguj się do swojego konta platformy Azure.
1. Wybierz subskrypcję.
1. Wybierz subskrypcję > **magazynu >** konta magazynu > **kontenerów** obiektów blob > kontenera.
1. Pobierz plik CSV, aby zobaczyć wynik. Ostatnia kolumna rejestruje prawdopodobieństwo opadu.

   ![Uzyskiwanie wyniku prognozy pogody za pomocą Azure Machine Learning Studio (wersja klasyczna)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Podsumowanie

Udało Ci się pomyślnie użyć programu Azure Machine Learning Studio (klasycznego), aby wytworzyć prawdopodobieństwo opadu na podstawie danych temperatury i wilgotności odbieranych przez centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]