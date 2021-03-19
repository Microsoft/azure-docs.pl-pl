---
title: Wyświetlanie metryk Azure Event Grid i Ustawianie alertów
description: W tym artykule opisano, jak używać Azure Portal do wyświetlania metryk dotyczących Azure Event Grid tematów i subskrypcji, a następnie tworzyć na nich alerty.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f6c119c16452246ec6eeb57ab392b29608938a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598562"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorowanie dostarczania komunikatów Event Grid 
W tym artykule opisano, jak używać portalu do wyświetlania metryk dotyczących tematów i subskrypcji Event Grid oraz tworzenia na nich alertów. 

> [!IMPORTANT]
> Listę obsługiwanych metryk Azure Event Grid można znaleźć w temacie [Metrics (metryki](metrics.md)).

## <a name="view-custom-topic-metrics"></a>Wyświetl metryki tematu niestandardowego

Po opublikowaniu tematu niestandardowego można wyświetlić metryki dla niego. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Na pasku wyszukiwania w temacie wpisz **Event Grid tematy**, a następnie wybierz pozycję **Event Grid tematy** z listy rozwijanej. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Wyszukaj i wybierz tematy Event Grid":::
3. Wybierz niestandardowy temat z listy tematów. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Wybieranie tematu niestandardowego":::
4. Wyświetl metryki dla niestandardowego tematu zdarzeń na stronie **tematu Event Grid** . Na poniższej ilustracji sekcja **podstawowe** informacje o grupie zasobów, subskrypcji itp. jest zminimalizowana. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Wyświetl metryki zdarzeń":::

    Wykresy z obsługiwanymi metrykami można tworzyć przy użyciu karty **metryki** na stronie **tematu Event Grid** .

    :::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Temat — Strona metryk":::

    Na przykład zapoznaj się z wykresem metryk dla metryki **opublikowanych zdarzeń** .

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Metryka opublikowanych zdarzeń":::


## <a name="view-subscription-metrics"></a>Wyświetl metryki subskrypcji
1. Przejdź do strony **tematu Event Grid** , wykonując czynności opisane w poprzedniej sekcji. 
2. Wybierz subskrypcję w dolnym okienku, jak pokazano w poniższym przykładzie. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Wybierz subskrypcję zdarzeń":::    

    Możesz również wyszukać **Event Grid subskrypcje** na pasku wyszukiwania w Azure Portal, wybrać **Typ tematu**, **subskrypcję** i **lokalizację** , aby wyświetlić subskrypcję zdarzeń. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Wybierz subskrypcję zdarzeń ze strony subskrypcji Event Grid":::        

    W przypadku tematów niestandardowych wybierz **Event Grid tematy** jako **Typ tematu**. W obszarze Tematy systemowe wybierz typ zasobu platformy Azure, na przykład **konta magazynu (BLOB, GPv2)**. 
3. Zapoznaj się z metrykami subskrypcji na stronie głównej subskrypcji na wykresie. Możesz wyświetlić metryki **Ogólne**, **Błędy** i **opóźnienia** dla ciągu ostatnich 1 godziny, 6 godzin, 12 godzin, 1 dzień, 7 dni lub 30 dni. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Metryki na stronie głównej subskrypcji":::    

## <a name="view-system-topic-metrics"></a>Wyświetl metryki tematu systemu

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Na pasku wyszukiwania w temacie wpisz **Event Grid tematy systemowe**, a następnie wybierz pozycję **Event Grid tematy systemowe** z listy rozwijanej. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Wyszukaj i wybierz Event Grid tematy systemowe":::
3. Wybierz temat system z listy tematów. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Wybieranie tematu systemu":::
4. Zapoznaj się z metrykami tematu system na stronie **tematu system Event Grid** . Na poniższej ilustracji sekcja **podstawowe** informacje o grupie zasobów, subskrypcji itp. jest zminimalizowana. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Wyświetl metryki tematu systemu na stronie Przegląd":::

    Wykresy z obsługiwanymi metrykami można tworzyć przy użyciu karty **metryki** na stronie **tematu Event Grid** .

    :::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Temat systemu-Strona metryki":::

    > [!IMPORTANT]
    > Listę obsługiwanych metryk Azure Event Grid można znaleźć w temacie [Metrics (metryki](metrics.md)).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- Aby dowiedzieć się, jak tworzyć alerty dotyczące metryk i operacji dziennika aktywności, zobacz [Ustawianie alertów](set-alerts.md).
- Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).
