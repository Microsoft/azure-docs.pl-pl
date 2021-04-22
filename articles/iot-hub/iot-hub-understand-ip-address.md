---
title: Opis adresu IP centrum IoT Hub | Microsoft Docs
description: Dowiedz się, jak odpytować adres IP centrum IoT i jego właściwości. Adres IP centrum IoT może ulec zmianie w niektórych scenariuszach, takich jak odzyskiwanie po awarii lub regionalny tryb failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 7d807a15d358bd621baedbff253f0c731e43ed26
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874175"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub ip

Prefiksy adresów IP publicznych IoT Hub są okresowo publikowane pod tagiem _usługi AzureIoTHub_ [.](../virtual-network/service-tags-overview.md)

> [!NOTE]
> W przypadku urządzeń wdrożonych w sieciach lokalnych program Azure IoT Hub integrację łączności z siecią wirtualną z prywatnymi punktami końcowymi. Zobacz [IoT Hub pomocy technicznej dotyczącej sieci wirtualnej,](./virtual-network-support.md) aby uzyskać więcej informacji.


Możesz użyć tych prefiksów adresów IP, aby kontrolować łączność między usługą IoT Hub i urządzeniami lub zasobami sieci w celu zaimplementowania różnych celów izolacji sieci:

| Cel | Odpowiednie scenariusze | Podejście |
|------|-----------|----------|
| Upewnij się, że urządzenia i usługi komunikują się IoT Hub punktami końcowymi | [Przesyłanie komunikatów z urządzenia do](./iot-hub-devguide-messaging.md) [chmury](./iot-hub-devguide-messages-c2d.md) i z chmury do urządzeń, [metody](./iot-hub-devguide-direct-methods.md)bezpośrednie, bliźniacze [bliźniacze](./iot-hub-devguide-device-twins.md) reprezentacji urządzenia i modułu oraz [strumienie urządzeń](./iot-hub-device-streams-overview.md) | Użyj _tagów usług AzureIoTHub_ i _EventHub,_ aby odnaleźć prefiksy adresów IP usług IoT Hub i Event Hub oraz odpowiednio skonfigurować reguły ZEZWALAJ dla ustawień zapory urządzeń i usług dla tych prefiksów adresów IP; porzuć ruch do innych docelowych adresów IP, z których urządzenia lub usługi nie mają się komunikować. |
| Upewnij się IoT Hub punkt końcowy urządzenia odbiera połączenia tylko z urządzeń i zasobów sieciowych | [Przesyłanie komunikatów z urządzenia do](./iot-hub-devguide-messaging.md) [chmury](./iot-hub-devguide-messages-c2d.md) i z chmury do urządzeń, [metody](./iot-hub-devguide-direct-methods.md)bezpośrednie, bliźniacze [bliźniacze](./iot-hub-devguide-device-twins.md) reprezentacji urządzenia i modułu oraz [strumienie urządzeń](./iot-hub-device-streams-overview.md) | Użyj IoT Hub [filtrowania adresów IP,](iot-hub-ip-filtering.md) aby zezwolić na połączenia z urządzeń i adresów IP zasobów sieciowych (zobacz [sekcję ograniczenia).](#limitations-and-workarounds) | 
| Upewnij się, że niestandardowe zasoby punktu końcowego tras (konta magazynu, usługa Service Bus i centra zdarzeń) są dostępne tylko z zasobów sieciowych | [Routing komunikatów](./iot-hub-devguide-messages-d2c.md) | Postępuj zgodnie ze wskazówkami zasobu w zakresie ograniczania łączności (na przykład za pośrednictwem reguł [zapory,](../storage/common/storage-network-security.md)linków prywatnych [lub](../private-link/private-endpoint-overview.md)punktów [końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md)). Użyj _tagów usługi AzureIoTHub,_ IoT Hub prefiksów adresów IP i dodaj reguły ALLOW dla tych prefiksów IP w konfiguracji zapory zasobu (zobacz [sekcję](#limitations-and-workarounds) ograniczenia). |



## <a name="best-practices"></a>Najlepsze rozwiązania

* Podczas dodawania reguł ALLOW w konfiguracji zapory urządzeń najlepiej jest podać określone porty [używane przez odpowiednie protokoły.](./iot-hub-devguide-protocols.md#port-numbers)

* Prefiksy adresów IP centrum IoT mogą ulec zmianie. Te zmiany są publikowane okresowo za pośrednictwem tagów usługi, zanim zajdą one w życie. Dlatego ważne jest, aby opracowywać procesy w celu regularnego pobierania i używania najnowszych tagów usługi. Ten proces można zautomatyzować za pomocą [interfejsu API odnajdywania tagów usług](../virtual-network/service-tags-overview.md#service-tags-on-premises). Należy pamiętać, że interfejs API odnajdywania tagów usługi jest nadal w wersji zapoznawczej i w niektórych przypadkach może nie tworzyć pełnej listy tagów i adresów IP. Dopóki interfejs API odnajdywania nie będzie ogólnie dostępny, rozważ użycie tagów usługi [w formacie JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)do pobrania. 

* Użyj usługi *AzureIoTHub.[ nazwa regionu]* identyfikujący prefiksy IP używane przez punkty końcowe centrum IoT w określonym regionie. Aby uwzględnić odzyskiwanie po awarii centrum danych lub regionalny tryb [failover,](iot-hub-ha-dr.md) upewnij się, że włączono również łączność z prefiksami IP regionu pary geograficznej IoT Hub twojej firmy.

* Skonfigurowanie reguł zapory w usłudze IoT Hub może zablokować łączność potrzebną do uruchamiania poleceń interfejsu wiersza polecenia platformy Azure i programu PowerShell względem IoT Hub. Aby tego uniknąć, można dodać reguły ZEZWALAJ dla prefiksów adresów IP klientów, aby ponownie włączyć interfejs wiersza polecenia lub klientów programu PowerShell do komunikowania się z IoT Hub.  


## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

* IoT Hub filtrowania adresów IP ma limit 100 reguł. Ten limit i można go podnieść za pośrednictwem żądań za pośrednictwem działu pomocy technicznej platformy Azure. 

* Skonfigurowane reguły [filtrowania adresów IP](iot-hub-ip-filtering.md) są stosowane tylko do punktów końcowych IoT Hub IP, a nie do wbudowanego punktu końcowego centrum zdarzeń usługi IoT Hub. Jeśli wymagane jest również zastosowanie filtrowania adresów IP w centrum zdarzeń, w którym są przechowywane komunikaty, możesz to zrobić, przenosząc własny zasób centrum zdarzeń, w którym można bezpośrednio skonfigurować żądane reguły filtrowania adresów IP. W tym celu należy aprowizować własny zasób centrum zdarzeń i skonfigurować routing komunikatów w celu wysyłania komunikatów do tego zasobu zamiast wbudowanego centrum zdarzeń IoT Hub usługi IoT Hub. [](./iot-hub-devguide-messages-d2c.md) Na koniec, jak omówiono w powyższej tabeli, aby włączyć funkcję routingu komunikatów, należy również zezwolić na łączność z prefiksami adresów IP usługi IoT Hub do aprowizowanych zasobów centrum zdarzeń.

* W przypadku routingu do konta magazynu zezwolenie na ruch z prefiksów adresów IP IoT Hub jest możliwe tylko wtedy, gdy konto magazynu znajduje się w innym regionie niż IoT Hub.

## <a name="support-for-ipv6"></a>Obsługa protokołu IPv6 

Protokół IPv6 nie jest obecnie obsługiwany na IoT Hub.
