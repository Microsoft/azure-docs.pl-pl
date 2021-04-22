---
title: Samouczek — konfigurowanie metryk i dzienników oraz korzystanie z nich w usłudze Azure IoT Hub
description: Samouczek — dowiedz się, jak skonfigurować metryki i dzienniki oraz używać ich w usłudze Azure IoT Hub. Zapewni to dane do przeanalizowania w celu zdiagnozowania problemów, które mogą wystąpić w centrum.
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
ms.openlocfilehash: 96a9f7c50f3e30d86497c7a612ddda248db3f703
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865697"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Samouczek: konfigurowanie metryk i dzienników oraz korzystanie z nich za pomocą centrum IoT

Za pomocą Azure Monitor można zbierać metryki i dzienniki dla centrum IoT, które mogą pomóc w monitorowaniu działania rozwiązania i rozwiązywaniu problemów, gdy wystąpią. W tym artykule dowiesz się, jak tworzyć wykresy na podstawie metryk, jak tworzyć alerty wyzwalane na podstawie metryk IoT Hub, jak wysyłać operacje i błędy do dzienników Azure Monitor oraz jak sprawdzać dzienniki pod uwagę błędów.

W tym samouczku użyto przykładu platformy Azure z przewodnika Szybki start Wysyłanie danych telemetrycznych platformy [.NET](quickstart-send-telemetry-dotnet.md) do wysyłania komunikatów do centrum IoT. Do wysyłania komunikatów zawsze możesz użyć urządzenia lub innego przykładu, ale może być wymagane odpowiednie zmodyfikowanie kilku kroków.

Znajomość pojęć Azure Monitor może być przydatna przed rozpoczęciem tego samouczka. Aby dowiedzieć się więcej, zobacz [Monitorowanie IoT Hub](monitor-iot-hub.md). Aby dowiedzieć się więcej na temat metryk i dzienników zasobów emitowanych przez IoT Hub, zobacz [Monitoring data reference (Monitorowanie danych referencyjnych).](monitor-iot-hub-reference.md)

Ten samouczek obejmuje wykonanie następujących zadań:

> [!div class="checklist"]
>
> * Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć centrum IoT Hub, zarejestrować symulowane urządzenie i utworzyć obszar roboczy usługi Log Analytics.  
> * Wysyłaj IoT Hub i dzienniki zasobów telemetrii urządzenia do dzienników Azure Monitor w obszarze roboczym usługi Log Analytics.
> * Użyj Eksploratora metryk, aby utworzyć wykres na podstawie wybranych metryk i przypiąć go do pulpitu nawigacyjnego.
> * Utwórz alerty dotyczące metryk, aby być powiadamiane pocztą e-mail, gdy wystąpią ważne warunki.
> * Pobierz i uruchom aplikację, która symuluje urządzenie IoT wysyłające komunikaty do centrum IoT.
> * Wyświetlanie alertów w przypadku wystąpienia warunków.
> * Wyświetl wykres metryk na pulpicie nawigacyjnym.
> * Wyświetlanie IoT Hub i operacji w dziennikach Azure Monitor dziennikach.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Na komputerze dewelopera zestaw .NET Core SDK co najmniej 2.1. Możesz pobrać zestaw SDK .NET Core dla wielu platform z repozytorium [.NET](https://dotnet.microsoft.com/download).

  Możesz sprawdzić bieżącą wersję języka C# na komputerze deweloperskim przy użyciu następującego polecenia:

  ```cmd/sh
  dotnet --version
  ```

- Konto e-mail umożliwiające odbieranie wiadomości e-mail.

- Upewnij się, że port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym samouczku używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Konfigurowanie zasobów

W tym samouczku potrzebujesz centrum IoT Hub, obszaru roboczego usługi Log Analytics i symulowanego urządzenia IoT. Te zasoby można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Użyj tej samej grupy zasobów i lokalizacji dla wszystkich zasobów. Po zakończeniu tego samouczka możesz usunąć wszystko w jednym kroku, usuwając grupę zasobów.

Poniżej znajdują się wymagane kroki.

1. Utwórz [grupę zasobów](../azure-resource-manager/management/overview.md).

2. Utwórz centrum IoT.

3. Utworzenie obszaru roboczego usługi Log Analytics.

4. Zarejestruj tożsamość urządzenia dla symulowanego urządzenia, które wysyła komunikaty do centrum IoT. Zapisz ciąg połączenia urządzenia, aby użyć go do skonfigurowania urządzenia symulowanego.

### <a name="set-up-resources-using-azure-cli"></a>Konfigurowanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Skopiuj i wklej ten skrypt w usłudze Cloud Shell. Przy założeniu, że użytkownik jest już zalogowany, usługa będzie kolejno uruchamiać po jednym wierszu skryptu. Wykonanie niektórych poleceń może trochę potrwać. Nowe zasoby są tworzone w grupie zasobów *ContosoResources.*

Nazwa niektórych zasobów musi być unikatowa na platformie Azure. Skrypt generuje losową wartość z funkcją `$RANDOM` i zapisuje ją w zmiennej. W przypadku tych zasobów skrypt dołącza tę losową wartość do podstawowej nazwy zasobu, dzięki czemu nazwa zasobu jest unikatowa.

Na subskrypcję jest dozwolone tylko jedno bezpłatne centrum IoT Hub. Jeśli masz już bezpłatne centrum IoT Hub w ramach subskrypcji, usuń je przed uruchomieniem skryptu lub zmodyfikuj skrypt, aby używać bezpłatnego centrum IoT lub IoT Hub, która korzysta z warstwy Standardowa lub Podstawowa.

Skrypt drukuje nazwę centrum IoT Hub, nazwę obszaru roboczego usługi Log Analytics i parametrów połączenia dla rejestrowanego urządzenia. Pamiętaj, aby je zanotować, ponieważ będą one potrzebne w dalszej części tego artykułu.

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
>Podczas tworzenia tożsamości urządzenia może wystąpić następujący błąd: *No keys found for policy iothubowner of IoT Hub ContosoTestHub*(Nie znaleziono kluczy dla zasad iothubowner IoT Hub ContosoTestHub). Aby naprawić ten błąd, zaktualizuj rozszerzenie IoT interfejsu wiersza polecenia platformy Azure, a następnie uruchom ponownie ostatnie dwa polecenia w skrypcie. 
>
>Poniżej przedstawiono polecenie umożliwiające zaktualizowanie rozszerzenia. Uruchom to polecenie w Cloud Shell wystąpienia.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Zbieranie dzienników połączeń i telemetrii urządzenia

IoT Hub emituje dzienniki zasobów dla kilku kategorii operacji; Jednak aby wyświetlić te dzienniki, należy utworzyć ustawienie diagnostyczne w celu wysłania ich do miejsca docelowego. Jednym z takich miejsc docelowych Azure Monitor dzienniki, które są zbierane w obszarze roboczym usługi Log Analytics. IoT Hub zasobów są pogrupowane w różne kategorie. W ustawieniu diagnostycznym można wybrać kategorie, które mają być wysyłane Azure Monitor do dzienników. W tym artykule zbierzemy dzienniki dotyczące operacji i błędów związanych z połączeniami i telemetrią urządzenia. Aby uzyskać pełną listę kategorii obsługiwanych przez program IoT Hub, [zobacz IoT Hub zasobów.](monitor-iot-hub-reference.md#resource-logs)

Aby utworzyć ustawienie diagnostyczne do wysyłania dzienników IoT Hub do dzienników Azure Monitor, wykonaj następujące kroki:

1. Najpierw, jeśli nie jesteś jeszcze w centrum w portalu, wybierz pozycję **Grupy** zasobów i wybierz grupę zasobów ContosoResources. Wybierz centrum IoT z wyświetlonej listy zasobów.

1. W bloku usługi IoT Hub wyszukaj sekcję **Monitorowanie**. Wybierz **pozycję Ustawienia diagnostyczne.** Następnie wybierz **pozycję Dodaj ustawienie diagnostyczne.**

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Zrzut ekranu przedstawiający wyróżnione ustawienia diagnostyczne w sekcji Monitorowanie.":::

1. W **okienku Ustawienia diagnostyczne** nadaj ustawieniu opisową nazwę, taką jak "Wyślij połączenia i telemetrię do dzienników".

1. W **obszarze Szczegóły kategorii** wybierz pozycję **Połączenia** i **Telemetria urządzenia.**

1. W **obszarze Szczegóły lokalizacji** docelowej wybierz pozycję Wyślij do usługi Log **Analytics,** a następnie użyj selektora obszaru roboczego usługi Log Analytics, aby wybrać zanotowyny wcześniej obszar roboczy. Po zakończeniu ustawienie diagnostyczne powinno wyglądać podobnie do poniższego zrzutu ekranu:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający ostateczne ustawienia dzienników diagnostycznych.":::

1. Wybierz pozycję **Zapisz**, aby zapisać ustawienia. Zamknij okienko **Ustawienia diagnostyczne.** Nowe ustawienie zostanie wyświetlona na liście ustawień diagnostycznych.

## <a name="set-up-metrics"></a>Konfigurowanie metryk

Teraz użyjemy Eksploratora metryk do utworzenia wykresu, który wyświetla metryki, które chcesz śledzić. Przypniesz ten wykres do domyślnego pulpitu nawigacyjnego w Azure Portal.

1. W lewym okienku centrum IoT wybierz pozycję **Metryki** w **sekcji** Monitorowanie.

1. W górnej części ekranu wybierz pozycję **Ostatnie 24 godziny (automatycznie).** Na wyświetlonej liście rozwijanej  wybierz pozycję Ostatnie **4 godziny** dla opcji Zakres czasu, ustaw opcję Poziom szczegółowości czasu na **1** minutę, a dla opcji Pokaż czas wybierz wartość **Lokalny.**   Wybierz **pozycję Zastosuj,** aby zapisać te ustawienia. Ustawienie powinno teraz mieć ustawienie **Czas lokalny: Ostatnie 4 godziny (1 minuta).**

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Zrzut ekranu przedstawiający ustawienia czasu metryk.":::

1. Na wykresie wyświetlane jest ustawienie metryki częściowej w zakresie centrum IoT. Pozostaw **wartości domyślne** w obszarach **Zakres** i Przestrzeń nazw metryk. Wybierz ustawienie **Metryka** i wpisz "Telemetria", a następnie wybierz pozycję **Wysłane komunikaty telemetryczne** z listy rozwijanej. **Agregacja** zostanie automatycznie ustawiona na **sumę**. Zwróć uwagę, że tytuł wykresu również się zmienia.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Zrzut ekranu przedstawiający dodawanie metryki komunikatów telemetrii wysłanych do wykresu.":::

1. Teraz wybierz **pozycję Dodaj metrykę,** aby dodać kolejną metrykę do wykresu. W obszarze **Metryka** wybierz pozycję **Łączna liczba użytych komunikatów**. **Agregacja** zostanie automatycznie ustawiona na **śr.**. Ponownie zwróć uwagę, że tytuł wykresu został zmieniony, aby uwzględnić tę metrykę.

   Teraz na ekranie jest widoczna zminimalizowana metryka *Wysłane komunikaty telemetryczne* oraz nowa metryka *Łączna liczba użytych komunikatów*.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Zrzut ekranu przedstawiający dodawanie metryki Total number of messages used do wykresu.":::

1. W prawym górnym rogu wykresu wybierz pozycję Przypnij **do pulpitu nawigacyjnego.**

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Zrzut ekranu przedstawiający przycisk Przypnij do pulpitu nawigacyjnego.":::

1. W **okienku Przypnij** do pulpitu nawigacyjnego wybierz **kartę** Istniejące. Wybierz **pozycję Prywatny,** a następnie wybierz pozycję **Pulpit nawigacyjny** z listy rozwijanej Pulpit nawigacyjny. Na koniec wybierz pozycję **Przypnij,** aby przypiąć wykres do domyślnego pulpitu nawigacyjnego Azure Portal. Jeśli nie przypniesz wykresu do pulpitu nawigacyjnego, ustawienia nie zostaną zachowane podczas zamykania Eksploratora metryk.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Zrzut ekranu przedstawiający ustawienia przypinania do pulpitu nawigacyjnego.":::

## <a name="set-up-metric-alerts"></a>Konfigurowanie alertów dotyczących metryk

Teraz skonfigurujemy alerty wyzwalane dla dwóch wysłanych komunikatów telemetrii *i* łącznej *liczby użytych komunikatów.*

*Wysłane komunikaty telemetryczne* są dobrą metryką do monitorowania przepływności komunikatów i uniknięcia jej ograniczania. W przypadku IoT Hub w warstwie Bezpłatna limit ograniczania wynosi 100 komunikatów na sekundę. W przypadku pojedynczego urządzenia nie będziemy w stanie osiągnąć tego rodzaju przepływności, dlatego zamiast tego skonfigurujemy alert wyzwalany, jeśli liczba komunikatów przekroczy 1000 w ciągu 5 minut. W środowisku produkcyjnym można ustawić sygnał na bardziej znaczącą wartość w zależności od warstwy, wersji i liczby jednostek centrum IoT.

*Łączna liczba użytych komunikatów* śledzi dzienną liczbę używanych komunikatów. Ta metryka jest resetowana codziennie o godzinie 00:00 czasu UTC. W przypadku przekroczenia dziennego limitu przydziału po przekroczeniu określonego progu IoT Hub nie będą już akceptować komunikatów. W przypadku IoT Hub w warstwie Bezpłatna dzienny limit przydziału komunikatów wynosi 8000. Skonfigurujemy alert do wyzwalania, jeśli łączna liczba komunikatów przekroczy 4000, 50% limitu przydziału. W praktyce prawdopodobnie ustawisz tę wartość procentową na wyższą wartość. Wartość dziennego limitu przydziału zależy od warstwy, wersji i liczby jednostek centrum IoT.

Aby uzyskać więcej informacji na temat limitów przydziału i limitów IoT Hub, zobacz [Limity przydziału i ograniczanie przepustowości.](iot-hub-devguide-quotas-throttling.md)

Aby skonfigurować alerty dotyczące metryk:

1. Przejdź do centrum IoT w Azure Portal.

1. W **obszarze Monitorowanie** wybierz pozycję **Alerty**. Następnie wybierz pozycję **Nowa reguła alertu.**  Zostanie **otwarte okienko Tworzenie reguły** alertu.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Zrzut ekranu przedstawiający okienko Tworzenie reguły alertu.":::

    W **okienku Tworzenie reguły alertu** znajdują się cztery sekcje:

    * **Zakres** jest już ustawiony na Centrum IoT, więc pozostawimy tę sekcję bez siebie.
    * **Warunek** ustawia sygnał i warunki, które będą wyzwalać alert.
    * **Akcje** konfigurują, co się stanie po wyzwoleniu alertu.
    * **Szczegóły reguły alertu** umożliwiają ustawienie nazwy i opisu alertu.

1. Najpierw skonfiguruj warunek wyzwalania alertu.

    1. W **obszarze Warunek** wybierz **pozycję Dodaj warunek**. W **okienku Konfigurowanie logiki sygnału** wpisz "telemetria" w polu wyszukiwania i wybierz pozycję **Wysłane komunikaty telemetrii**.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Zrzut ekranu przedstawiający wybieranie metryki.":::

    1. W **okienku Konfigurowanie logiki sygnału** ustaw lub potwierdź następujące pola w obszarze **Logika alertu** (możesz zignorować wykres):

       **Próg:**  *Statyczny*.

       **Operator:** *większe niż*.

       **Typ agregacji:** *Łącznie*.

       **Wartość progowa:** 1000.

       **Poziom szczegółowości agregacji (okres):** *5 minut.*

       **Częstotliwość oceny:** *co 1 minutę*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Zrzut ekranu przedstawiający ustawienia warunków alertu.":::

       Te ustawienia ustawiają sygnał tak, aby łączna liczba komunikatów w okresie 5 minut. Ta suma będzie oceniana co minutę, a jeśli suma z poprzednich 5 minut przekroczy 1000 komunikatów, alert zostanie wyzwolony.

       Wybierz **pozycję Gotowe,** aby zapisać logikę sygnału.

1. Teraz skonfiguruj akcję dla alertu.

    1. Po powrocie do **okienka Tworzenie reguły alertu** w **obszarze Akcje** wybierz **pozycję Dodaj grupy akcji.** W **okienku Wybierz grupę akcji do dołączenia do tej reguły alertu** wybierz **pozycję Utwórz grupę akcji.**

    1. Na karcie **Podstawowe** w **okienku Tworzenie grupy akcji** nadaj grupie akcji nazwę i nazwę wyświetlaną.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Zrzut ekranu przedstawiający kartę Podstawowe okienka Tworzenie grupy akcji.":::

    1. Wybierz **kartę** Powiadomienia. W **przypadku typu powiadomienia** wybierz z listy rozwijanej pozycję Wiadomość **e-mail/wiadomość SMS/Wypychanie/Głos.** Zostanie **otwarte okienko Poczta e-mail/wiadomość SMS/wypychanie/głos.**

    1. W **okienku Poczta e-mail/wiadomość SMS/wypychanie/głos** wybierz pozycję e-mail i wprowadź swój adres e-mail, a następnie wybierz przycisk **OK.**

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Zrzut ekranu przedstawiający ustawienie adresu e-mail.":::

    1. Po powrocie do **okienka** Powiadomienia wprowadź nazwę powiadomienia.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Zrzut ekranu przedstawiający ukończone okienko powiadomień.":::

    1. (Opcjonalnie) Jeśli wybierzesz **kartę Akcje,**  a następnie wybierzesz menu rozwijane Typ akcji, zobaczysz rodzaje akcji, które można wyzwolić za pomocą alertu. W tym artykule będziemy używać tylko powiadomień, więc możesz zignorować ustawienia na tej karcie.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Zrzut ekranu przedstawiający typy akcji dostępne w okienku Akcje.":::

    1. Wybierz **kartę Przeglądanie i tworzenie,** sprawdź ustawienia, a następnie wybierz pozycję **Utwórz.**

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Zrzut ekranu przedstawiający okienko Przeglądanie i tworzenie.":::

    1. Po powrocie do **okienka Tworzenie reguły alertu** zwróć uwagę, że nowa grupa akcji została dodana do akcji dla alertu.  

1. Na koniec skonfiguruj szczegóły reguły alertu i zapisz regułę alertu.

    1. W **okienku Tworzenie reguły alertu** w obszarze Szczegóły reguły alertu wprowadź nazwę i opis alertu. na przykład "Alert, jeśli ponad 1000 komunikatów w ciągu 5 minut". Upewnij się, **że pole Włącz regułę alertu podczas tworzenia** jest zaznaczone. Ukończona reguła alertu będzie wyglądać podobnie do tego zrzutu ekranu.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Zrzut ekranu przedstawiający ukończone okienko Tworzenie reguły alertu.":::

    1. Wybierz **pozycję Utwórz regułę alertu,** aby zapisać nową regułę.

1. Teraz skonfiguruj kolejny alert dla metryki *Łączna liczba użytych komunikatów*. Ta metryka jest przydatna, jeśli chcesz wysłać alert, gdy liczba użytych komunikatów zbliża się do dziennego limitu przydziału dla centrum IoT, od tego momentu centrum IoT rozpocznie odrzucanie komunikatów. Wykonaj kroki, które były wcześniej, z następującymi różnicami.

    * Dla sygnału w **okienku Konfigurowanie logiki sygnału** wybierz pozycję **Łączna liczba użytych komunikatów.**

    * W **okienku Konfigurowanie logiki sygnału** ustaw lub potwierdź następujące pola (możesz zignorować wykres):

       **Próg:**  *Statyczny*.

       **Operator:** *większe niż*.

       **Typ agregacji:** *Maksimum*.

       **Wartość progowa:** 4000.

       **Poziom szczegółowości agregacji (okres):** *1 minuta.*

       **Częstotliwość oceny:** *co 1 minutę*

       Te ustawienia ustawiają sygnał do działania, gdy liczba komunikatów osiągnie 4000. Metryka jest oceniana co minutę.

    * Po określeniu akcji dla reguły alertu wystarczy wybrać utworzoną wcześniej grupę akcji.

    * Dla szczegółów alertu wybierz inną nazwę i opis niż wcześniej.

1. Wybierz **pozycję Alerty** w **obszarze Monitorowanie** w lewym okienku centrum IoT. Teraz wybierz **pozycję Zarządzaj regułami alertów** w menu w górnej części **okienka Alerty.** Zostanie **otwarte okienko** Reguły. Powinny zostać teraz wyświetlony dwa alerty:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Zrzut ekranu przedstawiający okienko Reguły z nowymi regułami alertów.":::

1. Zamknij **okienko Reguły.**

Te ustawienia spowodują wyzwolenie alertu i otrzymasz powiadomienie e-mail, gdy w ciągu 5 minut zostanie wysłanych ponad 1000 wiadomości, a także gdy łączna liczba użytych komunikatów przekroczy 4000 (50% dziennego limitu przydziału centrum IoT w warstwie Bezpłatna).

## <a name="run-the-simulated-device-app"></a>Uruchamianie aplikacji urządzenia symulowanego

W [sekcji Konfigurowanie zasobów zarejestrowano](#set-up-resources) tożsamość urządzenia do użycia w celu symulowania przy użyciu urządzenia IoT. W tej sekcji pobierzesz aplikację konsolową .NET, która symuluje urządzenie, które wysyła komunikaty z urządzenia do chmury do usługi IoT Hub, skonfiguruje ją do wysyłania tych komunikatów do centrum IoT Hub, a następnie uruchom je.

> [!IMPORTANT]
>
> Pełne skonfigurowanie i włączeniu alertów przez program może potrwać do 10 IoT Hub. Odczekaj co najmniej 10 minut między skonfigurowaniem ostatniego alertu i uruchomieniem aplikacji urządzenia symulowanego.

Pobierz rozwiązanie na potrzeby [symulacji urządzenia IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Ten link pobiera do niego repo z kilkoma aplikacjami. Ten, którego szukasz, to iot-hub/Quickstarts/simulated-device/.

1. W lokalnym oknie terminalu przejdź do folderu głównego rozwiązania. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

1. Otwórz plik **SimulatedDevice.cs** w wybranym edytorze.

    1. Zastąp wartość zmiennej przy użyciu parametrów połączenia urządzenia zanotowych podczas uruchamiania `s_connectionString` skryptu w celu skonfigurowania zasobów.

    1. W metodzie zmień wartość z 1000 na 1, co skraca czas między wysyłaniem komunikatów z 1 sekundy do `SendDeviceToCloudMessagesAsync` `Task.Delay` 0,001 sekundy. Skrócenie tego opóźnienia zwiększy liczbę wysyłanych komunikatów. (Prawdopodobnie nie otrzymasz szybkości komunikatów 100 komunikatów na sekundę).

        ```csharp
        await Task.Delay(1);
        ```

    1. Zapisz zmiany w **plikach SimulatedDevice.cs.**

1. W lokalnym oknie terminalu uruchom następujące polecenie, aby zainstalować wymagane pakiety dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    dotnet restore
    ```

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby utworzyć i uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    dotnet run
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe symulowanego urządzenia.":::

Pozwól aplikacji działać przez co najmniej 10–15 minut. W idealnym przypadku niech będzie działać, dopóki nie przestanie wysyłać komunikatów (około 20–30 minut). Dzieje się tak po przekroczeniu dziennego limitu przydziału komunikatów dla centrum IoT i zaprzestaniu akceptowania kolejnych komunikatów.

> [!NOTE]
> Jeśli aplikacja urządzenia zostanie uruchomiona przez dłuższy czas po zatrzymaniu wysyłania komunikatów, może wystąpić wyjątek. Możesz bezpiecznie zignorować ten wyjątek i zamknąć okno aplikacji.

## <a name="view-metrics-chart-on-your-dashboard"></a>Wyświetlanie wykresu metryk na pulpicie nawigacyjnym

1. W lewym górnym rogu okna Azure Portal menu portalu, a następnie wybierz pozycję Pulpit **nawigacyjny.**

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania pulpitu nawigacyjnego.":::

1. Znajdź przypięty wcześniej wykres i kliknij dowolne miejsce na kafelku poza danymi wykresu, aby go rozwinąć. Przedstawia wysłane komunikaty telemetryczne i łączną liczbę komunikatów używanych na wykresie. Najnowsze liczby są wyświetlane w dolnej części wykresu. Możesz przenieść kursor na wykresie, aby wyświetlić wartości metryk dla określonych godzin. Możesz również zmienić wartość czasu i poziom szczegółowości w górnej części wykresu, aby zawęzić lub rozwinąć dane do interesującego okresu.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Zrzut ekranu przedstawiający wykres metryk.":::

   W tym scenariuszu przepływność komunikatów urządzenia symulowanego nie jest wystarczająco duża, aby IoT Hub ograniczać komunikaty. W scenariuszu, który faktycznie obejmuje ograniczanie przepustowości, wysyłane komunikaty telemetryczne przekraczają limit ograniczenia dla centrum IoT przez ograniczony czas. Ma to na celu przystosowanie do ruchu z serii. Aby uzyskać szczegółowe informacje, zobacz [Kształtowanie ruchu](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Wyświetlanie alertów

Gdy liczba wysłanych komunikatów przekroczy limity określone w zasadach alertów, zaczniesz otrzymywać alerty e-mail.

Aby sprawdzić, czy istnieją aktywne  alerty, wybierz pozycję Alerty w **obszarze Monitorowanie** w lewym okienku centrum IoT. Okienko **Alerty** zawiera liczbę wyzowanych alertów posortowanych według ważności dla określonego zakresu czasu.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Zrzut ekranu przedstawiający podsumowanie alertów.":::

Wybierz wiersz o ważności Ważność 3. Zostanie **otwarte okienko** Wszystkie alerty z listą wyzgodnych alertów oev 3.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Zrzut ekranu przedstawiający okienko Wszystkie alerty.":::

Wybierz jeden z alertów, aby wyświetlić szczegóły alertu.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Zrzut ekranu przedstawiający szczegóły alertu.":::

Sprawdź skrzynkę odbiorczą pod adresami e-mail Microsoft Azure. Wiersz tematu będzie opisywać wyzwolony alert. Na przykład *Azure: Aktywowana ważność: 3 alert, jeśli ponad 1000 komunikatów w ciągu 5 minut.* Treść będzie wyglądać podobnie do poniższej ilustracji:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Zrzut ekranu przedstawiający wiadomość e-mail na temat uruchomionych alertów.":::

## <a name="view-azure-monitor-logs"></a>Wyświetlanie Azure Monitor dzienników

W sekcji [Zbieranie dzienników](#collect-logs-for-connections-and-device-telemetry) połączeń i telemetrii urządzeń utworzono ustawienie diagnostyczne do wysyłania dzienników zasobów emitowanych przez centrum IoT na temat operacji telemetrii połączeń i urządzeń do usługi Azure Monitor Logs. W tej sekcji zostanie uruchomione zapytanie Kusto względem dzienników Azure Monitor, aby zaobserwować wszelkie błędy, które wystąpiły.

1. W **obszarze** Monitorowanie w okienku po lewej stronie centrum IoT hub Azure Portal pozycję **Dzienniki.** Zamknij początkowe okno **Zapytania,** jeśli zostanie otwarte.

1. W okienku Nowe zapytanie wybierz **kartę Zapytania,** a następnie rozwiń IoT Hub, aby wyświetlić listę zapytań domyślnych. 

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Zrzut ekranu IoT Hub zapytań domyślnych.":::

1. Wybierz zapytanie *Podsumowanie* błędu. Zapytanie zostanie wyświetlone w okienku Edytor zapytań. Wybierz **pozycję Uruchom** w okienku edytora i obserwuj wyniki zapytania. Rozwiń jeden z wierszy, aby wyświetlić szczegóły.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Zrzut ekranu przedstawiający dzienniki zwrócone przez zapytanie podsumowania Błędy.":::

   > [!NOTE]
   > Jeśli nie widzisz żadnych błędów, spróbuj uruchomić zapytanie *Ostatnio połączone* urządzenia. Powinno to zwrócić wiersz dla urządzenia symulowanego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć wszystkie zasoby utworzone w ramach tego samouczka, usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W tym przypadku usuwa centrum IoT Hub, obszar roboczy usługi Log Analytics i samą grupę zasobów. Jeśli wykresy metryk zostały przypięte do pulpitu nawigacyjnego, musisz usunąć je ręcznie, klikając trzy kropki w prawym górnym rogu każdego wykresu i wybierając pozycję **Usuń**. Pamiętaj, aby zapisać zmiany po usunięciu wykresów.

Aby usunąć grupę zasobów, użyj polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób używania IoT Hub metryk i dzienników, wykonując następujące zadania:

> [!div class="checklist"]
>
> * Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć centrum IoT Hub, zarejestrować symulowane urządzenie i utworzyć obszar roboczy usługi Log Analytics.  
> * Wysyłaj IoT Hub i dzienniki zasobów telemetrii urządzenia do dzienników Azure Monitor w obszarze roboczym usługi Log Analytics.
> * Użyj Eksploratora metryk, aby utworzyć wykres na podstawie wybranych metryk i przypiąć go do pulpitu nawigacyjnego.
> * Utwórz alerty dotyczące metryk, aby być powiadamiane pocztą e-mail, gdy wystąpią ważne warunki.
> * Pobierz i uruchom aplikację, która symuluje urządzenie IoT wysyłające komunikaty do centrum IoT.
> * Wyświetlanie alertów w przypadku wystąpienia warunków.
> * Wyświetl wykres metryk na pulpicie nawigacyjnym.
> * Wyświetlanie IoT Hub i operacji w dziennikach Azure Monitor dziennikach.

Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać stanem urządzenia IoT. 

> [!div class="nextstepaction"]
> [Konfigurowanie urządzeń z poziomu usługi zaplecza](tutorial-device-twins.md)