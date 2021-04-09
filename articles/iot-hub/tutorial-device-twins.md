---
title: Samouczek — synchronizacja stanu urządzenia z platformy Azure IoT Hub | Microsoft Docs
description: Samouczek — informacje na temat używania usługi Device bliźniaczych reprezentacji do konfigurowania urządzeń z chmury oraz odbierania danych o stanie i zgodności z urządzeń.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 7dbc0404679927bcef1647dfdf46ce3360216a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733303"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Samouczek: konfigurowanie urządzeń z poziomu usługi zaplecza

Oprócz odbierania telemetrii z urządzeń być może trzeba je będzie skonfigurować z poziomu usługi zaplecza. Jeśli wysyłasz żądaną konfiguracje do urządzeń, możesz również odbierać aktualizacje stanu i zgodności z tych urządzeń. Możesz na przykład ustawić docelowy zakres temperatur działania urządzenia lub zbierać informacje o wersji oprogramowania układowego z urządzeń.

Aby synchronizować informacje o stanie między urządzeniem i centrum IoT Hub, należy użyć _bliźniaczych reprezentacji urządzenia_. [Bliźniacza reprezentacja urządzenia](iot-hub-devguide-device-twins.md) to dokument JSON skojarzony z określonym urządzeniem i przechowywany przez usługę IoT Hub w chmurze, w której można [wykonywać zapytania](iot-hub-devguide-query-language.md) dotyczące tego urządzenia. Bliźniacza reprezentacja urządzenia zawiera _żądane właściwości_, _zgłaszane właściwości_ i _tagi_. Żądana właściwość jest ustawiana przez aplikację zaplecza i odczytywana przez urządzenie. Zgłaszana właściwość jest ustawiana przez urządzenie i odczytywana przez aplikację zaplecza. Tag jest ustawiany przez aplikację zaplecza, ale nie jest nigdy wysyłany do urządzenia. Tagi są używane do organizowania urządzeń. W tym samouczku przedstawiono sposób używania właściwości żądanych i zgłaszanych do synchronizowania informacji o stanie:

![Podsumowanie bliźniaczej reprezentacji](media/tutorial-device-twins/DeviceTwins.png)

Ten samouczek obejmuje wykonanie następujących zadań:

> [!div class="checklist"]
> * Utworzenie centrum IoT Hub i dodanie urządzenia testowego do rejestru tożsamości.
> * Użycie żądanych właściwości do wysyłania informacji o stanie do urządzenia symulowanego.
> * Użycie zgłaszanych właściwości do odbierania informacji o stanie z urządzenia symulowanego.

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

Do ukończenia czynności z tego samouczka niezbędna jest subskrypcja platformy Azure zawierająca centrum IoT Hub z urządzeniem dodanym do rejestru tożsamości urządzeń. Wpis w rejestrze tożsamości urządzeń umożliwia łączenie urządzenia symulowanego uruchamianego w tym samouczku z centrum.

Jeśli nie masz jeszcze skonfigurowanej usługi IoT Hub w ramach subskrypcji, możesz ją skonfigurować przy użyciu następującego skryptu interfejsu wiersza polecenia. Ten skrypt używa nazwy **tutorial-iot-hub** dla centrum IoT Hub. Po uruchomieniu skryptu zastąp tę nazwę własną unikatową nazwą. Skrypt tworzy grupę zasobów i centrum w regionie **Środkowe stany USA**, który można zmienić na bliższy. Skrypt pobiera parametry połączenia usługi IoT Hub używane w przykładowym zapleczu w celi połączenia z centrum IoT Hub:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-iot

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription.
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --partition-count 2 --sku F1

# Make a note of the service connection string, you need it later:
az iot hub connection-string show --name $hubname --policy-name service -o table

```

W tym samouczku jest używane urządzenie symulowane o nazwie **MyTwinDevice**. Poniższy skrypt dodaje to urządzenie do rejestru tożsamości i pobiera odpowiednie parametry połączenia:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity connection-string show --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Wysyłanie informacji o stanie

Żądane właściwości służą do wysyłania informacji o stanie z aplikacji zaplecza do urządzenia. W tej sekcji zobaczysz, jak:

* Odbierać i przetwarzać żądane właściwości na urządzeniu.
* Wysyłać żądane właściwości z aplikacji zaplecza.

Aby zobaczyć przykładowy kod urządzenia symulowanego, który umożliwia odbieranie żądanych właściwości, przejdź do folderu **iot-hub/Tutorials/DeviceTwins** pobranego przykładowego projektu platformy Node.js. Następnie otwórz plik SimulatedDevice.js w edytorze tekstów.

W poniższych sekcjach opisano kod uruchamiany na urządzeniu symulowanym w odpowiedzi na zmiany żądanych właściwości wysyłane z aplikacji zaplecza:

### <a name="retrieve-the-device-twin-object"></a>Pobieranie obiektu bliźniaczej reprezentacji urządzenia

Poniższy kod łączy się z centrum IoT Hub przy użyciu parametrów połączenia urządzenia:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

Poniższy kod pobiera bliźniaczą reprezentację z obiektu klienta:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Przykładowe żądane właściwości

Strukturę żądanych właściwości można definiować w dowolny sposób wygodny dla aplikacji. W tym przykładzie jest używana jedna właściwość najwyższego poziomu o nazwie **fanOn**, która grupuje pozostałe właściwości w postaci oddzielnych elementów **components**. Poniższy fragment kodu JSON przedstawia strukturę żądanych właściwości, których używa ten samouczek. Plik JSON znajduje się w desired.jspliku.

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>Tworzenie procedur obsługi

Procedury obsługi można tworzyć w przypadku aktualizacji żądanych właściwości, które odpowiadają na aktualizacje na różnych poziomach hierarchii JSON. Na przykład ta procedura obsługi widzi wszystkie zmiany żądanych właściwości wysyłane do urządzenia z poziomu aplikacji zaplecza. Zmienna **delta** zawiera żądane właściwości wysyłane z zaplecza rozwiązania:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

Poniższa procedura obsługi reaguje tylko na zmiany wprowadzone w żądanej właściwości **fanOn**:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Procedury obsługi dla wielu właściwości

W przedstawionym wcześniej przykładzie notacji JSON żądanych właściwości węzeł **climate** w obszarze **components** zawiera dwie właściwości: **minTemperature** i **maxTemperature**.

Lokalny obiekt **twin** urządzenia przechowuje kompletny zestaw właściwości żądanych i zgłaszanych. Zmienna **delta** wysłana z zaplecza może zaktualizować tylko podzestaw żądanych właściwości. W poniższym fragmencie kodu, gdy urządzenie symulowane odbiera aktualizację tylko z jednej wartości: **minTemperature** lub **maxTemperature**, używa wartości w lokalnym obiekcie twin dla innej wartości w celu skonfigurowania urządzenia:

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

Lokalny obiekt **twin** przechowuje kompletny zestaw właściwości żądanych i zgłaszanych. Zmienna **delta** wysłana z zaplecza może zaktualizować tylko podzestaw żądanych właściwości.

### <a name="handle-insert-update-and-delete-operations"></a>Obsługa operacji wstawiania, aktualizowania i usuwania

Żądane właściwości wysyłane z zaplecza nie wskazują, jaka operacja jest wykonywana na określonej żądanej właściwości. Operacja wykonywana przez kod musi pochodzić z bieżącego zestawu żądanych właściwości przechowywanych lokalnie i zmian wysyłanych z centrum.

Poniższy fragment kodu pokazuje, jak urządzenie symulowane obsługuje operacje wstawiania, aktualizowania i usuwania na liście elementów **components** w żądanych właściwościach. Możesz sprawdzić, jak używać wartości **null**, aby wskazać, że składnik powinien zostać usunięty:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Wysyłanie żądanych właściwości do urządzenia z poziomu zaplecza

Wiadomo już, jak urządzenie implementuje procedury obsługi w celu odbierania aktualizacji żądanej właściwości. W tej sekcji przedstawiono sposób wysyłania zmian żądanej właściwości do urządzenia z poziomu aplikacji zaplecza.

Aby zobaczyć przykładowy kod urządzenia symulowanego, który umożliwia odbieranie żądanych właściwości, przejdź do folderu **iot-hub/Tutorials/DeviceTwins** pobranego przykładowego projektu platformy Node.js. Następnie otwórz plik ServiceClient.js w edytorze tekstów.

Poniższy fragment kodu przedstawia sposób łączenia się z rejestrem tożsamości urządzeń i uzyskiwania dostępu do bliźniaczej reprezentacji określonego urządzenia:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

Poniższy fragment kodu przedstawia różne *poprawki* żądanej właściwości, które aplikacja zaplecza wysyła do urządzenia:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

Poniższy fragment kodu przedstawia sposób wysyłania aktualizacji żądanej właściwości z aplikacji zaplecza do urządzenia:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>Uruchamianie aplikacji

W tej sekcji uruchomisz dwie przykładowe aplikacje, aby zaobserwować, jak aplikacja zaplecza wysyła aktualizacje żądanej właściwości do aplikacji urządzenia symulowanego.

Aby uruchomić urządzenie symulowane i aplikacje zaplecza, potrzebujesz parametrów połączenia usługi i urządzenia. Parametry połączenia zostały zapisane podczas tworzenia zasobu na początku tego samouczka.

Aby uruchomić aplikację urządzenia symulowanego, otwórz powłokę lub okno wiersza polecenia i przejdź do folderu **iot-hub/Tutorials/DeviceTwins** w pobranym projekcie platformy Node.js. Następnie uruchom następujące polecenia:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Aby uruchomić aplikację zaplecza, otwórz inną powłokę lub okno wiersza polecenia. Następnie przejdź do folderu **iot-hub/Tutorials/DeviceTwins** w pobranym projekcie platformy Node.js. Następnie uruchom następujące polecenia:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji urządzenia symulowanego i prezentuje sposób obsługi aktualizacji żądanej właściwości **maxTemperature**. Możesz zapoznać się ze sposobem uruchamiania procedury obsługi najwyższego poziomu oraz procedur obsługi składnika climate:

![Zrzut ekranu pokazujący, jak działa program obsługi najwyższego poziomu i programy obsługi składników klimatycznych.](./media/tutorial-device-twins/SimulatedDevice1.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji zaplecza i prezentuje sposób wysłania aktualizacji do żądanej właściwości **maxTemperature**:

![Zrzut ekranu pokazujący dane wyjściowe aplikacji zaplecza oraz informacje o sposobie wysyłania aktualizacji.](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information&quot;></a>Odbieranie informacji o stanie

Aplikacja zaplecza otrzymuje informacje o stanie z urządzenia jako zgłaszane właściwości. Urządzenie ustawia zgłaszane właściwości, a następnie wysyła je do centrum. Aplikacja zaplecza może odczytywać bieżące wartości zgłaszanych właściwości bliźniaczej reprezentacji urządzenia przechowywanej w centrum.

### <a name=&quot;send-reported-properties-from-a-device&quot;></a>Wysyłanie zgłaszanych właściwości z urządzenia

Aktualizacje można wysłać do wartości zgłaszanych właściwości jako poprawki. Poniższy fragment kodu przedstawia szablon poprawki wysyłanej przez urządzenie symulowane. Urządzenie symulowane aktualizuje pola poprawki przed wysłaniem jej do centrum:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 &quot;Reported properties patches")]

Urządzenie symulowane używa następującej funkcji do wysyłania poprawki, która zawiera zgłaszane właściwości centrum:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Przetwarzanie zgłaszanych właściwości

Aplikacja zaplecza uzyskuje dostęp do bieżących wartości zgłaszanej właściwości urządzenia za pośrednictwem bliźniaczej reprezentacji urządzenia. Poniższy fragment kodu pokazuje, jak aplikacja zaplecza odczytuje wartości zgłaszanych właściwości urządzenia symulowanego:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>Uruchamianie aplikacji

W tej sekcji uruchomisz dwie przykładowe aplikacje, aby zaobserwować, jak aplikacja urządzenia symulowanego wysyła aktualizacje zgłaszanej właściwości do aplikacji zaplecza.

Będą to te same dwie aplikacje uruchomione wcześniej w celu sprawdzenia, jak żądane właściwości są wysyłane do urządzenia.

Aby uruchomić urządzenie symulowane i aplikacje zaplecza, potrzebujesz parametrów połączenia usługi i urządzenia. Parametry połączenia zostały zapisane podczas tworzenia zasobu na początku tego samouczka.

Aby uruchomić aplikację urządzenia symulowanego, otwórz powłokę lub okno wiersza polecenia i przejdź do folderu **iot-hub/Tutorials/DeviceTwins** w pobranym projekcie platformy Node.js. Następnie uruchom następujące polecenia:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Aby uruchomić aplikację zaplecza, otwórz inną powłokę lub okno wiersza polecenia. Następnie przejdź do folderu **iot-hub/Tutorials/DeviceTwins** w pobranym projekcie platformy Node.js. Następnie uruchom następujące polecenia:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji urządzenia symulowanego i prezentuje sposób wysyłania aktualizacji zgłaszanej właściwości do centrum:

![Symulowane urządzenie](./media/tutorial-device-twins/SimulatedDevice2.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji zaplecza i wyróżnia informacje o sposobie odbierania i przetwarzania zgłoszonej aktualizacji właściwości z urządzenia:

![Aplikacja zaplecza](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz ukończyć następny samouczek, pozostaw grupę zasobów i centrum IoT Hub, aby użyć ich później.

Jeśli nie potrzebujesz już tego centrum IoT, usuń je oraz grupę zasobów z poziomu portalu. Aby to zrobić, wybierz grupę zasobów **tutorial-iot-hub-rg** zawierającą centrum IoT Hub, a następnie kliknij przycisk **Usuń**.

Alternatywnie możesz użyć interfejsu wiersza polecenia:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób synchronizacji informacji o stanie między urządzeniami i centrum IoT Hub. Przejdź do następnego samouczka, aby dowiedzieć się, jak używać bliźniaczych reprezentacji urządzeń do implementowania procesu aktualizacji oprogramowania układowego.

> [!div class="nextstepaction"]
> [Wdrażanie procesu aktualizacji oprogramowania układowego urządzenia](tutorial-firmware-update.md)
