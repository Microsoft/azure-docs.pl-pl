---
title: Opis punktów końcowych usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — informacje o punktach końcowych skierowanych do urządzenia i usług IoT Hub.
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
ms.openlocfilehash: 53660ad93ab2218d546ae6f363873c4d66872e2b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730313"
---
# <a name="reference---iot-hub-endpoints"></a>Odwołanie — punkty końcowe usługi IoT Hub

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nazwy Centrum IoT

Nazwa hosta centrum IoT hub, który obsługuje punkty końcowe w portalu na stronie **Przegląd** centrum. Domyślnie nazwa DNS centrum IoT hub `{your iot hub name}.azure-devices.net`wygląda następująco: .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista wbudowanych punktów końcowych usługi IoT Hub

Usługa Azure IoT Hub to usługa wielodostępna, która udostępnia jej funkcje różnym podmiotom. Na poniższym diagramie przedstawiono różne punkty końcowe, które udostępnia centrum IoT Hub.

![Punkty końcowe centrum IoT Hub](./media/iot-hub-devguide-endpoints/endpoints.png)

Na poniższej liście opisano punkty końcowe:

* **Dostawca zasobów**. Dostawca zasobów usługi IoT Hub udostępnia interfejs [usługi Azure Resource Manager.](../azure-resource-manager/management/overview.md) Ten interfejs umożliwia właścicielom subskrypcji platformy Azure tworzenie i usuwanie centrów IoT oraz aktualizowanie właściwości centrum IoT. Właściwości usługi IoT Hub regulują [zasady zabezpieczeń na poziomie koncentratora,](iot-hub-devguide-security.md#access-control-and-permissions)w przeciwieństwie do kontroli dostępu na poziomie urządzenia i opcje funkcjonalne dla wiadomości z chmury do urządzenia i urządzenia do chmury. Dostawca zasobów usługi IoT Hub umożliwia również [eksportowanie tożsamości urządzeń](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Zarządzanie tożsamościami urządzeń**. Każdy centrum IoT udostępnia zestaw punktów końcowych HTTPS REST do zarządzania tożsamościami urządzeń (tworzenie, pobieranie, aktualizowanie i usuwanie). [Tożsamości urządzeń](iot-hub-devguide-identity-registry.md) są używane do uwierzytelniania urządzeń i kontroli dostępu.

* **Zarządzanie bliźniaczymi urządzeniami**. Każdy centrum IoT udostępnia zestaw usług https rest punktu końcowego do kwerendy i aktualizacji [bliźniaczych urządzeń](iot-hub-devguide-device-twins.md) (tagi aktualizacji i właściwości).

* **Zarządzanie ofertami pracy**. Każde centrum IoT udostępnia zestaw punktu końcowego HTTPS REST wychodzącego na usługę do kwerendy i zarządzania [zadaniami.](iot-hub-devguide-jobs.md)

* **Punkty końcowe urządzenia**. Dla każdego urządzenia w rejestrze tożsamości usługa IoT Hub uwidacznia zestaw punktów końcowych:

  * *Wysyłanie wiadomości z urządzenia do chmury*. Urządzenie używa tego punktu końcowego do [wysyłania wiadomości z urządzenia do chmury](iot-hub-devguide-messages-d2c.md).

  * *Odbieranie komunikatów z chmury do urządzenia*. Urządzenie używa tego punktu końcowego do odbierania ukierunkowanych [komunikatów z chmury do urządzenia.](iot-hub-devguide-messages-c2d.md)

  * *Inicjowanie przekazywania plików*. Urządzenie używa tego punktu końcowego do odbierania identyfikatora URI usługi Azure Storage SAS z usługi IoT Hub w celu [przekazania pliku.](iot-hub-devguide-file-upload.md)

  * *Pobieranie i aktualizowanie właściwości bliźniaczej reprezentacji urządzenia*. Urządzenie używa tego punktu końcowego, aby uzyskać dostęp do jego [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md)'s właściwości.

  * *Odbieranie żądań metody bezpośredniej*. Urządzenie używa tego punktu końcowego do nasłuchiwać [żądań metody bezpośredniej.](iot-hub-devguide-direct-methods.md)

    Te punkty końcowe są udostępniane przy użyciu protokołów [MQTT v3.1.1](https://mqtt.org/), HTTPS 1.1 i [AMQP 1.0.](https://www.amqp.org/) AMQP jest również dostępny przez [WebSockets](https://tools.ietf.org/html/rfc6455) na porcie 443.

* **Punkty końcowe usługi**. Każdy centrum IoT udostępnia zestaw punktów końcowych dla zaplecza rozwiązania do komunikowania się z urządzeniami. Z jednym wyjątkiem te punkty końcowe są udostępniane tylko przy użyciu protokołu [AMQP.](https://www.amqp.org/) Punkt końcowy wywołania metody jest narażony na protokół HTTPS.
  
  * *Odbieranie wiadomości z urządzenia do chmury*. Ten punkt końcowy jest zgodny z [usługą Azure Event Hubs.](https://azure.microsoft.com/documentation/services/event-hubs/) Usługa zaplecza może go używać do odczytywania [wiadomości z urządzenia do chmury](iot-hub-devguide-messages-d2c.md) wysyłanych przez urządzenia. Oprócz tego wbudowanego punktu końcowego można tworzyć niestandardowe punkty końcowe w centrum IoT hub.
  
  * *Wysyłaj wiadomości z chmury do urządzenia i odbieraj potwierdzenia dostarczenia*. Te punkty końcowe umożliwiają zapleczu rozwiązania wysyłanie niezawodnych [komunikatów z chmury do urządzenia](iot-hub-devguide-messages-c2d.md)i odbieranie odpowiednich potwierdzeń dostarczania lub wygaśnięcia.
  
  * *Odbieranie powiadomień o plikach*. Ten punkt końcowy obsługi wiadomości umożliwia otrzymywanie powiadomień o pomyślnym przesłaniu pliku przez urządzenia. 
  
  * *Bezpośrednie wywołanie metody*. Ten punkt końcowy umożliwia usłudze zaplecza wywoływanie [metody bezpośredniej](iot-hub-devguide-direct-methods.md) na urządzeniu.
  
  * *Odbieranie zdarzeń monitorowania operacji*. Ten punkt końcowy umożliwia odbieranie zdarzeń monitorowania operacji, jeśli centrum IoT został skonfigurowany do ich emisji. Aby uzyskać więcej informacji, zobacz [Monitorowanie operacji usługi IoT Hub](iot-hub-operations-monitoring.md).

W [artykule zestawów SDK usługi Azure IoT](iot-hub-devguide-sdks.md) opisano różne sposoby uzyskiwania dostępu do tych punktów końcowych.

Wszystkie punkty końcowe usługi IoT Hub używają protokołu [TLS,](https://tools.ietf.org/html/rfc5246) a żaden punkt końcowy nie jest nigdy narażony na kanały niezaszyfrowane/niezabezpieczone.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Istniejące usługi platformy Azure w ramach subskrypcji można połączyć z centrum IoT hub, aby działać jako punkty końcowe dla routingu wiadomości. Te punkty końcowe działają jako punkty końcowe usługi i są używane jako pochłaniacze dla tras komunikatów. Urządzenia nie mogą zapisywać bezpośrednio do dodatkowych punktów końcowych. Dowiedz się więcej o [routingu wiadomości](../iot-hub/iot-hub-devguide-messages-d2c.md).

Usługa IoT Hub obsługuje obecnie następujące usługi platformy Azure jako dodatkowe punkty końcowe:

* Kontenery usługi Azure Storage
* Event Hubs
* Kolejki usługi Service Bus
* Tematy dotyczące usługi Service Bus

Aby zapoznać się z limitami liczby punktów końcowych, które można dodać, zobacz [Przydziały i ograniczanie przepustowości](iot-hub-devguide-quotas-throttling.md).

Można użyć interfejsu API REST [Get Endpoint Health,](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) aby uzyskać stan kondycji punktów końcowych. Zaleca się przy użyciu [metryki Usługi IoT Hub](iot-hub-metrics.md) związane z opóźnieniem komunikatu routingu do identyfikowania i debugowania błędów, gdy kondycja punktu końcowego jest martwy lub w złej kondycji, ponieważ spodziewamy się opóźnienia być wyższe, gdy punkt końcowy znajduje się w jednym z tych stanów.

|Stan kondycji|Opis|
|---|---|
|Zdrowe|Punkt końcowy jest akceptowanie komunikatów zgodnie z oczekiwaniami.|
|Niezdrowe|Punkt końcowy nie akceptuje komunikatów zgodnie z oczekiwaniami, a usługa IoT Hub ponawia próbę wysłania danych do tego punktu końcowego. Stan punktu końcowego w złej kondycji zostaną zaktualizowane do dobrej kondycji, gdy Centrum IoT ustanowił ostatecznie spójny stan kondycji.|
|unknown|Centrum IoT hub nie nawiązał połączenia z punktem końcowym. Żadne wiadomości nie zostały dostarczone lub odrzucone z tego punktu końcowego.|
|Martwe|Punkt końcowy nie jest akceptowanie wiadomości, po IoT Hub ponowione wysyłanie wiadomości dla okresu ponownego procesu.|

## <a name="field-gateways"></a>Bramy polowe

W rozwiązaniu IoT *brama polowa* znajduje się między urządzeniami a punktami końcowymi usługi IoT Hub. Zazwyczaj znajduje się w pobliżu twoich urządzeń. Urządzenia komunikują się bezpośrednio z bramą polową przy użyciu protokołu obsługiwanego przez urządzenia. Brama pól łączy się z punktem końcowym usługi IoT Hub przy użyciu protokołu obsługiwanego przez Centrum IoT Hub. Brama polowa może być dedykowanym urządzeniem sprzętowym lub komputerem o niskim pouczeszaniu z oprogramowaniem bramy niestandardowej.

Zaimplementowanie bramy polowej za pomocą [usługi Azure IoT Edge.](/azure/iot-edge/) Usługi IoT Edge oferują funkcje, takie jak multipleksowanie komunikacji z wielu urządzeń na tym samym połączeniu Usługi IoT Hub.

## <a name="next-steps"></a>Następne kroki

Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Język zapytań usługi IoT Hub dla bliźniaczych bliźniąt urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md)
* [Limity przydziału i ograniczanie wydajności](iot-hub-devguide-quotas-throttling.md)
* [Obsługa usługi IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Opis adresu IP centrum IoT](iot-hub-understand-ip-address.md)