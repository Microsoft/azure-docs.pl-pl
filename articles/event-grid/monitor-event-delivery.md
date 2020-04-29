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
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76722139"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorowanie dostarczania komunikatów Event Grid 

W tym artykule opisano sposób korzystania z portalu w celu wyświetlenia stanu dostaw zdarzeń.

Event Grid zapewnia trwałe dostarczanie. Każdy z nich dostarcza każdy komunikat co najmniej raz dla każdej subskrypcji. Zdarzenia są natychmiast wysyłane do zarejestrowanego elementu webhook każdej subskrypcji. Jeśli element webhook nie potwierdzi odbioru zdarzenia w ciągu 60 sekund od pierwszej próby dostarczenia, Event Grid ponawianie prób dostarczenia zdarzenia.

Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Metryki dostarczania

W portalu są wyświetlane metryki dotyczące stanu dostarczania komunikatów o zdarzeniach.

W przypadku tematów miary są następujące:

* **Publikowanie powiodło się**: zdarzenie zostało pomyślnie wysłane do tematu i przetworzone z odpowiedzią 2xx.
* **Publikowanie nie powiodło**się: zdarzenie wysłane do tematu, ale odrzucone z kodem błędu.
* **Niedopasowane**: zdarzenie zostało pomyślnie opublikowane w temacie, ale nie jest zgodne z subskrypcją zdarzeń. Zdarzenie zostało porzucone.

W przypadku subskrypcji metryki są następujące:

* **Dostarczenie powiodło się**: zdarzenie zostało pomyślnie dostarczone do punktu końcowego subskrypcji i otrzymało odpowiedź 2xx.
* **Dostarczenie nie powiodło**się: zdarzenie wysłane do punktu końcowego subskrypcji, ale odebrano odpowiedź 4xx lub 5xx.
* **Wygasłe zdarzenia**: zdarzenie nie zostało dostarczone i wszystkie próby ponowienia zostały wysłane. Zdarzenie zostało porzucone.
* **Dopasowane zdarzenia**: zdarzenie w temacie zostało dopasowane przez subskrypcję zdarzeń.

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
