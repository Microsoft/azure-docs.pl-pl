---
title: Samouczek — aktualizowanie oprogramowania układowego urządzenia za pomocą usługi Azure IoT Hub | Microsoft Docs
description: Samouczek — informacje o implementacji procesu aktualizacji oprogramowania układowego urządzenia, który można wywołać z aplikacji zaplecza połączonej z Centrum IoT Hub.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/28/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 3fc257ff192ccb1bb05b233c6ac802696ece0054
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575724"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Samouczek: wdrażanie procesu aktualizacji oprogramowania układowego urządzenia

Może zaistnieć potrzeba zaktualizowania oprogramowania układowego na urządzeniach podłączonych do centrum IoT. Na przykład możesz chcieć dodać nowe funkcje do oprogramowania układowego lub zastosować poprawki zabezpieczeń. W wielu scenariuszach IoT fizyczne odwiedzanie urządzeń, a następnie ręczne stosowanie do nich aktualizacji oprogramowania układowego, jest niepraktyczne. W tym samouczku pokazano, jak uruchomić i monitorować proces aktualizacji oprogramowania układowego zdalnie przy użyciu aplikacji zaplecza podłączonej do centrum.

Aby utworzyć i monitorować proces aktualizacji oprogramowania układowego, aplikacja zaplecza w tym samouczku tworzy _konfigurację_ w centrum IoT. Funkcja [automatycznego zarządzania urządzeniami](./iot-hub-automatic-device-management.md) usługi IoT Hub używa tej konfiguracji do aktualizacji zestawu _żądanych właściwości bliźniaczej reprezentacji urządzenia_ na wszystkich urządzeniach typu „chiller” (chłodziarka). Żądane właściwości określają szczegóły wymaganej aktualizacji oprogramowania układowego. Podczas wykonywania procesu aktualizacji oprogramowania układowego urządzeń typu „chiller” (chłodziarek) urządzenia te wysyłają raporty o swoim stanie do aplikacji zaplecza za pomocą _zgłoszonych właściwości bliźniaczej reprezentacji urządzenia_. Aplikacja zaplecza może przy użyciu konfiguracji monitorować zgłoszone właściwości wysyłane z urządzenia i śledzić proces aktualizacji oprogramowania układowego do jego ukończenia:

![Proces aktualizacji oprogramowania układowego](media/tutorial-firmware-update/Process.png)

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Utworzenie centrum IoT i dodanie urządzenia testowego do rejestru tożsamości urządzeń.
> * Utworzenie konfiguracji definiującej aktualizację oprogramowania układowego.
> * Symulowanie procesu aktualizacji oprogramowania układowego na urządzeniu.
> * Otrzymywanie aktualizacji stanu z urządzenia w miarę postępu aktualizacji oprogramowania układowego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane przy użyciu środowiska Node.js. Potrzebujesz Node.js v10. x. x lub nowszego na komputerze deweloperskim.

Node.js można pobrać dla wielu platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

Pobierz przykładowy projekt Node.js z https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip i wyodrębnij archiwum ZIP.

Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym samouczku używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure

Do ukończenia tego samouczka niezbędna jest subskrypcja platformy Azure zawierająca centrum IoT z urządzeniem dodanym do rejestru tożsamości urządzeń. Wpis w rejestrze tożsamości urządzeń umożliwia łączenie urządzenia symulowanego uruchamianego w tym samouczku z centrum.

Jeśli nie masz jeszcze centrum IoT Hub w subskrypcji, możesz skonfigurować je przy użyciu poniższego skryptu interfejsu wiersza polecenia. Ten skrypt używa nazwy **tutorial-iot-hub** dla centrum IoT Hub. Po uruchomieniu skryptu zastąp tę nazwę własną unikatową nazwą. Skrypt tworzy grupę zasobów i centrum w regionie **Środkowe stany USA**, który można zmienić na bliższy. Skrypt pobiera parametry połączenia usługi IoT Hub używane w przykładowej aplikacji zaplecza w celu nawiązania połączenia z centrum IoT:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create a free-tier IoT Hub. You can have only one free IoT Hub per subscription. Free tier hubs can have only 2 partitions.
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --partition-count 2 --sku F1

# Make a note of the service connection string, you need it later
az iot hub connection-string show --name $hubname --policy-name service -o table

```

W tym samouczku jest używane urządzenie symulowane o nazwie **MyFirmwareUpdateDevice**. Poniższy skrypt dodaje to urządzenie do rejestru tożsamości urządzeń, ustawia wartość tagu i pobiera odpowiednie parametry połączenia:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"device type":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity connection-string show --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Jeśli uruchamiasz te polecenia w wierszu polecenia systemu Windows lub w wierszu polecenia programu PowerShell, zobacz stronę [porad dotyczących rozszerzenia azure-iot-cli-extension](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips), aby uzyskać informacje na temat sposobu umieszczania ciągów JSON w cudzysłowach.

## <a name="start-the-firmware-update"></a>Rozpoczynanie aktualizacji oprogramowania układowego

Można utworzyć [automatyczną konfigurację zarządzania urządzeniami](iot-hub-automatic-device-management.md#create-a-configuration) w aplikacji zaplecza, aby rozpocząć proces aktualizacji oprogramowania układowego na wszystkich urządzeniach oznaczonych za pomocą **urządzenia** chłodzenia. W tej sekcji zobaczysz, jak:

* Utworzyć konfigurację z poziomu aplikacji zaplecza.
* Monitorować zadanie do ukończenia.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>Rozpoczynanie uaktualnienia oprogramowania układowego z poziomu aplikacji zaplecza przy użyciu żądanych właściwości

Aby wyświetlić kod aplikacji zaplecza, który tworzy konfigurację, przejdź do folderu **iot-hub/Tutorials/FirmwareUpdate** w pobranym przykładowym projekcie Node.js. Następnie otwórz plik ServiceClient.js w edytorze tekstów.

Aplikacja zaplecza tworzy następującą konfigurację:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

Konfiguracja zawiera następujące sekcje:

* `content` — określa żądane właściwości oprogramowania układowego wysyłane do wybranych urządzeń.
* `metrics` — określa zapytania uruchamiane w celu wyświetlenia raportu stanu aktualizacji oprogramowania układowego.
* `targetCondition` — wybiera urządzenia, które otrzymają aktualizację oprogramowania układowego.
* `priorty` — ustawia względny priorytet tej konfiguracji w odniesieniu do innych konfiguracji.

Aplikacja zaplecza używa następującego kodu do utworzenia konfiguracji w celu ustawienia żądanych właściwości:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

Po utworzeniu konfiguracji aplikacja monitoruje aktualizację oprogramowania układowego:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

Konfiguracja raportuje dwa rodzaje metryk:

* Metryki systemu, które raportują liczbę urządzeń docelowych i liczbę urządzeń, na których aktualizacja została zastosowana.
* Metryki niestandardowe generowane przez zapytania dodane do konfiguracji. W tym samouczku zapytania raportują liczbę urządzeń znajdujących się na każdym etapie procesu aktualizacji.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Odpowiadanie na żądanie uaktualnienia oprogramowania układowego na urządzeniu

Aby wyświetlić kod urządzenia symulowanego, który obsługuje żądane właściwości oprogramowania układowego wysyłane z aplikacji zaplecza, przejdź do folderu **iot-hub/Tutorials/FirmwareUpdate** w pobranym przykładowym projekcie Node.js. Następnie otwórz plik SimulatedDevice.js w edytorze tekstów.

Aplikacja urządzenia symulowanego tworzy procedurę obsługi aktualizacji żądanych właściwości **properties.desired.firmware** w bliźniaczej reprezentacji urządzenia. W procedurze obsługi przeprowadza pewne podstawowe kontrole żądanych właściwości przed uruchomieniem procesu aktualizacji:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>Aktualizacja oprogramowania układowego

Funkcja **InitiateFirmwareUpdateFlow** uruchamia aktualizację. Funkcja ta używa funkcji **waterfall** do uruchamiania faz procesu aktualizacji w sekwencji. W tym przykładzie aktualizacja oprogramowania układowego składa się z czterech faz. Pierwsza faza to pobieranie obrazu, druga faza to weryfikowanie obrazu za pomocą sumy kontrolnej, trzecia faza to stosowanie obrazu, a ostatnia faza to ponowne uruchamianie urządzenia:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

Podczas procesu aktualizacji urządzenie symulowane raportuje postęp za pomocą zgłoszonych właściwości:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

Poniższy fragment kodu przedstawia implementację fazy pobierania. W fazie pobierania urządzenie symulowane używa zgłoszonych właściwości do wysyłania informacji o stanie do aplikacji zaplecza:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

W tej sekcji uruchomisz dwie przykładowe aplikacje, aby obserwować, jak aplikacja zaplecza tworzy konfigurację w celu zarządzania procesem aktualizacji oprogramowania układowego na urządzeniu symulowanym.

Aby uruchomić urządzenie symulowane i aplikacje zaplecza, potrzebujesz parametrów połączenia usługi i urządzenia. Parametry połączenia zostały zapisane podczas tworzenia zasobu na początku tego samouczka.

Aby uruchomić aplikację urządzenia symulowanego, otwórz powłokę lub okno wiersza polecenia i przejdź do folderu **iot-hub/Tutorials/FirmwareUpdate** w pobranym projekcie platformy Node.js. Następnie uruchom następujące polecenia:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Aby uruchomić aplikację zaplecza, otwórz inną powłokę lub okno wiersza polecenia. Następnie przejdź do folderu **iot-hub/Tutorials/FirmwareUpdate** w pobranym projekcie Node.js. Następnie uruchom następujące polecenia:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji urządzenia symulowanego i prezentuje sposób, w jaki odpowiada ona na aktualizację żądanych właściwości oprogramowania układowego z aplikacji zaplecza:

![Symulowane urządzenie](./media/tutorial-firmware-update/SimulatedDevice.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji zaplecza i prezentuje sposób, w jaki tworzy ona konfigurację w celu aktualizacji żądanych właściwości oprogramowania układowego:

![Zrzut ekranu pokazujący dane wyjściowe aplikacji zaplecza.](./media/tutorial-firmware-update/BackEnd1.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji zaplecza i prezentuje sposób, w jaki monitoruje ona metryki aktualizacji oprogramowania układowego z urządzenia symulowanego:

![Aplikacja zaplecza](./media/tutorial-firmware-update/BackEnd2.png)

Ponieważ automatyczne konfiguracje urządzeń są uruchamiane podczas tworzenia, a następnie co pięć minut, może nie być widoczna każda aktualizacja stanu wysłana do aplikacji zaplecza. Metryki możesz również wyświetlić w portalu, w sekcji **Automatyczne zarządzanie urządzeniami -> Konfiguracja urządzenia IoT** centrum IoT:

![Wyświetlanie konfiguracji w portalu](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz ukończyć następny samouczek, pozostaw grupę zasobów i centrum IoT Hub, aby użyć ich później.

Jeśli nie potrzebujesz już tego centrum IoT, usuń je oraz grupę zasobów z poziomu portalu. Aby to zrobić, wybierz grupę zasobów **tutorial-iot-hub-rg** zawierającą centrum IoT Hub, a następnie kliknij przycisk **Usuń**.

Alternatywnie możesz użyć interfejsu wiersza polecenia:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób implementacji procesu aktualizacji oprogramowania układowego dla podłączonych urządzeń. Przejdź do następnego samouczka, aby dowiedzieć się, jak za pomocą narzędzi portalu usługi Azure IoT Hub i poleceń interfejsu wiersza polecenia platformy Azure przetestować łączność urządzeń.

> [!div class="nextstepaction"]
> [Testowanie łączności z centrum IoT za pomocą urządzenia symulowanego](tutorial-connectivity.md)
