---
title: Zarządzanie urządzeniami Azure IoT za pomocą rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Użyj rozszerzenia IoT dla narzędzia interfejsu wiersza polecenia platformy Azure dla usługi Azure IoT Hub zarządzanie urządzeniami, korzystając z metod bezpośrednich i opcji zarządzania żądanymi właściwościami sznurka.
author: chrissie926
manager: ''
keywords: Zarządzanie urządzeniami Azure IoT, zarządzanie urządzeniami w usłudze Azure IoT Hub, zarządzanie urządzeniami IoT, zarządzanie urządzeniami w usłudze IoT Hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 499c0c065cb38313c98ed9412ab1f08d70dbc654
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199530"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Korzystanie z rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure dla usługi Azure IoT Hub zarządzanie urządzeniami

![Diagram kompleksowy](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) to rozszerzenie IoT typu open source, które dodaje możliwości [interfejsu wiersza polecenia platformy Azure](/cli/azure/overview). Interfejs wiersza polecenia platformy Azure zawiera poleceń służących do współpracy z punktami końcowymi Azure Resource Manager i zarządzania. Na przykład możesz użyć interfejsu wiersza polecenia platformy Azure, aby utworzyć maszynę wirtualną platformy Azure lub Centrum IoT. Rozszerzenie interfejsu wiersza polecenia umożliwia usłudze platformy Azure rozszerzanie interfejsu wiersza polecenia platformy Azure zapewniającego dostęp do dodatkowych funkcji specyficznych dla usługi. Rozszerzenie IoT oferuje deweloperom programu IoT dostęp do wszystkich IoT Hub, IoT Edge i IoT Hub Device Provisioning Service funkcji.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie  |
|----------------------------|-----------|
| Metody bezpośrednie             | Nadawanie urządzeniu działania takie jak uruchamianie lub zatrzymywanie wysyłania komunikatów lub ponowne uruchamianie urządzenia.                                        |
| Właściwości dotyczące sznurka    | Umieść urządzenie w określonych stanach, takich jak ustawienie diody LED na zieloną lub ustawienie interwału wysyłania danych telemetrycznych na 30 minut.         |
| Właściwości zgłoszone przez sznurek   | Pobierz zgłoszony stan urządzenia. Na przykład urządzenie raportuje diodę LED teraz.                                    |
| Znaczniki bliźniaczych                  | Przechowuj metadane specyficzne dla urządzenia w chmurze. Na przykład lokalizacja wdrożenia maszyny sprzedaży.                         |
| Zapytania dotyczące sznurka urządzenia        | Wykonaj zapytanie dotyczące wszystkich bliźniaczych reprezentacji urządzeń, aby pobrać te bliźniaczych reprezentacji z dowolnym warunkiem, na przykład identyfikując urządzenia, które są dostępne do użycia. |

Aby uzyskać bardziej szczegółowe wyjaśnienie różnic i wskazówek dotyczących korzystania z tych opcji, zobacz [wskazówki dotyczące komunikacji między urządzeniami i chmurą](iot-hub-devguide-d2c-guidance.md) oraz [wskazówki dotyczące komunikacji między chmurą i urządzeniem](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). IoT Hub utrzymuje sznurki urządzenia dla każdego urządzenia, które nawiązuje z nim połączenie. Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń, zobacz [wprowadzenie do usługi Device bliźniaczych reprezentacji](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak używać rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co robisz

Uruchom interfejs wiersza polecenia platformy Azure i rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Potrzebne elementy

* Ukończ samouczek [gry online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jedno z samouczków dotyczących urządzeń; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Te elementy obejmują następujące wymagania:

  - Aktywna subskrypcja platformy Azure.
  - Usługa Azure IoT Hub w ramach Twojej subskrypcji.
  - Aplikacja kliencka, która wysyła komunikaty do usługi Azure IoT Hub.

* Upewnij się, że urządzenie jest uruchomione w aplikacji klienckiej w ramach tego samouczka.

* [Python 2.7 x lub Python 3. x](https://www.python.org/downloads/)

* Interfejs wiersza polecenia platformy Azure. Jeśli musisz ją zainstalować, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az –version` w celu przeprowadzenia weryfikacji.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Zainstaluj rozszerzenie IoT. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-iot`. [Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) opisuje kilka sposobów instalowania rozszerzenia.

## <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Zaloguj się do konta platformy Azure, uruchamiając następujące polecenie:

```azurecli
az login
```

## <a name="direct-methods"></a>Metody bezpośrednie

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Wymagane właściwości sznurka urządzenia

Ustaw żądany interwał właściwości = 3000, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Ta właściwość może zostać odczytana z urządzenia.

## <a name="device-twin-reported-properties"></a>Właściwości zgłoszone przez urządzenie

Aby uzyskać raportowane właściwości urządzenia, należy uruchomić następujące polecenie:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Jedną z właściwości raportowanych przez sznurek jest $metadata. $lastUpdated, która pokazuje ostatni czas aktualizacji zgłoszonego zestawu właściwości przez aplikację urządzenia.

## <a name="device-twin-tags"></a>Znaczniki sznurka urządzenia

Wyświetl Tagi i właściwości urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Dodaj rolę pola = temperatura&wilgotność do urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Zapytania dotyczące sznurka urządzenia

Zbadaj urządzenia za pomocą tagu role = "temperatura&wilgotność", uruchamiając następujące polecenie:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Wykonaj zapytanie o wszystkie urządzenia z wyjątkiem tych, które mają tag role = "temperatura&wilgotność", uruchamiając następujące polecenie:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak monitorować komunikaty z urządzenia do chmury i wysyłać komunikaty z chmury do urządzenia między urządzeniem IoT i usługą Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]