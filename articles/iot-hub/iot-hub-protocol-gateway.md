---
title: Brama protokołu IoT Azure | Microsoft Docs
description: Jak za pomocą bramy protokołu Azure IoT zwiększyć IoT Hub możliwości i obsługę protokołów, aby umożliwić urządzeniom łączenie się z koncentratorem za pomocą protokołów nieobsługiwanych przez IoT Hub natywnie.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 315d76715973b79a971f3ca15bcc5186d20135a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87325036"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Obsługa dodatkowych protokołów dla IoT Hub

Platforma Azure IoT Hub natywnie obsługuje komunikację za pośrednictwem protokołów MQTT, AMQP i HTTPS. W niektórych przypadkach urządzenia lub bramy pól mogą nie być w stanie używać jednego z tych standardowych protokołów i wymagają adaptacji protokołów. W takich przypadkach można użyć bramy niestandardowej. Brama niestandardowa umożliwia adaptację protokołu dla punktów końcowych IoT Hub przez mostkowanie ruchu do i z IoT Hub. Możesz użyć [bramy protokołu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) jako bramy niestandardowej, aby włączyć adaptację protokołu dla IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Brama protokołu IoT Azure

Brama protokołu IoT Azure to platforma służąca do adaptacji protokołów, która została zaprojektowana w celu zapewnienia dużej skali komunikacji między urządzeniami dwukierunkowymi i IoT Hub. Brama protokołu jest składnikiem przekazującym, który akceptuje połączenia urządzeń przez określony protokół. Mostkuje ruch do IoT Hub ponad AMQP 1,0.

Bramę protokołu można wdrożyć na platformie Azure w wysoce skalowalny sposób przy użyciu usługi Azure Service Fabric, roli procesu roboczego platformy Azure Cloud Services lub Windows Virtual Machines. Ponadto bramy protokołu można wdrożyć w środowiskach lokalnych, takich jak bramy pól.

Brama protokołu usługi Azure IoT zawiera adapter protokołu MQTT, który umożliwia dostosowanie zachowania protokołu MQTT w razie potrzeby. Ponieważ IoT Hub zapewnia wbudowaną obsługę protokołu MQTT v 3.1.1, należy rozważyć użycie karty protokołu MQTT, jeśli wymagane są dostosowania protokołów lub określone wymagania dotyczące dodatkowych funkcji.

Karta MQTT pokazuje również model programowania służący do tworzenia kart protokołów dla innych protokołów. Ponadto model programowania bramy usługi Azure IoT umożliwia podłączenie niestandardowych składników do wyspecjalizowanego przetwarzania, takiego jak uwierzytelnianie niestandardowe, przekształcenia komunikatów, kompresja/dekompresja lub Szyfrowanie/odszyfrowywanie ruchu między urządzeniami i IoT Hub.

W celu zapewnienia elastyczności Brama protokołu Azure IoT i implementacja MQTT są udostępniane w projekcie oprogramowania open source. Możesz użyć projektu open source, aby dodać obsługę różnych protokołów i wersji protokołów, lub dostosować implementację do scenariusza. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o bramie protokołu Azure IoT i sposobie jej używania i wdrażania w ramach rozwiązania IoT, zobacz:

* [Repozytorium bramy protokołu Azure IoT w witrynie GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Przewodnik dewelopera bramy protokołu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Aby dowiedzieć się więcej o planowaniu wdrożenia IoT Hub, zobacz:

* [Porównaj z Event Hubs](iot-hub-compare-event-hubs.md)

* [Skalowanie, wysoka dostępność i odzyskiwanie po awarii](iot-hub-scaling.md)

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)