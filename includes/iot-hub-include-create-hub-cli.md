---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: ab17b12913630774c4fe6d99a4830080de02e541
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712598"
---
W poniższych sekcjach opisano Konfigurowanie terminalu i tworzenie Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure. Aby skonfigurować Terminal, na którym są uruchomione polecenie interfejsu wiersza polecenia platformy Azure, można użyć Azure Cloud Shell opartej na przeglądarce lub lokalnego terminalu.
* Aby użyć Cloud Shell, przejdź do następnej sekcji: [uruchom Cloud Shell](#launch-the-cloud-shell). 
* Aby użyć terminalu lokalnego, Pomiń następną sekcję i przejdź do pozycji [Otwórz Terminal lokalny](#open-a-local-terminal).

## <a name="launch-the-cloud-shell"></a>Uruchom Cloud Shell
W tej sekcji utworzysz sesję Cloud Shell i skonfigurujesz środowisko terminalu.

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.  

Aby uruchomić Cloud Shell:

1. Wybierz przycisk **Cloud Shell** w prawym górnym pasku menu w Azure Portal. 

    ![Przycisk Azure Portal Cloud Shell](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony komunikat z prośbą o utworzenie magazynu, który jest wymagany do korzystania z Cloud Shell.  Wybierz subskrypcję, aby utworzyć konto magazynu i udział plików Microsoft Azure. 

2. Wybierz preferowane środowisko interfejsu wiersza polecenia na liście rozwijanej **Wybierz środowisko** . Ten przewodnik Szybki Start używa środowiska **bash** . W środowisku programu PowerShell są również wykonywane wszystkie następujące polecenia interfejsu CLI. 

    ![Wybierz środowisko interfejsu wiersza polecenia](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Pomiń następną sekcję i przejdź do sekcji [Instalowanie rozszerzenia usługi Azure IoT](#install-the-azure-iot-extension). 

## <a name="open-a-local-terminal"></a>Otwórz Terminal lokalny
W przypadku wybrania opcji używania terminalu lokalnego zamiast Cloud Shell należy wykonać tę sekcję.  

1. Otwórz Terminal lokalny.
1. Uruchom polecenie [AZ login](/cli/azure/reference-index?preserve-view=true&view=azure-cli-latest#az_login) :

   ```azurecli
   az login
   ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

    W przeciwnym razie Otwórz stronę przeglądarki pod adresem https://aka.ms/devicelogin i wprowadź kod autoryzacji wyświetlany w terminalu.

    Jeśli przeglądarka sieci Web nie jest dostępna lub nie uda się otworzyć przeglądarki sieci Web, użyj przepływu kodu urządzenia z `az login --use-device-code` .

1. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

    Aby dowiedzieć się więcej na temat różnych metod uwierzytelniania, zobacz [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure]( /cli/azure/authenticate-azure-cli ).

1. Przejdź do następnej sekcji: [Zainstaluj rozszerzenie Azure IoT](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>Instalowanie rozszerzenia usługi Azure IoT
W tej sekcji instalujesz rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure z powłoką interfejsu wiersza polecenia. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

> [!IMPORTANT]
> Polecenia terminalu w pozostałej części tego przewodnika Szybki Start działają tak samo w Cloud Shell lub na terminalu lokalnym. Aby uruchomić polecenie, wybierz opcję **Kopiuj** , aby skopiować blok kodu w tym przewodniku Szybki Start. Następnie wklej go do powłoki interfejsu wiersza polecenia i uruchom go.

Uruchom polecenie [AZ Extension Add](/cli/azure/extension?preserve-view=true&view=azure-cli-latest#az-extension-add) . 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub
W tej sekcji utworzysz Centrum IoT i grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure.  Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Usługa IoT Hub działa jako centralny centrum komunikatów do komunikacji dwukierunkowej między aplikacją IoT a urządzeniami. 

Aby utworzyć Centrum IoT i grupę zasobów:

1. Uruchom polecenie [AZ Group Create](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-create) , aby utworzyć grupę zasobów. Następujące polecenie tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* . 
    >[!NOTE]
    > Opcjonalnie można ustawić alternatywną lokalizację. Aby wyświetlić dostępne lokalizacje, uruchom polecenie `az account list-locations` . W tym samouczku używa się *wschodniego* , jak pokazano w przykładowym poleceniu. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Uruchom polecenie [AZ IoT Hub Create](/cli/azure/iot/hub?preserve-view=true&view=azure-cli-latest#az-iot-hub-create) , aby utworzyć Centrum IoT Hub. Utworzenie centrum IoT Hub może potrwać kilka minut. 

    *YourIotHubName*. Zastąp ten symbol zastępczy i otaczające nawiasy klamrowe w poniższym poleceniu przy użyciu nazwy wybranej dla Centrum IoT. Nazwa Centrum IoT musi być globalnie unikatowa na platformie Azure. Użyj nazwy Centrum IoT w pozostałej części tego przewodnika Szybki Start wszędzie tam, gdzie zobaczysz symbol zastępczy.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Tworzenie symulowanego urządzenia
W tej sekcji utworzysz symulowane urządzenie IoT połączone z Centrum IoT Hub. 

Aby utworzyć symulowane urządzenie:
1. Uruchom polecenie [AZ IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity?preserve-view=true&view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-create) w POWŁOCE interfejsu wiersza polecenia. Spowoduje to utworzenie symulowanej tożsamości urządzenia. 

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    *urządzenie*. Tej nazwy można użyć bezpośrednio dla symulowanego identyfikatora urządzenia w dalszej części tego artykułu. Opcjonalnie użyj innej nazwy. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Uruchom polecenie [AZ IoT Hub Device-Identity Connection-String show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string?preserve-view=true&view=azure-cli-latest#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) . 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    Dane wyjściowe parametrów połączenia mają następujący format:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Zapisz parametry połączenia w bezpiecznej lokalizacji. 

> [!NOTE]
> Pozostaw otwartą powłokę interfejsu wiersza polecenia. Zostanie ona użyta w dalszych krokach.