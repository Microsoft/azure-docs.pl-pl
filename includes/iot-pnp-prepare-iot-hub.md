---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: ea5d4ef26fb14e22b871bb4bfa1054cb749d38e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673033"
---
## <a name="prepare-an-iot-hub"></a>Przygotowywanie Centrum IoT Hub

Aby wykonać kroki opisane w tym artykule, potrzebujesz usługi Azure IoT Hub w ramach subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, najpierw Zaloguj się do subskrypcji platformy Azure za pomocą programu `az login` . Jeśli uruchamiasz te polecenia w Azure Cloud Shell, nastąpi automatyczne logowanie.

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, `az` wersja powinna być **2.8.0** lub nowsza, a Azure Cloud Shell używa najnowszej wersji. Użyj `az --version` polecenia, aby sprawdzić wersję zainstalowaną na komputerze.

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia:

```azurecli-interactive
az extension add --name azure-iot
```

Jeśli nie masz jeszcze usługi IoT Hub do użycia, uruchom następujące polecenia, aby utworzyć grupę zasobów i bezpłatne centrum IoT w Twojej subskrypcji. Zamień na `<YourIoTHubName>` wybraną nazwę centrum:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

Uruchom następujące polecenie, aby utworzyć tożsamość urządzenia w centrum IoT Hub. Zastąp `<YourIoTHubName>` `<YourDeviceID>` symbole zastępcze i własnymi _nazwami IoT Hub_ i wybranym przez siebie _identyfikatorem urządzenia_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
