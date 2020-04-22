---
title: Brama protokołu IoT platformy Azure | Dokumenty firmy Microsoft
description: Jak używać bramy protokołu IoT platformy Azure, aby rozszerzyć możliwości usługi IoT Hub i obsługę protokołu, aby umożliwić urządzeniom łączenie się z koncentratorem przy użyciu protokołów nieobjętych natywnie obsługiwanymi przez centrum IoT Hub.
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
ms.openlocfilehash: bfd000e2b69f052e25f0ea6cd286b0ca3aef7519
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759712"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Obsługa dodatkowych protokołów dla Usługi IoT Hub

Usługa Azure IoT Hub natywnie obsługuje komunikację za pośrednictwem protokołów MQTT, AMQP i HTTPS. W niektórych przypadkach urządzenia lub bramy terenowe mogą nie być w stanie użyć jednego z tych standardowych protokołów i wymagać dostosowania protokołu. W takich przypadkach można użyć bramy niestandardowej. Brama niestandardowa umożliwia dostosowanie protokołu dla punktów końcowych usługi IoT Hub przez mostkowanie ruchu do i z usługi IoT Hub. Bramy protokołu [IoT platformy Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) można użyć jako bramy niestandardowej, aby włączyć dostosowanie protokołu dla Usługi IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Brama protokołu IoT platformy Azure

Brama protokołu Azure IoT to struktura adaptacji protokołu, która jest przeznaczona do komunikacji na dużą skalę, dwukierunkowe urządzenia z Centrum IoT Hub. Brama protokołu jest składnikiem przekazu, który akceptuje połączenia urządzeń za pośrednictwem określonego protokołu. Mosty ruchu do Usługi IoT Hub zaamotki AMQP 1.0.

Bramę protokołu można wdrożyć na platformie Azure w wysoce skalowalny sposób przy użyciu usługi Azure Service Fabric, ról procesowych usług w chmurze azure lub maszyn wirtualnych systemu Windows. Ponadto bramę protokołu można wdrożyć w środowiskach lokalnych, takich jak bramy w terenie.

Brama protokołu IoT platformy Azure zawiera kartę protokołu MQTT, która umożliwia dostosowanie zachowania protokołu MQTT w razie potrzeby. Ponieważ Centrum IoT zapewnia wbudowaną obsługę protokołu MQTT v3.1.1, należy rozważyć użycie karty protokołu MQTT tylko wtedy, gdy wymagane są dostosowania protokołu lub określone wymagania dotyczące dodatkowych funkcji.

Karta MQTT demonstruje również model programowania dla tworzenia kart protokołu dla innych protokołów. Ponadto model programowania bramy bramy protokołu Azure IoT umożliwia podłączenie niestandardowych składników do specjalistycznego przetwarzania, takich jak uwierzytelnianie niestandardowe, przekształcenia wiadomości, kompresja/dekompresja lub szyfrowanie/odszyfrowywanie ruchu między urządzeniami a centrum IoT Hub.

Aby zapewnić elastyczność, brama protokołu Azure IoT i implementacja MQTT są dostępne w projekcie oprogramowania typu open source. Można użyć projektu open source, aby dodać obsługę różnych protokołów i wersji protokołu lub dostosować implementację dla scenariusza. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o bramie protokołu Azure IoT oraz o tym, jak używać jej i wdrażać ją w ramach rozwiązania IoT, zobacz:

* [Repozytorium bramy protokołu IoT platformy Azure w usłudze GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Przewodnik dla deweloperów bramy protokołu IoT platformy Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Aby dowiedzieć się więcej o planowaniu wdrożenia usługi IoT Hub, zobacz:

* [Porównaj z Centrami zdarzeń](iot-hub-compare-event-hubs.md)

* [Skalowanie, wysoka dostępność i odzyskiwanie po awarii](iot-hub-scaling.md)

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)