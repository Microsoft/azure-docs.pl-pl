---
title: plik dołączany
description: plik dołączany
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 362c13771e7382ead1ba5aebd99a69fd86cd718c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793317"
---
<!-- operation names for the diag logs for IoT Hub -->

|Nazwa operacji|Poziom|Opis|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Informacje|Nie można obliczyć komunikatu z warunkiem dającym. Na przykład jeśli w komunikacie nie ma właściwości w warunku kwerendy trasy. Dowiedz się więcej o [składni zapytań routingu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).|
|RouteEvaluationError|Błąd|Wystąpił błąd podczas oceny komunikatu z powodu problemu z formatem wiadomości. Na przykład ten błąd zostanie zarejestrowany, jeśli nie określono kodowania zawartości lub typ zawartości jest nieprawidłowy w komunikacie. Należy je ustawić we [właściwościach systemu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties).|
|DroppedMessage|Błąd|Komunikat został porzucony i nie został rozesłany. Może to być spowodowane przyczynami braku zgodności komunikatów routingu lub punktów końcowych, a komunikat nie mógł zostać dostarczony po kilku ponownych próbach. Zalecamy uzyskanie dalszych informacji o punkcie końcowym za pomocą interfejsu API REST [Uzyskaj kondycję punktu końcowego](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointUnhealthy|Błąd|Punkt końcowy nie akceptuje komunikatów z IoT Hub i IoT Hub próbuje ponownie wysłać komunikaty. Zalecamy zaobserwowanie ostatniego znanego błędu za pośrednictwem interfejsu API REST [Uzyskaj kondycję punktu końcowego](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointDead|Błąd|Punkt końcowy nie akceptuje komunikatów od IoT Hub przez ponad godzinę. Zalecamy zaobserwowanie ostatniego znanego błędu za pośrednictwem interfejsu API REST [Uzyskaj kondycję punktu końcowego](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointHealthy|Informacje|Punkt końcowy jest w dobrej kondycji i otrzymuje komunikaty z IoT Hub. Ten komunikat nie jest rejestrowany w sposób ciągły, ale jest rejestrowany tylko wtedy, gdy punkt końcowy jest w dobrej kondycji. Ten komunikat oznacza, że IoT Hub nie mógł wysłać komunikatów do punktu końcowego, ale punkt końcowy jest teraz w dobrej kondycji.|
|OrphanedMessage|Informacje|Komunikat nie jest zgodny z żadną trasą.|
|InvalidMessage|Błąd|Komunikat jest nieprawidłowy z powodu niezgodności z punktem końcowym. Zalecamy sprawdzenie konfiguracji punktu końcowego.|


Operacje *UndefinedRouteEvaluation*, *RouteEvaluationError* i *OrphanedMessage* są ograniczone i rejestrowane nie częściej niż raz na minutę za IoT Hub.