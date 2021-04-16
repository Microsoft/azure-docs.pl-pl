---
title: Zarządzanie urządzeniami Azure IoT przy użyciu rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Użyj rozszerzenia IoT dla narzędzia interfejsu wiersza polecenia platformy Azure Azure IoT Hub zarządzania urządzeniami, w tym metod bezpośrednich i opcji zarządzania żądanymi właściwościami bliźniaczej reprezentacji.
author: chrissie926
manager: ''
keywords: azure iot device management, azure iot hub device management, device management iot, iot hub device management
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: a5bc7e195efd62f430fdf2aa0cb606dbcff79528
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567200"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Używanie rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure Azure IoT Hub zarządzania urządzeniami

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

Z tego artykułu dowiesz się, jak używać rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure z różnymi opcjami zarządzania na komputerze dewelopera. [Rozszerzenie IoT dla interfejsu wiersza polecenia](https://github.com/Azure/azure-iot-cli-extension) platformy Azure to rozszerzenie IoT typu open source, które zwiększa możliwości interfejsu wiersza [polecenia platformy Azure.](/cli/azure/overview) Interfejs wiersza polecenia platformy Azure zawiera polecenia służące do interakcji Azure Resource Manager punktami końcowymi zarządzania. Możesz na przykład użyć interfejsu wiersza polecenia platformy Azure, aby utworzyć maszynę wirtualną platformy Azure lub centrum IoT. Rozszerzenie interfejsu wiersza polecenia umożliwia usłudze platformy Azure rozszerzenie interfejsu wiersza polecenia platformy Azure, zapewniając dostęp do dodatkowych funkcji specyficznych dla usługi. Rozszerzenie IoT zapewnia deweloperom IoT dostęp do wiersza polecenia do wszystkich IoT Hub, IoT Edge i IoT Hub Device Provisioning Service możliwości.

| Opcja zarządzania          | Zadanie  |
|----------------------------|-----------|
| Metody bezpośrednie             | Nakłoń urządzenie do działania, takiego jak uruchamianie lub zatrzymywanie wysyłania komunikatów lub ponowne uruchamianie urządzenia.                                        |
| Żądane właściwości bliźniaczej reprezentacji    | Umieść urządzenie w pewnych stanach, takich jak ustawienie diody LED na zielono lub ustawienie interwału wysyłania danych telemetrycznych na 30 minut.         |
| Zgłoszone właściwości bliźniaczej reprezentacji   | Pobierz raportny stan urządzenia. Na przykład urządzenie zgłasza, że dioda LED miga teraz.                                    |
| Tagi bliźniaczych reprezentacji                  | Przechowywanie metadanych specyficznych dla urządzenia w chmurze. Na przykład lokalizacja wdrożenia automatu do sprzedaży.                         |
| Zapytania bliźniaczej reprezentacji urządzenia        | Zapytanie dotyczące wszystkich bliźniaczych reprezentacji urządzeń w celu pobrania tych bliźniaczych reprezentacji z dowolnymi warunkami, takimi jak zidentyfikowanie urządzeń, które są dostępne do użycia. |

Aby uzyskać bardziej szczegółowe wyjaśnienie różnic i wskazówek dotyczących korzystania z tych opcji, zobacz [Wskazówki](iot-hub-devguide-d2c-guidance.md) dotyczące komunikacji między urządzeniami a chmurą i Wskazówki dotyczące komunikacji między [chmurą a urządzeniem.](iot-hub-devguide-c2d-guidance.md)

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). IoT Hub utrzymuje bliźniacze reprezentacji urządzenia dla każdego urządzenia, które się z nim łączy. Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń, zobacz [Wprowadzenie do bliźniaczych reprezentacji urządzeń.](iot-hub-node-node-twin-getstarted.md)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [samouczek dotyczący symulatora online](iot-hub-raspberry-pi-web-simulator-get-started.md) urządzenia Raspberry Pi lub jeden z samouczków urządzenia. Na przykład możesz przejść do urządzenia [Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md) z node.jslub do jednego z przewodników Szybki start Wysyłanie telemetrii. [](quickstart-send-telemetry-dotnet.md) Te artykuły obejmują następujące wymagania:

  * Aktywna subskrypcja platformy Azure.
  * Centrum Azure IoT Hub w ramach Twojej subskrypcji.
  * Aplikacja kliency, która wysyła komunikaty do centrum Azure IoT Hub.

* Upewnij się, że urządzenie jest uruchomione z aplikacją kliencyjną w trakcie tego samouczka.

* [Python 2.7x lub Python 3.x](https://www.python.org/downloads/)

* Interfejs wiersza polecenia platformy Azure. Jeśli musisz go zainstalować, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Interfejs wiersza polecenia platformy Azure musi mieć co najmniej wersję 2.0.70 lub nowszą. Użyj polecenia `az –version` w celu przeprowadzenia weryfikacji.

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

## <a name="device-twin-desired-properties"></a>Żądane właściwości bliźniaczej reprezentacji urządzenia

Ustaw żądany interwał właściwości = 3000, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Tę właściwość można odczytać z urządzenia.

## <a name="device-twin-reported-properties"></a>Zgłoszone właściwości bliźniaczej reprezentacji urządzenia

Pobierz zgłoszone właściwości urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Jedna z zgłoszonych właściwości bliźniaczej reprezentacji to $metadata.$lastUpdated, która pokazuje czas ostatniej aktualizacji zestawu zgłaszanych właściwości przez aplikację urządzenia.

## <a name="device-twin-tags"></a>Tagi bliźniaczej reprezentacji urządzenia

Wyświetl tagi i właściwości urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Dodaj rolę pola = temperature&do urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Zapytania bliźniaczych reprezentacji urządzeń

Zapytania dotyczące urządzeń z tagiem roli = "temperature&humidity" (temperatura&wilgotności), uruchamiając następujące polecenie:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Aby odpytywać wszystkie urządzenia z wyjątkiem urządzeń z tagiem roli = "temperature&humidity" (temperatura&wilgotności), uruchamiając następujące polecenie:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak monitorować komunikaty z urządzenia do chmury i wysyłać komunikaty z chmury do urządzenia między urządzeniem IoT a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]