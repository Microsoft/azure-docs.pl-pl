---
title: Limity usługi publicznej wersji zapoznawczej — usługa Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się więcej na temat usług Azure Digital bliźniaczych reprezentacji, subskrypcji, wystąpień i limitów szybkości.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370387"
---
# <a name="public-preview-service-limits"></a>Limity usługi w publicznej wersji zapoznawczej

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

W publicznej wersji zapoznawczej usługa Azure Digital bliźniaczych reprezentacji ma następującą tymczasową subskrypcję, wystąpienie i limity szybkości dla istniejących klientów. Te ograniczenia istnieją, aby uprościć uczenie się o nowej usłudze i jej wielu funkcjach, a także zwiększyć lub usunąć ją z ogólnej dostępności.

## <a name="per-subscription-limits"></a>Limity dla subskrypcji

W publicznej wersji zapoznawczej Każda subskrypcja platformy Azure może w danym momencie utworzyć lub uruchomić tylko jedno wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Jeśli usuniesz wystąpienie, możesz utworzyć nowe.

## <a name="per-instance-limits"></a>Limity dla wystąpień

Z kolei każde wystąpienie usługi Azure Digital bliźniaczych reprezentacji może mieć następujące możliwości:

- Dokładnie jeden osadzony zasób **IoTHub** , który jest tworzony automatycznie podczas aprowizacji usługi.
- Dokładnie jeden punkt końcowy **EventHub** dla typu zdarzenia **DeviceMessage**.
- Maksymalnie trzy punkty końcowe **EventHub**, **ServiceBus**lub **EventGrid** typu zdarzenia **SensorChange**, **SpaceChange**, **TopologyOperation**lub **UdfCustom**.

> [!NOTE]
> Niektóre parametry, które są zwykle zdefiniowane w procesie tworzenia powyższych jednostek usługi Azure IoT, nie są wymagane w publicznej wersji zapoznawczej.
> - Aby zapoznać się z najnowszymi specyfikacjami interfejsu API, zapoznaj się z [dokumentacją dotyczącą struktury Swagger](./how-to-use-swagger.md) .

## <a name="azure-digital-twins-management-api-limits"></a>Limity interfejsu API usługi Azure Digital bliźniaczych reprezentacji Management

Limity liczby żądań dla interfejsu API usługi Azure Digital bliźniaczych reprezentacji Management to:

- 100 żądań na sekundę do interfejsu API usługi Azure Digital bliźniaczych reprezentacji Management.
- Do 1 000 obiektów zwracanych przez pojedyncze zapytanie interfejsu API zarządzania bliźniaczych reprezentacji Azure Digital.

> [!IMPORTANT]
> W przypadku przekroczenia limitu 1 000-obiektowego występuje błąd i musi uprościć zapytanie.

## <a name="user-defined-functions-rate-limits"></a>Limity szybkości funkcji zdefiniowanych przez użytkownika

Poniższe limity ustawiają łączną liczbę wszystkich wywołań funkcji zdefiniowanych przez użytkownika w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji:

- 400 wywołań biblioteki klienta na sekundę
- 100 wywołań **SendNotification** na sekundę

> [!NOTE]
> Następujące akcje mogą spowodować tymczasowe stosowanie dodatkowych limitów szybkości:
> - Zmiany wprowadzone w metadanych obiektu topologii
> - Aktualizacje wprowadzone w definicji funkcji zdefiniowanej przez użytkownika
> - Urządzenia wysyłające dane telemetryczne po raz pierwszy

## <a name="device-telemetry-limits"></a>Limity telemetrii urządzeń

Następujące ograniczenia ograniczają łączną liczbę wszystkich komunikatów, które urządzenia mogą wysłać do wystąpienia usługi Azure Digital bliźniaczych reprezentacji:

- 100 komunikatów na sekundę na wszystkich urządzeniach
-    25 komunikatów na sekundę na urządzenie

## <a name="next-steps"></a>Następne kroki

- Aby wypróbować próbkę Digital bliźniaczych reprezentacji na platformie Azure, przejdź do [przewodnika Szybki Start, aby znaleźć dostępne pokoje](./quickstart-view-occupancy-dotnet.md).
