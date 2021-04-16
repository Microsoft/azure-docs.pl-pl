---
title: Wizualizacja danych w czasie rzeczywistym z Azure IoT Hub — Power BI
description: Użyj Power BI, aby wizualizować dane temperatury i wilgotności, które są zbierane z czujnika i wysyłane do centrum Azure IoT Hub.
author: robinsh
keywords: wizualizacja danych w czasie rzeczywistym, wizualizacja danych na żywo, wizualizacja danych z czujników
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 0b099f4ce91fd24e8d7baec054bcfc5a6cf0b032
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567115"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Wizualizowanie danych z czujników w czasie rzeczywistym z Azure IoT Hub przy użyciu Power BI

![Diagram end-to-end](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Z tego artykułu dowiesz się, jak wizualizować dane czujników w czasie rzeczywistym odbierane przez centrum Azure IoT Hub przy użyciu Power BI. Jeśli chcesz spróbować zwizualizować dane w centrum IoT Hub za pomocą aplikacji internetowej, zobacz Używanie aplikacji internetowej do wizualizacji danych czujników w czasie rzeczywistym z Azure IoT Hub [.](iot-hub-live-data-visualization-in-web-apps.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [samouczek dotyczący symulatora online urządzenia Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jeden z samouczków urządzenia. Na przykład możesz przejść do urządzenia [Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) z node.jslub do jednego z przewodników Szybki start [Wysyłanie](quickstart-send-telemetry-dotnet.md) telemetrii. Te artykuły obejmują następujące wymagania:
  
  * Aktywna subskrypcja platformy Azure.
  * Centrum Azure IoT Hub w ramach subskrypcji.
  * Aplikacja kliency, która wysyła komunikaty do centrum Azure IoT Hub.

* Konto usługi Power BI. (Wypróbuj[Power BI bezpłatnie](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, konfigurowanie i uruchamianie Stream Analytics zadań

Zacznijmy od utworzenia zadania Stream Analytics zadań. Po utworzeniu zadania należy zdefiniować dane wejściowe, wyjściowe i zapytanie używane do pobierania danych.

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zadanie**  >  **Internet rzeczy**  >  **Stream Analytics zasobów.**

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów:** użyj tej samej grupy zasobów, która jest używana przez centrum IoT Hub.

   **Lokalizacja:** użyj tej samej lokalizacji co grupa zasobów.

   ![Tworzenie zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Wybierz przycisk **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz Stream Analytics zadania.

2. W obszarze **Topologia zadania** wybierz pozycję **Dane wejściowe**.

3. W **okienku Dane wejściowe** wybierz pozycję **Dodaj wejście strumienia,** a następnie **IoT Hub** pozycję z listy rozwijanej. W nowym okienku wejściowym wprowadź następujące informacje:

   **Alias danych wejściowych:** wprowadź unikatowy alias danych wejściowych.

   **Wybierz IoT Hub subskrypcji:** wybierz ten przycisk radiowy.

   **Subskrypcja:** wybierz subskrypcję platformy Azure używaną w tym samouczku.

   **IoT Hub:** wybierz IoT Hub, których używasz w tym samouczku.

   **Punkt końcowy**: wybierz pozycję **Obsługa komunikatów**.

   **Nazwa zasad dostępu** współdzielonych: wybierz nazwę zasad dostępu współdzielonych, które mają Stream Analytics dla centrum IoT. W tym samouczku możesz wybrać *usługę*. Zasady *usługi* są tworzone domyślnie w nowych centrach IoT Hub i przyznaje uprawnienia do wysyłania i odbierania w punktach końcowych po stronie chmury ujawnionych przez centrum IoT. Aby dowiedzieć się więcej, zobacz [Kontrola dostępu i uprawnienia](iot-hub-devguide-security.md#access-control-and-permissions).

   **Klucz zasad dostępu współdzielonych:** to pole jest wypełniane automatycznie na podstawie wybranej nazwy zasad dostępu współdzieleniowego.

   **Grupa odbiorców:** wybierz utworzoną wcześniej grupę odbiorców.

   Pozostaw wartości domyślne we wszystkich pozostałych polach.

   ![Dodawanie danych wejściowych do zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Wybierz pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Dane wyjściowe**.

2. W **okienku Dane wyjściowe** wybierz pozycję **Dodaj** i **Power BI**.

3. W **okienku Power BI — Nowe** dane  wyjściowe wybierz pozycję Autoryzuj i postępuj zgodnie z monitami, aby zalogować się do Power BI konta.

4. Po zalogowaniu się do Power BI wprowadź następujące informacje:

   **Alias danych wyjściowych:** unikatowy alias danych wyjściowych.

   **Obszar roboczy grupy:** wybierz obszar roboczy grupy docelowej.

   **Nazwa zestawu** danych: wprowadź nazwę zestawu danych.

   **Nazwa tabeli:** wprowadź nazwę tabeli.

   **Tryb uwierzytelniania:** pozostaw wartość domyślną.

   ![Dodawanie danych wyjściowych do zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Wybierz pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.

   ![Dodawanie zapytania do zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Wybierz **pozycję Zapisz zapytanie.**

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu Stream Analytics pozycję **Przegląd,** a następnie wybierz pozycję **Rozpocznij**  >    >  **teraz.** Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Tworzenie i publikowanie raportu Power BI w celu wizualizacji danych

Poniższe kroki pokazują, jak utworzyć i opublikować raport przy użyciu usługa Power BI. Możesz wykonać te kroki, z pewnymi zmianami, jeśli chcesz użyć "nowego wyglądu" w Power BI. Aby zrozumieć różnice i sposób nawigowania w "nowym wyglądze", zobacz ["Nowy wygląd"](/power-bi/consumer/service-new-look)usługa Power BI .

1. Upewnij się, że przykładowa aplikacja jest uruchomiona na urządzeniu. Jeśli nie, możesz zapoznać się z samouczkami w obszarze [Konfigurowanie urządzenia.](./iot-hub-raspberry-pi-kit-node-get-started.md)

2. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/en-us/).

3. Wybierz użyty obszar roboczy Mój **obszar roboczy.**

4. Wybierz **pozycję Zestawy danych.**

   Powinien zostać wyświetlony zestaw danych określony podczas tworzenia danych wyjściowych dla Stream Analytics danych.

5. Dla utworzonego zestawu danych wybierz **pozycję Dodaj raport** (pierwszą ikonę po prawej stronie nazwy zestawu danych).

   ![Tworzenie raportu Power BI Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   1. W **okienku Wizualizacje** na stronie tworzenia raportu wybierz ikonę wykresu liniowego, aby dodać wykres liniowy.

   2. W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics.

   3. Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   4. Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

      Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

      ![Dodawanie wykresu liniowego temperatury do raportu Power BI Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. W tym celu kliknij pustą część kanwy i wykonaj powyższe kroki, aby umieścić element **EventEnqueuedUtcTime** na osi x i wilgotność na osi y. 

   ![Dodawanie wykresu liniowego wilgotności do raportu Power BI Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Wybierz **pozycję Zapisz,** aby zapisać raport.

9. Wybierz **pozycję** Raporty w okienku po lewej stronie, a następnie wybierz właśnie utworzony raport.

10. Wybierz **pozycję Publikuj** plik w sieci  >  **Web.**

    ![Wybieranie opcji Publikuj w sieci Web dla raportu Power BI Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Jeśli otrzymasz powiadomienie o konieczności skontaktowania się z administratorem w celu włączenia tworzenia kodu osadzania, może być konieczne skontaktowanie się z nim. Przed ukończeniem tego kroku należy włączyć tworzenie kodu osadzania.
    >
    > ![Skontaktuj się z administratorem z powiadomieniem](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Wybierz **pozycję Utwórz kod osadzania,** a następnie wybierz pozycję **Opublikuj**.

Podano link do raportu, który można udostępnić innym osobom w celu uzyskania dostępu do raportu, oraz fragment kodu, za pomocą których można zintegrować raport z blogiem lub witryną internetową.

![Publikowanie raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

Firma Microsoft oferuje również [Power BI mobilnych do](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) wyświetlania pulpitów nawigacyjnych Power BI i raportów na urządzeniu przenośnym oraz korzystania z tych aplikacji.

## <a name="next-steps"></a>Następne kroki

Pomyślnie za pomocą usługi Power BI wizualizowanie danych czujników w czasie rzeczywistym z centrum Azure IoT Hub.

Aby uzyskać inny sposób wizualizowania danych z Azure IoT Hub, zobacz Używanie aplikacji internetowej do wizualizacji danych czujników w czasie rzeczywistym [z Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
