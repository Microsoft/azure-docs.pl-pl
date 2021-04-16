---
title: Opis Azure IoT Hub końcowych | Microsoft Docs
description: Przewodnik dla deweloperów — informacje IoT Hub dotyczące punktów końcowych dla urządzeń i usług.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: d2b9ea2e075ddcf20860ccb9ab1f2eff654993ad
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499380"
---
# <a name="reference---iot-hub-endpoints"></a>Odwołanie — IoT Hub końcowe

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub nazw

Nazwę hosta centrum IoT, które hostuje punkty końcowe, można znaleźć w portalu na stronie  **Przegląd** centrum. Domyślnie nazwa DNS centrum IoT wygląda następująco: `{your iot hub name}.azure-devices.net` .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista wbudowanych punktów IoT Hub końcowych

Azure IoT Hub to usługa wielodostępna, która uwidacznia jej funkcje różnym aktorom. Na poniższym diagramie przedstawiono różne punkty końcowe, które IoT Hub uwidacznia.

![Punkty końcowe centrum IoT Hub](./media/iot-hub-devguide-endpoints/endpoints.png)

Na poniższej liście opisano punkty końcowe:

* **Dostawca zasobów**. Dostawca IoT Hub zasobów uwidacznia [interfejs Azure Resource Manager](../azure-resource-manager/management/overview.md) interfejsu. Ten interfejs umożliwia właścicielom subskrypcji platformy Azure tworzenie i usuwanie centrów IoT oraz aktualizowanie właściwości centrum IoT. IoT Hub zarządzania zasadami zabezpieczeń na poziomie [centrum,](iot-hub-devguide-security.md#access-control-and-permissions)w przeciwieństwie do kontroli dostępu na poziomie urządzenia, oraz opcjami funkcjonalnymi komunikatów z chmury do urządzenia i z urządzenia do chmury. Dostawca IoT Hub zasobów umożliwia również [eksportowanie tożsamości urządzeń.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

* **Zarządzanie tożsamościami urządzeń.** Każde centrum IoT udostępnia zestaw punktów końcowych REST protokołu HTTPS do zarządzania tożsamościami urządzeń (tworzenie, pobieranie, aktualizowanie i usuwanie). [Tożsamości urządzeń są](iot-hub-devguide-identity-registry.md) używane do uwierzytelniania urządzeń i kontroli dostępu.

* **Zarządzanie bliźniaczymi reprezentacjiami urządzeń.** Każde centrum IoT udostępnia zestaw punktu końcowego REST HTTPS dla usługi w celu wykonywania zapytań i aktualizowania bliźniaczych reprezentacji urządzeń [(aktualizowanie](iot-hub-devguide-device-twins.md) tagów i właściwości). 

* **Zarządzanie zadaniami**. Każde centrum IoT udostępnia zestaw punktu końcowego REST HTTPS dla usługi do wykonywania zapytań i zarządzania [zadaniami.](iot-hub-devguide-jobs.md)

* **Punkty końcowe urządzenia.** Dla każdego urządzenia w rejestrze tożsamości IoT Hub uwidacznia zestaw punktów końcowych. Z wyjątkiem przypadków, gdy wspomniano, te punkty końcowe są udostępniane przy użyciu protokołów [MQTT 3.1.1,](https://mqtt.org/)HTTPS 1.1 i [AMQP 1.0.](https://www.amqp.org/) AmQP i MQTT są również dostępne za [pośrednictwem portów WebSocket](https://tools.ietf.org/html/rfc6455) na porcie 443.

  * *Wysyłanie komunikatów z urządzenia do chmury.* Urządzenie używa tego punktu końcowego do [wysyłania komunikatów z urządzenia do chmury.](iot-hub-devguide-messages-d2c.md)

  * *Odbieranie komunikatów z chmury do urządzenia.* Urządzenie używa tego punktu końcowego do odbierania [komunikatów z chmury do urządzenia.](iot-hub-devguide-messages-c2d.md)

  * *Zainicjuj przekazywanie plików.* Urządzenie używa tego punktu końcowego do odbierania URI sygnatury dostępu współdzielonego usługi Azure Storage IoT Hub przekazania [pliku.](iot-hub-devguide-file-upload.md)

  * *Pobieranie i aktualizowanie właściwości bliźniaczej reprezentacji urządzenia.* Urządzenie używa tego punktu końcowego, aby uzyskać dostęp [do właściwości](iot-hub-devguide-device-twins.md)bliźniaczej reprezentacji urządzenia. Protokół HTTPS nie jest obsługiwany.

  * *Odbieraj żądania metody bezpośredniej*. Urządzenie używa tego punktu końcowego do [nasłuchiwać żądań](iot-hub-devguide-direct-methods.md)metody bezpośredniej. Protokół HTTPS nie jest obsługiwany.

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **Punkty końcowe usługi**. Każde centrum IoT udostępnia zestaw punktów końcowych dla twojego serwera końcowego rozwiązania w celu komunikowania się z urządzeniami. Z jednym wyjątkiem te punkty końcowe są udostępniane tylko przy użyciu [protokołów AMQP](https://www.amqp.org/) i AMQP za pośrednictwem protokołów WebSockets. Punkt końcowy wywołania metody bezpośredniej jest ujawniony za pośrednictwem protokołu HTTPS.
  
  * *Odbieranie komunikatów z urządzenia do chmury.* Ten punkt końcowy jest zgodny [z](https://azure.microsoft.com/documentation/services/event-hubs/)Azure Event Hubs . Usługa back-end może używać jej do odczytywania komunikatów wysyłanych z urządzenia do [chmury](iot-hub-devguide-messages-d2c.md) wysyłanych przez urządzenia. Oprócz tego wbudowanego punktu końcowego można tworzyć niestandardowe punkty końcowe w centrum IoT.
  
  * *Wysyłaj komunikaty z chmury do urządzenia i odbieraj potwierdzenia dostarczenia.* Te punkty końcowe umożliwiają zadomowieniu rozwiązania wysyłanie niezawodnych komunikatów z chmury do urządzenia oraz odbieranie odpowiednich potwierdzeń dostarczenia lub wygaśnięcia. [](iot-hub-devguide-messages-c2d.md)
  
  * *Otrzymywanie powiadomień o plikach.* Ten punkt końcowy obsługi komunikatów umożliwia otrzymywanie powiadomień o pomyślnym przekazyniu pliku przez urządzenia. 
  
  * *Bezpośrednie wywołania metody*. Ten punkt końcowy umożliwia usłudze back-end wywoływanie [metody bezpośredniej](iot-hub-devguide-direct-methods.md) na urządzeniu.
  
  * *Odbieranie zdarzeń monitorowania operacji.* Ten punkt końcowy umożliwia odbieranie zdarzeń monitorowania operacji, jeśli twoje centrum IoT hub zostało skonfigurowane do ich emitowania. Aby uzyskać więcej informacji, [zobacz IoT Hub monitorowania operacji.](iot-hub-operations-monitoring.md)

W [artykule Azure IoT SDKs (Zestawy SDK usługi Azure IoT)](iot-hub-devguide-sdks.md) opisano różne sposoby uzyskiwania dostępu do tych punktów końcowych.

Wszystkie IoT Hub końcowe używają protokołu [TLS](https://tools.ietf.org/html/rfc5246) i żaden punkt końcowy nie jest kiedykolwiek ujawniony w niezaszyfrowanych/niezabezpieczonych kanałach.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Istniejące usługi platformy Azure w subskrypcjach platformy Azure można połączyć z centrum IoT Hub, aby pełnić swoją usługę jako punkty końcowe routingu komunikatów. Te punkty końcowe działają jako punkty końcowe usługi i są używane jako ujścia dla tras komunikatów. Urządzenia nie mogą zapisywać bezpośrednio w dodatkowych punktach końcowych. Dowiedz się więcej o [routingu komunikatów.](../iot-hub/iot-hub-devguide-messages-d2c.md)

IoT Hub obecnie obsługuje następujące usługi platformy Azure jako dodatkowe punkty końcowe:

* Kontenery usługi Azure Storage
* Event Hubs
* Kolejki usługi Service Bus
* Tematy dotyczące usługi Service Bus

Aby uzyskać informacje o limitach liczby punktów końcowych, które można dodać, zobacz [Limity przydziału i ograniczanie przepustowości.](iot-hub-devguide-quotas-throttling.md)

## <a name="endpoint-health"></a>Kondycja punktu końcowego

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>Bramy w terenie

W rozwiązaniu IoT *brama w* terenie znajduje się między urządzeniami i punktami IoT Hub końcowymi. Zwykle znajduje się w pobliżu urządzeń. Urządzenia komunikują się bezpośrednio z bramą w terenie przy użyciu protokołu obsługiwanego przez urządzenia. Brama pola łączy się z punktem IoT Hub przy użyciu protokołu obsługiwanego przez IoT Hub. Brama w terenie może być dedykowanym urządzeniem sprzętowym lub komputerem o niskim poziomie zasilania z uruchomionym oprogramowaniem bramy niestandardowej.

Za pomocą [Azure IoT Edge](../iot-edge/index.yml) można zaimplementować bramę w terenie. IoT Edge oferuje funkcje, takie jak multipleksowanie komunikacji z wielu urządzeń do tego samego IoT Hub połączenia.

## <a name="next-steps"></a>Następne kroki

Inne tematy referencyjne w tym IoT Hub dla deweloperów obejmują:

* [IoT Hub zapytań dla bliźniaczych reprezentacji urządzeń, zadań i routingu komunikatów](iot-hub-devguide-query-language.md)
* [Limity przydziału i ograniczanie wydajności](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub obsługi MQTT](iot-hub-mqtt-support.md)
* [Opis adresu IP centrum IoT](iot-hub-understand-ip-address.md)
