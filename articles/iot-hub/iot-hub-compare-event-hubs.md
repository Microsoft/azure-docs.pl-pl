---
title: Porównaj usługę Azure IoT Hub z usługą Azure Event Hubs | Microsoft Docs
description: Porównanie IoT Hub i Event Hubs usługi platformy Azure podkreślają różnice funkcjonalne i przypadki użycia. Porównanie obejmuje obsługiwane protokoły, zarządzanie urządzeniami, monitorowanie i przekazywanie plików.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 56bb179c50862f09f1b789c359db97976017bbb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142767"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Łączenie urządzeń IoT z platformą Azure: IoT Hub i Event Hubs

Platforma Azure udostępnia usługi przeznaczone dla różnych typów łączności i komunikacji, które ułatwiają łączenie danych z możliwościami chmury. Zarówno IoT Hub platformy Azure, jak i Azure Event Hubs to usługi w chmurze, które mogą pozyskiwać duże ilości danych, przetwarzać lub przechowywać dane w celu uzyskania szczegółowych informacji w firmie. Te dwie usługi są podobne w przypadku, gdy obsługują pozyskiwanie danych z małymi opóźnieniami i wysoką niezawodnością, ale są przeznaczone do różnych celów. IoT Hub został opracowany z myślą o unikatowych wymaganiach dotyczących łączenia urządzeń IoT z chmurą platformy Azure, gdy Event Hubs został zaprojektowany do przesyłania strumieniowego danych Big Data. Firma Microsoft zaleca używanie IoT Hub platformy Azure do łączenia urządzeń IoT z platformą Azure

Azure IoT Hub to brama w chmurze, która łączy urządzenia IoT w celu zbierania danych i uzyskiwania informacji o firmie i automatyzacji. Ponadto IoT Hub obejmuje funkcje, które wzbogacają relacje między urządzeniami i systemami zaplecza. Możliwości komunikacji dwukierunkowej oznaczają, że podczas odbierania danych z urządzeń można także wysyłać polecenia i zasady z powrotem do urządzeń. Na przykład użyj komunikatów z chmury do urządzenia, aby zaktualizować właściwości lub wywołać akcje zarządzania urządzeniami. Komunikacja między chmurą i urządzeniem umożliwia również wysyłanie danych z chmury do urządzeń brzegowych przy użyciu Azure IoT Edge. Unikatowa tożsamość na poziomie urządzenia udostępniana przez IoT Hub pomaga lepiej zabezpieczyć swoje rozwiązanie IoT przed potencjalnymi atakami. 

[Azure Event Hubs](../event-hubs/event-hubs-about.md) to usługa przesyłania strumieniowego danych Big Data w systemie Azure. Jest ona przeznaczona dla scenariuszy przesyłania strumieniowego danych o dużej przepływności, w których klienci mogą wysyłać miliardy żądań dziennie. Usługa Event Hubs używa partycjonowanego modelu odbiorców do skalowania strumienia w poziomie i jest zintegrowana z usługami danych big data i analiz platformy Azure, w tym z usługami Databricks, Stream Analytics, ADLS i HDInsight. Dzięki funkcjom, takim jak Event Hubs przechwytywania i autodostrajania, usługa ta została zaprojektowana do obsługi aplikacji i rozwiązań dotyczących danych Big Data. Ponadto IoT Hub używa Event Hubs dla ścieżki przepływu telemetrii, dzięki czemu Twoje rozwiązanie IoT ma również zalety niekorzystnej mocy Event Hubs.

Podsumowując, oba rozwiązania są przeznaczone do pozyskiwania danych na dużą skalę. Tylko IoT Hub zawiera bogate możliwości specyficzne dla usługi IoT, które umożliwiają zmaksymalizowanie wartości biznesowej łączącej urządzenia IoT z chmurą platformy Azure.  Jeśli podróż IoT rozpocznie się dopiero po rozpoczęciu pracy z usługą IoT Hub do obsługi scenariuszy pozyskiwania danych, zapewnisz natychmiastowy dostęp do w pełni funkcjonalnych funkcji IoT, gdy wymagają ich biznes i wymagania techniczne.

W poniższej tabeli przedstawiono szczegółowe informacje na temat tego, jak dwie warstwy IoT Hub porównać do Event Hubs podczas oceniania ich pod kątem możliwości IoT. Aby uzyskać więcej informacji o warstwach Standardowa i podstawowa IoT Hub, zobacz [jak wybrać odpowiednią warstwę IoT Hub](iot-hub-scaling.md).

| Funkcja IoT | Warstwa standardowa IoT Hub | Warstwa Podstawowa IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Obsługa komunikatów przesyłanych z urządzeń do chmury | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |
| Protokoły: HTTPS, AMQP, AMQP za pośrednictwem obiektów WebSockets | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |
| Protokoły: MQTT, MQTT za pośrednictwem obiektów WebSockets | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Tożsamość dla urządzenia | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Przekazywanie plików z urządzeń | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Device Provisioning Service | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Obsługa komunikatów z chmury do urządzenia | ![Zaznacz][checkmark] |  |  |
| Sznurki urządzenia i zarządzanie urządzeniami | ![Zaznacz][checkmark] |  |  |
| Strumienie urządzeń (wersja zapoznawcza) | ![Zaznacz][checkmark] |  |  |
| IoT Edge | ![Zaznacz][checkmark] |  |  |

Nawet jeśli jedynym przypadkiem użycia jest pozyskiwanie danych z urządzenia do chmury, zdecydowanie zalecamy użycie IoT Hub jako usługi, która jest przeznaczona do łączności urządzeń IoT. 

### <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o możliwościach IoT Hub, zapoznaj się z [przewodnikiem dewelopera IoT Hub](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png