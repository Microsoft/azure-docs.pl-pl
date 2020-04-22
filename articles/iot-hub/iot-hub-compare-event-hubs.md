---
title: Porównanie usługi Azure IoT Hub z usługą Azure Event Hubs | Dokumenty firmy Microsoft
description: Porównanie usługi Platformy Azure usługi Usługi Usługi IoT Hub i Event Hubs, w których wyróżniono różnice funkcjonalne i przypadki użycia. Porównanie obejmuje obsługiwane protokoły, zarządzanie urządzeniami, monitorowanie i przekazywanie plików.
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
ms.openlocfilehash: 7aa59f8181ad60b3d43846a3f4f1f471a050b238
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733436"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Łączenie urządzeń IoT z platformą Azure: Centrum IoT i centra zdarzeń

Platforma Azure oferuje usługi opracowane specjalnie dla różnych typów łączności i komunikacji, aby ułatwić łączenie danych z możliwością chmury. Usługa Azure IoT Hub i usługi Azure Event Hubs to usługi w chmurze, które mogą pozyskiwania dużych ilości danych i przetwarzać lub przechowywać te dane w celu uzyskania szczegółowych informacji biznesowych. Dwie usługi są podobne, ponieważ obie obsługują pozyskiwania danych o niskim opóźnieniu i wysokiej niezawodności, ale są przeznaczone do różnych celów. Usługa IoT Hub została opracowana w celu spełnienia unikatowych wymagań dotyczących łączenia urządzeń IoT z chmurą platformy Azure, podczas gdy usługa Event Hubs została zaprojektowana do przesyłania strumieniowego danych w dużych zbiorach danych. Firma Microsoft zaleca łączenie urządzeń IoT z platformą Azure za pomocą usługi Azure IoT Hub

Usługa Azure IoT Hub to brama w chmurze, która łączy urządzenia IoT w celu zbierania danych i kierowania analizą biznesową i automatyzacją. Ponadto Centrum IoT zawiera funkcje, które wzbogacają relacje między urządzeniami a systemami zaplecza. Funkcje komunikacji dwukierunkowej oznaczają, że podczas odbierania danych z urządzeń można również wysyłać polecenia i zasady z powrotem do urządzeń. Na przykład użyj wiadomości z chmury do urządzenia, aby zaktualizować właściwości lub wywołać akcje zarządzania urządzeniami. Komunikacja między chmurami a urządzeniami umożliwia również wysyłanie analizy chmury do urządzeń brzegowych za pomocą usługi Azure IoT Edge. Unikatowa tożsamość na poziomie urządzenia udostępniana przez centrum IoT Hub pomaga lepiej zabezpieczyć rozwiązanie IoT przed potencjalnymi atakami. 

[Usługa Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) to usługa przesyłania strumieniowego dużych zbiorów danych platformy Azure. Jest ona przeznaczona dla scenariuszy przesyłania strumieniowego danych o dużej przepływności, w których klienci mogą wysyłać miliardy żądań dziennie. Usługa Event Hubs używa partycjonowanego modelu odbiorców do skalowania strumienia w poziomie i jest zintegrowana z usługami danych big data i analiz platformy Azure, w tym z usługami Databricks, Stream Analytics, ADLS i HDInsight. Dzięki funkcjom, takim jak Przechwytywanie i Automatyczne nadmuchiwanie centrów zdarzeń, ta usługa została zaprojektowana do obsługi aplikacji i rozwiązań do obsługi dużych zbiorów danych. Ponadto usługa IoT Hub używa centrów zdarzeń do ścieżki przepływu telemetrii, dzięki czemu rozwiązanie IoT korzysta również z ogromnej mocy centrów zdarzeń.

Podsumowując, oba rozwiązania są przeznaczone do pozyskiwania danych na masową skalę. Tylko Usługa IoT Hub udostępnia zaawansowane funkcje specyficzne dla IoT, które są przeznaczone do maksymalizacji wartości biznesowej łączenia urządzeń IoT z chmurą platformy Azure.  Jeśli proces IoT dopiero się zaczyna, począwszy od usługi IoT Hub, aby obsługiwać scenariusze pozyskiwania danych, zapewni Ci natychmiastowy dostęp do w pełni funkcjonalnych funkcji IoT, gdy wymagają tego twoje potrzeby biznesowe i techniczne.

Poniższa tabela zawiera szczegółowe informacje o tym, jak dwie warstwy Usługi IoT Hub porównać z Centrum zdarzeń podczas oceny ich możliwości IoT. Aby uzyskać więcej informacji na temat standardowych i podstawowych warstw usługi IoT Hub, zobacz [Jak wybrać odpowiednią warstwę Usługi IoT Hub](iot-hub-scaling.md).

| Możliwość IoT | Warstwa standardowa usługi IoT Hub | Warstwa podstawowa usługi IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Wiadomości między urządzeniami a chmurą | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |
| Protokoły: HTTPS, AMQP, AMQP over webSockets | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |
| Protokoły: MQTT, MQTT przez webSockets | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Tożsamość na urządzenie | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Przesyłanie plików z urządzeń | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Usługa inicjowania obsługi administracyjnej urządzeń | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Wiadomości między chmurami a urządzeniem | ![Zaznacz][checkmark] |  |  |
| Podwójne urządzenie i zarządzanie urządzeniami | ![Zaznacz][checkmark] |  |  |
| Strumienie urządzeń (wersja zapoznawcza) | ![Zaznacz][checkmark] |  |  |
| IoT Edge | ![Zaznacz][checkmark] |  |  |

Nawet jeśli jedynym przypadkiem użycia jest pozyskiwania danych z urządzenia do chmury, zdecydowanie zaleca się korzystanie z Usługi IoT Hub, ponieważ zapewnia usługę, która jest przeznaczona dla łączności urządzenia IoT. 

### <a name="next-steps"></a>Następne kroki

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz [przewodnik dla deweloperów usługi IoT Hub.](iot-hub-devguide.md)

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
