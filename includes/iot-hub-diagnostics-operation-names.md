---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7d6718fb25b3743a50c52f11c8e19d80839b485c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95555822"
---
<!-- operation names for the diag logs for IoT Hub -->

|Nazwa operacji|Poziom|Opis|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Informacje|Nie można obliczyć komunikatu z warunkiem dającym. Na przykład jeśli w komunikacie nie ma właściwości w warunku kwerendy trasy. Dowiedz się więcej o [składni zapytań routingu](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md).|
|RouteEvaluationError|Błąd|Wystąpił błąd podczas oceny komunikatu z powodu problemu z formatem wiadomości. Na przykład ten błąd zostanie zarejestrowany, jeśli nie określono kodowania zawartości lub typ zawartości jest nieprawidłowy w komunikacie. Należy je ustawić we [właściwościach systemu](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties).|
|DroppedMessage|Błąd|Komunikat został porzucony i nie został rozesłany. Może to być spowodowane przyczynami braku zgodności komunikatów routingu lub punktów końcowych, a komunikat nie mógł zostać dostarczony po kilku ponownych próbach. Zalecamy uzyskanie dalszych informacji o punkcie końcowym za pomocą interfejsu API REST [Uzyskaj kondycję punktu końcowego](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointUnhealthy|Błąd|Punkt końcowy nie akceptuje komunikatów z IoT Hub i IoT Hub próbuje ponownie wysłać komunikaty. Zalecamy zaobserwowanie ostatniego znanego błędu za pośrednictwem interfejsu API REST [Uzyskaj kondycję punktu końcowego](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointDead|Błąd|Punkt końcowy nie akceptuje komunikatów od IoT Hub przez ponad godzinę. Zalecamy zaobserwowanie ostatniego znanego błędu za pośrednictwem interfejsu API REST [Uzyskaj kondycję punktu końcowego](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointHealthy|Informacje|Punkt końcowy jest w dobrej kondycji i otrzymuje komunikaty z IoT Hub. Ten komunikat nie jest rejestrowany w sposób ciągły, ale jest rejestrowany tylko wtedy, gdy punkt końcowy jest w dobrej kondycji. Ten komunikat oznacza, że IoT Hub nie mógł wysłać komunikatów do punktu końcowego, ale punkt końcowy jest teraz w dobrej kondycji.|
|OrphanedMessage|Informacje|Komunikat nie jest zgodny z żadną trasą.|
|InvalidMessage|Błąd|Komunikat jest nieprawidłowy z powodu niezgodności z punktem końcowym. Zalecamy sprawdzenie konfiguracji punktu końcowego.|


Operacje *UndefinedRouteEvaluation*, *RouteEvaluationError* i *OrphanedMessage* są ograniczone i rejestrowane nie częściej niż raz na minutę za IoT Hub.