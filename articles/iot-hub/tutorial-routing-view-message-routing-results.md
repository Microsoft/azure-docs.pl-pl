---
title: Wyświetlanie wyników routingu komunikatów usługi Azure IoT Hub (.NET) | Microsoft Docs
description: Po skonfigurowaniu wszystkich zasobów przy użyciu części 1 samouczka Dodaj możliwość kierowania wiadomości do Azure Stream Analytics i wyświetlania wyników w usłudze PowerBI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: ddba41acfcfe497059a661b701906cda0d4d77c6
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398378"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Samouczek: część 2 — Wyświetlanie rozesłanych komunikatów

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Reguły routingu komunikatów

Są to reguły routingu wiadomości. zostały one skonfigurowane w części 1 tego samouczka i zobaczysz, że działają one w tej drugiej części.

|Wartość |Wynik|
|------|------|
|level=„storage” |Zapis w usłudze Azure Storage.|
|level=„critical” |Zapis w kolejce usługi Service Bus. Aplikacja logiki pobiera komunikat z kolejki i używa usługi Office 365 do wysłania go pocztą e-mail.|
|default |Wyświetlanie tych danych przy użyciu usługi Power BI.|

Teraz utworzysz zasoby, do których będą kierowane komunikaty, uruchom aplikację w celu wysłania komunikatów do centrum i zobacz, jak działa Routing.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki  

Kolejka usługi Service Bus jest używana do odbierania komunikatów oznaczonych jako krytyczne. Skonfiguruj aplikację logiki, aby monitorować kolejkę usługi Service Bus i wysyłać wiadomość e-mail po dodaniu komunikatu do kolejki.

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **+ Utwórz zasób**. W polu wyszukiwania wpisz ciąg **aplikacja logiki** i naciśnij klawisz Enter. W wyświetlonych wynikach wyszukiwania wybierz pozycję Aplikacja logiki, a następnie wybierz pozycję **Utwórz** , aby przejść do okienka **Tworzenie aplikacji logiki** . Wypełnij pola.

   **Nazwa**: to pole zawiera nazwę aplikacji logiki. W tym samouczku jest używana nazwa **ContosoLogicApp**.

   **Subskrypcja**: wybierz subskrypcję platformy Azure.

   **Grupa zasobów**: wybierz pozycję **Użyj istniejącej** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

   **Lokalizacja**: użyj swojej lokalizacji. W tym samouczku jest używany region **Zachodnie stany USA**.

   **Log Analytics**: ten przełącznik powinien być wyłączony.

   ![Ekran Tworzenie aplikacji logiki](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Wybierz przycisk **Utwórz**. Wdrożenie aplikacji może potrwać kilka minut.

2. Teraz przejdź do aplikacji logiki. Najprostszym sposobem, aby uzyskać dostęp do aplikacji logiki, jest wybranie **grup zasobów**, wybranie grupy zasobów (w tym samouczku jest używany program **ContosoResources**), a następnie wybierz aplikację logiki z listy zasobów. 

    Zostanie wyświetlona strona Projektanta aplikacji usługi Logic Apps (w celu wyświetlenia całej strony może być konieczne przewinięcie w prawo). Na stronie projektanta Logic Apps przewiń w dół do momentu wyświetlenia kafelka z informacją o **pustej aplikacji logiki +** i wybierz ją. Domyślna karta to "dla użytkownika". Jeśli to okienko jest puste, zaznacz **wszystkie** , aby wyświetlić wszystkie dostępne łączniki i wyzwalacze.

3. Wybierz pozycję **Service Bus** z listy łączników.

   ![Lista łączników](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Zostanie wyświetlona lista wyzwalaczy. Wybierz **, kiedy wiadomość zostanie odebrana w kolejce (Autouzupełnianie)/Service Bus**.

   ![Lista wyzwalaczy dla Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Na następnym ekranie wprowadź nazwę połączenia. W tym samouczku jest używana nazwa **ContosoConnection**.

   ![Konfigurowanie połączenia dla kolejki Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Wybierz przestrzeń nazw Service Bus. W tym samouczku jest używana nazwa **ContosoSBNamespace**. Po wybraniu przestrzeni nazw portal wykonuje zapytanie w przestrzeni nazw usługi Service Bus w celu pobrania kluczy. Wybierz pozycję **RootManageSharedAccessKey** i wybierz pozycję **Utwórz**.

   ![Kończenie konfigurowania połączenia](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Na następnym ekranie wybierz nazwę kolejki (w tym samouczku jest używana kolejka **contososbqueue**) z listy rozwijanej. W pozostałych polach można użyć wartości domyślnych.

   ![Opcje kolejki](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Teraz skonfiguruj akcję wysyłania wiadomości e-mail po odebraniu komunikatu w kolejce. W projektancie Logic Apps wybierz pozycję **+ nowy krok** , aby dodać krok, a następnie wybierz pozycję **wszystkie** , aby wyświetlić wszystkie dostępne opcje. W okienku **Wybierz akcję** Znajdź i wybierz pozycję **Office 365 Outlook**. Na ekranie Akcje wybierz pozycję **Wyślij wiadomość e-mail/Office 365 Outlook**.  

   ![Opcje usługi Office 365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Zaloguj się do konta służbowego, aby skonfigurować połączenie. W przypadku przeprowadzenia tej operacji po prostu spróbuj ponownie. Podaj adresy e-mail adresatów wiadomości e-mail. Określ temat, a następnie wpisz treść wiadomości, którą ma zobaczyć adresat. Na potrzeby testowania wprowadź własny adres e-mail jako adresata.

   Wybierz pozycję **Dodaj zawartość dynamiczną** , aby wyświetlić zawartość z wiadomości, którą można uwzględnić. Wybierz pozycję **Zawartość** — będzie ona obejmować komunikat z wiadomości e-mail.

   ![Opcje poczty e-mail dla aplikacji logiki](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Wybierz pozycję **Zapisz**. Następnie zamknij Projektanta aplikacji logiki.

## <a name="set-up-azure-stream-analytics"></a>Konfigurowanie usługi Azure Stream Analytics

Aby wyświetlić dane w wizualizacji usługi Power BI, najpierw skonfiguruj zadanie usługi Stream Analytics w celu pobrania danych. Pamiętaj, że tylko komunikaty, w których właściwość **level** ma wartość **normal**, są wysyłane do domyślnego punktu końcowego i zostaną pobrane przez zadanie usługi Stream Analytics na potrzeby wizualizacji usługi Power BI.

### <a name="create-the-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**  >  **Internet rzeczy**  >  **Stream Analytics zadanie**.

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej. W tym samouczku jest używana nazwa **contosoJob**.

   **Subskrypcja**: subskrypcja platformy Azure, która jest używana na potrzeby samouczka.

   **Grupa zasobów**: użyj grupy zasobów stosowanej już w centrum IoT Hub. W tym samouczku jest używana grupa **ContosoResources**.

   **Lokalizacja**: użyj lokalizacji stosowanej już w skrypcie konfiguracji. W tym samouczku jest używany region **Zachodnie stany USA**.

   ![Tworzenie zadania usługi Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Wybierz pozycję **Utwórz** , aby utworzyć zadanie. Wdrożenie może potrwać kilka minut.

    Aby wrócić do zadania, wybierz pozycję **grupy zasobów**. W tym samouczku jest używana grupa **ContosoResources**. Wybierz grupę zasobów, a następnie wybierz zadanie Stream Analytics na liście zasobów.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. W obszarze **topologia zadania**wybierz pozycję **dane wejściowe**.

2. W okienku **dane** wejściowe wybierz pozycję **Dodaj strumień wejściowy** i wybierz pozycję IoT Hub. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias wejściowy**: w tym samouczku jest używany alias **contosoinputs**.

   **Wybierz IoT Hub z subskrypcji**: zaznacz tę opcję przycisku radiowego.

   **Subskrypcja**: wybierz subskrypcję platformy Azure, która jest używana w tym samouczku.

   **IoT Hub**: wybierz Centrum IoT. W tym samouczku jest używane centrum **ContosoTestHub**.

   **Punkt końcowy**: wybierz pozycję **Obsługa komunikatów**. (W przypadku wybrania pozycji Monitorowanie operacji otrzymasz dane telemetryczne dotyczące centrum IoT Hub, a nie wysyłane dane). 

   **Nazwa zasad dostępu współdzielonego**: wybierz pozycję **Usługa**. Portal wypełni klucz wstępny zasad dostępu współdzielonego za Ciebie.

   **Grupa konsumentów**: Wybierz grupę odbiorców skonfigurowaną w części 1 tego samouczka. W tym samouczku jest używana grupa **contosoconsumers**.
   
   W pozostałych polach zaakceptuj wartości domyślne. 

   ![Konfigurowanie danych wejściowych dla zadania usługi Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Wybierz pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **topologia zadania**wybierz pozycję dane **wyjściowe**.

2. W okienku dane **wyjściowe** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Power BI**. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias wyjściowy**: unikatowy alias danych wyjściowych. W tym samouczku jest używany alias **contosooutputs**. 

   **Nazwa zestawu danych**: nazwa zestawu danych do użycia w usłudze Power BI. W tym samouczku jest używany zestaw danych **contosodataset**. 

   **Nazwa tabeli**: nazwa tabeli do użycia w usłudze Power BI. W tym samouczku jest używana tabela **contosotable**.

   Zaakceptuj wartości domyślne w pozostałych polach.

3. Wybierz pozycję **Autoryzuj**i zaloguj się na koncie Power BI. (Może to potrwać więcej niż jedna próba).

   ![Konfigurowanie danych wyjściowych dla zadania usługi Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Wybierz pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **topologia zadania**wybierz pozycję **zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania. W tym samouczku jest używany alias **contosoinputs**.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania. W tym samouczku jest używany alias **contosooutputs**.

   ![Konfigurowanie zapytania dla zadania usługi Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Wybierz pozycję **Zapisz**.

5. Zamknij okienko Zapytanie. Powrócisz do widoku zasobów w grupie zasobów. Wybierz zadanie Stream Analytics. W tym samouczku nosi ono nazwę **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W Stream Analytics zadania wybierz pozycję **Rozpocznij**  >  **teraz**  >  **Start**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

Do skonfigurowania raportu usługi Power BI potrzebujesz danych, dlatego po utworzeniu urządzenia i uruchomieniu aplikacji do symulacji urządzenia skonfigurujesz usługę Power BI.

## <a name="run-simulated-device-app"></a>Uruchom symulowaną aplikację urządzenia

W części 1 tego samouczka skonfigurujesz urządzenie w celu symulowania użycia urządzenia IoT. W tej sekcji pobrano aplikację konsolową .NET, która symuluje wysyłanie komunikatów z urządzenia do chmury do usługi IoT Hub (przy założeniu, że aplikacja i zasoby nie zostały jeszcze pobrane w części 1).

Ta aplikacja wysyła komunikaty dla każdej z różnych metod routingu komunikatów. Znajduje się w nim również folder do pobrania zawierający kompletny plik szablonu i parametrów Azure Resource Manager, a także skrypty interfejsu wiersza polecenia platformy Azure i programu PowerShell.

Jeśli nie pobrano plików z repozytorium w części 1 tego samouczka, możesz pobrać je teraz z [symulacji urządzenia IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Wybranie tego linku spowoduje pobranie repozytorium z kilkoma aplikacjami. szukane rozwiązanie to IoT-Hub/samouczki/Routing/IoT_SimulatedDevice. sln. 

Kliknij dwukrotnie plik rozwiązania (IoT_SimulatedDevice. sln), aby otworzyć kod w programie Visual Studio, a następnie otwórz Program.cs. Zastąp element `{your hub name}` nazwą hosta centrum IoT Hub. Format nazwy hosta centrum IoT Hub to **{nazwa-centrum-iot}.azure-devices.net**. W tym samouczku jest używana nazwa hosta centrum **ContosoTestHub.azure-devices.net**. Następnie zastąp ciąg `{your device key}` kluczem urządzenia, który został wcześniej zapisany podczas konfigurowania urządzenia symulowanego. 

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

   ![Wyniki wiadomości e-mail](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   W związku z tym następujące instrukcje są spełnione. 

   * Routing do kolejki usługi Service Bus działa poprawnie.
   * Pobieranie komunikatu z kolejki usługi Service Bus do aplikacji logiki działa poprawnie.
   * Łącznik aplikacji logiki do programu Outlook działa poprawnie. 

2. W [Azure Portal](https://portal.azure.com)wybierz pozycję **grupy zasobów** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**. 

    Wybierz konto magazynu, wybierz pozycję **kontenery**, a następnie wybierz kontener. W tym samouczku jest używany kontener **contosoresults**. Powinien pojawić się folder, w którym będzie można przechodzić do szczegółów katalogów do momentu wyświetlenia co najmniej jednego pliku. Otwórz jeden z tych plików; zawierają one wpisy kierowane do konta magazynu. 

   ![Pliki wyników w magazynie](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Ten wynik oznacza, że następująca instrukcja ma wartość true.

   * Routing do konta magazynu działa poprawnie.

Teraz, gdy aplikacja nadal działa, skonfiguruj wizualizację Power BI, aby zobaczyć komunikaty przechodzące przez domyślny Routing.

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurowanie wizualizacji usługi Power BI

1. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/).

2. Przejdź do obszaru **Obszary robocze** i wybierz obszar roboczy ustawiony podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana nazwa **Mój obszar roboczy**. 

3. Wybierz pozycję **zestawy danych**. Jeśli nie masz żadnych zestawów danych, odczekaj kilka minut i sprawdź ponownie.

   Powinien pojawić się wymieniony na liście zestaw danych, który został wybrany podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używany zestaw danych **contosodataset**. (Wyświetlenie zestawu danych po raz pierwszy może potrwać 5–10 minut).

4. W obszarze **Akcje**wybierz pierwszą ikonę, aby utworzyć raport.

   ![Obszar roboczy Power BI z wyróżnioną akcją i ikoną raportu](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   * Na stronie Tworzenie raportu Dodaj wykres liniowy, wybierając ikonę wykresu liniowego.

     ![Wizualizacje i pola](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana tabela **contosotable**.

   * Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   * Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

   Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

6. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby skonfigurować drugi wykres, postępuj zgodnie z tym samym procesem dla pierwszego wykresu, umieszczając **EventEnqueuedUtcTime** na osi x (**osi**) i **wilgotności** na osi y (**wartości**).

   ![Końcowy Power BI Raport z dwoma wykresami](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Wybierz pozycję **Zapisz** , aby zapisać raport, wprowadzając nazwę raportu, jeśli zostanie wyświetlony monit.

Powinny być widoczne dane na obydwu wykresach. Ten wynik oznacza, że prawdziwe są następujące instrukcje:

   * Routing do domyślnego punktu końcowego działa poprawnie.
   * Zadanie usługi Azure Stream Analytics jest poprawnie przesyłane strumieniowo.
   * Wizualizacja usługi Power BI została prawidłowo skonfigurowana.

Można odświeżyć wykresy, aby zobaczyć najnowsze dane, wybierając przycisk Odśwież w górnej części okna Power BI. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów 

Jeśli chcesz usunąć wszystkie zasoby platformy Azure utworzone w ramach obu części tego samouczka, Usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W naszym przypadku nastąpi usunięcie centrum IoT Hub, przestrzeni nazw i kolejki usługi Service Bus, aplikacji logiki, konta magazynu i samej grupy zasobów. Możesz również usunąć zasoby Power BI i wyczyścić wiadomości e-mail wysyłane w ramach tego samouczka.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Oczyszczanie zasobów w wizualizacji usługi Power BI

Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/). Przejdź do swojego obszaru roboczego. W tym samouczku jest używana nazwa **Mój obszar roboczy**. Aby usunąć wizualizację Power BI, przejdź do pozycji zestawy danych, a następnie wybierz ikonę kosza, aby usunąć zestaw danych. W tym samouczku jest używany zestaw danych **contosodataset**. Podczas usuwania zestawu danych usuwany jest również raport.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Czyszczenie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć grupę zasobów, użyj polecenia [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` została ustawiona na **ContosoResources** z powrotem na początku tego samouczka.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Czyszczenie zasobów przy użyciu programu PowerShell

Aby usunąć grupę zasobów, użyj polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup` została ustawiona na **ContosoResources** z powrotem na początku tego samouczka.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Wyczyść testowe wiadomości e-mail

Możesz również usunąć liczbę wiadomości e-mail w skrzynce odbiorczej, które zostały wygenerowane przez aplikację logiki, gdy aplikacja urządzenia była uruchomiona.

## <a name="next-steps"></a>Następne kroki

W tym dwuczęściowym samouczku przedstawiono sposób używania routingu komunikatów do kierowania komunikatów IoT Hub do różnych miejsc docelowych, wykonując następujące zadania.  

**Część I: Tworzenie zasobów, Konfigurowanie routingu komunikatów**
> [!div class="checklist"]
> * Tworzenie zasobów — Centrum IoT Hub, konto magazynu, kolejka Service Bus i symulowane urządzenie.
> * Skonfiguruj punkty końcowe i trasy komunikatów w IoT Hub dla konta magazynu i kolejki Service Bus.

**Część II: wysyłanie komunikatów do centrum, wyświetlanie rozesłanych wyników**
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
