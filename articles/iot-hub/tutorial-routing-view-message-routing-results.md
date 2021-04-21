---
title: Samouczek — wyświetlanie Azure IoT Hub routingu komunikatów (.NET) | Microsoft Docs
description: Samouczek — po skonfigurowaniu wszystkich zasobów przy użyciu części 1 samouczka dodaj możliwość rozsyłania komunikatów do usługi Azure Stream Analytics i wyświetlania wyników w Power BI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 7d6b5f02a752e7bbdc1d4da83b60b1a86aba5626
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775029"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Samouczek: część 2 — wyświetlanie komunikatów tras

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Reguły routingu komunikatów

Są to reguły routingu komunikatów. Zostały one ustawione w części 1 tego samouczka i zobaczysz, że działają one w drugiej części.

|Wartość |Wynik|
|------|------|
|level=„storage” |Zapis w usłudze Azure Storage.|
|level=„critical” |Zapis w kolejce usługi Service Bus. Aplikacja logiki pobiera komunikat z kolejki i używa usługi Office 365 do wysłania go pocztą e-mail.|
|default |Wyświetlanie tych danych przy użyciu usługi Power BI.|

Teraz utworzysz zasoby, do których będą kierowane komunikaty, uruchom aplikację w celu wysyłania komunikatów do centrum i zobacz routing w działaniu.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki  

Kolejka usługi Service Bus jest używana do odbierania komunikatów oznaczonych jako krytyczne. Skonfiguruj aplikację logiki, aby monitorować kolejkę usługi Service Bus i wysyłać wiadomość e-mail po dodaniu komunikatu do kolejki.

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **+ Utwórz zasób.** W polu wyszukiwania wpisz ciąg **aplikacja logiki** i naciśnij klawisz Enter. Z wyświetlonych wyników wyszukiwania wybierz pozycję Aplikacja logiki, a następnie wybierz **pozycję Utwórz,** aby kontynuować do **okienka Tworzenie aplikacji logiki.** Wypełnij pola.

   **Nazwa**: to pole zawiera nazwę aplikacji logiki. W tym samouczku jest używana nazwa **ContosoLogicApp**.

   **Subskrypcja**: wybierz subskrypcję platformy Azure.

   **Grupa zasobów:** wybierz **pozycję Użyj istniejącej** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

   **Lokalizacja**: użyj swojej lokalizacji. W tym samouczku jest używany region **Zachodnie stany USA**.

   **Włącz usługę Log Analytics:** ten przełącznik powinien być wyłączony.

   ![Ekran Tworzenie aplikacji logiki](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Wybierz przycisk **Utwórz**. Wdrażanie aplikacji może potrwać kilka minut.

2. Teraz przejdź do aplikacji logiki. Najprostszym sposobem uzyskania dostępu do aplikacji logiki jest wybranie grupy **zasobów,** wybranie grupy zasobów (w tym samouczku jest używana **grupa ContosoResources),** a następnie wybranie aplikacji logiki z listy zasobów. 

    Zostanie wyświetlona strona Projektanta aplikacji usługi Logic Apps (w celu wyświetlenia całej strony może być konieczne przewinięcie w prawo). Na stronie Logic Apps Projektanta przewiń w dół do kafelka z pustą aplikacją **logiki +** i wybierz go. Karta domyślna to "Dla Ciebie". Jeśli to okienko jest puste, wybierz pozycję **Wszystkie,** aby wyświetlić wszystkie dostępne łączniki i wyzwalacze.

3. Wybierz **Service Bus** z listy łączników.

   ![Lista łączników](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Zostanie wyświetlona lista wyzwalaczy. Wybierz **pozycję Po otrzymaniu komunikatu w kolejce (autouzupełnia) / Service Bus**.

   ![Lista wyzwalaczy dla Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Na następnym ekranie wprowadź nazwę połączenia. W tym samouczku jest używana nazwa **ContosoConnection**.

   ![Konfigurowanie połączenia dla kolejki Service Bus kolejki](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Wybierz przestrzeń Service Bus nazw. W tym samouczku jest używana nazwa **ContosoSBNamespace**. Po wybraniu przestrzeni nazw portal wykonuje zapytanie w przestrzeni nazw usługi Service Bus w celu pobrania kluczy. Wybierz **pozycję RootManageSharedAccessKey** i wybierz **pozycję Utwórz**.

   ![Kończenie konfigurowania połączenia](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Na następnym ekranie wybierz nazwę kolejki (w tym samouczku jest używana kolejka **contososbqueue**) z listy rozwijanej. W pozostałych polach można użyć wartości domyślnych.

   ![Opcje kolejki](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Teraz skonfiguruj akcję wysyłania wiadomości e-mail po odebraniu komunikatu w kolejce. W projektancie Logic Apps wybierz **pozycję + Nowy krok,** aby dodać krok, a następnie wybierz pozycję **Wszystkie,** aby wyświetlić wszystkie dostępne opcje. W **okienku Wybierz akcję** znajdź i wybierz pozycję **Office 365 Outlook.** Na ekranie Akcje wybierz pozycję **Wyślij wiadomość e-mail/Office 365 Outlook.**  

   ![Opcje usługi Office 365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Zaloguj się do konta służbowego, aby skonfigurować połączenie. Jeśli ten przekłynie ten czas, po prostu spróbuj ponownie. Podaj adresy e-mail adresatów wiadomości e-mail. Określ temat, a następnie wpisz treść wiadomości, którą ma zobaczyć adresat. Na potrzeby testowania wprowadź własny adres e-mail jako adresata.

   Wybierz **pozycję Dodaj zawartość dynamiczną,** aby wyświetlić zawartość z komunikatu, który można uwzględnić. Wybierz pozycję **Zawartość** — będzie ona obejmować komunikat z wiadomości e-mail.

   ![Opcje poczty e-mail dla aplikacji logiki](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Wybierz pozycję **Zapisz**. Następnie zamknij Projektanta aplikacji logiki.

## <a name="set-up-azure-stream-analytics"></a>Konfigurowanie usługi Azure Stream Analytics

Aby wyświetlić dane w wizualizacji usługi Power BI, najpierw skonfiguruj zadanie usługi Stream Analytics w celu pobrania danych. Pamiętaj, że tylko komunikaty, w których właściwość **level** ma wartość **normal**, są wysyłane do domyślnego punktu końcowego i zostaną pobrane przez zadanie usługi Stream Analytics na potrzeby wizualizacji usługi Power BI.

### <a name="create-the-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób** Internet rzeczy  >    >  **Stream Analytics zadania**.

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej. W tym samouczku jest używana nazwa **contosoJob**.

   **Subskrypcja:** subskrypcja platformy Azure, z których korzystasz w ramach tego samouczka.

   **Grupa zasobów**: użyj grupy zasobów stosowanej już w centrum IoT Hub. W tym samouczku jest używana grupa **ContosoResources**.

   **Lokalizacja**: użyj lokalizacji stosowanej już w skrypcie konfiguracji. W tym samouczku jest używany region **Zachodnie stany USA**.

   ![Tworzenie zadania usługi Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Wybierz **pozycję Utwórz,** aby utworzyć zadanie. Wdrożenie może potrwać kilka minut.

    Aby wrócić do zadania, wybierz pozycję **Grupy zasobów.** W tym samouczku jest używana grupa **ContosoResources**. Wybierz grupę zasobów, a następnie wybierz Stream Analytics na liście zasobów.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. W **obszarze Topologia zadania** wybierz pozycję Dane **wejściowe**.

2. W **okienku Dane wejściowe** wybierz pozycję **Dodaj wejście strumienia** i wybierz pozycję IoT Hub. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias wejściowy**: w tym samouczku jest używany alias **contosoinputs**.

   **Wybierz IoT Hub subskrypcji:** wybierz tę opcję przycisku radiowego.

   **Subskrypcja:** wybierz subskrypcję platformy Azure używaną w tym samouczku.

   **IoT Hub:** wybierz centrum IoT Hub. W tym samouczku jest używane centrum **ContosoTestHub**.

   **Punkt końcowy**: wybierz pozycję **Obsługa komunikatów**. (W przypadku wybrania pozycji Monitorowanie operacji otrzymasz dane telemetryczne dotyczące centrum IoT Hub, a nie wysyłane dane). 

   **Nazwa zasad dostępu współdzielonych:** Wybierz **usługę**. Portal wypełni klucz wstępny zasad dostępu współdzielonego za Ciebie.

   **Grupa konsumentów:** wybierz grupę konsumentów ustawioną w części 1 tego samouczka. W tym samouczku jest używana grupa **contosoconsumers**.
   
   W pozostałych polach zaakceptuj wartości domyślne. 

   ![Konfigurowanie danych wejściowych dla zadania usługi Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Wybierz pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W **obszarze Topologia zadania** wybierz pozycję Dane **wyjściowe**.

2. W **okienku Dane wyjściowe** wybierz pozycję **Dodaj,** a następnie wybierz pozycję **Power BI**. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias wyjściowy**: unikatowy alias danych wyjściowych. W tym samouczku jest używany alias **contosooutputs**. 

   **Nazwa zestawu danych**: nazwa zestawu danych do użycia w usłudze Power BI. W tym samouczku jest używany zestaw danych **contosodataset**. 

   **Nazwa tabeli**: nazwa tabeli do użycia w usłudze Power BI. W tym samouczku jest używana tabela **contosotable**.

  **Tryb uwierzytelniania:** wybierz tryb do użycia.

   Zaakceptuj wartości domyślne w pozostałych polach.

3. Wybierz **pozycję** Autoryzuj i zaloguj się do Power BI konta. (Może to potrwać więcej niż jedną próbę).

   ![Konfigurowanie danych wyjściowych zadania usługi Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Wybierz pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W **obszarze Topologia zadania** wybierz pozycję **Zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania. W tym samouczku jest używany alias **contosoinputs**.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania. W tym samouczku jest używany alias **contosooutputs**.

   ![Konfigurowanie zapytania dla zadania usługi Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Wybierz pozycję **Zapisz**.

5. Zamknij okienko Zapytanie. Powrócisz do widoku zasobów w grupie zasobów. Wybierz Stream Analytics zadania. W tym samouczku nosi ono nazwę **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu Stream Analytics wybierz pozycję **Uruchom**  >  **teraz**  >  **Uruchom.** Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

Do skonfigurowania raportu usługi Power BI potrzebujesz danych, dlatego po utworzeniu urządzenia i uruchomieniu aplikacji do symulacji urządzenia skonfigurujesz usługę Power BI.

## <a name="run-simulated-device-app"></a>Uruchamianie aplikacji symulowanego urządzenia

W części 1 tego samouczka skonfigurujemy urządzenie do symulowania przy użyciu urządzenia IoT. W tej sekcji pobierzemy aplikację konsolową .NET, która symuluje to urządzenie wysyłające komunikaty z urządzenia do chmury do centrum IoT (przy założeniu, że aplikacja i zasoby nie zostały jeszcze pobrani w części 1).

Ta aplikacja wysyła komunikaty dla każdej z różnych metod routingu komunikatów. W pliku do pobrania znajduje się również folder zawierający kompletny szablon Azure Resource Manager plik parametrów, a także skrypty interfejsu wiersza polecenia platformy Azure i programu PowerShell.

Jeśli nie pobrano plików z repozytorium w części 1 tego samouczka, pobierz je teraz z symulacji urządzenia [IoT.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Wybranie tego linku pobiera repozytorium z kilkoma aplikacjami. Szukane rozwiązanie to iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Kliknij dwukrotnie plik rozwiązania (IoT_SimulatedDevice.sln), aby otworzyć kod w pliku Visual Studio, a następnie otwórz plik Program.cs. Zastąp element `{your hub name}` nazwą hosta centrum IoT Hub. Format nazwy hosta centrum IoT Hub to **{nazwa-centrum-iot}.azure-devices.net**. W tym samouczku jest używana nazwa hosta centrum **ContosoTestHub.azure-devices.net**. Następnie zastąp ciąg `{your device key}` kluczem urządzenia, który został wcześniej zapisany podczas konfigurowania urządzenia symulowanego. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Uruchamianie i testowanie

Uruchom aplikację konsolową. Zaczekaj kilka minut. Możesz zobaczyć komunikaty wysyłane na ekranie konsoli aplikacji.

Aplikacja wysyła nowy komunikat z urządzenia do centrum IoT Hub co sekundę. Komunikat zawiera obiekt serializowany w notacji JSON z identyfikatorem urządzenia, temperaturą, wilgotnością i poziomem komunikatu ustawianym domyślnie na `normal`. Poziom jest losowo przypisywany jako `critical` lub `storage`, co powoduje kierowanie komunikatu do konta magazynu lub do kolejki usługi Service Bus (która wyzwala aplikację logiki, aby wysłać wiadomość e-mail). Odczyty domyślne (`normal`) zostaną wyświetlone w raporcie analizy biznesowej konfigurowanym w kolejnym kroku.

Jeśli wszystko zostanie prawidłowo skonfigurowane, w tym momencie powinny być widoczne następujące wyniki:

1. Możesz zacząć otrzymywać wiadomości e-mail dotyczące komunikatów o krytycznym znaczeniu.

   ![Wynikowe wiadomości e-mail](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Ten wynik oznacza, że następujące instrukcje są prawdziwe. 

   * Routing do kolejki usługi Service Bus działa poprawnie.
   * Pobieranie komunikatu z kolejki usługi Service Bus do aplikacji logiki działa poprawnie.
   * Łącznik aplikacji logiki do programu Outlook działa poprawnie. 

2. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Grupy zasobów** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**. 

    Wybierz konto magazynu, wybierz pozycję **Kontenery,** a następnie wybierz pozycję Kontener. W tym samouczku jest używany kontener **contosoresults**. Powinien pojawić się folder, w którym będzie można przechodzić do szczegółów katalogów do momentu wyświetlenia co najmniej jednego pliku. Otwórz jeden z tych plików; zawierają one wpisy kierowane do konta magazynu. 

   ![Pliki wyników w magazynie](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Ten wynik oznacza, że następująca instrukcja jest prawdziwa.

   * Routing do konta magazynu działa poprawnie.

Teraz, gdy aplikacja jest nadal uruchomiona, skonfiguruj wizualizację Power BI, aby zobaczyć komunikaty przychodzące przez routing domyślny.

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurowanie wizualizacji usługi Power BI

1. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/).

2. Przejdź do obszaru **Obszary robocze** i wybierz obszar roboczy ustawiony podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana nazwa **Mój obszar roboczy**. 

3. Wybierz **pozycję Zestawy danych.** Jeśli nie masz żadnych zestawów danych, odczekaj kilka minut i sprawdź ponownie.

   Powinien pojawić się wymieniony na liście zestaw danych, który został wybrany podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używany zestaw danych **contosodataset**. (Wyświetlenie zestawu danych po raz pierwszy może potrwać 5–10 minut).

4. W **obszarze AKCJE** wybierz pierwszą ikonę, aby utworzyć raport.

   ![Power BI z wyróżniona ikony Akcje i raport](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   * Na stronie tworzenia raportu dodaj wykres liniowy, wybierając ikonę wykresu liniowego.

     ![Wizualizacje i pola](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana tabela **contosotable**.

   * Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   * Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

   Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

6. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby skonfigurować drugi wykres, wykonaj ten sam proces dla pierwszego wykresu, umieszczając obiekt **EventEnqueuedUtcTime** na osi x **(oś**) i wilgotność na osi y **(wartości**). 

   ![Ostateczny raport Power BI z dwoma wykresami](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Wybierz **pozycję** Zapisz, aby zapisać raport, wprowadzając nazwę raportu, jeśli zostanie wyświetlony monit.

Powinny być widoczne dane na obydwu wykresach. Ten wynik oznacza, że następujące instrukcje są prawdziwe:

   * Routing do domyślnego punktu końcowego działa poprawnie.
   * Zadanie usługi Azure Stream Analytics jest poprawnie przesyłane strumieniowo.
   * Wizualizacja usługi Power BI została prawidłowo skonfigurowana.

Możesz odświeżyć wykresy, aby wyświetlić najnowsze dane, wybierając przycisk Odśwież w górnej części Power BI danych. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów 

Jeśli chcesz usunąć wszystkie zasoby platformy Azure utworzone w obu częściach tego samouczka, usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W naszym przypadku nastąpi usunięcie centrum IoT Hub, przestrzeni nazw i kolejki usługi Service Bus, aplikacji logiki, konta magazynu i samej grupy zasobów. Możesz również usunąć zasoby Power BI i wyczyścić wiadomości e-mail wysłane podczas tego samouczka.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Oczyszczanie zasobów w wizualizacji usługi Power BI

Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/). Przejdź do swojego obszaru roboczego. W tym samouczku jest używana nazwa **Mój obszar roboczy**. Aby usunąć wizualizację Power BI, przejdź do sekcji Zestawy danych i wybierz ikonę kosza, aby usunąć zestaw danych. W tym samouczku jest używany zestaw danych **contosodataset**. Podczas usuwania zestawu danych usuwany jest również raport.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Oczyszczanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć grupę zasobów, użyj polecenia [az group delete](/cli/azure/group#az_group_delete). `$resourceGroup`Na początku tego samouczka ustawiono wartość **ContosoResources.**

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Oczyszczanie zasobów przy użyciu programu PowerShell

Aby usunąć grupę zasobów, użyj polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup`Na początku tego samouczka ustawiono wartość **ContosoResources.**

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Czyszczenie testowych wiadomości e-mail

Możesz również usunąć liczbę wiadomości e-mail w skrzynce odbiorczej, które zostały wygenerowane za pośrednictwem aplikacji logiki, gdy aplikacja urządzenia była uruchomiona.

## <a name="next-steps"></a>Następne kroki

W tym 2-częściowy samouczek przedstawiono sposób używania routingu komunikatów do kierowania komunikatów IoT Hub do różnych miejsc docelowych, wykonując następujące zadania.  

**Część I: Tworzenie zasobów, konfigurowanie routingu komunikatów**
> [!div class="checklist"]
> * Tworzenie zasobów — centrum IoT Hub, konta magazynu, Service Bus kolejki i urządzenia symulowanego.
> * Skonfiguruj punkty końcowe i trasy komunikatów w IoT Hub dla konta magazynu i Service Bus kolejki.

**Część II: Wysyłanie komunikatów do centrum, wyświetlanie trasowane wyniki**
> [!div class="checklist"]
> * Tworzenie aplikacji logiki, która jest wyzwalana i wysyła wiadomość e-mail po dodaniu komunikatu do kolejki usługi Service Bus.
> * Pobieranie i uruchamianie aplikacji, która symuluje urządzenie IoT wysyłające komunikaty do centrum przy użyciu różnych opcji routingu.
> * Tworzenie wizualizacji usługi Power BI na potrzeby danych wysyłanych do domyślnego punktu końcowego.
> * Przeglądanie wyników...
> * ...w kolejce usługi Service Bus i wiadomościach e-mail.
> * ...na koncie magazynu.
> * ...w wizualizacji usługi Power BI.

Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać stanem urządzenia IoT. 

> [!div class="nextstepaction"]
> [Konfigurowanie i używanie metryk i diagnostyki w usłudze IoT Hub](tutorial-use-metrics-and-diags.md)