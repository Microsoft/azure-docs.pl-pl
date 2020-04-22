---
title: Typowe interfejsy - IoT Plug and Play Preview | Dokumenty firmy Microsoft
description: Opis wspólnych interfejsów dla deweloperów IoT Plug and Play
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770486"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT Plug and Play Preview — wspólne interfejsy

Oczekuje się, że wszystkie urządzenia IoT Plug and Play zaimplementują niektóre typowe interfejsy. Typowe interfejsy korzystają z rozwiązań IoT, ponieważ zapewniają spójne funkcje. [Certyfikacja](tutorial-build-device-certification.md) wymaga, aby urządzenie zaimplementować kilka wspólnych interfejsów. Typowe definicje interfejsu można pobrać z repozytorium modelu publicznego.

## <a name="summary-of-common-interfaces"></a>Podsumowanie wspólnych interfejsów

| Nazwa | ID | Opis | Zaimplementowano przez zestaw SDK usługi Azure IoT | Musi być zadeklarowany w modelu zdolności |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informacje o modelu | urn:azureiot:ModelWykrywanie:ModelInformacja:1 | Dla urządzeń, aby zadeklarować identyfikator modelu możliwości i interfejsów. Wymagane dla wszystkich urządzeń IoT Plug and Play. | Tak | Nie |
| Informacje o sdku cyfrowego klienta bliźniaczego | urn:azureiot:Klient:SDKInformacja:1 | Zestaw SDK klienta do łączenia urządzenia z platformą Azure. Wymagane do [certyfikacji](tutorial-build-device-certification.md) | Tak | Nie |
| Informacje o urządzeniu | urn:azureiot:DeviceManagement:DeviceInformation:1 | Informacje o sprzęcie i systemie operacyjnym o urządzeniu. Wymagane do [certyfikacji](tutorial-build-device-certification.md) | Nie | Tak |
| Definicja modelu | urn:azureiot:ModelDiscovery:ModelDefinition:1 | Dla urządzeń, aby zadeklarować pełną definicję jego modelu możliwości i interfejsów. Należy zaimplementować, gdy definicje modelu nie są hostowane w repozytorium modelu. | Nie | Tak |
| Cyfrowa bliźniacza reprezentacja | urn:azureiot:ModelDiscovery:DigitalTwin:1 | Dla deweloperów rozwiązań, aby pobrać identyfikator modelu możliwości i identyfikatory interfejsu dla cyfrowego bliźniaczego. Ten interfejs nie jest zadeklarowany ani zaimplementowany przez urządzenie Typu Plug and Play IoT. | Nie | Nie |

- Implementowane przez zestaw SDK usługi Azure IoT — czy zestaw SDK usługi Azure IoT implementuje możliwości zadeklarowane w interfejsach. Urządzenia typu Plug and Play usługi IoT korzystające z zestawu Azure IoT SDK nie muszą implementować tego interfejsu.
- Musi być zadeklarowany w modelu możliwości — jeśli "tak", `"implements":` ten interfejs musi być zadeklarowany w sekcji modelu możliwości urządzenia dla tego urządzenia IoT Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Pobieranie definicji interfejsu z publicznego repozytorium

### <a name="cli"></a>Interfejs wiersza polecenia

Rozszerzenia IoT platformy Azure dla platformy Azure CLI można użyć do pobierania wspólnych interfejsów z repozytorium modelu publicznego.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. Użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń.

1. Wprowadź **pozycję Plug and Play,** a następnie wybierz polecenie **IoT Plug and Play: Otwórz repozytorium modelu.** Wybierz **pozycję Repozytorium publiczne**. Publiczne repozytorium modelu otwiera się w programie VS Code.

1. W repozytorium modelu publicznego wprowadź nazwę interfejsu w polu wyszukiwania.

1. Aby utworzyć lokalną kopię interfejsu, zaznacz ją w wynikach wyszukiwania, a następnie wybierz pozycję **Pobierz**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o wspólnych interfejsach, oto kilka dodatkowych zasobów:

- [Cyfrowy język podwójnej definicji (DTDL)](https://aka.ms/DTDL)
- [C (zestaw SDK urządzenia)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
