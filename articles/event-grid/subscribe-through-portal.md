---
title: Subskrypcje usługi Azure Event Grid za pośrednictwem portalu
description: W tym artykule opisano sposób tworzenia subskrypcji usługi Event Grid dla obsługiwanych źródeł, takich jak usługa Azure Blob Storage, przy użyciu witryny Azure portal.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 599f48ed241010d8551bd110c7f778c9ef508eac
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393177"
---
# <a name="subscribe-to-events-through-portal"></a>Subskrybowanie wydarzeń za pośrednictwem portalu

W tym artykule opisano sposób tworzenia subskrypcji usługi Event Grid za pośrednictwem portalu.

## <a name="create-event-subscriptions"></a>Tworzenie subskrypcji zdarzeń

Aby utworzyć subskrypcję usługi Event Grid dla dowolnego [z obsługiwanych źródeł zdarzeń,](overview.md#event-sources)należy wykonać następujące kroki. W tym artykule pokazano, jak utworzyć subskrypcję usługi Event Grid dla subskrypcji platformy Azure.

1. Wybierz pozycję **Wszystkie usługi**.

   ![Wybierz wszystkie usługi](./media/subscribe-through-portal/select-all-services.png)

1. Wyszukaj **subskrypcje w siatce zdarzeń** i wybierz ją z dostępnych opcji.

   ![Wyszukiwanie](./media/subscribe-through-portal/search.png)

1. Wybierz pozycję **+ Subskrypcja zdarzeń**.

   ![Dodawanie subskrypcji](./media/subscribe-through-portal/add-subscription.png)

1. Wybierz typ subskrypcji, którą chcesz utworzyć. Na przykład, aby zasubskrybować zdarzenia dla subskrypcji platformy Azure, wybierz **subskrypcje platformy Azure** i subskrypcję docelową.

   ![Wybierz subskrypcję platformy Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Aby zasubskrybować wszystkie typy zdarzeń dla tego źródła zdarzeń, należy zachować opcję **Subskrybuj wszystkie typy zdarzeń.** W przeciwnym razie wybierz typy zdarzeń dla tej subskrypcji.

   ![Wybierz typy zdarzeń](./media/subscribe-through-portal/select-event-types.png)

1. Podaj dodatkowe szczegóły dotyczące subskrypcji zdarzeń, takie jak punkt końcowy obsługi zdarzeń i nazwa subskrypcji.

   ![Podaj szczegóły subskrypcji](./media/subscribe-through-portal/provide-subscription-details.png)

1. Aby włączyć martwe literowanie i dostosować zasady ponawiania prób, wybierz opcję **Dodatkowe funkcje**.

   ![Wybieranie dodatkowych obiektów](./media/subscribe-through-portal/select-additional-features.png)

1. Wybierz kontener do przechowywania zdarzeń, które nie są dostarczane, i ustawić sposób wysyłania ponownych prób.

   ![Włączanie martwego napisu i ponawiania próby](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Po zakończeniu wybierz pozycję **Utwórz**.

## <a name="create-subscription-on-resource"></a>Tworzenie subskrypcji w zasobie

Niektóre źródła zdarzeń obsługują tworzenie subskrypcji zdarzeń za pośrednictwem interfejsu portalu dla tego zasobu. Wybierz źródło zdarzenia i **poszukaj zdarzenia** w lewym okienku.

![Podaj szczegóły subskrypcji](./media/subscribe-through-portal/resource-events.png)

Portal przedstawia opcje tworzenia subskrypcji zdarzeń, które są istotne dla tego źródła.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o dostarczaniu zdarzeń i ponownych próbach, [w ucho.](delivery-and-retry.md)
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z usługi Event Grid, zobacz [Tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
