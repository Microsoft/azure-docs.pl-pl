---
title: Szybki start — wysyłanie danych telemetrycznych do Azure IoT Hub (interfejs wiersza polecenia)
description: W tym przewodniku Szybki start deweloperzy nie IoT Hub, jak rozpocząć pracę przy użyciu interfejsu wiersza polecenia platformy Azure w celu utworzenia centrum IoT Hub, wysyłania danych telemetrycznych i wyświetlania komunikatów między urządzeniem a centrum.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
- 'Role: Cloud Development'
- devx-track-azurecli
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 843db24707b8c826fe48e9d50aa7ec5bc135399f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863609"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT i monitorowanie ich za pomocą interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start użyjemy interfejsu wiersza polecenia platformy Azure, aby utworzyć urządzenie IoT Hub i symulowane, wysłać dane telemetryczne urządzenia do centrum i wysłać komunikat z chmury do urządzenia. Za pomocą tej Azure Portal wizualizacji metryk urządzeń. Jest to podstawowy przepływ pracy dla deweloperów, którzy używają interfejsu wiersza polecenia do interakcji IoT Hub aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) je bezpłatnie.
- Interfejs wiersza polecenia platformy Azure. Wszystkie polecenia w tym przewodniku Szybki start można uruchamiać przy użyciu Azure Cloud Shell, interaktywnej powłoki interfejsu wiersza polecenia uruchamianej w przeglądarce. Jeśli używasz Cloud Shell, nie musisz niczego instalować. Jeśli wolisz używać interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Niezależnie od tego, czy uruchamiasz interfejs wiersza polecenia lokalnie, czy Cloud Shell, nie otwieraj portalu w przeglądarce.  Użyjemy go w dalszej części tego przewodnika Szybki start.

## <a name="launch-the-cloud-shell"></a>Uruchom Cloud Shell
W tej sekcji uruchomisz wystąpienie Azure Cloud Shell. Jeśli używasz interfejsu wiersza polecenia lokalnie, przejdź do sekcji [Przygotowywanie dwóch sesji interfejsu wiersza polecenia](#prepare-two-cli-sessions).

Aby uruchomić Cloud Shell:

1. Wybierz przycisk **Cloud Shell** na pasku menu w prawym górnym rogu Azure Portal. 

    ![Azure Portal Cloud Shell przycisku](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Jeśli po raz pierwszy używasz Cloud Shell, zostanie wyświetlony monit o utworzenie magazynu, który jest wymagany do korzystania z Cloud Shell.  Wybierz subskrypcję, aby utworzyć konto magazynu i Microsoft Azure Files. 

2. Wybierz preferowane środowisko interfejsu wiersza polecenia z **listy rozwijanej Wybierz** środowisko. W tym przewodniku Szybki start używane jest **środowisko powłoki Bash.** Wszystkie poniższe polecenia interfejsu wiersza polecenia działają również w środowisku programu PowerShell. 

    ![Wybieranie środowiska interfejsu wiersza polecenia](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Przygotowywanie dwóch sesji interfejsu wiersza polecenia

W tej sekcji przygotujemy dwie sesje interfejsu wiersza polecenia platformy Azure. Jeśli używasz karty Cloud Shell, dwie sesje zostaną uruchomione na osobnych kartach przeglądarki. Jeśli używasz lokalnego klienta interfejsu wiersza polecenia, uruchom dwa oddzielne wystąpienia interfejsu wiersza polecenia. Użyjesz pierwszej sesji jako urządzenia symulowanego, a drugiej sesji do monitorowania i wysyłania komunikatów. Aby uruchomić polecenie, wybierz pozycję **Kopiuj,** aby skopiować blok kodu w tym przewodniku Szybki start, wkleić go do sesji powłoki i uruchomić.

Interfejs wiersza polecenia platformy Azure wymaga zalogowania się na koncie platformy Azure. Cała komunikacja między sesją powłoki interfejsu wiersza polecenia platformy Azure i centrum IoT jest uwierzytelniana i szyfrowana. W związku z tym ten przewodnik Szybki start nie wymaga dodatkowego uwierzytelniania, którego można użyć z rzeczywistym urządzeniem, takiego jak ciąg połączenia.

*  Uruchom polecenie [az extension add,](/cli/azure/extension#az_extension_add) aby dodać rozszerzenie IoT Microsoft Azure interfejsu wiersza polecenia platformy Azure do powłoki interfejsu wiersza polecenia. Rozszerzenie IOT dodaje IoT Hub, IoT Edge i usługę IoT Device Provisioning Service (DPS) do interfejsu wiersza polecenia platformy Azure.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Po zainstalowaniu rozszerzenia usługi Azure IOT nie trzeba instalować go ponownie w żadnej Cloud Shell usługi. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Otwórz drugą sesję interfejsu wiersza polecenia.  Jeśli używasz aplikacji, wybierz Cloud Shell **Otwórz nową sesję.** Jeśli używasz interfejsu wiersza polecenia lokalnie, otwórz drugie wystąpienie. 

    >[!div class="mx-imgBorder"]
    >![Otwieranie nowej Cloud Shell sesji](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub
W tej sekcji użyjemy interfejsu wiersza polecenia platformy Azure, aby utworzyć grupę zasobów i IoT Hub.  Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Usługa IoT Hub pełni rolę centrum komunikatów dla dwukierunkowej komunikacji między aplikacją IoT i urządzeniami. 

> [!TIP]
> Opcjonalnie możesz utworzyć grupę zasobów platformy Azure, IoT Hub i inne zasoby przy użyciu metod [Azure Portal](iot-hub-create-through-portal.md), [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)lub innych metod programowych.  

1. Uruchom polecenie [az group create,](/cli/azure/group#az_group_create) aby utworzyć grupę zasobów. Następujące polecenie tworzy grupę zasobów o *nazwie MyResourceGroup* w *lokalizacji eastus.* 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Uruchom polecenie [az iot hub create,](/cli/azure/iot/hub#az_iot_hub_create) aby utworzyć centrum IoT. Tworzenie centrum IoT może potrwać kilka minut. 

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. Nazwa centrum IoT musi być globalnie unikatowa na platformie Azure. Ten symbol zastępczy jest używany w pozostałej części tego przewodnika Szybki start do reprezentowania nazwy centrum IoT.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Tworzenie i monitorowanie urządzenia
W tej sekcji utworzysz urządzenie symulowane w pierwszej sesji interfejsu wiersza polecenia. Symulowane urządzenie wysyła dane telemetryczne urządzenia do centrum IoT. W drugiej sesji interfejsu wiersza polecenia monitorujesz zdarzenia i dane telemetryczne oraz wysyłasz komunikat z chmury do urządzenia symulowanego.

Aby utworzyć i uruchomić symulowane urządzenie:
1. Uruchom polecenie [az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) w pierwszej sesji interfejsu wiersza polecenia. Powoduje to utworzenie tożsamości urządzenia symulowanego. 

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    *simDevice*. W pozostałej części tego przewodnika Szybki start możesz użyć tej nazwy bezpośrednio dla urządzenia symulowanego. Opcjonalnie użyj innej nazwy. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Uruchom polecenie [az iot device simulate](/cli/azure/iot/device#az_iot_device_simulate) w pierwszej sesji interfejsu wiersza polecenia.  To uruchamia symulowane urządzenie. Urządzenie wysyła dane telemetryczne do centrum IoT i odbiera z niego komunikaty.  

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Aby monitorować urządzenie:
1. W drugiej sesji interfejsu wiersza polecenia uruchom [polecenie az iot hub monitor-events.](/cli/azure/iot/hub#az_iot_hub_monitor_events) Rozpocznie się monitorowanie symulowanego urządzenia. Dane wyjściowe pokazują dane telemetryczne wysyłane przez symulowane urządzenie do centrum IoT.

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Cloud Shell monitorowania zdarzeń](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Po monitorowaniu symulowanego urządzenia w drugiej sesji interfejsu wiersza polecenia naciśnij klawisze Ctrl+C, aby zatrzymać monitorowanie. 

## <a name="use-the-cli-to-send-a-message"></a>Wysyłanie komunikatu przy użyciu interfejsu wiersza polecenia
W tej sekcji użyjemy drugiej sesji interfejsu wiersza polecenia, aby wysłać komunikat do urządzenia symulowanego.

1. W pierwszej sesji interfejsu wiersza polecenia upewnij się, że urządzenie symulowane jest uruchomione. Jeśli urządzenie zostało zatrzymane, uruchom następujące polecenie, aby je uruchomić:

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. W drugiej sesji interfejsu wiersza polecenia uruchom [polecenie az iot device c2d-message send.](/cli/azure/iot/device/c2d-message#az_iot_device_c2d-message-send) Powoduje to wysłanie komunikatu z chmury do urządzenia z centrum IoT do urządzenia symulowanego. Komunikat zawiera ciąg i dwie pary klucz-wartość.  

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Opcjonalnie możesz wysyłać komunikaty z chmury do urządzenia przy użyciu Azure Portal. Aby to zrobić, przejdź do strony przeglądu aplikacji, IoT Hub urządzenia **IoT,** wybierz urządzenie symulowane i wybierz pozycję Komunikat **do urządzenia.** 

1. W pierwszej sesji interfejsu wiersza polecenia upewnij się, że urządzenie symulowane odebrało komunikat. 

    ![Cloud Shell komunikat z chmury do urządzenia](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Po wyświetleniu komunikatu zamknij drugą sesję interfejsu wiersza polecenia. Nie otwieraj pierwszej sesji interfejsu wiersza polecenia. Użyj go do oczyszczenia zasobów w późniejszym kroku.

## <a name="view-messaging-metrics-in-the-portal"></a>Wyświetlanie metryk komunikatów w portalu
Ten Azure Portal umożliwia zarządzanie wszystkimi aspektami IoT Hub i urządzeń. W typowej IoT Hub, która pozyska dane telemetryczne z urządzeń, można monitorować urządzenia lub wyświetlać metryki dotyczące telemetrii urządzenia. 

Aby zwizualizować metryki obsługi komunikatów w Azure Portal:
1. W menu nawigacji po lewej stronie w portalu wybierz pozycję **Wszystkie zasoby.** Zawiera listę wszystkich zasobów w ramach subskrypcji, w tym utworzonego centrum IoT. 

1. Wybierz link w utworzonym centrum IoT. W portalu zostanie wyświetlona strona przeglądu centrum.

1. Wybierz **pozycję Metryki** w lewym okienku IoT Hub. 

    ![IoT Hub metryk komunikatów](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Wprowadź nazwę centrum IoT w **zakresie**.

2. W obszarze Przestrzeń nazw metryk wybierz pozycję Standardowe metryki usługi *IoT* **Hub.**

3. Wybierz *pozycję Łączna liczba komunikatów używanych w* **metryki**. 

4. Umieść wskaźnik myszy nad obszarem osi czasu, w którym urządzenie wysłało komunikaty. Łączna liczba komunikatów w punkcie w czasie jest wyświetlana w lewym dolnym rogu osi czasu.

    ![Wyświetlanie Azure IoT Hub metryk](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Opcjonalnie możesz użyć listy **rozwijanej** Metryka, aby wyświetlić inne metryki na urządzeniu symulowanym. Na przykład *ukończone dostawy komunikatów C2D lub* *Łączna liczba urządzeń (wersja zapoznawcza)*. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie potrzebujesz już zasobów platformy Azure utworzonych w tym przewodniku Szybki start, możesz je usunąć za pomocą interfejsu wiersza polecenia platformy Azure.

Jeśli będziesz kontynuować pracę z następnym zalecanym artykułem, możesz zachować już utworzone zasoby i użyć ich ponownie. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

Aby usunąć grupę zasobów na podstawie nazwy:
1. Uruchom [polecenie az group delete.](/cli/azure/group#az_group_delete) Spowoduje to usunięcie grupy zasobów, IoT Hub i utworzonej rejestracji urządzenia.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Uruchom polecenie [az group list,](/cli/azure/group#az_group_list) aby potwierdzić usunięcie grupy zasobów.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start interfejs wiersza polecenia platformy Azure został użyty do utworzenia centrum IoT Hub, utworzenia symulowanego urządzenia, wysłania danych telemetrycznych, monitorowania telemetrii, wysłania komunikatu z chmury do urządzenia i oczyszczenia zasobów. Za pomocą tej Azure Portal wizualizować metryki obsługi komunikatów na urządzeniu.

Jeśli jesteś deweloperem urządzeń, sugerowanym następnym krokiem jest szybki start z telemetrią, który korzysta z zestawu SDK urządzeń usługi Azure IoT dla języka C. Opcjonalnie zapoznaj się z jednym z dostępnych artykułów szybkiego startu dotyczących telemetrii usługi Azure IoT Hub w preferowanym języku lub zestawie SDK.

> [!div class="nextstepaction"]
> [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT (C)](quickstart-send-telemetry-c.md)