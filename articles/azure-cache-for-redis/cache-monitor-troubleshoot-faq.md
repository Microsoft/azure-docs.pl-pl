---
title: Pamięć podręczna systemu Azure na potrzeby monitorowania Redis i rozwiązywania problemów
description: Poznaj odpowiedzi na często zadawane pytania, które ułatwiają monitorowanie i rozwiązywanie problemów z usługą Azure cache for Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537528"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Pamięć podręczna systemu Azure na potrzeby monitorowania Redis i rozwiązywania problemów
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące sposobu monitorowania i rozwiązywania problemów z usługą Azure cache for Redis.

## <a name="common-questions-and-answers"></a>Typowe pytania i odpowiedzi
W tej sekcji omówiono następujące często zadawane pytania:

* [Jak mogę monitorować kondycję i wydajność mojej pamięci podręcznej?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Dlaczego widzę limity czasu?](#why-am-i-seeing-timeouts)
* [Dlaczego mój klient odłączył się od pamięci podręcznej?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Jak mogę monitorować kondycję i wydajność mojej pamięci podręcznej?
Pamięć podręczna Microsoft Azure wystąpień Redis można monitorować w [Azure Portal](https://portal.azure.com). Możesz wyświetlić metryki, przypiąć wykresy metryk do tablicy startowej, dostosować zakres daty i godziny wykresów monitorowania, dodać i usunąć metryki z wykresów i ustawić alerty w przypadku spełnienia określonych warunków. Aby uzyskać więcej informacji, zobacz [monitorowanie usługi Azure cache for Redis](cache-how-to-monitor.md).

**Menu zasobów** usługi Azure cache for Redis zawiera również kilka narzędzi do monitorowania i rozwiązywania problemów z pamięcią podręczną.

* **Diagnozowanie i rozwiązywanie problemów** zawiera informacje o typowych problemach i strategiach ich rozwiązywania.
* **Kondycja zasobu** obserwuje zasób i informuje o tym, że działa zgodnie z oczekiwaniami. Aby uzyskać więcej informacji o usłudze Azure Resource Health, zobacz [Omówienie usługi Azure Resource Health](../service-health/resource-health-overview.md).
* **Nowe żądanie obsługi** oferuje opcje otwierania żądania obsługi dla pamięci podręcznej.

Te narzędzia umożliwiają monitorowanie kondycji pamięci podręcznej platformy Azure dla wystąpień Redis i ułatwiają zarządzanie aplikacjami buforowania. Aby uzyskać więcej informacji, zobacz sekcję "Obsługa & ustawień rozwiązywania problemów" w temacie [jak skonfigurować usługę Azure cache for Redis](cache-configure.md).

### <a name="why-am-i-seeing-timeouts"></a>Dlaczego widzę limity czasu?
Limity czasu są wykonywane na kliencie, który jest używany do komunikowania się z Redis. Gdy polecenie jest wysyłane do serwera Redis, polecenie jest umieszczane w kolejce, a serwer Redis ostatecznie wybiera polecenie i wykonuje je. Jednak klient może przekroczyć limit czasu w trakcie tego procesu, a jeśli zostanie zgłoszony wyjątek na stronie wywołującej. Aby uzyskać więcej informacji na temat rozwiązywania problemów z limitem czasu, zobacz [wyjątki limitów czasu](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions) [po stronie klienta](cache-troubleshoot-client.md) i stackexchange. Redis.

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Dlaczego mój klient odłączył się od pamięci podręcznej?
Poniżej przedstawiono kilka typowych przyczyn odłączenia pamięci podręcznej.

* Przyczyny po stronie klienta
  * Aplikacja kliencka została wdrożona ponownie.
  * Aplikacja kliencka wykonała operację skalowania.
    * W przypadku Cloud Services lub Web Apps może to być spowodowane skalowaniem automatycznym.
  * Zmieniono warstwę sieciową po stronie klienta.
  * Wystąpił błąd przejściowy w kliencie lub w węzłach sieciowych między klientem a serwerem.
  * Osiągnięto limity progu przepustowości.
  * Operacje związane z procesorem CPU trwały zbyt długo.
* Przyczyny po stronie serwera
  * W przypadku standardowej oferty pamięci podręcznej usługa Azure cache for Redis zainicjowała przechodzenie w tryb failover z węzła podstawowego do węzła repliki.
  * Platforma Azure nastąpiła poprawka do wystąpienia, w którym została wdrożona pamięć podręczna
    * Może to dotyczyć aktualizacji serwera Redis lub ogólnej konserwacji maszyn wirtualnych.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania i rozwiązywania problemów z pamięcią podręczną platformy Azure dla wystąpień Redis, zobacz [Jak monitorować usługę Azure cache for Redis](cache-how-to-monitor.md) i różne przewodniki dotyczące rozwiązywania problemów.

Dowiedz się więcej o usłudze [Azure cache for Redis — często zadawane pytania](cache-faq.md).