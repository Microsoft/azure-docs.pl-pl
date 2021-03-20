---
title: Rozwiązywanie problemów z błędami wewnętrznymi usługi Azure IoT Hub 500xxx
description: Dowiedz się, jak naprawić błędy wewnętrzne 500xxx
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1333a135f3e123757e268513f73e8329537e630b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92538208"
---
# <a name="500xxx-internal-errors"></a>500xxx błędy wewnętrzne

W tym artykule opisano przyczyny i rozwiązania **500xxx błędów wewnętrznych**.

## <a name="symptoms"></a>Objawy

Żądanie IoT Hub kończy się niepowodzeniem z powodu błędu rozpoczynającego się od 500 i/lub niektórych rodzajów "błędu serwera". Poniżej przedstawiono niektóre możliwości:

* **500001 błąd servererror** IoT Hub: Wystąpił problem po stronie serwera.

* **500008 GenericTimeout**: IoT Hub nie może zakończyć żądania połączenia przed upływem limitu czasu.

* **Serviceniedostępny (brak kodu błędu)**: IoT Hub napotkał błąd wewnętrzny.

* **InternalServerError (brak kodu błędu)**: IoT Hub napotkał błąd wewnętrzny.

## <a name="cause"></a>Przyczyna

Może istnieć wiele przyczyn odpowiedzi na błąd 500xxx. We wszystkich przypadkach problem jest najprawdopodobniej przejściowy. Mimo że zespół IoT Hub działa trudno, aby zachować umowę [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), małe podzestawy węzłów IoT Hub mogą czasami powodować błędy przejściowe. Gdy urządzenie próbuje nawiązać połączenie z węzłem, który ma problemy, zostanie wyświetlony ten błąd.

## <a name="solution"></a>Rozwiązanie

Aby wyeliminować błędy 500xxx, wydaj ponownie urządzenie. Aby [automatycznie zarządzać ponownymi próbami](./iot-hub-reliability-features-in-sdks.md#connection-and-retry), upewnij się, że korzystasz z najnowszej wersji [zestawów SDK usługi Azure IoT](./iot-hub-devguide-sdks.md). Najlepszym rozwiązaniem w przypadku przejściowej obsługi błędów i ponownych prób zapoznaj się z tematem [Obsługa błędów przejściowych](/azure/architecture/best-practices/transient-faults).  Jeśli problem będzie nadal występować, sprawdź [Resource Health](./iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) i [Stan platformy Azure](https://status.azure.com/) , aby sprawdzić, czy IoT Hub ma znany problem. Możesz również użyć [funkcji ręcznego przełączania do trybu failover](./tutorial-manual-failover.md). Jeśli nie występują żadne znane problemy, a problem nie zniknie, [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/options/) w celu uzyskania dalszych badań.
