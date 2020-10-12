---
title: Informacje o adresie IP wystąpienia usługi IoT Device Provisioning (DPS) | Microsoft Docs
description: Zapoznaj się z kwerendą dotyczącą adresu usługi IoT Device Provisioning (DPS) i jej właściwości. Adres IP wystąpienia DPS może ulec zmianie w niektórych scenariuszach, takich jak odzyskiwanie po awarii lub regionalne przełączanie do trybu failover.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79284930"
---
# <a name="iot-hub-dps-ip-addresses"></a>Adresy IP IoT Hub DPS

Prefiksy adresów IP dla publicznych punktów końcowych IoT Hub Device Provisioning Service (DPS) są publikowane okresowo w [tagu usługi](../virtual-network/service-tags-overview.md) _AzureIoTHub_ . Te prefiksy adresów IP mogą być używane do kontrolowania łączności między wystąpieniem usługi IoT DPS a urządzeniami sieciowymi w celu zaimplementowania różnych celów izolacji sieci:

| Cel | Podejście |
|------|----------|
| Upewnij się, że urządzenia i usługi komunikują się tylko z IoT Hub punktami końcowymi DPS | Użyj znacznika usługi _AzureIoTHub_ w celu odnalezienia wystąpień IoT Hub DPS. Skonfiguruj odpowiednie prefiksy adresów IP w ustawieniach zapory dla urządzeń i usług. Skonfiguruj reguły do usuwania ruchu do innych docelowych adresów IP, z którymi nie chcesz komunikować się z urządzeniami lub usługami. |
| Upewnij się, że punkt końcowy usługi DPS IoT Hub odbiera połączenia tylko z urządzeń i zasobów sieciowych | Użyj [funkcji filtru IP](iot-dps-ip-filtering.md) usługi IoT DPS, aby utworzyć reguły filtru dla interfejsów API usług urządzenia i DPS. Te reguły filtru mogą służyć do zezwalania na połączenia tylko z urządzeń i adresów IP zasobów sieciowych (patrz sekcja [ograniczenia](#limitations-and-workarounds) ). | 




## <a name="best-practices"></a>Najlepsze rozwiązania

* W przypadku dodawania reguł ZEZWALAnia w konfiguracji zapory urządzeń najlepiej jest podać określone [porty używane przez odpowiednie protokoły](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* Prefiksy adresów IP wystąpień IoT DPS mogą ulec zmianie. Te zmiany są okresowo publikowane za pośrednictwem tagów usługi przed rozpoczęciem działania. W związku z tym ważne jest, aby opracować procesy do regularnego pobierania i używania najnowszych tagów usługi. Ten proces można zautomatyzować za pomocą [interfejsu API odnajdywania tagów usług](../virtual-network/service-tags-overview.md#service-tags-on-premises). Interfejs API odnajdywania tagów usług jest nadal w wersji zapoznawczej, a w niektórych przypadkach może nie być możliwe utworzenie pełnej listy tagów i adresów IP. Dopóki interfejs API odnajdowania jest ogólnie dostępny, należy rozważyć użycie [tagów usługi w formacie JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Użyj *AzureIoTHub. [ Nazwa regionu]* tag do identyfikowania prefiksów IP używanych przez punkty końcowe DPS w określonym regionie. W przypadku odzyskiwania po awarii centrum danych lub [regionalnej pracy awaryjnej](../iot-hub/iot-hub-ha-dr.md)upewnij się, że połączenie z PREFIKSAMI adresów IP w regionie wystąpienia usługi DPS jest również włączone.

* Skonfigurowanie reguł zapory dla wystąpienia usługi DPS może uniemożliwić wyłączenie łączności wymaganej do uruchomienia interfejsu wiersza polecenia platformy Azure i poleceń programu PowerShell. Aby uniknąć tych problemów z łącznością, można dodać reguły ZEZWALAnia dla prefiksów adresów IP klientów, aby ponownie włączyć interfejsu wiersza polecenia lub klientów programu PowerShell w celu komunikowania się z wystąpieniem usługi DPS.  


## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

* Funkcja filtru IP DPS ma limit 100 reguł. Ten limit może być zgłaszany przez żądania za pośrednictwem obsługi klienta platformy Azure. 

* Skonfigurowane [reguły filtrowania adresów IP](iot-dps-ip-filtering.md) są stosowane tylko w punktach końcowych punktu dystrybucji, a nie w połączonych punktach końcowych IoT Hub. Filtrowanie adresów IP dla połączonych centrów IoT musi być skonfigurowane osobno. Aby uzyskać więcej informacji, zobacz [IoT Hub reguł filtrowania adresów IP](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Obsługa protokołu IPv6 

Protokół IPv6 nie jest obecnie obsługiwany w IoT Hub lub DPS.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o konfiguracjach adresów IP za pomocą DPS, zobacz:

* [Konfigurowanie filtrowania adresów IP](iot-dps-ip-filtering.md)
