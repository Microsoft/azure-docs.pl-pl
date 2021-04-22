---
title: Jak przenieść urządzenie do Azure IoT Central z IoT Hub
description: Jak przenieść urządzenie do Azure IoT Central z IoT Hub
author: philmea
ms.author: philmea
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 79aead5b374714e7856897a9b85349198341cb3d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872735"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Jak przenieść urządzenie do Azure IoT Central z IoT Hub

*Ten artykuł dotyczy operatorów i deweloperów urządzeń.*  

W tym artykule opisano sposób przenoszenia urządzenia do aplikacji Azure IoT Central z IoT Hub. 

Urządzenie najpierw łączy się z punktem końcowym usługi DPS w celu pobrania informacji, które są potrzebne do nawiązania połączenia z aplikacją. Wewnętrznie aplikacja IoT Central używa centrum IoT do obsługi łączności urządzeń.  

Urządzenie można połączyć z centrum IoT bezpośrednio przy użyciu parametrów połączenia lub usługi DPS. [Azure IoT Hub usługa Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) jest trasą dla IoT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Aby przenieść urządzenie do Azure IoT Central

Aby połączyć urządzenie z IoT Central z IoT Hub urządzenie musi zostać zaktualizowane:

* Identyfikator [zakresu](../../iot-dps/concepts-service.md) aplikacji IoT Central.
* Klucz pochodzący z klucza [sygnatury](concepts-get-connected.md) dostępu współdzielonego grupy lub [certyfikatu X.509](../../iot-hub/iot-hub-x509ca-overview.md)

Aby korzystać z IoT Central, musi być dostępny szablon urządzenia, który modeluje właściwości/dane telemetryczne/polecenia implementne przez urządzenie. Aby uzyskać więcej informacji, zobacz [Get connected to IoT Central](concepts-get-connected.md) (Uzyskiwanie połączenia z [IoT Central) i What are device templates? (Co to są szablony urządzeń?)](concepts-device-templates.md)

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzeń, niektóre sugerowane następne kroki to:

- Zapoznaj się z przykładowym kodem, który pokazuje, jak używać tokenów SAS w samouczku: tworzenie aplikacji klienckiej i łączenie jej z [aplikacją Azure IoT Central aplikacji](tutorial-connect-device.md)
- Dowiedz się, [jak połączyć urządzenia z certyfikatami X.509](how-to-connect-devices-x509.md) przy użyciu zestawu SDK Node.js dla aplikacji IoT Central
- Dowiedz się, jak [monitorować łączność urządzeń przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
- Dowiedz się, jak zdefiniować nowy typ urządzenia [IoT w Azure IoT Central aplikacji](./howto-set-up-template.md)
- Przeczytaj o [Azure IoT Edge i Azure IoT Central](./concepts-iot-edge.md)