---
title: Konfigurowanie i używanie metryk i dzienników diagnostycznych w usłudze Azure IoT Hub
description: Dowiedz się, jak skonfigurować metryki i dzienniki diagnostyczne przy użyciu usługi Azure IoT Hub i korzystać z nich. Umożliwi to analizowanie danych, które mogą być pomocne w diagnozowaniu problemów z centrum.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: b070d6600f18b87ec16f1f9894d577f4c0a6c51c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149193"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Samouczek: Konfigurowanie metryk i dzienników diagnostycznych za pomocą Centrum IoT Hub oraz korzystanie z nich

Jeśli w Twoim środowisku produkcyjnym działa rozwiązanie IoT Hub, możesz chcieć skonfigurować kilka metryk i włączyć dzienniki diagnostyczne. W razie wystąpienia problemu możesz wówczas przyjrzeć się danym, które pomogą Ci zdiagnozować ten problem i szybciej go rozwiązać. W tym artykule zobaczysz, jak włączać dzienniki diagnostyczne i jak wyszukiwać w nich błędy. Skonfigurujesz także kilka metryk do obserwowania oraz alerty uruchamiane, gdy metryki osiągną określone granice. Na przykład możesz otrzymywać wiadomość e-mail, gdy liczba wysłanych komunikatów telemetrycznych przekroczy określoną granicę lub gdy liczba użytych komunikatów zbliży się do dziennego limitu przydziału komunikatów dozwolonego w usłudze IoT Hub. 

Przykładowym przypadkiem użycia jest stacja benzynowa, na której dystrybutory są urządzeniami IoT komunikującymi się z usługą IoT Hub. Po zweryfikowaniu kart kredytowych finalne transakcje są zapisywane w magazynie danych. Gdy urządzenia IoT przestają komunikować się z centrum i wysyłać komunikaty, znacznie trudniej rozwiązać problem, jeśli nie widać, co się dzieje.

W tym samouczku do wysyłania komunikatów do centrum IoT służy przykład platformy Azure z modułu [Routing usługi IoT Hub](tutorial-routing.md).

Ten samouczek obejmuje wykonanie następujących zadań:

> [!div class="checklist"]
> * Tworzenie centrum IoT, urządzenia symulowanego i konta magazynu przy użyciu interfejsu wiersza polecenia platformy Azure.  
> * Włączanie dzienników diagnostycznych.
> * Włączanie metryk.
> * Konfigurowanie alertów dla tych metryk. 
> * Pobieranie i uruchamianie aplikacji, która symuluje urządzenie IoT wysyłające komunikaty do centrum. 
> * Uruchamianie aplikacji przed rozpoczęciem wyzwalania alertów. 
> * Wyświetlanie wyników metryk i sprawdzanie dzienników diagnostycznych. 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Zainstaluj [program Visual Studio](https://www.visualstudio.com/). 

- Konto e-mail umożliwiające odbieranie wiadomości e-mail.

- Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym samouczku używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Konfigurowanie zasobów

W tym samouczku potrzebujesz centrum IoT, konta magazynu i symulowanego urządzenia IoT. Te zasoby można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Użyj tej samej grupy zasobów i lokalizacji dla wszystkich zasobów. Po zakończeniu możesz usunąć wszystko w jednym kroku, usuwając grupę zasobów.

Poniżej przedstawiono wymagane kroki.

1. Utwórz [grupę zasobów](../azure-resource-manager/management/overview.md). 

2. Utwórz centrum IoT.

3. Utwórz standardowe konto magazynu w wersji 1 przy użyciu replikacji Standard_LRS.

4. Utwórz tożsamość urządzenia symulowanego, które wysyła wiadomości do centrum. Zapisz klucz na potrzeby fazy testowania.

### <a name="set-up-resources-using-azure-cli"></a>Konfigurowanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Skopiuj i wklej ten skrypt w usłudze Cloud Shell. Przy założeniu, że użytkownik jest już zalogowany, usługa będzie kolejno uruchamiać po jednym wierszu skryptu. Nowe zasoby są tworzone w grupie zasobów ContosoResources.

Zmienne, które muszą być globalnie unikatowe, mają dołączony element `$RANDOM`. Gdy skrypt jest uruchamiany i zmienne są ustawione, wygenerowany zostaje losowy ciąg liczbowy i dołączony na końcu stałego ciągu, dzięki czemu staje się on unikatowy.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Podczas tworzenia tożsamości urządzenia może zostać wyświetlony następujący błąd: *nie znaleziono kluczy dla zasad iothubowner IoT Hub ContosoTestHub*. Aby naprawić ten błąd, zaktualizuj rozszerzenie IoT interfejsu wiersza polecenia platformy Azure, a następnie uruchom ponownie ostatnie dwa polecenia w skrypcie. 
>
>Poniżej przedstawiono polecenie umożliwiające zaktualizowanie rozszerzenia. Uruchom je w swoim wystąpieniu usługi Cloud Shell.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="enable-the-diagnostic-logs"></a>Włączanie dzienników diagnostycznych 

[Dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md) są domyślnie wyłączone po utworzeniu nowego centrum IoT. W tej sekcji włączysz dzienniki diagnostyczne dla swojego centrum.

1. Najpierw, jeśli nie jesteś jeszcze w swoim centrum w portalu, kliknij pozycję **Grupy zasobów**, a następnie kliknij grupę zasobów Contoso-Resources. Wybierz centrum z listy wyświetlanych zasobów. 

2. W bloku usługi IoT Hub wyszukaj sekcję **Monitorowanie**. Kliknij pozycję **Ustawienia diagnostyczne**. 

   ![Zrzut ekranu przedstawiający fragment bloku usługi IoT Hub dotyczący ustawień diagnostycznych.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Upewnij się, że subskrypcja i grupa zasobów są poprawne. W obszarze **Typ zasobu** usuń zaznaczenie pola wyboru **Zaznacz wszystko**, a następnie znajdź i zaznacz pozycję **IoT Hub**. (Zaznacza znacznik wyboru obok *opcji wszystkie* ponownie, ignorując go). W obszarze **zasób**wybierz nazwę centrum. Ekran powinien wyglądać jak na poniższej ilustracji: 

   ![Zrzut ekranu przedstawiający fragment bloku usługi IoT Hub dotyczący ustawień diagnostycznych.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Teraz kliknij pozycję **Włącz diagnostykę**. Zostanie wyświetlone okienko Ustawienia diagnostyczne. Określ nazwę ustawień dzienników diagnostycznych jako „diags-hub”.

5. Zaznacz pozycję **Zarchiwizuj na koncie magazynu**. 

   ![Zrzut ekranu przedstawiający włączanie archiwizacji na koncie magazynu w ustawieniach diagnostyki.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Kliknij pozycję **Konfiguruj**, aby wyświetlić ekran **Wybieranie konta magazynu**, wybierz właściwe konto (*contosostoragemon*) i kliknij przycisk **OK**, aby wrócić do okienka Ustawienia diagnostyczne. 

   ![Zrzut ekranu przedstawiający włączanie archiwizacji na koncie magazynu w ustawieniach dzienników diagnostycznych.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. W obszarze **DZIENNIK** zaznacz pozycje **Połączenia** i **Dane telemetryczne z urządzenia**, a następnie dla każdej z nich ustaw **Okres przechowywania (w dniach)** wynoszący 7 dni. Ekran Ustawienia diagnostyczne powinien teraz wyglądać jak na poniższej ilustracji:

   ![Zrzut ekranu przedstawiający ostateczne ustawienia dzienników diagnostycznych.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Kliknij polecenie **Zapisz**, aby zapisać ustawienia. Zamknij okienko Ustawienia diagnostyczne.

Później, podczas przeglądania dzienników diagnostycznych, będzie można przejrzeć wpisy rejestrowania łączenia i rozłączania dla urządzenia. 

## <a name="set-up-metrics"></a>Konfigurowanie metryk 

Teraz skonfiguruj kilka metryk, aby obserwować, kiedy komunikaty są wysyłane do centrum. 

1. W okienku ustawień centrum IoT kliknij opcję **Metryki** w sekcji **Monitorowanie**.

2. W górnej części ekranu kliknij pozycję **Ostatnie 24 godziny (automatycznie)**. W wyświetlonym menu rozwijanym wybierz pozycję **Ostatnie 4 godziny** dla ustawienia **Zakres czasu** i ustaw **Stopień szczegółowości czasu** na **1 minuta**, czas lokalny. Kliknij pozycję **Zastosuj**, aby zapisać te ustawienia. 

   ![Zrzut ekranu przedstawiający ustawienia czasu metryk.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Domyślnie istnieje jeden wpis metryki. Pozostaw domyślną grupę zasobów i przestrzeń nazw metryki. Na liście rozwijanej **Metryka** wybierz pozycję **Wysłane komunikaty telemetryczne**. W obszarze **Agregacja** ustaw wartość **Suma**.

   ![Zrzut ekranu przedstawiający dodawanie metryki dla wysłanych komunikatów telemetrycznych.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Teraz kliknij pozycję **Dodaj metrykę**, aby dodać kolejną metrykę do wykresu. Wybierz grupę zasobów (**ContosoTestHub**). W obszarze **Metryka** wybierz pozycję **Łączna liczba użytych komunikatów**. W obszarze **Agregacja** ustaw wartość **Średnia**. 

   Teraz na ekranie jest widoczna zminimalizowana metryka *Wysłane komunikaty telemetryczne* oraz nowa metryka *Łączna liczba użytych komunikatów*.

   ![Zrzut ekranu przedstawiający dodawanie metryki dla wysłanych komunikatów telemetrycznych.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Kliknij pozycję **Przypnij do pulpitu nawigacyjnego**. Metryka zostanie przypięta do pulpitu nawigacyjnego witryny Azure Portal, aby można było ponownie uzyskać do niej dostęp. Jeśli nie przypniesz jej do pulpitu nawigacyjnego, ustawienia nie zostaną zachowane.

## <a name="set-up-alerts"></a>Konfigurowanie alertów

Przejdź do centrum w portalu. Kliknij pozycję **Grupy zasobów**, wybierz pozycję *ContosoResources*, a następnie wybierz centrum IoT *ContosoTestHub*. 

Usługa IoT Hub nie została jeszcze zmigrowana do [metryk w usłudze Azure Monitor](../azure-monitor/platform/data-platform.md#metrics). Musisz używać [alertów klasycznych](../azure-monitor/platform/alerts-classic.overview.md).

1. W obszarze **Monitorowanie** kliknij pozycję **Alerty**. Spowoduje to wyświetlenie głównego ekranu alertów. 

   ![Zrzut ekranu przedstawiający sposób znajdowania alertów klasycznych.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Aby uzyskać w tym miejscu dostęp do alertów klasycznych, kliknij pozycję **Wyświetl alerty klasyczne**. 

    ![Zrzut ekranu przedstawiający ekran alertów klasycznych.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Wypełnij następujące pola: 

    **Subskrypcja**: pozostaw to pole ustawione na bieżącą subskrypcję.

    **Źródło**: Ustaw to pole na *metryki*.

    **Grupa zasobów**: Ustaw to pole na bieżącą grupę zasobów, *ContosoResources*. 

    **Typ zasobu**: Ustaw to pole na IoT Hub. 

    **Zasób**: wybierz Centrum IoT Hub, *ContosoTestHub*.

3. Kliknij pozycję **Dodaj alert metryki (klasyczny)**, aby skonfigurować nowy alert.

    Wypełnij następujące pola:

    **Nazwa**: Podaj nazwę reguły alertu, na przykład *komunikaty telemetryczne*.

    **Opis**: Podaj opis alertu, na przykład alert, *gdy wysyłane są 1000 komunikaty telemetryczne*. 

    **Źródło**: Ustaw tę wartość na *metryki*.

    W polach **Subskrypcja**, **Grupa zasobów** i **Zasób** należy ustawić wartości wybrane na ekranie **Wyświetl alerty klasyczne**. 

    W polu **Metryka** ustaw wartość *Wysłane komunikaty telemetryczne*.

    ![Zrzut ekranu przedstawiający konfigurowanie alertu klasycznego dla wysłanych komunikatów telemetrycznych.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Po wykresie ustaw następujące pola:

   **Warunek**: wartość *większa niż*.

   **Próg**: ustaw na 1000.

   **Okres**: Ustaw na wartość *w ciągu ostatnich 5 minut*.

   **Adresaci wiadomości e-mail z powiadomieniem**: Umieść tutaj swój adres e-mail. 

   ![Zrzut ekranu przedstawiający dolną część ekranu alertów.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Kliknij przycisk **OK**, aby zapisać alert. 

5. Teraz skonfiguruj kolejny alert dla metryki *Łączna liczba użytych komunikatów*. Ta metryka jest przydatna, jeśli chcesz otrzymywać alert, gdy liczba użytych komunikatów będzie zbliżała się do limitu przydziału dla centrum IoT, aby wiedzieć, że centrum wkrótce rozpocznie odrzucanie komunikatów.

   Na ekranie **Wyświetl alerty klasyczne** kliknij pozycję **Dodaj alert metryki (klasyczny)**, a następnie wypełnij poniższe pola w okienku **Dodaj regułę**.

   **Nazwa**: Podaj nazwę reguły alertu, na przykład *liczbę używanych komunikatów*.

   **Opis**: Podaj opis alertu, na przykład alert, *Kiedy zbliża się do limitu przydziału*.

   **Źródło**: Ustaw to pole na *metryki*.

    W polach **Subskrypcja**, **Grupa zasobów** i **Zasób** należy ustawić wartości wybrane na ekranie **Wyświetl alerty klasyczne**. 

    W polu **Metryka** ustaw wartość *Łączna liczba użytych komunikatów*.

6. Pod wykresem wypełnij następujące pola:

   **Warunek**: wartość *większa niż*.

   **Próg**: ustaw na 1000.

   **Okres**: Ustaw to pole na wartość w *ciągu ostatnich 5 minut*. 

   **Adresaci wiadomości e-mail z powiadomieniem**: Umieść tutaj swój adres e-mail. 

   Kliknij przycisk **OK**, aby zapisać regułę. 

5. Teraz w okienku alertów klasycznych powinny być wyświetlane dwa alerty: 

   ![Zrzut ekranu przedstawiający ekran alertów klasycznych z nowymi regułami alertów.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Zamknij okienko alertów. 
    
    Dzięki tym ustawieniom będziesz otrzymywać alert, gdy liczba wysłanych wiadomości będzie większa niż 400 i gdy łączna liczba użytych komunikatów przekroczy wartość LICZBA.

## <a name="run-simulated-device-app"></a>Uruchamianie aplikacji urządzenia symulowanego

Wcześniej w sekcji konfigurowania skryptu skonfigurowano urządzenia do symulowania użycia urządzenia IoT. Ta sekcja zawiera instrukcje dotyczące pobierania aplikacji konsolowej .NET, która symuluje urządzenie wysyłające komunikaty z urządzenia do chmury do centrum IoT Hub.  

Pobierz rozwiązanie na potrzeby [symulacji urządzenia IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Ten link umożliwia pobranie repozytorium z kilkoma aplikacjami. szukane rozwiązanie dotyczy usługi IoT-Hub/samouczków/routingu/.

Kliknij dwukrotnie plik rozwiązania (SimulatedDevice.sln), aby otworzyć kod w programie Visual Studio, a następnie otwórz plik Program.cs. Zastąp element `{iot hub hostname}` nazwą hosta centrum IoT Hub. Format nazwy hosta centrum IoT Hub to **{nazwa-centrum-iot}.azure-devices.net**. W tym samouczku jest używana nazwa hosta centrum **ContosoTestHub.azure-devices.net**. Następnie zastąp ciąg `{device key}` kluczem urządzenia, który został wcześniej zapisany podczas konfigurowania urządzenia symulowanego. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Uruchamianie i testowanie 

W pliku Program.cs zmień wartość `Task.Delay` z 1000 na 10, co zmniejszy ilość czasu między wysłaniem kolejnych komunikatów z 1 sekundy na 0,01 sekundy. Skrócenie tego opóźnienia zwiększy liczbę wysyłanych komunikatów.

```csharp
await Task.Delay(10);
```

Uruchom aplikację konsolową. Zaczekaj kilka minut (10—15). Komunikaty wysyłane z urządzenia symulowanego do centrum możesz zobaczyć na ekranie konsoli aplikacji.

### <a name="see-the-metrics-in-the-portal"></a>Wyświetlanie metryk w portalu

Otwórz metryki na pulpicie nawigacyjnym. Zmień wartości czasu na *Ostatnie 30 minut* ze stopniem szczegółowości czasu *1 minuta*. Na wykresie zostaną wyświetlone wysłane komunikaty telemetryczne i łączna liczba użytych komunikatów, z najnowszymi liczbami u dołu wykresu.

   ![Zrzut ekranu przedstawiający metryki.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Wyświetlanie alertów

Wróć do alertów. Kliknij pozycję **Grupy zasobów**, wybierz pozycję *ContosoResources*, a następnie wybierz centrum *ContosoTestHub*. Na wyświetlonej stronie właściwości centrum wybierz pozycję **Alerty**, a następnie **Wyświetl alerty klasyczne**. 

Gdy liczba wysłanych komunikatów przekroczy limit, zaczniesz otrzymywać alerty e-mail. Aby sprawdzić, czy istnieją aktywne alerty, przejdź do centrum i wybierz pozycję **Alerty**. Zostaną wyświetlone aktywne alerty oraz informacje o ostrzeżeniach (jeśli istnieją). 

   ![Zrzut ekranu przedstawiający uruchomione alerty.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Kliknij alert dotyczący komunikatów telemetrycznych. Zostanie wyświetlony wynik metryki i wykres z wynikami. Wiadomość e-mail z ostrzeżeniem o uruchomionym alercie wygląda jak na poniższej ilustracji:

   ![Zrzut ekranu przedstawiający wiadomość e-mail na temat uruchomionych alertów.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Wyświetlanie dzienników diagnostycznych

Skonfiguruj dzienniki diagnostyczne tak, aby były eksportowane do magazynu obiektów blob. Przejdź do grupy zasobów i wybierz swoje konto magazynu *contosostoragemon*. Wybierz pozycję Obiekty blob, a następnie otwórz kontener *insights-logs-connections*. Przewijaj, aż dotrzesz do bieżącej daty, a następnie wybierz najnowszy plik. 

   ![Zrzut ekranu przedstawiający przewijanie do kontenera magazynu w celu wyświetlenia dzienników diagnostycznych.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Kliknij pozycję **Pobierz**, aby go pobrać i otworzyć. Zostaną wyświetlone dzienniki urządzenia łączącego się i rozłączającego w miarę wysyłania komunikatów do centrum. Oto przykład:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów 

Aby usunąć wszystkie zasoby utworzone w ramach tego samouczka, usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku następuje również usunięcie centrum IoT, konta magazynu i samej grupy zasobów. Jeśli do pulpitu nawigacyjnego są przypięte metryki, musisz usunąć je ręcznie, klikając wielokropek w prawym górnym rogu każdej z nich i wybierając pozycję **Usuń**.

Aby usunąć grupę zasobów, użyj polecenia [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób korzystania z metryk i dzienników diagnostycznych przez wykonywanie następujących czynności:

> [!div class="checklist"]
> * Tworzenie centrum IoT, urządzenia symulowanego i konta magazynu przy użyciu interfejsu wiersza polecenia platformy Azure.  
> * Włączanie dzienników diagnostycznych. 
> * Włączanie metryk.
> * Konfigurowanie alertów dla tych metryk. 
> * Pobieranie i uruchamianie aplikacji, która symuluje urządzenie IoT wysyłające komunikaty do centrum. 
> * Uruchamianie aplikacji przed rozpoczęciem wyzwalania alertów. 
> * Wyświetlanie wyników metryk i sprawdzanie dzienników diagnostycznych. 

Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać stanem urządzenia IoT. 

> [!div class="nextstepaction"]
> [Konfigurowanie urządzeń z poziomu usługi zaplecza](tutorial-device-twins.md)