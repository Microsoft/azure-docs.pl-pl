---
title: Azure Event Hubs — wizualizowanie anomalii danych w zdarzeniach w czasie rzeczywistym
description: 'Samouczek: wizualizowanie anomalii dotyczących danych w zdarzeniach w czasie rzeczywistym wysyłanych do Microsoft Azure Event Hubs'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: bb7d8da2498005b8b2e1183a836d9385f3d31e5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783759"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Samouczek: wizualizowanie anomalii dotyczących danych w zdarzeniach w czasie rzeczywistym wysyłanych do usługi Azure Event Hubs

Dzięki usłudze Azure Event Hubs można za pomocą usługi Azure Stream Analytics sprawdzić dane przychodzące i wyodrębnić anomalie, które następnie można wizualizować w usłudze Power BI. Załóżmy, że mamy tysiące urządzeń, wysyłających nieustannie dane w czasie rzeczywistym do centrum zdarzeń, co daje w efekcie miliony zdarzeń na sekundę. Jak sprawdzić takie ilości danych pod kątem anomalii lub błędów w danych? Na przykład co zrobić, jeśli urządzenia wysyłają transakcje kartą kredytową i musisz przechwycić wszystkie transakcje w wielu krajach/regionach w ciągu 5-sekundowego przedziału czasu? Może się to zdarzyć w przypadku kradzieży kart kredytowych, a następnie użycia ich do realizowania zakupów na całym świecie w tym samym czasie. 

W tym samouczku przeprowadzamy symulację takiej sytuacji. Możesz uruchomić aplikację, która tworzy i wysyła transakcje kart kredytowych do centrum zdarzeń. Następnie odczytasz strumień danych w czasie rzeczywistym za pomocą funkcji Azure Stream Analytics, która oddziela prawidłowe transakcje od nieprawidłowych transakcji, Power BI wizualnie zidentyfikować transakcje oznaczone jako nieprawidłowe.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Tworzenie centrum zdarzeń
> * Uruchamianie aplikacji wysyłającej transakcje kart kredytowych
> * Konfigurowanie zadania usługi Stream Analytics do przetwarzania tych transakcji
> * Konfigurowanie wizualizacji usługi Power BI do wyświetlania wyników

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [utwórz][] bezpłatne konto.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Zainstaluj [Visual Studio](https://www.visualstudio.com/). 
- Aby analizować dane wyjściowe z zadania usługi Stream Analytics, potrzebne jest konto usługi Power BI. Możesz [wypróbować bezpłatnie usługę Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-resources"></a>Konfigurowanie zasobów

W tym samouczku potrzebujesz przestrzeni nazw usługi Event Hubs i centrum zdarzeń. Możesz utworzyć te zasoby przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Użyj tej samej grupy zasobów i lokalizacji dla wszystkich zasobów. Po zakończeniu możesz usunąć wszystko w jednym kroku, usuwając grupę zasobów.

W poniższych sekcjach opisano sposób wykonywania tych wymaganych czynności. Postępuj zgodnie z instrukcjami dotyczącymi interfejsu wiersza polecenia *lub* programu PowerShell, aby wykonać następujące czynności:

1. Utwórz [grupę zasobów](../azure-resource-manager/management/overview.md). 

2. Utwórz przestrzeń nazw usługi Event Hubs. 

3. Utwórz centrum zdarzeń.

> [!NOTE]
> Każdy skrypt zawiera zestaw zmiennych, które są potrzebne w dalszej części tego samouczka. Są to nazwa grupy zasobów ($resourceGroup), przestrzeń nazw centrum zdarzeń (**$eventHubNamespace**) oraz nazwa centrum zdarzeń (**$eventHubName**). W dalszej części tego artykułu są one poprzedzone znakiem dolara ($), aby było wiadomo, że zostały ustawione w skrypcie.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Konfigurowanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Skopiuj i wklej ten skrypt w usłudze Cloud Shell. Przy założeniu, że użytkownik jest już zalogowany, usługa będzie kolejno uruchamiać po jednym wierszu skryptu.

Zmienne, które muszą być globalnie unikatowe, mają dołączony element `$RANDOM`. Gdy skrypt jest uruchamiany i zmienne są ustawione, wygenerowany zostaje losowy ciąg liczbowy i dołączony na końcu stałego ciągu, dzięki czemu staje się on unikatowy.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Konfigurowanie zasobów za pomocą programu Azure PowerShell

Skopiuj i wklej ten skrypt w usłudze Cloud Shell. Przy założeniu, że użytkownik jest już zalogowany, usługa będzie kolejno uruchamiać po jednym wierszu skryptu.

Zmienne, które muszą być globalnie unikatowe, mają dołączony element `$(Get-Random)`. Gdy skrypt jest uruchamiany i zmienne są ustawione, wygenerowany zostaje losowy ciąg liczbowy i dołączony na końcu stałego ciągu, dzięki czemu staje się on unikatowy.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>Uruchamianie aplikacji w celu wygenerowania danych zdarzenia testowego

Przykłady Event Hubs [w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) obejmują Narzędzie do wykrywania anomalii, która generuje dane testowe. Symuluje ona korzystanie z kart kredytowych, zapisując w centrum zdarzeń transakcje kart kredytowych, w tym zapisując co pewien czas wiele transakcji dla tej samej karty kredytowej w wielu lokalizacjach, aby mogły one zostać oznaczone jako anomalie. Aby uruchomić tę aplikację, wykonaj następujące kroki: 

1. Pobierz [przykłady dotyczące usługi Azure Event Hubs](https://github.com/Azure/azure-event-hubs/archive/master.zip) z usługi GitHub i rozpakuj je lokalnie.
2. Przejdź do folderu **\azure-event-hubs-master\samples\DotNet. \\** 
3. Przejdź do folderu **Azure.Messaging.EventHubs\AnomalyDetector \\** i kliknij dwukrotnie plik **AnomalyDetector.sln,** aby otworzyć rozwiązanie w Visual Studio. 

    Aby użyć starej wersji przykładu, która używa starego pakietu Microsoft.Azure.EventHubs, otwórz rozwiązanie z folderu **Microsoft.Azure.EventHubs\AnomalyDetector.** 
3. Otwórz plik Program.cs i zastąp **parametry połączenia usługi Event Hubs** parametrami połączenia zapisanymi podczas uruchamiania skryptu. 
4. Zastąp **nazwę centrum zdarzeń** swoją nazwą centrum zdarzeń. Naciśnij klawisz F5, aby uruchomić aplikację. Rozpocznie się wysyłanie zdarzeń do centrum zdarzeń, które będzie kontynuowane, dopóki nie zostanie wysłany 1000 zdarzeń. Istnieje kilka przypadków, gdy aplikacja musi być uruchomiona, aby można było pobrać dane. Sytuacje te wskazano w instrukcjach poniżej, gdy są wymagane.

## <a name="set-up-azure-stream-analytics"></a>Konfigurowanie usługi Azure Stream Analytics

Teraz możesz przesyłać strumieniowo dane do centrum zdarzeń. Aby użyć tych danych w wizualizacji usługi Power BI, rozpocznij od skonfigurowania zadania usługi Stream Analytics po kątem pobierania danych, które będą następnie przekazywane do wizualizacji usługi Power BI.

### <a name="create-the-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie Azure Portal kliknij pozycję **Utwórz zasób**. W polu wyszukiwania wpisz **stream analytics**, a następnie naciśnij klawisz **Enter**. Wybierz **zadanie usługi Stream Analytics**. Kliknij przycisk **Utwórz** w okienku zadania usługi Stream Analytics. 

2. Wprowadź następujące informacje dotyczące zadania:

   **Nazwa zadania**: użyj nazwy **contosoEHjob**. To pole określa nazwę zadania i musi być ona globalnie unikatowa.

   **Subskrypcja**: Wybierz subskrypcję.

   **Grupa zasobów**: użyj tej samej grupy zasobów, które są używane przez centrum zdarzeń (**ContosoResourcesEH**).

   **Lokalizacja**: użyj lokalizacji stosowanej już w skrypcie konfiguracji (**Zachodnie stany USA**).

   ![Zrzut ekranu pokazujący sposób tworzenia nowego zadania usługi Azure Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    Zaakceptuj wartości domyślne w pozostałych polach. Kliknij pozycję **Utwórz**. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

Jeśli nie jesteś w portalu w okienku **zadania usługi Stream Analytics**, możesz wrócić do zadania usługi Stream Analytics, klikając pozycję **Grupy zasobów** w portalu, a następnie wybierając grupę zasobów (**ContosoResourcesEH**). Ta akcja wyświetli wszystkie zasoby w grupie, a następnie będzie można wybrać zadanie usługi Stream Analytics. 

Danymi wejściowymi dla zadania usługi Stream Analytics są transakcje kart kredytowych z centrum zdarzeń.

> [!NOTE]
> Wartości zmiennych zaczynających się od znaku dolara ($) są ustawiane w skryptach uruchamiania w poprzednich sekcjach. Należy użyć tych samych wartości co podczas określania tych pól, a konkretnie nazwy centrum zdarzeń i przestrzeni nazw usługi Event Hubs.

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.

2. W okienku **Dane wejściowe** kliknij pozycję **Dodaj wejście strumienia** i wybierz pozycję Event Hubs. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias danych wejściowych**: użyj **contosoinputs**. To pole zawiera nazwę strumienia wejściowego używanego podczas definiowania zapytań dla danych.

   **Subskrypcja**: Wybierz subskrypcję.

   **Przestrzeń nazw usługi Event Hubs**: wybierz swoją przestrzeń nazw centrum zdarzeń ($**eventHubNamespace**). 

   **Nazwa centrum zdarzeń**: kliknij pozycję **Użyj istniejącej** i wybierz swoje centrum zdarzeń ($**eventHubName**).

   **Nazwa zasad usługi Event Hubs**: wybierz **RootManageSharedAccessKey**.

   **Grupa konsumentów usługi Event Hubs**: pozostaw to pole puste, aby użyć domyślnej grupy odbiorców.

   Zaakceptuj wartości domyślne w pozostałych polach.

   ![Zrzut ekranu przedstawiający sposób dodawania strumienia wejściowego do zadania usługi Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. Kliknij pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**. To pole zawiera nazwę strumienia wyjściowego używanego podczas definiowania zapytań dla danych.

2. W okienku **Dane wyjściowe** kliknij pozycję **Dodaj**, a następnie wybierz pozycję **Power BI**. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias danych wyjściowych**: użyj **contosooutputs**. To pole zawiera unikatowy alias dla danych wyjściowych. 

   **Nazwa zestawu danych**: użyj **contosoehdataset**. To pole zawiera nazwę zestawu danych do użycia w usłudze Power BI. 

   **Nazwa tabeli**: użyj **contosoehtable**. To pole zawiera nazwę tabeli do użycia w usłudze Power BI. 

   Zaakceptuj wartości domyślne w pozostałych polach.

   ![Zrzut ekranu przedstawiający sposób konfigurowania danych wyjściowych zadania usługi Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. Kliknij pozycję **Autoryzuj** i zaloguj się do konta usługi Power BI.

4. Zaakceptuj wartości domyślne w pozostałych polach.

5. Kliknij pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

To zapytanie służy do pobierania danych, które ostatecznie są wysyłane do wizualizacji usługi Power BI. Używa ono danych **contosoinputs** i **contosooutputs**, który zostały wcześniej zdefiniowane podczas konfigurowania zadania. To zapytanie pobiera transakcje kart kredytowych, które uzna za fałszywe, czyli transakcje, w ramach których ten sam numer karty kredytowej został wykorzystany w wielu transakcjach w różnych lokalizacjach w ciągu 5-sekundowego przedziału czasu.

1. W obszarze **Topologia zadania** kliknij pozycję **Zapytanie**.

2. Zastąp to zapytanie następującym zapytaniem: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Kliknij pozycję **Zapisz**.

### <a name="test-the-query-for-the-stream-analytics-job"></a>Testowanie zapytania dla zadania usługi Stream Analytics 

1. Uruchom aplikację do wykrywania anomalii, aby wysłać dane do centrum zdarzeń podczas konfigurowania i uruchamiania testu. 

2. W okienku Zapytanie kliknij kropki obok danych wejściowych **contosoinputs**, a następnie wybierz pozycję **Przykładowe dane z danych wejściowych**.

3. Określ, że chcesz dane z trzech minut, a następnie kliknij przycisk **OK**. Poczekaj, aż otrzymasz powiadomienie, że próbka danych została przygotowana.

4. Kliknij przycisk **Testuj** i upewnij się, że wyświetlane są wyniki. Wyniki są wyświetlane w sekcji **Wyniki** dolnego okienka po prawej stronie, poniżej zapytania.

5. Zamknij okienko Zapytanie.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W ramach zadania usługi Stream Analytics kliknij pozycje **Uruchom**, następnie **Teraz**, a potem **Uruchom**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurowanie wizualizacji usługi Power BI

1. Uruchom aplikację do wykrywania anomalii, aby wysłać dane do centrum zdarzeń podczas konfigurowania wizualizacji usługi Power BI. Konieczne może być uruchomienie jej wielokrotnie, ponieważ przy każdym uruchomieniu generuje ona zaledwie 1000 transakcji.

2. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/).

3. Przejdź do obszaru **Mój obszar roboczy**.

4. Kliknij pozycję **Zestawy danych**.

   Powinien zostać wyświetlony zestaw danych wybrany podczas tworzenia danych wyjściowych zadania usługi Stream Analytics (**contosoehdataset**). Wyświetlenie zestawu danych po raz pierwszy może potrwać 5–10 minut.

5. Kliknij przycisk **Pulpity nawigacyjne**, następnie kliknij przycisk **Utwórz** i wybierz pozycję **Pulpit nawigacyjny**.

   ![Zrzut ekranu przedstawiający przyciski Pulpity nawigacyjne i Utwórz.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Podaj nazwę pulpitu nawigacyjnego, a następnie kliknij przycisk **Utwórz**. Użyj nazwy **Anomalie dotyczące kart kredytowych**.

   ![Zrzut ekranu przedstawiający wprowadzanie nazwy pulpitu nawigacyjnego.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. Na stronie Pulpit nawigacyjny kliknij pozycję **Dodaj kafelek,** wybierz pozycję **Niestandardowe dane przesyłane strumieniowo** w sekcji **DANE W CZASIE RZECZYWISTYM,** a następnie kliknij przycisk **Dalej.**

   ![Zrzut ekranu przedstawiający określanie źródła dla kafelka.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Wybierz zestaw danych (**contosoehdataset**) i kliknij przycisk **Dalej**.

   ![Zrzut ekranu przedstawiający określanie zestawu danych.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. Wybierz pozycję **Karta** dla typu wizualizacji. W **obszarze Pola** kliknij pozycję Dodaj **wartość,** a następnie wybierz pozycję `fraudulentuses` .

   ![Zrzut ekranu przedstawiający określanie typu wizualizacji i pól.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   Kliknij przycisk **Dalej**.

10. Ustaw tytuł **Oszukańcze użycia** i podtytuł **Suma w ciągu kilku ostatnich minut**. Kliknij pozycję **Zastosuj**. Kafelek zostanie zapisany na pulpicie nawigacyjnym.

    ![Zrzut ekranu przedstawiający określanie tytułu i podtytułu dla kafelka pulpitu nawigacyjnego.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > Po uruchomieniu przykładowej aplikacji i rozpoczęciu strumieniowego przesyłania danych do centrum zdarzeń liczba na tym kafelku zacznie się szybko zmieniać (co sekundę). Oznacza to, że zapytanie usługi Stream Analytics faktycznie aktualizuje wartość **co sekundę**. Zaktualizuj zapytanie do 3-minutowego okna wirowania, aby wyświetlać sumę z ostatnich kilku minut. 
11. Dodaj kolejną wizualizację. Powtórz ponownie kilka pierwszych kroków:

    * Kliknij pozycję **Dodaj kafelek**.
    * Wybierz pozycję **Niestandardowe dane przesyłane strumieniowo**. 
    * Kliknij przycisk **Dalej**.
    * Wybierz zestaw danych, a następnie kliknij przycisk **Dalej**. 

12. W obszarze **Typ wizualizacji** wybierz opcję **Wykres liniowy**.

13. W **obszarze Oś** kliknij pozycję Dodaj **wartość** i wybierz pozycję `windowend` . 

14. W **obszarze Wartości** kliknij pozycję Dodaj **wartość** i wybierz pozycję `fraudulentuses` .

15. W obszarze **Okno czasowe do wyświetlenia** wybierz ostatnie 5 minut. Kliknij przycisk **Dalej**.

16. Podaj tytuł **Pokaż oszukańcze użycia w czasie** i pozostaw puste pole podtytułu dla kafelka, a następnie kliknij przycisk **Zastosuj**. Nastąpi powrót do pulpitu nawigacyjnego.

17. Uruchom ponownie aplikację do wykrywania anomalii, aby wysłać nieco danych do centrum zdarzeń. Kafelek **Oszukańcze użycia** zmieni się, analizując dane, a na wykresie liniowym zostaną wyświetlone dane. 

    ![Zrzut ekranu przedstawiający wyniki usługi Power BI](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć wszystkie utworzone zasoby, usuń dane wizualizacji usługi Power BI, a następnie usuń grupę zasobów. Usunięcie grupy zasobów spowoduje usunięcie wszystkich zasobów w tej grupie. W tym przypadku spowoduje to usunięcie centrum zdarzeń, przestrzeni nazw centrum zdarzeń, zadania usługi Stream Analytics i samej grupy zasobów. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Oczyszczanie zasobów w wizualizacji usługi Power BI

Zaloguj się do swojego konta usługi Power BI. Przejdź do obszaru **Mój obszar roboczy**. W wierszu z nazwą swojego pulpitu nawigacyjnego kliknij ikonę kosza. Następnie przejdź do obszaru **Zestawy danych** i kliknij ikonę kosza, aby usunąć zestaw danych (**contosoehdataset**).

### <a name="clean-up-resources-using-azure-cli"></a>Oczyszczanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć grupę zasobów, użyj polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>Oczyszczanie zasobów przy użyciu programu PowerShell

Aby usunąć grupę zasobów, użyj polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Tworzenie centrum zdarzeń
> * Uruchamianie aplikacji, która symuluje zdarzenia i wysyła je do centrum zdarzeń
> * Konfigurowanie zadania usługi Stream Analytics do przetwarzania zdarzeń wysłanych do centrum zdarzeń
> * Konfigurowanie wizualizacji usługi Power BI do wyświetlania wyników

Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat usługi Azure Event Hubs.

> [!div class="nextstepaction"]
> [Wprowadzenie do wysyłania komunikatów do usługi Azure Event Hubs przy użyciu biblioteki .NET Standard](event-hubs-dotnet-standard-getstarted-send.md)

[tworzenie bezpłatnego konta]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
