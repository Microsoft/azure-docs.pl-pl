---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub (CLI) — szybki start
description: Ten przewodnik Szybki Start przedstawia deweloperów, którzy IoT Hub jak rozpocząć pracę przy użyciu interfejsu wiersza polecenia platformy Azure, aby utworzyć Centrum IoT, wysyłać dane telemetryczne i wyświetlać komunikaty między urządzeniem a centrum.
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
ms.openlocfilehash: e2b8eecc629e9da75ea15815ee38844c48abb019
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87499912"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i monitorowanie go za pomocą interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku szybki start użyjesz interfejsu wiersza polecenia platformy Azure w celu utworzenia IoT Hub i symulowanego urządzenia, wysłania telemetrii urządzenia do centrum oraz wysłania komunikatu z chmury do urządzenia. Należy również użyć Azure Portal do wizualizacji metryk urządzeń. Jest to podstawowy przepływ pracy dla deweloperów korzystających z interfejsu wiersza polecenia w celu współdziałania z aplikacją IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
- Interfejs wiersza polecenia platformy Azure. Wszystkie polecenia w tym przewodniku szybki start można uruchomić za pomocą Azure Cloud Shell, interaktywnej powłoki interfejsu wiersza polecenia, która jest uruchamiana w przeglądarce. W przypadku korzystania z Cloud Shell nie trzeba instalować żadnych elementów. Jeśli wolisz używać interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki Start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Bez względu na to, czy uruchamiasz interfejs wiersza polecenia lokalnie, czy w Cloud Shell, należy pozostawić Portal otwarty w przeglądarce.  Używasz go później w tym przewodniku Szybki Start.

## <a name="launch-the-cloud-shell"></a>Uruchom Cloud Shell
W tej sekcji uruchomisz wystąpienie Azure Cloud Shell. Jeśli używasz interfejsu wiersza polecenia lokalnie, przejdź do sekcji [przygotowanie dwóch sesji interfejsu wiersza polecenia](#prepare-two-cli-sessions).

Aby uruchomić Cloud Shell:

1. Wybierz przycisk **Cloud Shell** w prawym górnym pasku menu w Azure Portal. 

    ![Przycisk Azure Portal Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony komunikat z prośbą o utworzenie magazynu, który jest wymagany do korzystania z Cloud Shell.  Wybierz subskrypcję, aby utworzyć konto magazynu i udział plików Microsoft Azure. 

2. Wybierz preferowane środowisko interfejsu wiersza polecenia na liście rozwijanej **Wybierz środowisko** . Ten przewodnik Szybki Start używa środowiska **bash** . W środowisku programu PowerShell są również wykonywane wszystkie następujące polecenia interfejsu CLI. 

    ![Wybierz środowisko interfejsu wiersza polecenia](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Przygotuj dwie sesje interfejsu wiersza polecenia

W tej sekcji przygotowasz dwie sesje interfejsu wiersza polecenia platformy Azure. Jeśli używasz Cloud Shell, zostaną uruchomione dwie sesje na osobnych kartach przeglądarki. W przypadku korzystania z lokalnego klienta interfejsu wiersza polecenia uruchamiane są dwa oddzielne wystąpienia interfejsu wiersza polecenia. Będziesz używać pierwszej sesji jako symulowanego urządzenia, a druga sesja do monitorowania i wysyłania komunikatów. Aby uruchomić polecenie, wybierz opcję **Kopiuj** w celu skopiowania bloku kodu w tym przewodniku Szybki Start, wklej go do sesji powłoki i uruchom go.

Interfejs wiersza polecenia platformy Azure wymaga zalogowania się do konta platformy Azure. Cała komunikacja między sesją powłoki interfejsu wiersza polecenia platformy Azure i centrum IoT Hub jest uwierzytelniana i szyfrowana. W związku z tym ten przewodnik Szybki Start nie wymaga dodatkowego uwierzytelniania, którego można używać z rzeczywistym urządzeniem, na przykład z parametrami połączenia.

*  Uruchom polecenie [AZ Extension Add](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) , aby dodać rozszerzenie Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do powłoki interfejsu wiersza polecenia. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Po zainstalowaniu rozszerzenia Azure IOT nie trzeba go instalować ponownie w żadnej sesji Cloud Shell. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Otwórz drugą sesję interfejsu wiersza polecenia.  Jeśli używasz Cloud Shell, wybierz pozycję **Otwórz nową sesję**. Jeśli używasz interfejsu wiersza polecenia lokalnie, Otwórz drugie wystąpienie. 

    >[!div class="mx-imgBorder"]
    >![Otwórz nową sesję Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub
W tej sekcji utworzysz grupę zasobów i IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure.  Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. IoT Hub pełni rolę centralnego centrum komunikatów na potrzeby komunikacji dwukierunkowej między Twoją aplikacją IoT a urządzeniami. 

> [!TIP]
> Opcjonalnie można utworzyć grupę zasobów platformy Azure, IoT Hub i inne zasoby przy użyciu [Azure Portal](iot-hub-create-through-portal.md), [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)lub innych metod programistycznych.  

1. Uruchom polecenie [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) , aby utworzyć grupę zasobów. Następujące polecenie tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* . 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Uruchom polecenie [AZ IoT Hub Create](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) , aby utworzyć Centrum IoT Hub. Utworzenie centrum IoT Hub może potrwać kilka minut. 

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. Nazwa Centrum IoT musi być globalnie unikatowa na platformie Azure. Ten symbol zastępczy jest używany w pozostałej części tego przewodnika Szybki Start do reprezentowania nazwy Centrum IoT.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Tworzenie i monitorowanie urządzenia
W tej sekcji utworzysz symulowane urządzenie w pierwszej sesji interfejsu wiersza polecenia. Symulowane urządzenie wysyła dane telemetryczne urządzenia do centrum IoT Hub. W drugiej sesji interfejsu wiersza polecenia można monitorować zdarzenia i dane telemetryczne oraz wysyłać komunikaty z chmury do urządzenia do symulowanego urządzenia.

Aby utworzyć i uruchomić symulowane urządzenie:
1. Uruchom polecenie [AZ IoT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-create) w pierwszej sesji interfejsu wiersza polecenia. Spowoduje to utworzenie symulowanej tożsamości urządzenia. 

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    *simDevice*. Tej nazwy można użyć bezpośrednio dla symulowanego urządzenia w pozostałej części tego przewodnika Szybki Start. Opcjonalnie użyj innej nazwy. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Uruchom polecenie [AZ IoT Device symulacja](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/device?view=azure-cli-latest#ext-azure-iot-az-iot-device-simulate) w pierwszej sesji interfejsu wiersza polecenia.  Spowoduje to uruchomienie symulowanego urządzenia. Urządzenie wysyła dane telemetryczne do centrum IoT Hub i odbiera z niego komunikaty.  

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Aby monitorować urządzenie:
1. W drugiej sesji interfejsu wiersza polecenia Uruchom polecenie [AZ IoT Hub monitor-Events](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events) . Spowoduje to rozpoczęcie monitorowania symulowanego urządzenia. Wyjście przedstawia dane telemetryczne wysyłane przez urządzenie symulowane do centrum IoT Hub.

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Zdarzenia monitorowania Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Po monitorowaniu symulowanego urządzenia w drugiej sesji interfejsu wiersza polecenia naciśnij klawisze CTRL + C, aby zatrzymać monitorowanie. 

## <a name="use-the-cli-to-send-a-message"></a>Wysyłanie komunikatu przy użyciu interfejsu wiersza polecenia
W tej sekcji zostanie użyta druga sesja interfejsu wiersza polecenia w celu wysłania komunikatu do symulowanego urządzenia.

1. W pierwszej sesji interfejsu wiersza polecenia upewnij się, że symulowane urządzenie jest uruchomione. Jeśli urządzenie zostało zatrzymane, uruchom następujące polecenie, aby je uruchomić:

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. W drugiej sesji interfejsu wiersza polecenia Uruchom polecenie [AZ IoT Device C2D-Message Send](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/device/c2d-message?view=azure-cli-latest#ext-azure-iot-az-iot-device-c2d-message-send) . Spowoduje to wysłanie komunikatu z chmury do urządzenia z Centrum IoT Hub do urządzenia symulowanego. Komunikat zawiera ciąg i dwie pary klucz-wartość.  

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Opcjonalnie możesz wysyłać komunikaty z chmury do urządzenia przy użyciu Azure Portal. W tym celu przejdź do strony przeglądu IoT Hub, wybierz pozycję **urządzenia IoT**, wybierz symulowane urządzenie, a następnie wybierz pozycję **komunikat do urządzenia**. 

1. W pierwszej sesji interfejsu wiersza polecenia upewnij się, że symulowane urządzenie odebrało komunikat. 

    ![Cloud Shell komunikat z chmury do urządzenia](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Po wyświetleniu komunikatu Zamknij drugą sesję interfejsu wiersza polecenia. Pozostaw pierwszą otwartą sesję interfejsu wiersza polecenia. Służy do czyszczenia zasobów w późniejszym kroku.

## <a name="view-messaging-metrics-in-the-portal"></a>Wyświetlanie metryk komunikatów w portalu
Azure Portal pozwala zarządzać wszystkimi aspektami IoT Hub i urządzeń. W typowej aplikacji IoT Hub, która pozyskuje dane telemetryczne z urządzeń, warto monitorować urządzenia lub wyświetlać metryki na potrzeby telemetrii urządzeń. 

Aby wizualizować metryki komunikatów w Azure Portal:
1. W menu nawigacji po lewej stronie portalu wybierz pozycję **wszystkie zasoby**. Spowoduje to wyświetlenie listy wszystkich zasobów w ramach subskrypcji, w tym utworzonego Centrum IoT. 

1. Wybierz link w utworzonym Centrum IoT. W portalu zostanie wyświetlona strona przegląd dla centrum.

1. W lewym okienku IoT Hub wybierz pozycję **metryki** . 

    ![Metryki komunikatów IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Wprowadź nazwę Centrum IoT Hub w **zakresie**.

2. Wybierz pozycję *metryki standardowe w usłudze IoT Hub* w **przestrzeni nazw metryki**.

3. Wybierz *łączną liczbę komunikatów używanych* w **metryce**. 

4. Umieść wskaźnik myszy na obszarze osi czasu, w którym urządzenie wysłało komunikaty. Całkowita liczba komunikatów w punkcie w czasie pojawia się w lewym dolnym rogu osi czasu.

    ![Wyświetl metryki usługi Azure IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Opcjonalnie użyj listy rozwijanej **Metryka** , aby wyświetlić inne metryki na symulowanym urządzeniu. Na przykład *C2d komunikaty zostały ukończone* lub *łączna liczba urządzeń (wersja zapoznawcza)*. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli zasoby platformy Azure utworzone w ramach tego przewodnika Szybki Start nie są już potrzebne, można je usunąć za pomocą interfejsu wiersza polecenia platformy Azure.

W przypadku kontynuowania następnego zalecanego artykułu można zachować zasoby, które zostały już utworzone, i użyć ich ponownie. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

Aby usunąć grupę zasobów na podstawie nazwy:
1. Uruchom polecenie [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Spowoduje to usunięcie grupy zasobów, IoT Hub i utworzonej rejestracji urządzenia.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Uruchom polecenie [AZ Group list](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) , aby potwierdzić, że grupa zasobów została usunięta.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start użyto interfejsu wiersza polecenia platformy Azure do utworzenia Centrum IoT Hub, utworzenia symulowanego urządzenia, wysłania telemetrii, monitorowania telemetrii, wysłania komunikatu z chmury do urządzenia oraz oczyszczenia zasobów. Użyto Azure Portal do wizualizacji metryk komunikatów na urządzeniu.

Jeśli jesteś deweloperem urządzenia, sugerowanym następnym krokiem jest wyświetlenie przewodnika Szybki Start dotyczącego usługi Azure IoT, który używa zestawu SDK urządzeń dla języka C. Opcjonalnie można zapoznać się z jednym z dostępnych artykułów szybkiego startu usługi Azure IoT Hub w preferowanym języku lub zestawie SDK.

> [!div class="nextstepaction"]
> [Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub (C)](quickstart-send-telemetry-c.md)
