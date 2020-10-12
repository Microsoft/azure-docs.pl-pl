---
title: plik dołączany
description: plik dołączany
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d03579f704879bd8d012bb0bb326659d1f778dee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793331"
---
[Uzyskaj kondycję punktu końcowego](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) w interfejsie API REST zapewnia stan kondycji punktów końcowych, a także ostatni znany błąd, aby zidentyfikować przyczynę, w której punkt końcowy nie jest w dobrej kondycji. W poniższej tabeli wymieniono najbardziej typowe błędy.

|Ostatni znany błąd|Opis/gdy występuje|Możliwe środki zaradcze|
|-----|-----|-----|
|Administracyjnej|Wystąpił błąd przejściowy, a IoT Hub spróbuje ponownie wykonać operację.|Obserwuj trasy [dzienników diagnostycznych](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes).|
|InternalError|Wystąpił błąd podczas dostarczania komunikatu do punktu końcowego.|Jest to wewnętrzny wyjątek, ale również obserwuje [dzienniki diagnostyczne](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes)tras.|
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