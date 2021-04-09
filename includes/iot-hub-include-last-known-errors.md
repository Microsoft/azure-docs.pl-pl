---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 54f4835a904b897370cf9f075ae3c005b1114992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560958"
---
[Uzyskaj kondycję punktu końcowego](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) w interfejsie API REST zapewnia stan kondycji punktów końcowych, a także ostatni znany błąd, aby zidentyfikować przyczynę, w której punkt końcowy nie jest w dobrej kondycji. W poniższej tabeli wymieniono najbardziej typowe błędy.

|Ostatni znany błąd|Opis/gdy występuje|Możliwe środki zaradcze|
|-----|-----|-----|
|Administracyjnej|Wystąpił błąd przejściowy, a IoT Hub spróbuje ponownie wykonać operację.|Zwróć uwagę na [trasy dzienników zasobów](../articles/iot-hub/monitor-iot-hub-reference.md#routes).|
|InternalError|Wystąpił błąd podczas dostarczania komunikatu do punktu końcowego.|Jest to wewnętrzny wyjątek, ale również obserwuje [dzienniki zasobów trasy](../articles/iot-hub/monitor-iot-hub-reference.md#routes).|
|Brak autoryzacji|IoT Hub nie ma uprawnień do wysyłania komunikatów do określonego punktu końcowego.|Sprawdź, czy parametry połączenia są aktualne dla punktu końcowego. Jeśli została zmieniona, należy rozważyć aktualizację IoT Hub. Jeśli punkt końcowy korzysta z tożsamości zarządzanej, sprawdź, czy podmiot zabezpieczeń IoT Hub ma wymagane uprawnienia w elemencie docelowym.|
|Ograniczone|IoT Hub jest ograniczany podczas zapisywania komunikatów w punkcie końcowym.|Przejrzyj limity ograniczania dla odnośnego punktu końcowego. Zmodyfikuj konfiguracje dla punktu końcowego, aby w razie potrzeby skalować w górę.|
|Limit czasu|Limit czasu operacji.|Spróbuj ponownie wykonać operację.|
|Nie znaleziono|Zasób docelowy nie istnieje.|Upewnij się, że zasób docelowy istnieje.|
|Nie znaleziono kontenera|Kontener magazynu nie istnieje.|Upewnij się, że kontener magazynu istnieje.|
|Kontener wyłączony|Kontener magazynu jest wyłączony.|Upewnij się, że kontener magazynu jest włączony.|
|MaxMessageSizeExceeded|Routing komunikatów ma limit rozmiaru komunikatu równy 256 KB. rozmiar przesyłanego komunikatu przekracza ten limit.|Sprawdź, czy rozmiar komunikatu można zmniejszyć, używając mniejszej liczby właściwości aplikacji lub mniejszych wzbogacania komunikatów.|
|PartitioningAndDuplicateDetectionNotSupported|Usługa Service Bus może nie mieć włączonego wykrywania duplikatów.|Wyłącz wykrywanie duplikatów z poziomu Service Bus lub Rozważ użycie jednostki bez wykrywania duplikatów.|
|SessionfulEntityNotSupported|Usługa Service Bus może nie mieć włączonych sesji.|Wyłącz sesję z Service Bus lub Rozważ użycie jednostki bez sesji.|
|NoMatchingSubscriptionsForMessage|Brak subskrypcji do zapisu wiadomości w temacie usługi Service Bus.|Utwórz subskrypcję dla IoT Hub komunikatów, do których mają być kierowane.|
|EndpointExternallyDisabled|Punkt końcowy nie jest w stanie aktywnym, dlatego IoT Hub może wysyłać do niego komunikaty.|Włącz punkt końcowy, aby przywrócić stan aktywności.|
|DeviceMaximumQueueDepthExceeded|Osiągnięto limit rozmiaru usługi Service Bus.|Rozważ usunięcie komunikatów z Event Hubs docelowej, aby zezwolić na pozyskiwanie nowych komunikatów w Event Hubs.|