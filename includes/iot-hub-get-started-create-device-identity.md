---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: b1a863498603006e37ab98b838ffd426b962d788
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755426"
---
W tej sekcji utworzysz tożsamość urządzenia dla tego artykułu przy użyciu interfejsu wiersza polecenia platformy Azure. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

1. Otwórz usługę [Azure Cloud Shell](https://shell.azure.com/).

1. W Azure Cloud Shell Uruchom następujące polecenie, aby zainstalować rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Utwórz nową tożsamość urządzenia o nazwie `myDeviceId` i Pobierz parametry połączenia urządzenia z następującymi poleceniami:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Zanotuj parametry połączenia urządzenia z wyniku. Ten ciąg połączenia urządzenia jest używany przez aplikację urządzenia do nawiązywania połączenia z IoT Hub jako urządzenie.

<!-- images and links -->
