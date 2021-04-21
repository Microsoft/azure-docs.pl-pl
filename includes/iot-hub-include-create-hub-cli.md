---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: be51d4db6c98b63fcb1c9c60df045b494536d78f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766530"
---
W poniższych sekcjach skonfigurujemy terminal i użyjemy interfejsu wiersza polecenia platformy Azure do utworzenia centrum IoT. Aby skonfigurować terminal z poleceniami interfejsu wiersza polecenia platformy Azure, możesz użyć interfejsu Azure Cloud Shell lub terminala lokalnego.
* Aby użyć Cloud Shell, przejdź do następnej sekcji: [Uruchamianie Cloud Shell](#launch-the-cloud-shell). 
* Aby użyć terminalu lokalnego, pomiń następną sekcję i przejdź [do sekcji Otwieranie lokalnego terminalu](#open-a-local-terminal).

## <a name="launch-the-cloud-shell"></a>Uruchom Cloud Shell
W tej sekcji utworzysz sesję Cloud Shell i skonfigurujesz środowisko terminalu.

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.  

Aby uruchomić Cloud Shell:

1. Wybierz przycisk **Cloud Shell** na pasku menu w prawym górnym rogu Azure Portal. 

    ![Azure Portal Cloud Shell przycisku](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Jeśli po raz pierwszy używasz Cloud Shell, zostanie wyświetlony monit o utworzenie magazynu, który jest wymagany do korzystania z Cloud Shell.  Wybierz subskrypcję, aby utworzyć konto magazynu i Microsoft Azure Files. 

2. Wybierz preferowane środowisko interfejsu wiersza polecenia z **listy rozwijanej** Wybierz środowisko. W tym przewodniku Szybki start używane jest **środowisko powłoki Bash.** Wszystkie poniższe polecenia interfejsu wiersza polecenia działają również w środowisku programu PowerShell. 

    ![Wybieranie środowiska interfejsu wiersza polecenia](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Pomiń następną sekcję i przejdź [do sekcji Instalowanie rozszerzenia usługi Azure IoT.](#install-the-azure-iot-extension) 

## <a name="open-a-local-terminal"></a>Otwieranie terminalu lokalnego
Jeśli chcesz użyć terminalu lokalnego, a nie Cloud Shell, wykonaj tę sekcję.  

1. Otwórz lokalny terminal.
1. Uruchom polecenie [az login:](/cli/azure/reference-index#az_login)

   ```azurecli
   az login
   ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

    W przeciwnym razie otwórz stronę przeglądarki pod https://aka.ms/devicelogin adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

    Jeśli przeglądarka internetowa nie jest dostępna lub nie można jej otworzyć, użyj przepływu kodu urządzenia z usługą `az login --use-device-code` .

1. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

    Aby dowiedzieć się więcej na temat różnych metod uwierzytelniania, zobacz [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure]( /cli/azure/authenticate-azure-cli ).

1. Przejdź do następnej sekcji: [Instalowanie rozszerzenia usługi Azure IoT.](#install-the-azure-iot-extension) 

## <a name="install-the-azure-iot-extension"></a>Instalowanie rozszerzenia usługi Azure IoT
W tej sekcji zainstalujesz rozszerzenie IoT Microsoft Azure interfejsu wiersza polecenia platformy Azure do powłoki interfejsu wiersza polecenia. Rozszerzenie IOT dodaje IoT Hub, IoT Edge i usługę IoT Device Provisioning Service (DPS) do interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Polecenia terminalu w pozostałej części tego przewodnika Szybki start działają tak samo w Cloud Shell lub terminalu lokalnym. Aby uruchomić polecenie, wybierz pozycję **Kopiuj,** aby skopiować blok kodu w tym przewodniku Szybki start. Następnie wklej go do powłoki interfejsu wiersza polecenia i uruchom go.

Uruchom [polecenie az extension add.](/cli/azure/extension#az_extension_add) 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub
W tej sekcji utworzysz centrum IoT i grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure.  Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Centrum IoT działa jako centrum komunikatów dla dwukierunkowej komunikacji między aplikacją IoT i urządzeniami. 

Aby utworzyć centrum IoT i grupę zasobów:

1. Uruchom polecenie [az group create,](/cli/azure/group#az_group_create) aby utworzyć grupę zasobów. Następujące polecenie tworzy grupę zasobów o *nazwie MyResourceGroup* w *lokalizacji eastus.* 
    >[!NOTE]
    > Opcjonalnie możesz ustawić alternatywną lokalizację. Aby wyświetlić dostępne lokalizacje, `az account list-locations` uruchom . W tym samouczku jest *używany region eastus,* jak pokazano w przykładowym poleceniu. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Uruchom polecenie [az iot hub create,](/cli/azure/iot/hub#az_iot_hub_create) aby utworzyć centrum IoT Hub. Tworzenie centrum IoT może potrwać kilka minut. 

    *YourIotHubName*. Zastąp ten symbol zastępczy i otaczające nawiasy klamrowe w poniższym poleceniu, używając nazwy wybranej dla centrum IoT. Nazwa centrum IoT musi być globalnie unikatowa na platformie Azure. Użyj nazwy centrum IoT w pozostałej części tego przewodnika Szybki start wszędzie tam, gdzie zobaczysz symbol zastępczy.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Tworzenie symulowanego urządzenia
W tej sekcji utworzysz symulowane urządzenie IoT połączone z centrum IoT. 

Aby utworzyć urządzenie symulowane:
1. Uruchom polecenie [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) w powłoki interfejsu wiersza polecenia. Powoduje to utworzenie tożsamości urządzenia symulowanego. 

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    *myDevice*. Możesz użyć tej nazwy bezpośrednio dla identyfikatora urządzenia symulowanego w pozostałej części tego artykułu. Opcjonalnie użyj innej nazwy. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Uruchom polecenie [az iot hub device-identity connection-string show.](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    Dane wyjściowe parametrów połączenia mają następujący format:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Zapisz ciąg połączenia w bezpiecznej lokalizacji. 

> [!NOTE]
> Nie otwieraj powłoki interfejsu wiersza polecenia. Zostanie ona użyta w dalszych krokach.