---
title: Rozwiązanie do zdalnego monitorowania Dodawanie urządzenia brzegowego — Azure | Microsoft Docs
description: W tym artykule opisano sposób dodawania urządzenia IoT Edge do akceleratora rozwiązania do zdalnego monitorowania
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 88d732cc3feaa2c9a528443d4a783227f3d8e641
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446793"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Dodawanie urządzenia IoT Edge do akceleratora rozwiązania do monitorowania zdalnego

Aby dodać urządzenie [IoT Edge](../iot-edge/about-iot-edge.md) do akceleratora rozwiązania, wykonaj następujące dwa kroki:

1. Dodaj urządzenie brzegowe na stronie **Device Explorer** w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego.
1. Zainstaluj środowisko uruchomieniowe IoT Edge na urządzeniu brzegowym.

## <a name="add-the-iot-edge-device"></a>Dodaj urządzenie IoT Edge

Aby dodać urządzenie usługi IoT Edge do akceleratora rozwiązania do monitorowania zdalnego, przejdź do strony **Device Explorer** w internetowym interfejsie użytkownika i kliknij pozycję **+ Nowe urządzenie**.

W panelu **nowe urządzenie** wybierz pozycję **IoT Edge urządzenie**. Dla innych właściwości możesz pozostawić ustawienia domyślne. Następnie kliknij pozycję **Zastosuj**:

![Dodawanie urządzenia usługi IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternatywne sposoby dodawania urządzenia IoT Edge

Istnieje również możliwość zarejestrowania urządzenia IoT Edge bezpośrednio z wystąpieniem IoT Hub w akceleratorze rozwiązania. Musisz znać nazwę Centrum IoT w akceleratorze rozwiązania, zanim skorzystasz z następujących przewodników:

- [Zarejestruj nowe urządzenie Azure IoT Edge z poziomu Azure Portal](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=azure-portal%2Cwindows)
- [Rejestrowanie nowego urządzenia Azure IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=azure-cli%2Cwindows)
- [Zarejestruj nowe urządzenie Azure IoT Edge z poziomu Visual Studio Code](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=visual-studio-code%2Cwindows)

Po zarejestrowaniu urządzenia bezpośrednio w usłudze IoT Hub w akceleratorze rozwiązania do monitorowania zdalnego jest on wyświetlany na stronie **Device Explorer** w interfejsie użytkownika sieci Web.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska uruchomieniowego usługi IoT Edge

Aby można było wdrażać moduły na urządzeniu brzegowym, należy zainstalować środowisko uruchomieniowe IoT Edge na rzeczywistym urządzeniu. Poniższe przewodniki przedstawiają sposób instalowania środowiska uruchomieniowego na popularnych platformach urządzeń:

- [Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Linux (x64)](../iot-edge/how-to-install-iot-edge.md)
- [Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge.md)
- [Instalowanie Azure IoT Edge środowiska uruchomieniowego w systemie Windows do użycia z kontenerami systemu Windows](../iot-edge/how-to-install-iot-edge.md)
- [Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Windows do użycia z kontenerami systemu Linux](../iot-edge/how-to-install-iot-edge.md)
- [Zainstaluj środowisko uruchomieniowe IoT Edge w systemie Windows IoT Core](../iot-edge/how-to-install-iot-edge.md)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy przygotowano urządzenie IoT Edge, następnym krokiem jest wdrożenie modułów. Zobacz [Importowanie pakietu IoT Edge do akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-import-edge-package.md)