---
title: Jak przenieść urządzenie do usługi Azure IoT Central z IoT Hub
description: Jak przenieść urządzenie do usługi Azure IoT Central z IoT Hub
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 543c3f1c72857098540cc2a77e8a0093b907b799
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210839"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Jak przenieść urządzenie do usługi Azure IoT Central z IoT Hub

*Ten artykuł ma zastosowanie do operatorów i deweloperów urządzeń.*  

W tym artykule opisano sposób transferu urządzenia do aplikacji IoT Central platformy Azure z IoT Hub. 

Urządzenie najpierw łączy się z punktem końcowym DPS, aby pobrać informacje potrzebne do nawiązania połączenia z aplikacją. Wewnętrznie aplikacja IoT Central używa Centrum IoT do obsługi łączności urządzeń.  

Urządzenie może być połączone z usługą IoT Hub bezpośrednio przy użyciu parametrów połączenia lub usługi DPS. [Usługa Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) jest trasą dla IoT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Aby przenieść urządzenie do usługi Azure IoT Central

Aby podłączyć urządzenie do IoT Central z IoT Hub należy zaktualizować urządzenie przy użyciu:

* [Identyfikator zakresu](../../iot-dps/concepts-service.md) aplikacji IoT Central.
* Klucz pochodzący z klucza [SAS grupy](concepts-get-connected.md) lub [certyfikatu X. 509](../../iot-hub/iot-hub-x509ca-overview.md)

Aby można było korzystać z IoT Central, musi istnieć szablon urządzenia, który modeluje właściwości/dane telemetryczne/polecenia implementowane przez urządzenie. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie połączenia z usługą IoT Central](concepts-get-connected.md) i [co to są szablony urządzeń?](concepts-device-templates.md)

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, Oto kilka sugerowanych następnych kroków:

- Zapoznaj się z przykładowym kodem, który pokazuje, jak używać tokenów SAS w [samouczku: Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central](tutorial-connect-device.md)
- Dowiedz się, jak [łączyć urządzenia z certyfikatami X. 509 za pomocą zestawu SDK urządzeń Node.js dla IoT Central aplikacji](how-to-connect-devices-x509.md)
- Dowiedz się, jak [monitorować łączność z urządzeniem przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
- Dowiedz się, jak [zdefiniować nowy typ urządzenia IoT w aplikacji usługi Azure IoT Central](./howto-set-up-template.md)
- Przeczytaj informacje o [Azure IoT Edge urządzeniach i platformie Azure IoT Central](./concepts-iot-edge.md)