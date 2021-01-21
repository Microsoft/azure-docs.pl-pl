---
title: Samouczek — Konfigurowanie metryk i dzienników oraz korzystanie z nich za pomocą usługi Azure IoT Hub
description: Samouczek — informacje na temat konfigurowania metryk i dzienników przy użyciu usługi Azure IoT Hub oraz korzystania z nich. Umożliwi to analizowanie danych, które mogą być pomocne w diagnozowaniu problemów z centrum.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: bf834a6dd648ffc8f4b1633dbb383f33cd99335f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625202"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Samouczek: Konfigurowanie metryk i dzienników przy użyciu Centrum IoT Hub oraz korzystanie z nich

Za pomocą Azure Monitor można zbierać metryki i dzienniki dla Centrum IoT Hub, które mogą pomóc w monitorowaniu działania rozwiązania i rozwiązywaniu problemów występujących podczas ich występowania. W tym artykule przedstawiono sposób tworzenia wykresów opartych na metrykach, sposobach tworzenia alertów, które wyzwalają metryki, jak wysyłać IoT Hub operacje i błędy do dzienników Azure Monitor oraz jak sprawdzać błędy w dziennikach.

W tym samouczku za pomocą przykładu platformy Azure z poziomu [przewodnika szybkiego startu programu .NET](quickstart-send-telemetry-dotnet.md) można wysyłać komunikaty do centrum IoT Hub. Możesz zawsze używać urządzenia lub innego przykładu do wysyłania wiadomości, ale może być konieczne zmodyfikowanie kilku kroków.

Przed rozpoczęciem tego samouczka mogą być pomocne pewne informacje dotyczące Azure Monitor pojęć. Aby dowiedzieć się więcej, zobacz [Monitor IoT Hub](monitor-iot-hub.md). Aby dowiedzieć się więcej na temat metryk i dzienników zasobów emitowanych przez IoT Hub, zobacz [Informacje o monitorowaniu danych](monitor-iot-hub-reference.md).

Ten samouczek obejmuje wykonanie następujących zadań:

> [!div class="checklist"]
>
> * Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć Centrum IoT, zarejestrować symulowane urządzenie i utworzyć obszar roboczy Log Analytics.  
> * Wysyłanie IoT Hub połączeń i dzienników zasobów telemetrii urządzeń do Azure Monitor dzienników w obszarze roboczym Log Analytics.
> * Użyj Eksploratora metryk, aby utworzyć wykres oparty na wybranych metrykach i przypiąć go do pulpitu nawigacyjnego.
> * Utwórz alerty metryk, aby otrzymywać powiadomienia pocztą e-mail o wystąpieniu ważnych warunków.
> * Pobierz i uruchom aplikację, która symuluje wysyłanie komunikatów przez urządzenie IoT do centrum IoT Hub.
> * Wyświetl alerty w przypadku wystąpienia warunków.
> * Wyświetl wykres metryk na pulpicie nawigacyjnym.
> * Wyświetlanie IoT Hub błędów i operacji w dziennikach Azure Monitor.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Na komputerze deweloperskim jest wymagany zestaw .NET Core SDK 2,1 lub nowszy. Możesz pobrać zestaw SDK .NET Core dla wielu platform z repozytorium [.NET](https://www.microsoft.com/net/download/all).

  Możesz sprawdzić bieżącą wersję języka C# na komputerze deweloperskim przy użyciu następującego polecenia:

  ```cmd/sh
  dotnet --version
  ```

- Konto e-mail umożliwiające odbieranie wiadomości e-mail.

- Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym samouczku używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Konfigurowanie zasobów

Na potrzeby tego samouczka potrzebujesz usługi IoT Hub, obszaru roboczego Log Analytics i symulowanego urządzenia IoT. Te zasoby można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Użyj tej samej grupy zasobów i lokalizacji dla wszystkich zasobów. Po zakończeniu tego samouczka możesz usunąć wszystko w jednym kroku, usuwając grupę zasobów.

Poniżej przedstawiono wymagane kroki.

1. Utwórz [grupę zasobów](../azure-resource-manager/management/overview.md).

2. Utwórz centrum IoT.

3. Utworzenie obszaru roboczego usługi Log Analytics.

4. Zarejestruj tożsamość urządzenia symulowanego urządzenia, które wysyła komunikaty do centrum IoT. Zapisz parametry połączenia urządzenia, aby skonfigurować symulowane urządzenie.

### <a name="set-up-resources-using-azure-cli"></a>Konfigurowanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Skopiuj i wklej ten skrypt w usłudze Cloud Shell. Przy założeniu, że użytkownik jest już zalogowany, usługa będzie kolejno uruchamiać po jednym wierszu skryptu. Wykonanie niektórych poleceń może zająć trochę czasu. Nowe zasoby są tworzone w grupie zasobów *ContosoResources*.

Nazwa niektórych zasobów musi być unikatowa na platformie Azure. Skrypt generuje losową wartość za pomocą `$RANDOM` funkcji i zapisuje ją w zmiennej. W przypadku tych zasobów skrypt dołącza tę wartość losową do nazwy podstawowej zasobu, co sprawia, że nazwa zasobu jest unikatowa.

Dla każdej subskrypcji dozwolony jest tylko jeden bezpłatny Centrum IoT Hub. Jeśli masz już bezpłatne centrum IoT Hub w ramach subskrypcji, usuń je przed uruchomieniem skryptu lub zmodyfikuj skrypt, aby korzystał z bezpłatnego centrum IoT lub IoT Hub korzystającego z warstwy Standardowa lub podstawowa.

Skrypt drukuje nazwę Centrum IoT, nazwę obszaru roboczego Log Analytics i parametry połączenia dla zarejestrowanego urządzenia. Pamiętaj o tym, że będą one potrzebne w dalszej części tego artykułu.

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
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Podczas tworzenia tożsamości urządzenia może zostać wyświetlony następujący błąd: *nie znaleziono kluczy dla zasad iothubowner IoT Hub ContosoTestHub*. Aby naprawić ten błąd, zaktualizuj rozszerzenie IoT interfejsu wiersza polecenia platformy Azure, a następnie uruchom ponownie ostatnie dwa polecenia w skrypcie. 
>
>Poniżej przedstawiono polecenie umożliwiające zaktualizowanie rozszerzenia. Uruchom to polecenie w wystąpieniu Cloud Shell.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Zbieranie dzienników połączeń i danych telemetrycznych urządzeń

IoT Hub emituje dzienniki zasobów dla kilku kategorii operacji; Aby jednak wyświetlić te dzienniki, należy utworzyć ustawienie diagnostyczne w celu wysłania ich do miejsca docelowego. Jednym z takich miejsc docelowych są dzienniki Azure Monitor, które są zbierane w Log Analytics obszarze roboczym. Dzienniki zasobów IoT Hub są pogrupowane w różnych kategoriach. Możesz wybrać kategorie, które mają być wysyłane do Azure Monitor dzienników w ustawieniu diagnostyki. W tym artykule będziemy zbierać dzienniki dotyczące operacji i błędów, które mają na celu wykonywanie połączeń i danych telemetrycznych urządzeń. Aby uzyskać pełną listę kategorii obsługiwanych przez IoT Hub, zobacz [IoT Hub dzienników zasobów](monitor-iot-hub-reference.md#resource-logs).

Aby utworzyć ustawienie diagnostyczne do wysyłania dzienników zasobów IoT Hub do dzienników Azure Monitor, wykonaj następujące kroki:

1. Po pierwsze, jeśli nie jesteś jeszcze w Twoim centrum w portalu, wybierz pozycję **grupy zasobów** i wybierz grupę zasobów ContosoResources. Wybierz Centrum IoT Hub z wyświetlonej listy zasobów.

1. W bloku usługi IoT Hub wyszukaj sekcję **Monitorowanie**. Wybierz pozycję **Ustawienia diagnostyczne**. Następnie wybierz pozycję **Dodaj ustawienie diagnostyczne**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Zrzut ekranu, który wyróżnia ustawienia diagnostyczne w sekcji monitorowanie.":::

1. W okienku **Ustawienia diagnostyki** Nadaj nazwę opisową, taką jak "wysyłanie połączeń i danych telemetrycznych do dzienników".

1. W obszarze **szczegóły kategorii** wybierz pozycję **połączenia** i dane **telemetryczne urządzenia**.

1. W obszarze **szczegóły miejsca docelowego** wybierz pozycję **Wyślij do log Analytics**, a następnie użyj selektora obszarów roboczych log Analytics, aby wybrać wcześniej zanotowany obszar roboczy. Po zakończeniu ustawienie diagnostyczne powinno wyglądać podobnie do poniższego zrzutu ekranu:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający ostateczne ustawienia dzienników diagnostycznych.":::

1. Wybierz pozycję **Zapisz**, aby zapisać ustawienia. Zamknij okienko **ustawień diagnostycznych** . Nowe ustawienie jest widoczne na liście ustawień diagnostycznych.

## <a name="set-up-metrics"></a>Konfigurowanie metryk

Teraz będziemy używać Eksploratora metryk do tworzenia wykresu, który będzie wyświetlał metryki do śledzenia. Ten wykres zostanie przypinany do domyślnego pulpitu nawigacyjnego w Azure Portal.

1. W okienku po lewej stronie Centrum IoT wybierz pozycję **metryki** w sekcji **monitorowanie** .

1. W górnej części ekranu wybierz pozycję **ostatnie 24 godziny (automatyczne)**. Na wyświetlonej liście rozwijanej wybierz **pozycję ostatnie 4 godziny** dla **zakresu czasu**, ustaw **stopień szczegółowości czasu** na **1 minutę**, a następnie wybierz pozycję **Local** dla opcji **Pokaż czas jako**. Wybierz pozycję **Zastosuj** , aby zapisać te ustawienia. To ustawienie powinno teraz powiedzieć **czas lokalny: ostatnie 4 godziny (1 minuta)**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Zrzut ekranu przedstawiający ustawienia czasu metryk.":::

1. Na wykresie znajduje się część ustawienia metryki, która została wyświetlona w zakresie do centrum IoT. Wartości przestrzeni nazw **zakresu** i **metryki** pozostaw wartość domyślną. Wybierz ustawienie **metryki** i wpisz "telemetrię", a następnie wybierz pozycję **komunikaty telemetryczne wysyłane** z listy rozwijanej. **Agregacja** zostanie automatycznie ustawiona na **Suma**. Zauważ, że tytuł wykresu również ulega zmianie.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Zrzut ekranu pokazujący Dodawanie do wykresu metryk wysyłania komunikatów telemetrycznych.":::

1. Teraz wybierz pozycję **Dodaj metrykę** , aby dodać kolejną metrykę do wykresu. W obszarze **Metryka** wybierz pozycję **Łączna liczba użytych komunikatów**. **Agregacja** zostanie automatycznie ustawiona na wartość **średnia**. Zwróć uwagę, że tytuł wykresu został zmieniony w celu uwzględnienia tej metryki.

   Teraz na ekranie jest widoczna zminimalizowana metryka *Wysłane komunikaty telemetryczne* oraz nowa metryka *Łączna liczba użytych komunikatów*.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Zrzut ekranu pokazujący Dodawanie całkowitej liczby komunikatów użytych do wykresu.":::

1. W prawym górnym rogu wykresu wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Zrzut ekranu, który podświetla przycisk Przypnij do pulpitu nawigacyjnego.":::

1. W okienku **Przypnij do pulpitu nawigacyjnego** wybierz **istniejącą** kartę. Wybierz pozycję **prywatny** , a następnie wybierz pozycję **pulpit nawigacyjny** z listy rozwijanej pulpitu nawigacyjnego. Na koniec wybierz pozycję **Przypnij** , aby przypiąć wykres do domyślnego pulpitu nawigacyjnego w Azure Portal. Jeśli wykres nie zostanie przypinany do pulpitu nawigacyjnego, ustawienia nie będą zachowywane po zakończeniu pracy z Eksploratorem pomiarów.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Zrzut ekranu przedstawiający ustawienia przypięcia do pulpitu nawigacyjnego.":::

## <a name="set-up-metric-alerts"></a>Konfigurowanie alertów dotyczących metryk

Teraz skonfigurujemy alerty do wyzwalania dwóch *wysłanych komunikatów telemetrycznych* metryk i *całkowitej liczby użytych komunikatów*.

*Wysłane komunikaty telemetryczne* są dobrą metryką umożliwiającą monitorowanie przepływności komunikatów i uniknięcie ograniczenia przepustowości. W przypadku IoT Hub w warstwie Bezpłatna limit ograniczania wynosi 100 komunikatów/s. W przypadku jednego urządzenia nie będzie można osiągnąć tego rodzaju przepływności, dlatego skonfigurujemy alert, aby wyzwolić, czy liczba komunikatów przekracza 1000 w okresie 5 minut. W środowisku produkcyjnym można ustawić bardziej znaczącą wartość na podstawie warstwy, wersji i liczby jednostek Centrum IoT.

*Całkowita liczba użytych komunikatów* śledzi dzienną liczbę używanych komunikatów. Ta Metryka jest resetowana codziennie o godzinie 00:00 czasu UTC. Jeśli limit przydziału dzienny przekracza określony próg, IoT Hub nie będzie już akceptować komunikatów. W przypadku IoT Hub w warstwie Bezpłatna dzienny limit przydziału komunikatów wynosi 8000. Skonfigurujemy alert, aby wyzwolić, gdy całkowita liczba komunikatów przekracza 4000, 50% limitu przydziału. W ramach tej opcji prawdopodobnie ustawisz tę wartość procentową na wyższą. Wartość dziennego limitu przydziału zależy od warstwy, wersji i liczby jednostek Centrum IoT.

Aby uzyskać więcej informacji na temat limitów przydziału i ograniczania przepustowości w IoT Hub, zobacz temat [przydziały i ograniczanie przepustowości](iot-hub-devguide-quotas-throttling.md).

Aby skonfigurować alerty metryk:

1. Przejdź do centrum IoT Hub w Azure Portal.

1. W obszarze **monitorowanie** wybierz pozycję **alerty**. Następnie wybierz pozycję **Nowa reguła alertu**.  Zostanie otwarte okienko **Utwórz regułę alertu** .

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Zrzut ekranu przedstawiający okienko Tworzenie reguły alertów.":::

    W okienku **Tworzenie reguły alertów** dostępne są cztery sekcje:

    * **Zakres** jest już ustawiony na centrum IoT, więc pozostawimy tę sekcję osobno.
    * **Warunek** ustawia sygnał i warunki, które będą wyzwalać alert.
    * **Akcje** konfiguruje, co się stanie po wyzwoleniu alertu.
    * **Szczegóły reguły alertu** umożliwiają ustawienie nazwy i opisu alertu.

1. Najpierw skonfiguruj warunek, na którym zostanie wyzwolony alert.

    1. W obszarze **warunek** wybierz pozycję **Wybierz warunek**. W okienku **Konfigurowanie logiki sygnałów** w polu wyszukiwania wpisz ciąg "Telemetria" i wybierz pozycję **wysłane komunikaty telemetryczne**.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Zrzut ekranu przedstawiający Wybieranie metryki.":::

    1. W okienku **Konfigurowanie logiki sygnału** Ustaw lub potwierdź następujące pola w obszarze **logika alertu** (Możesz zignorować wykres):

       **Próg**:  *statyczny*.

       **Operator**: *większe niż*.

       **Typ agregacji**: *łącznie*.

       **Wartość progowa**: 1000.

       **Stopień szczegółowości agregacji (okres)**: *5 minut*.

       **Częstotliwość oceny**: *co 1 minutę*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Zrzut ekranu przedstawiający ustawienia warunków alertów.":::

       Ustawienia te ustawiają sygnał do całkowitej liczby komunikatów w okresie 5 minut. Ta suma będzie Szacowana co minutę, a jeśli łączna liczba dla 5 minut przekracza 1000 komunikatów, zostanie wyzwolony alert.

       Wybierz pozycję **gotowe** , aby zapisać logikę sygnału.

1. Teraz skonfiguruj akcję dla alertu.

    1. Wróć do okienka **Tworzenie reguły alertu** , w obszarze **Akcje** wybierz pozycję **Wybierz grupę akcji**. W okienku **Wybierz grupę akcji do dołączenia do tej reguły alertu** wybierz pozycję **Utwórz grupę akcji**.

    1. Na karcie **podstawy** w okienku **Tworzenie grupy akcji** nadaj grupie akcji nazwę i nazwę wyświetlaną.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Zrzut ekranu przedstawiający kartę podstawowe w okienku Tworzenie grupy akcji.":::

    1. Wybierz kartę **powiadomienia** . W polu **Typ powiadomienia** wybierz pozycję **Poczta E-mail/wiadomość SMS/wypychanie/głos** z listy rozwijanej. Zostanie otwarte okienko **powiadomień e-mail/wiadomości SMS/wypychania/głosu** .

    1. W okienku **wiadomości e-mail/SMS/wypychanie/głos** wybierz pozycję e-mail i wprowadź adres e-mail, a następnie wybierz przycisk **OK**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Zrzut ekranu przedstawiający ustawienie adresu e-mail.":::

    1. Wróć do okienka **powiadomienia** , a następnie wprowadź nazwę powiadomienia.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Zrzut ekranu przedstawiający okienko ukończone powiadomienia.":::

    1. Obowiązkowe W przypadku wybrania karty **Akcje** , a następnie wybrania listy rozwijanej **Typ akcji** można zobaczyć rodzaje akcji, które można wyzwolić przy użyciu alertu. W tym artykule będziemy używać tylko powiadomień, więc możesz zignorować ustawienia na tej karcie.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Zrzut ekranu przedstawiający typy akcji dostępne w okienku Akcje.":::

    1. Wybierz kartę **Przegląd i tworzenie** , sprawdź ustawienia, a następnie wybierz pozycję **Utwórz**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Zrzut ekranu przedstawiający okienko przegląd i tworzenie.":::

    1. W okienku **Tworzenie reguły alertów** Zwróć uwagę, że nowa grupa akcji została dodana do akcji dla alertu.  

1. Na koniec Skonfiguruj szczegóły reguły alertu i Zapisz regułę alertu.

    1. W okienku **Tworzenie reguły alertu** w obszarze Szczegóły reguły alertu wprowadź nazwę i opis alertu. na przykład "alert, jeśli więcej niż 1000 komunikatów przekracza 5 minut". Upewnij się, że jest zaznaczona opcja **Włącz regułę alertów po utworzeniu** . Ukończona reguła alertu będzie wyglądać podobnie do tego zrzutu ekranu.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Zrzut ekranu przedstawiający okienko ukończono tworzenie reguły alertu.":::

    1. Wybierz pozycję **Utwórz regułę alertu** , aby zapisać nową regułę.

1. Teraz skonfiguruj kolejny alert dla metryki *Łączna liczba użytych komunikatów*. Ta Metryka jest przydatna, jeśli chcesz wysłać Alert, gdy liczba użytych komunikatów zbliża się do dziennego limitu przydziału dla Centrum IoT. w tym momencie Centrum IoT rozpocznie odrzucanie komunikatów. Postępuj zgodnie z poniższymi krokami, z następującymi różnicami.

    * Dla sygnału w okienku **Konfigurowanie logiki sygnału** wybierz pozycję **łączna liczba użytych komunikatów**.

    * W okienku **Konfigurowanie logiki sygnału** Ustaw lub potwierdź następujące pola (można zignorować wykres):

       **Próg**:  *statyczny*.

       **Operator**: *większe niż*.

       **Typ agregacji**: *maksimum*.

       **Wartość progowa**: 4000.

       **Stopień szczegółowości agregacji (okres)**: *1 minuta*.

       **Częstotliwość oceny**: *co 1 minutę*

       Te ustawienia ustawiają sygnał do uruchomienia, gdy liczba komunikatów osiągnie 4000. Metryka jest szacowana co minutę.

    * Po określeniu akcji dla reguły alertu wystarczy wybrać utworzoną wcześniej grupę akcji.

    * W polu Szczegóły alertu wybierz inną nazwę i opis, niż wcześniej.

1. Wybierz pozycję **alerty**, w obszarze **monitorowanie** w lewym okienku Centrum IoT. Teraz wybierz pozycję **Zarządzaj regułami alertów** w menu w górnej części okienka **alerty** . Zostanie otwarte okienko **reguły** . Powinny teraz być widoczne Twoje dwa alerty:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Zrzut ekranu przedstawiający okienko reguł z nowymi regułami alertów.":::

1. Zamknij okienko **reguły** .

Dzięki tym ustawieniom alert zostanie wyzwolony i otrzymasz powiadomienie e-mail, gdy więcej niż 1000 komunikatów zostanie wysłanych w ciągu 5 minut, a także wtedy, gdy całkowita liczba użytych komunikatów przekroczy 4000 (50% dziennego limitu przydziału dla Centrum IoT w warstwie Bezpłatna).

## <a name="run-the-simulated-device-app"></a>Uruchamianie aplikacji symulowanego urządzenia

W sekcji [Konfigurowanie zasobów](#set-up-resources) zarejestrowano tożsamość urządzenia do użycia w celu symulowania użycia urządzenia IoT. W tej sekcji pobrano aplikację konsolową .NET, która symuluje urządzenie wysyłające komunikaty z urządzenia do chmury do IoT Hub, skonfiguruje je do wysyłania tych komunikatów do centrum IoT Hub, a następnie uruchomi je. 

> [!IMPORTANT]
>
> Alerty mogą być w pełni skonfigurowane i włączone przez IoT Hub. Zaczekaj co najmniej 10 minut od momentu skonfigurowania ostatniego alertu i uruchomienia aplikacji symulowanego urządzenia.

Pobierz rozwiązanie na potrzeby [symulacji urządzenia IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Ten link umożliwia pobranie repozytorium z kilkoma aplikacjami. tym, dla którego szukasz, jest w centrum IoT-Hub/przewodnikach szybki start/symulowanych — urządzenie/.

1. W oknie terminalu lokalnego przejdź do folderu głównego rozwiązania. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

1. Otwórz plik **SimulatedDevice.cs** w wybranym edytorze.

    1. Zastąp wartość `s_connectionString` zmiennej parametrami połączenia urządzenia zanotowanymi podczas uruchamiania skryptu w celu skonfigurowania zasobów.

    1. W `SendDeviceToCloudMessagesAsync` metodzie Zmień wartość `Task.Delay` z 1000 na 1, co zmniejsza czas między wysłaniem komunikatów z 1 s do 0,001 sekund. Skrócenie tego opóźnienia zwiększy liczbę wysyłanych komunikatów. (Prawdopodobnie nie otrzymasz współczynnika komunikatów 100 komunikatów na sekundę).

        ```csharp
        await Task.Delay(1);
        ```

    1. Zapisz zmiany w **SimulatedDevice.cs**.

1. W oknie terminalu lokalnego Uruchom następujące polecenie, aby zainstalować wymagane pakiety dla aplikacji symulowanego urządzenia:

    ```cmd/sh
    dotnet restore
    ```

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby utworzyć i uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    dotnet run
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe symulowanego urządzenia.":::

Pozwól, aby aplikacja działała przez co najmniej 10-15 minut. Najlepiej, aby uruchomić je do momentu zatrzymania wysyłania komunikatów (około 20-30 minut). Dzieje się tak w przypadku przekroczenia dziennego limitu przydziału komunikatów dla Centrum IoT, który zatrzymał akceptowanie kolejnych komunikatów.

> [!NOTE]
> Jeśli aplikacja urządzenia jest uruchomiona przez dłuższy czas po zatrzymaniu wysyłania komunikatów przez użytkownika, może wystąpić wyjątek. Można bezpiecznie zignorować ten wyjątek i zamknąć okno aplikacji.

## <a name="view-metrics-chart-on-your-dashboard"></a>Wyświetlanie wykresu metryk na pulpicie nawigacyjnym

1. W lewym górnym rogu Azure Portal Otwórz menu Portal, a następnie wybierz pozycję **pulpit nawigacyjny**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania pulpitu nawigacyjnego.":::

1. Znajdź wykres, który został przypięty wcześniej, i kliknij w dowolnym miejscu na kafelku poza danymi wykresu, aby go rozwinąć. Pokazuje wysłane komunikaty telemetryczne i łączną liczbę komunikatów używanych na wykresie. Najnowsze numery pojawiają się w dolnej części wykresu. Możesz przenieść kursor na wykresie, aby wyświetlić wartości metryk dla określonych godzin. Możesz również zmienić wartość czasu i stopień szczegółowości w górnej części wykresu, aby zawęzić lub rozszerzyć dane do przedziału czasu.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Zrzut ekranu przedstawiający wykres metryk.":::

   W tym scenariuszu przepływność komunikatów symulowanego urządzenia nie jest wystarczająco duża, aby spowodować, że IoT Hub do ograniczania swoich komunikatów. W scenariuszu, który faktycznie obejmuje ograniczenie przepustowości, można zobaczyć, że wysłane komunikaty telemetryczne przekraczają limit ograniczenia dla Centrum IoT przez ograniczony czas. Jest to związane z ruchem sieciowym. Aby uzyskać szczegółowe informacje, zobacz [kształtowanie ruchu](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Wyświetlanie alertów

Gdy liczba wysłanych komunikatów przekracza limity ustawione w regułach alertów, zaczynasz otrzymywać alerty e-mail.

Aby sprawdzić, czy istnieją aktywne alerty, wybierz pozycję **alerty** w obszarze **monitorowanie** w lewym okienku Centrum IoT. W okienku **alerty** zostanie wyświetlona liczba alertów, które zostały wywołane posortowane według ważności dla określonego zakresu czasu.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Zrzut ekranu przedstawiający podsumowanie alertów.":::

Wybierz wiersz dla ważności ważność 3. Zostanie otwarte okienko **wszystkie alerty** i zostanie wyświetlona lista alertów ważność 3, które zostały wyzwolone.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Zrzut ekranu przedstawiający okienko wszystkie alerty.":::

Wybierz jeden z alertów, aby wyświetlić szczegóły alertu.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Zrzut ekranu przedstawiający szczegóły alertu.":::

Sprawdź skrzynkę odbiorczą, aby otrzymywać wiadomości e-mail z Microsoft Azure. Wiersz tematu zawiera opis alertu, który został wyzwolony. Na przykład *platforma Azure: uaktywniona ważność: 3 alert, jeśli więcej niż 1000 komunikatów przekracza 5 minut*. Treść będzie wyglądać podobnie do poniższej ilustracji:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Zrzut ekranu przedstawiający wiadomość e-mail na temat uruchomionych alertów.":::

## <a name="view-azure-monitor-logs"></a>Wyświetlanie dzienników Azure Monitor

W sekcji [zbieranie dzienników dla połączeń i telemetrii urządzeń](#collect-logs-for-connections-and-device-telemetry) utworzono ustawienie diagnostyczne umożliwiające wysyłanie dzienników zasobów emitowanych przez Centrum IoT w celu nawiązania połączenia i operacji telemetrii urządzenia z dziennikami Azure monitor. W tej sekcji zostanie uruchomione zapytanie Kusto z dziennikami Azure Monitor, aby obserwować wszystkie błędy, które wystąpiły.

1. W obszarze **monitorowanie** w lewym okienku Centrum IoT w obszarze Azure Portal wybierz pozycję **dzienniki**. Zamknij okno początkowe **zapytania** , jeśli zostanie otwarte.

1. W okienku nowe zapytanie wybierz kartę **zapytania** , a następnie rozwiń węzeł **IoT Hub** , aby wyświetlić listę domyślnych zapytań.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Zrzut ekranu przedstawiający IoT Hub domyślnych zapytań.":::

1. Wybierz zapytanie *podsumowujące błędy* . Zapytanie zostanie wyświetlone w okienku Edytora zapytań. Wybierz pozycję **Uruchom** w okienku edytora i obserwuj wyniki zapytania. Rozwiń jeden z wierszy, aby wyświetlić szczegóły.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Zrzut ekranu przedstawiający dzienniki zwracane przez zapytanie podsumowania błędów.":::

   > [!NOTE]
   > Jeśli nie widzisz żadnych błędów, spróbuj uruchomić zapytanie *ostatnio połączonych urządzeń* . Powinno to zwrócić wiersz symulowanego urządzenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć wszystkie zasoby utworzone w ramach tego samouczka, usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku usuwa Centrum IoT Hub, obszar roboczy Log Analytics i samą grupę zasobów. Jeśli przypięto wykresy metryk do pulpitu nawigacyjnego, należy usunąć je ręcznie, klikając trzy kropki w prawym górnym rogu każdego wykresu i wybierając pozycję **Usuń**. Pamiętaj, aby zapisać zmiany po usunięciu wykresów.

Aby usunąć grupę zasobów, użyj polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się używać metryk IoT Hub i dzienników, wykonując następujące zadania:

> [!div class="checklist"]
>
> * Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć Centrum IoT, zarejestrować symulowane urządzenie i utworzyć obszar roboczy Log Analytics.  
> * Wysyłanie IoT Hub połączeń i dzienników zasobów telemetrii urządzeń do Azure Monitor dzienników w obszarze roboczym Log Analytics.
> * Użyj Eksploratora metryk, aby utworzyć wykres oparty na wybranych metrykach i przypiąć go do pulpitu nawigacyjnego.
> * Utwórz alerty metryk, aby otrzymywać powiadomienia pocztą e-mail o wystąpieniu ważnych warunków.
> * Pobierz i uruchom aplikację, która symuluje wysyłanie komunikatów przez urządzenie IoT do centrum IoT Hub.
> * Wyświetl alerty w przypadku wystąpienia warunków.
> * Wyświetl wykres metryk na pulpicie nawigacyjnym.
> * Wyświetlanie IoT Hub błędów i operacji w dziennikach Azure Monitor.

Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać stanem urządzenia IoT. 

> [!div class="nextstepaction"]
> [Konfigurowanie urządzeń z poziomu usługi zaplecza](tutorial-device-twins.md)