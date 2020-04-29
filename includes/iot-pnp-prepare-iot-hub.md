---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234264"
---
## <a name="prepare-an-iot-hub"></a>Przygotowywanie Centrum IoT Hub

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz również usługi Azure IoT Hub w ramach subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . Jeśli nie masz Centrum IoT Hub, postępuj zgodnie [z tymi instrukcjami, aby je utworzyć](../articles/iot-hub/iot-hub-create-using-cli.md).

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, najpierw Zaloguj się do subskrypcji platformy Azure za `az login`pomocą programu. Jeśli uruchamiasz te polecenia w Azure Cloud Shell, nastąpi automatyczne logowanie.

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, `az` wersja powinna być **2.0.73** lub nowsza; Azure Cloud Shell używa najnowszej wersji. Użyj `az --version` polecenia, aby sprawdzić wersję zainstalowaną na komputerze.

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia:

```azurecli-interactive
az extension add --name azure-iot
```

Uruchom następujące polecenie, aby utworzyć tożsamość urządzenia w centrum IoT Hub. Zastąp symbole zastępcze **YourIoTHubName** i **YourDeviceID** własną _nazwą IoT Hub_ i wybranym przez siebie _identyfikatorem urządzenia_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Uruchom następujące polecenie, aby pobrać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia (Uwaga do użycia później):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
