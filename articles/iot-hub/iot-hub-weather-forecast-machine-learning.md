---
title: Prognoza pogody przy użyciu Azure Machine Learning z danymi IoT Hub
description: Użyj Azure Machine Learning, aby przewidzieć prawdopodobieństwo oddeszczu w oparciu o dane dotyczące temperatury i wilgotności, które Centrum IoT zbiera z czujnika.
author: robinsh
manager: philmea
keywords: Prognoza pogody — Uczenie maszynowe
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: 5551655843b8d3ed5b6d70f5d6ed3a0eb4d0e92f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83746976"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Prognoza pogody przy użyciu danych czujników z Centrum IoT Hub w Azure Machine Learning

![Diagram kompleksowy](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Uczenie maszynowe to technika analizy danych, która ułatwia komputerom uczenie się na podstawie istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Usługa Azure Machine Learning to oparta na chmurze usługa analizy predykcyjnej, która pozwala na szybkie tworzenie i wdrażanie modeli predykcyjnych jako rozwiązań analitycznych.

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak używać Azure Machine Learning do prognozowania pogody (szansa deszczu) przy użyciu danych temperatury i wilgotności z usługi Azure IoT Hub. Szansa deszczu to wyjście przygotowanego modelu przewidywania pogody. Model jest tworzony na podstawie historycznych danych w celu przewidywania szans oddeszczu na podstawie temperatury i wilgotności.

## <a name="what-you-do"></a>Co robisz

- Wdróż model prognozowania pogody jako usługę sieci Web.
- Przygotuj Centrum IoT Hub do dostępu do danych, dodając grupę odbiorców.
- Utwórz zadanie Stream Analytics i skonfiguruj zadanie w programie:
  - Odczytaj dane temperatury i wilgotności z Centrum IoT Hub.
  - Wywołaj usługę sieci Web, aby uzyskać deszczową szansę.
  - Zapisz wynik w usłudze Azure Blob Storage.
- Użyj Eksplorator usługi Microsoft Azure Storage, aby wyświetlić prognozę pogody.

## <a name="what-you-need"></a>Co jest potrzebne

- Ukończ samouczek [gry online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jedno z samouczków dotyczących urządzeń; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Usługa Azure IoT Hub w ramach Twojej subskrypcji.
  - Aplikacja kliencka, która wysyła komunikaty do usługi Azure IoT Hub.
- Konto [Azure Machine Learning Studio (klasyczne)](https://studio.azureml.net/) .

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Wdrażanie modelu przewidywania pogody jako usługi sieci Web

W tej sekcji uzyskasz model prognozowania pogody z biblioteki AI platformy Azure. Następnie Dodaj moduł języka R do modelu, aby wyczyścić dane temperatury i wilgotności. Na koniec należy wdrożyć model jako predykcyjną usługę sieci Web.

### <a name="get-the-weather-prediction-model"></a>Pobieranie modelu przewidywania pogody

W tej sekcji uzyskasz model prognozowania pogody z Azure AI Gallery i otworzyć go w Azure Machine Learning Studio (klasyczny).

1. Przejdź do [strony modelu przewidywania pogody](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Otwórz stronę modelu przewidywania pogody w Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Kliknij pozycję **Otwórz w programie Studio (klasyczny)** , aby otworzyć model w Microsoft Azure Machine Learning Studio (klasyczny).

   ![Otwórz model prognozowania pogody w Azure Machine Learning Studio (klasyczny)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Dodawanie modułu języka R do czyszczenia danych temperatury i wilgotności

Aby model działał prawidłowo, dane temperatury i wilgotności muszą być konwertowane na dane liczbowe. W tej sekcji dodasz moduł języka R do modelu przewidywania pogody, który usuwa wszystkie wiersze, które mają wartości danych dla temperatury lub wilgotności, których nie można przekonwertować na wartości liczbowe.

1. Po lewej stronie okna Azure Machine Learning Studio kliknij strzałkę, aby rozwinąć panel Narzędzia. Wprowadź ciąg "Execute" w polu wyszukiwania. Wybierz moduł **wykonywania skryptu języka R** .

   ![Wybierz pozycję wykonaj moduł skryptu języka R](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Przeciągnij moduł **Uruchom skrypt języka R** w sąsiedztwo nieznanego modułu **danych** i istniejącego modułu **wykonywania skryptu języka r** na diagramie. Usuń połączenie między **czystymi brakującymi danymi** i modułami **wykonywania skryptu języka R** , a następnie Połącz dane wejściowe i wyjściowe nowego modułu, jak pokazano.

   ![Dodaj moduł wykonywania skryptu języka R](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Wybierz nowy moduł **skryptu Execute R** , aby otworzyć jego okno właściwości. Skopiuj i wklej następujący kod do pola **skryptu języka R** .

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Po zakończeniu okno właściwości powinno wyglądać podobnie do poniższego:

   ![Dodaj kod do wykonania modułu skryptu języka R](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Wdróż predykcyjną usługę sieci Web

W tej sekcji można sprawdzić poprawność modelu, skonfigurować predykcyjną usługę sieci Web na podstawie modelu, a następnie wdrożyć usługę sieci Web.

1. Kliknij przycisk **Uruchom** , aby sprawdzić poprawność kroków w modelu. Wykonanie tego kroku może potrwać kilka minut.

   ![Uruchom eksperyment, aby sprawdzić poprawność kroków](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Kliknij kolejno pozycje **konfiguracja usługa sieci Web**  >  **predykcyjna usługa sieci**Web. Zostanie otwarty diagram eksperymentów predykcyjnych.

   ![Wdróż model prognozowania pogody w Azure Machine Learning Studio (klasyczny)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Na diagramie eksperymentów predykcyjnych Usuń połączenie między modułem **danych wejściowych usługi sieci Web** i **zestawem danych pogody** u góry. Następnie przeciągnij moduł **danych wejściowych usługi sieci Web** w sąsiedztwie modułu **modelu oceny** i połącz go, tak jak pokazano:

   ![Połącz dwa moduły w Azure Machine Learning Studio (klasyczny)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Kliknij przycisk **Uruchom** , aby sprawdzić poprawność kroków w modelu.

1. Kliknij pozycję **Wdróż usługę sieci Web** , aby wdrożyć model jako usługę sieci Web.

1. Na pulpicie nawigacyjnym modelu Pobierz **skoroszyt Excel 2010 lub wcześniejszy** dla **żądania/odpowiedzi**.

   > [!Note]
   > Upewnij się, że pobierasz **skoroszyt programu Excel 2010 lub wcześniejszy** , nawet jeśli korzystasz z nowszej wersji programu Excel na komputerze.

   ![Pobierz program Excel dla punktu końcowego odpowiedzi na żądanie](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Otwórz skoroszyt programu Excel, zanotuj **adres URL usługi sieci Web** i **klucz dostępu**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, Konfigurowanie i uruchamianie zadania Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij kolejno pozycje **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.
1. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów**: Użyj tej samej grupy zasobów, która jest używana przez Centrum IoT Hub.

   **Lokalizacja**: Użyj tej samej lokalizacji co grupa zasobów.

   **Przypnij do pulpitu nawigacyjnego**: zaznacz tę opcję, aby mieć łatwy dostęp do centrum IoT Hub z pulpitu nawigacyjnego.

   ![Tworzenie zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie Stream Analytics.
1. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.
1. W okienku **dane wejściowe** kliknij pozycję **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wejściowy**: unikatowy alias dla danych wejściowych.

   **Źródło**: wybierz pozycję **IoT Hub**.

   **Grupa konsumentów**: Wybierz utworzoną grupę odbiorców.

   ![Dodawanie danych wejściowych do zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**.
1. W okienku dane **wyjściowe** kliknij pozycję **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wyjściowy**: unikatowy alias danych wyjściowych.

   **Ujścia**: Wybierz **BLOB Storage**.

   **Konto magazynu**: konto magazynu dla magazynu obiektów BLOB. Możesz utworzyć konto magazynu lub użyć istniejącego.

   **Kontener**: kontener, w którym zapisano obiekt BLOB. Można utworzyć kontener lub użyć istniejącego.

   **Format serializacji zdarzenia**: wybierz opcję **CSV**.

   ![Dodawanie danych wyjściowych do zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Dodaj funkcję do zadania Stream Analytics, aby wywołać wdrożoną usługę sieci Web

1. W obszarze **topologia zadania**kliknij pozycję **funkcje**  >  **Dodaj**.
1. Wprowadź następujące informacje.

   **Alias funkcji**: ENTER `machinelearning` .

   **Typ funkcji**: wybierz pozycję **Azure ml**.

   **Opcja importowania**: wybierz pozycję **Importuj z innej subskrypcji**.

   **Adres URL**: wprowadź adres URL usługi sieci Web zanotowany w skoroszycie programu Excel.

   **Klucz**: Wprowadź klucz dostępu zanotowany w skoroszycie programu Excel.

   ![Dodawanie funkcji do zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Zapytanie**.
1. Zastąp istniejący kod następującym kodem:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.

   Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.

1. Kliknij pozycję **Zapisz**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu Stream Analytics kliknij pozycję **Rozpocznij**  >  **teraz**  >  **Start**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania usługi Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Użyj Eksplorator usługi Microsoft Azure Storage, aby wyświetlić prognozę pogody

Uruchom aplikację kliencką, aby rozpocząć zbieranie i wysyłanie danych dotyczących temperatury i wilgotności do centrum IoT. Dla każdego komunikatu otrzymanego przez Centrum IoT Hub zadanie Stream Analytics wywołuje usługę sieci Web Prognozowanie pogody w celu wygenerowania szansy deszczu. Następnie wynik zostanie zapisany w usłudze Azure Blob Storage. Eksplorator usługi Azure Storage to narzędzie, którego można użyć do wyświetlenia wyniku.

1. [Pobierz i zainstaluj Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/).
1. Otwórz Eksplorator usługi Azure Storage.
1. Zaloguj się do swojego konta platformy Azure.
1. Wybierz subskrypcję.
1. Kliknij swoją subskrypcję, > **konta magazynu** > Twoje konto magazynu > **kontenery obiektów BLOB** > kontenera.
1. Pobierz plik CSV, aby zobaczyć wynik. Ostatnia kolumna rejestruje szansę deszczu.

   ![Pobierz wynik prognozy pogody przy użyciu Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Podsumowanie

Pomyślnie użyto Azure Machine Learning do wygenerowania szansy deszczu na podstawie danych temperatury i wilgotności odbieranych przez Centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
