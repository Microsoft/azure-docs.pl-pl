---
title: Informacje o adresie IP Centrum IoT Hub | Microsoft Docs
description: Zapoznaj się z badaniem adresu IP Centrum IoT i jego właściwości. Adres IP Centrum IoT Hub może ulec zmianie w niektórych scenariuszach, takich jak odzyskiwanie po awarii lub regionalne przełączanie do trybu failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 1bfae8c7afbfdc6e73dd8bb17b94e6543361e9ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83848232"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub adresy IP

Prefiksy adresów IP IoT Hub publicznych punktów końcowych są publikowane okresowo w _AzureIoTHub_ [tagu usługi](../virtual-network/service-tags-overview.md)AzureIoTHub.

> [!NOTE]
> W przypadku urządzeń wdrożonych wewnątrz sieci lokalnych platforma Azure IoT Hub obsługuje integrację łączności sieci wirtualnej z prywatnymi punktami końcowymi. Aby uzyskać więcej informacji, zobacz [obsługa IoT Hub sieci wirtualnej](./virtual-network-support.md) .


Te prefiksy adresów IP mogą być używane do kontrolowania łączności między IoT Hub i urządzeniami i zasobami sieciowymi w celu zaimplementowania różnych celów izolacji sieci:

| Cel | Odpowiednie scenariusze | Podejście |
|------|-----------|----------|
| Upewnij się, że urządzenia i usługi komunikują się tylko z IoT Hub punktami końcowymi | Przesyłanie komunikatów [z urządzeń do chmury](./iot-hub-devguide-messaging.md)i [komunikacji między urządzeniami,](./iot-hub-devguide-messages-c2d.md) [metodami bezpośrednimi](./iot-hub-devguide-direct-methods.md), [bliźniaczych reprezentacji urządzeń i modułów](./iot-hub-devguide-device-twins.md) oraz [strumienie urządzeń](./iot-hub-device-streams-overview.md) | Użyj tagów usługi _AzureIoTHub_ i _EventHub_ , aby odnaleźć prefiksy adresów IP IoT Hub i centrum zdarzeń, a następnie skonfiguruj reguły zezwalania dla ustawień zapory urządzenia i usług odpowiednio dla tych prefiksów adresów IP. upuść ruch na inne docelowe adresy IP, z którymi nie chcesz komunikować się z urządzeniami ani usługami. |
| Upewnij się, że punkt końcowy urządzenia IoT Hub odbiera połączenia tylko z urządzeń i zasobów sieciowych | Przesyłanie komunikatów [z urządzeń do chmury](./iot-hub-devguide-messaging.md)i [komunikacji między urządzeniami,](./iot-hub-devguide-messages-c2d.md) [metodami bezpośrednimi](./iot-hub-devguide-direct-methods.md), [bliźniaczych reprezentacji urządzeń i modułów](./iot-hub-devguide-device-twins.md) oraz [strumienie urządzeń](./iot-hub-device-streams-overview.md) | Użyj [funkcji IoT Hub filtr IP](iot-hub-ip-filtering.md) , aby zezwolić na połączenia z urządzeń i adresów IP zasobów sieciowych (patrz sekcja [ograniczenia](#limitations-and-workarounds) ). | 
| Upewnij się, że niestandardowe zasoby punktu końcowego (konta magazynu, Magistrala usług i centrów zdarzeń) są dostępne tylko dla zasobów sieci | [Routing komunikatów](./iot-hub-devguide-messages-d2c.md) | Postępuj zgodnie ze wskazówkami dotyczącymi zasobów, aby ograniczyć łączność (na przykład za pośrednictwem [reguł zapory](../storage/common/storage-network-security.md), [linków prywatnych](../private-link/private-endpoint-overview.md)lub [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md)); Użyj tagów usługi _AzureIoTHub_ , aby odnaleźć prefiksy adresów IP IoT Hub i dodać reguły zezwalania dla tych prefiksów IP w konfiguracji zapory zasobu (patrz sekcja [ograniczenia](#limitations-and-workarounds) ). |



## <a name="best-practices"></a>Najlepsze rozwiązania

* W przypadku dodawania reguł ZEZWALAnia w konfiguracji zapory urządzeń najlepiej jest podać określone [porty używane przez odpowiednie protokoły](./iot-hub-devguide-protocols.md#port-numbers).

* Prefiksy adresów IP w usłudze IoT Hub mogą ulec zmianie. Te zmiany są okresowo publikowane za pośrednictwem tagów usługi przed rozpoczęciem działania. W związku z tym ważne jest, aby opracować procesy do regularnego pobierania i używania najnowszych tagów usługi. Ten proces można zautomatyzować za pomocą [interfejsu API odnajdywania tagów usług](../virtual-network/service-tags-overview.md#service-tags-on-premises). Należy pamiętać, że interfejs API odnajdowania tagów usług jest nadal w wersji zapoznawczej, a w niektórych przypadkach może nie być możliwe utworzenie pełnej listy tagów i adresów IP. Dopóki interfejs API odnajdowania jest ogólnie dostępny, należy rozważyć użycie [tagów usługi w formacie JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Użyj *AzureIoTHub. [ Nazwa regionu]* tag do identyfikowania prefiksów IP używanych przez punkty końcowe Centrum IoT Hub w określonym regionie. W przypadku odzyskiwania po awarii centrum danych lub [regionalnej pracy w trybie failover](iot-hub-ha-dr.md) upewnij się również, że połączenie z PREFIKSAMI adresów IP w regionie pary geograficznej IoT Hub jest włączone.

* Skonfigurowanie reguł zapory w IoT Hub może uniemożliwić nawiązywanie połączeń wymaganych do uruchamiania poleceń interfejsu wiersza polecenia platformy Azure i programu PowerShell w IoT Hub. Aby tego uniknąć, można dodać reguły ZEZWALAnia dla prefiksów adresów IP klientów, aby ponownie włączyć klienta interfejsu wiersza polecenia lub programu PowerShell w celu komunikowania się z IoT Hub.  


## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

* Funkcja filtru IP IoT Hub ma limit 10 reguł. Ten limit może być zgłaszany przez żądania za pośrednictwem obsługi klienta platformy Azure. 

* Skonfigurowane [reguły filtrowania adresów IP](iot-hub-ip-filtering.md) są stosowane tylko w przypadku punktów końcowych IP IoT Hub, a nie do wbudowanego punktu końcowego centrum zdarzeń centrum IoT Hub. Jeśli trzeba również zastosować filtrowanie adresów IP w centrum zdarzeń, w którym są przechowywane wiadomości, można to zrobić przy użyciu własnego zasobu centrum zdarzeń, w którym można bezpośrednio skonfigurować żądane reguły filtrowania adresów IP. W tym celu należy udostępnić własny zasób centrum zdarzeń i skonfigurować [Routing komunikatów](./iot-hub-devguide-messages-d2c.md) w celu wysyłania komunikatów do tego zasobu zamiast wbudowanego centrum zdarzeń IoT Hub. Na koniec, zgodnie z opisem w powyższej tabeli, aby włączyć funkcję routingu wiadomości, należy również zezwolić na połączenie z prefiksami adresów IP IoT Hub z zainicjowanym zasobem centrum zdarzeń.

* W przypadku routingu do konta magazynu Zezwalanie na ruch z prefiksów adresów IP IoT Hub jest możliwe tylko wtedy, gdy konto magazynu znajduje się w innym regionie niż IoT Hub.

## <a name="support-for-ipv6"></a>Obsługa protokołu IPv6 

Protokół IPv6 nie jest obecnie obsługiwany w IoT Hub.
