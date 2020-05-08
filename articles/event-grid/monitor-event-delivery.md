---
title: Monitorowanie dostarczania komunikatów Azure Event Grid
description: W tym artykule opisano, jak używać Azure Portal do wyświetlania stanu dostarczania komunikatów Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 7a01ab91fe84aaa1fe55018754eddbf8b8f89643
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890851"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorowanie dostarczania komunikatów Event Grid 

W tym artykule opisano sposób korzystania z portalu w celu wyświetlenia stanu dostaw zdarzeń.

Event Grid zapewnia trwałe dostarczanie. Każdy z nich dostarcza każdy komunikat co najmniej raz dla każdej subskrypcji. Zdarzenia są natychmiast wysyłane do zarejestrowanego elementu webhook każdej subskrypcji. Jeśli element webhook nie potwierdzi odbioru zdarzenia w ciągu 60 sekund od pierwszej próby dostarczenia, Event Grid ponawianie prób dostarczenia zdarzenia.

Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Metryki dostarczania

W portalu są wyświetlane metryki dotyczące stanu dostarczania komunikatów o zdarzeniach.

W przypadku tematów poniżej przedstawiono niektóre z tych metryk:

* **Publikowanie powiodło się**: zdarzenie zostało pomyślnie wysłane do tematu i przetworzone z odpowiedzią 2xx.
* **Publikowanie nie powiodło**się: zdarzenie wysłane do tematu, ale odrzucone z kodem błędu.
* **Niedopasowane**: zdarzenie zostało pomyślnie opublikowane w temacie, ale nie jest zgodne z subskrypcją zdarzeń. Zdarzenie zostało porzucone.

Poniżej przedstawiono niektóre metryki dotyczące subskrypcji:

* **Dostarczenie powiodło się**: zdarzenie zostało pomyślnie dostarczone do punktu końcowego subskrypcji i otrzymało odpowiedź 2xx.
* **Dostarczenie nie powiodło się**: za każdym razem, gdy usługa próbuje dostarczyć, a procedura obsługi zdarzeń nie zwraca kodu 2xx sukcesu, licznik **niepowodzeń dostarczania** jest zwiększany. Jeśli próba dostarczenia tego samego zdarzenia zostanie przebiegać wielokrotnie i nie powiedzie się, licznik **błąd dostarczania nie** zostanie zwiększony dla każdego błędu.
* **Wygasłe zdarzenia**: zdarzenie nie zostało dostarczone i wszystkie próby ponowienia zostały wysłane. Zdarzenie zostało porzucone.
* **Dopasowane zdarzenia**: zdarzenie w temacie zostało dopasowane przez subskrypcję zdarzeń.

    > [!NOTE]
    > Aby uzyskać pełną listę metryk, zobacz [metryki obsługiwane przez Azure Event Grid](metrics.md).

## <a name="event-subscription-status"></a>Stan subskrypcji zdarzeń

Aby wyświetlić metryki dla subskrypcji zdarzeń, można wyszukać według typu subskrypcji lub subskrypcji dla określonego zasobu.

Aby wyszukać według typu subskrypcji zdarzeń, wybierz pozycję **wszystkie usługi**.

![Wybierz wszystkie usługi](./media/monitor-event-delivery/all-services.png)

Wyszukaj ciąg **Event Grid** i wybierz opcję **Event Grid subskrypcje** z dostępnych opcji.

![Wyszukaj subskrypcje zdarzeń](./media/monitor-event-delivery/search-and-select.png)

Filtrowanie według typu zdarzenia, subskrypcji i lokalizacji. Wybierz **metryki** dla subskrypcji do wyświetlenia.

![Filtrowanie subskrypcji zdarzeń](./media/monitor-event-delivery/filter-events.png)

Wyświetl metryki dotyczące tematu i subskrypcji zdarzenia.

![Wyświetl metryki zdarzeń](./media/monitor-event-delivery/subscription-metrics.png)

Aby znaleźć metryki dla określonego zasobu, wybierz ten zasób. Następnie wybierz pozycję **zdarzenia**.

![Wybieranie zdarzeń dla zasobu](./media/monitor-event-delivery/select-events.png)

Są wyświetlane metryki dla subskrypcji dla tego zasobu.

## <a name="custom-event-status"></a>Stan zdarzenia niestandardowego

Po opublikowaniu tematu niestandardowego można wyświetlić metryki dla niego. Wybierz grupę zasobów dla tematu i wybierz temat.

![Wybieranie tematu niestandardowego](./media/monitor-event-delivery/select-custom-topic.png)

Wyświetl metryki dla niestandardowego tematu zdarzeń.

![Wyświetl metryki zdarzeń](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Ustawianie alertów

Możesz ustawić alerty dla tematu i metryk na poziomie domeny dla tematów niestandardowych i domen zdarzeń. W bloku przegląd dla programu wybierz pozycję **alerty** z menu po lewej stronie, w którym mają być wyświetlane reguły alertów, a następnie zarządzaj nimi. [Dowiedz się więcej o alertach Azure Monitor](../azure-monitor/platform/alerts-overview.md)

![Wyświetl metryki zdarzeń](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
