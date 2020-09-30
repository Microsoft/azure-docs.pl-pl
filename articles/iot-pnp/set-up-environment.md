---
title: Skonfiguruj zasoby IoT potrzebne do Plug and Play IoT | Microsoft Docs
description: Utwórz IoT Hub i wystąpienie usługi Device Provisioning Service do użycia w przewodnikach Szybki Start i samouczków IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a78dce748a70c99581e047280aa9ed1ca6598558
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580043"
---
# <a name="set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Skonfiguruj swoje środowisko dla przewodników Szybki Start i samouczków Plug and Play IoT

Przed ukończeniem dowolnych przewodników Szybki Start i samouczków Plug and Play IoT należy skonfigurować Centrum IoT i usługę Device Provisioning Service (DPS) w ramach subskrypcji platformy Azure. Potrzebne będą również lokalne kopie plików modelu używanych przez przykładowe aplikacje i narzędzie Azure IoT Explorer.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Aby uniknąć konieczności instalacji interfejsu wiersza polecenia platformy Azure lokalnie, można użyć Azure Cloud Shell, aby skonfigurować usługi w chmurze.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Tworzenie zasobów

Utwórz grupę zasobów platformy Azure dla zasobów:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Utwórz centrum IoT. Następujące polecenie używa nazwy `my-pnp-hub` jako przykładu dla nazwy usługi IoT Hub do utworzenia. Wybierz unikatową nazwę Centrum IoT Hub, która ma być używana zamiast `my-pnp-hub` :

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Utwórz wystąpienie DPS. Następujące polecenie używa nazwy `my-pnp-dps` jako przykładu dla nazwy wystąpienia usługi DPS do utworzenia. Wybierz unikatową nazwę wystąpienia usługi DPS do użycia zamiast `my-pnp-dps` :

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Aby połączyć wystąpienie DPS z Centrum IoT, użyj następujących poleceń. Zamień `my-pnp-dps` i `my-pnp-hub` z unikatowymi nazwami, które zostały wcześniej wybrane:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>Pobierz ustawienia

Niektóre Przewodniki Szybki Start i samouczki korzystają z parametrów połączenia dla Centrum IoT. Podczas konfigurowania narzędzia Azure IoT Explorer potrzebne są również parametry połączenia. Pobierz parametry połączenia i zanotuj ją teraz. Zamień na `my-pnp-hub` unikatową nazwę wybraną dla Centrum IoT Hub:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

Większość przewodników Szybki Start i samouczków korzysta z *zakresu identyfikatorów* konfiguracji programu DPS. Pobierz zakres identyfikatorów i zanotuj go teraz. Zamień na `my-pnp-dps` unikatową nazwę wybraną dla wystąpienia usługi DPS:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Wszystkie Przewodniki Szybki Start i samouczki korzystają z rejestracji urządzenia w usłudze DPS. Użyj poniższego polecenia, aby utworzyć `my-pnp-device` *rejestrację poszczególnych urządzeń* w wystąpieniu DPS. Zamień na `my-pnp-dps` unikatową nazwę wybraną dla wystąpienia usługi DPS. Zanotuj wartości Identyfikator rejestracji i klucz podstawowy, które mają być używane w przewodnikach Szybki Start i samouczków:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Tworzenie zmiennych środowiskowych

Utwórz pięć zmiennych środowiskowych w celu skonfigurowania przykładów w przewodnikach Szybki Start i samouczków, aby użyć usługi Device Provisioning (DPS) do nawiązania połączenia z Centrum IoT:

* **IOTHUB_DEVICE_SECURITY_TYPE**: wartość `DPS` .
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: zakres identyfikatorów DPS został wcześniej zanotowany.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: wartość `my-pnp-device` .
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: klucz podstawowy rejestracji, dla którego wykonano wcześniej uwagi.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: wartość `global.azure-devices-provisioning.net`

Przykłady usługi potrzebują następujących zmiennych środowiskowych, aby zidentyfikować centrum i urządzenie, z którymi można nawiązać połączenie:

* **IOTHUB_CONNECTION_STRING**: parametry połączenia usługi IoT Hub, dla których wykonano wcześniej adnotację.
* **IOTHUB_DEVICE_ID**: `my-pnp-device` .
* **DEVICE_ID**: `my-pnp-device` .

Na przykład w powłoce bash systemu Linux:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
export DEVICE_ID="my-pnp-device"
```

Na przykład w wierszu polecenia systemu Windows:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
set DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>Pobierz pliki modelu

Przewodniki Szybki Start i samouczki korzystają z przykładowych plików modeli dla kontrolera temperatury i termostatu. Aby pobrać przykładowe pliki modelu:

1. Utwórz folder o nazwie *modele* na komputerze lokalnym.

1. Kliknij prawym przyciskiem myszy [TemperatureController.jsna](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) i Zapisz plik JSON w folderze *modele* .

1. Kliknij prawym przyciskiem myszy [Thermostat.jsna](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) i Zapisz plik JSON w folderze *modele* .

## <a name="install-the-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Przewodniki Szybki Start i samouczki korzystają z narzędzia **Azure IoT Explorer** . Przejdź do [wersji programu Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) i rozwiń listę zasobów dla najnowszej wersji. Pobierz i zainstaluj najnowszą wersję aplikacji dla danego systemu operacyjnego.

Przy pierwszym uruchomieniu narzędzia zostanie wyświetlony monit o podanie parametrów połączenia usługi IoT Hub. Użyj parametrów połączenia, które zostały wcześniej wykonane.

Skonfiguruj narzędzie tak, aby korzystało z pobranych wcześniej plików modeli. Na stronie głównej narzędzia wybierz pozycję **IoT Plug and Play ustawienia**, a następnie **Dodaj folder > Local**. Wybierz utworzony wcześniej folder *modele* . Następnie wybierz pozycję **Zapisz** , aby zapisać ustawienia.

Aby dowiedzieć się więcej, zobacz [Instalowanie i korzystanie z programu Azure IoT Explorer](howto-use-iot-explorer.md).

## <a name="remove-the-resources"></a>Usuwanie zasobów

Możesz użyć wystąpienia usługi IoT Hub i usługi DPS dla wszystkich przewodników Szybki Start i samouczków usługi IoT Plug and Play, więc wystarczy wykonać kroki opisane w tym artykule tylko raz. Gdy skończysz, możesz je usunąć z subskrypcji za pomocą następującego polecenia:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu środowiska, możesz wypróbować jeden z przewodników szybki start lub samouczków, takich jak:

> [!div class="nextstepaction"]
> [Łączenie przykładowej aplikacji urządzenia IoT Plug and Play do IoT Hub (Node.js)](quickstart-connect-device-node.md)
