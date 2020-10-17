---
title: Wizualizacja danych w czasie rzeczywistym danych FRM Azure IoT Hub — Power BI
description: Użyj Power BI, aby wizualizować dane temperatury i wilgotności zbierane z czujnika i wysyłane do usługi Azure IoT Hub.
author: robinsh
keywords: Wizualizacja danych w czasie rzeczywistym, Wizualizacja danych na żywo, Wizualizacja danych czujników
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 6d10d0202e9e4c813cffe6373acfb5200ebb3266
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146773"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Wizualizowanie danych z czujników w czasie rzeczywistym z poziomu platformy Azure IoT Hub przy użyciu Power BI

![Diagram kompleksowy](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak wizualizować dane czujników w czasie rzeczywistym, które usługa Azure IoT Hub otrzymuje przy użyciu Power BI. Jeśli chcesz spróbować wizualizować dane w centrum IoT przy użyciu aplikacji sieci Web, zobacz [Używanie aplikacji sieci Web do wizualizacji danych z czujników w czasie rzeczywistym z usługi Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co robisz

* Przygotuj Centrum IoT Hub do dostępu do danych, dodając grupę odbiorców.

* Utwórz, skonfiguruj i uruchom zadanie Stream Analytics na potrzeby transferu danych z Centrum IoT Hub do konta usługi Power BI.

* Utwórz i Opublikuj raport Power BI, aby wizualizować dane.

## <a name="what-you-need"></a>Potrzebne elementy

* Ukończ samouczek [gry online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jedno z samouczków dotyczących urządzeń; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Te artykuły obejmują następujące wymagania:
  
  * Aktywna subskrypcja platformy Azure.
  * Usługa Azure IoT Hub w ramach Twojej subskrypcji.
  * Aplikacja kliencka, która wysyła komunikaty do usługi Azure IoT Hub.

* Konto usługi Power BI. ([Wypróbuj Power BI bezpłatnie](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, Konfigurowanie i uruchamianie zadania Stream Analytics

Zacznijmy od utworzenia zadania Stream Analytics. Po utworzeniu zadania należy zdefiniować dane wejściowe, dane wyjściowe i zapytanie używane do pobierania danych.

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**  >  **Internet rzeczy**  >  **Stream Analytics zadanie**.

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów**: Użyj tej samej grupy zasobów, która jest używana przez Centrum IoT Hub.

   **Lokalizacja**: Użyj tej samej lokalizacji co grupa zasobów.

   ![Tworzenie zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Wybierz pozycję **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie Stream Analytics.

2. W obszarze **Topologia zadania** wybierz pozycję **Dane wejściowe**.

3. W okienku **dane** wejściowe wybierz pozycję **Dodaj strumień wejściowy**, a następnie wybierz pozycję **IoT Hub** z listy rozwijanej. W okienku nowe dane wejściowe wprowadź następujące informacje:

   **Alias wejściowy**: wprowadź unikatowy alias dla danych wejściowych.

   **Wybierz IoT Hub z subskrypcji**: Wybierz ten przycisk radiowy.

   **Subskrypcja**: wybierz subskrypcję platformy Azure, która jest używana w tym samouczku.

   **IoT Hub**: Wybierz IoT Hub używany w tym samouczku.

   **Punkt końcowy**: wybierz pozycję **Obsługa komunikatów**.

   **Nazwa zasad dostępu współdzielonego**: wybierz nazwę zasad dostępu współdzielonego, które mają być używane przez zadanie Stream Analytics dla Centrum IoT. Na potrzeby tego samouczka możesz wybrać pozycję *Usługa*. Zasady *usługi* są tworzone domyślnie w nowych centrach IoT i przyznają uprawnienia do wysyłania i odbierania na punktach końcowych w chmurze uwidocznionych przez Centrum IoT. Aby dowiedzieć się więcej, zobacz [Kontrola dostępu i uprawnienia](iot-hub-devguide-security.md#access-control-and-permissions).

   **Klucz zasad dostępu współdzielonego**: to pole jest wypełniane automatycznie na podstawie wyboru nazwy zasad dostępu współdzielonego.

   **Grupa konsumentów**: Wybierz utworzoną wcześniej grupę odbiorców.

   Pozostaw domyślne wszystkie inne pola.

   ![Dodawanie danych wejściowych do zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Wybierz pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Dane wyjściowe**.

2. W okienku dane **wyjściowe** wybierz pozycję **Dodaj** i **Power BI**.

3. W okienku **Power BI nowe dane wyjściowe** wybierz pozycję **Autoryzuj** i postępuj zgodnie z monitami, aby zalogować się do konta Power BI.

4. Po zalogowaniu się do Power BI wprowadź następujące informacje:

   **Alias wyjściowy**: unikatowy alias dla danych wyjściowych.

   **Obszar roboczy grupy**: Wybierz obszar roboczy grupy docelowej.

   **Nazwa zestawu danych**: Wprowadź nazwę zestawu danych.

   **Nazwa tabeli**: Wprowadź nazwę tabeli.

   **Tryb uwierzytelniania**: pozostaw wartość domyślną.

   ![Dodawanie danych wyjściowych do zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Wybierz pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.

   ![Dodawanie zapytania do zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Wybierz pozycję **Zapisz zapytanie**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu Stream Analytics wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Rozpocznij**  >  **teraz**  >  **Start**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Utwórz i Opublikuj raport Power BI, aby wizualizować dane

Poniższe kroki pokazują, jak utworzyć i opublikować raport przy użyciu usługa Power BI. Jeśli chcesz użyć "nowego wyglądu" w Power BI, możesz wykonać następujące czynności. Aby zrozumieć różnice i jak nawigować w "nowym wyszukiwaniu", zobacz ["nowy wygląd" Usługa Power BI](/power-bi/consumer/service-new-look).

1. Upewnij się, że przykładowa aplikacja jest uruchomiona na urządzeniu. Jeśli nie, możesz zapoznać się z samouczkami w obszarze [Konfigurowanie urządzenia](./iot-hub-raspberry-pi-kit-node-get-started.md).

2. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/en-us/).

3. Wybierz obszar roboczy, który jest używany, **Mój obszar roboczy**.

4. Wybierz pozycję **zestawy danych**.

   Powinien zostać wyświetlony zestaw danych, który został określony podczas tworzenia danych wyjściowych dla zadania Stream Analytics.

5. Dla utworzonego zestawu danych wybierz pozycję **Dodaj raport** (Pierwsza ikona z prawej strony nazwy zestawu danych).

   ![Tworzenie raportu Power BI firmy Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   1. W okienku **wizualizacje** na stronie Tworzenie raportu wybierz ikonę wykres liniowy, aby dodać wykres liniowy.

   2. W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics.

   3. Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   4. Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

      Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

      ![Dodaj wykres liniowy dla temperatury do raportu Power BI firmy Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby to zrobić, kliknij pustą część kanwy i postępuj zgodnie z powyższymi krokami, aby umieścić **EventEnqueuedUtcTime** na osi x i **wilgotności** na osi y.

   ![Dodaj wykres liniowy dla wilgotności do raportu Power BI firmy Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Wybierz pozycję **Zapisz** , aby zapisać raport.

9. W okienku po lewej stronie wybierz pozycję **raporty** , a następnie wybierz właśnie utworzony raport.

10. Wybierz pozycję **plik**  >  **Publikuj w sieci Web**.

    ![Wybierz pozycję Publikuj w sieci Web, aby zgłosić raport Power BI firmy Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Jeśli otrzymasz powiadomienie, aby skontaktować się z administratorem w celu włączenia tworzenia kodu osadzania, może być konieczne skontaktowanie się z nimi. Tworzenie kodu osadzania musi być włączone, aby można było wykonać ten krok.
    >
    > ![Skontaktuj się z powiadomieniem administratora](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Wybierz pozycję **Utwórz kod osadzania**, a następnie wybierz pozycję **Publikuj**.

Otrzymasz link do raportu, który można udostępnić każdemu użytkownikowi, który będzie miał dostęp do raportów, i fragment kodu, którego możesz użyć do zintegrowania raportu w blogu lub witrynie sieci Web.

![Publikowanie raportu Power BI firmy Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

Firma Microsoft oferuje również [Power BI aplikacje mobilne](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) do wyświetlania Power BI pulpitów nawigacyjnych i raportów na urządzeniu przenośnym oraz współpracy z nimi.

## <a name="next-steps"></a>Następne kroki

Pomyślnie użyto Power BI do wizualizacji danych czujników w czasie rzeczywistym z usługi Azure IoT Hub.

Aby uzyskać inny sposób wizualizacji danych z usługi Azure IoT Hub, zobacz [Korzystanie z aplikacji sieci Web w celu wizualizowania danych czujników w czasie rzeczywistym z poziomu platformy azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]