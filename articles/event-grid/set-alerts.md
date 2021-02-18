---
title: Ustawianie alertów dla Azure Event Grid metryk i operacji dziennika aktywności
description: W tym artykule opisano sposób tworzenia alertów dotyczących Azure Event Grid metryk i operacji dziennika aktywności.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48cb402e31435cb3e9390e8aeb461fcc5f90702f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572012"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Ustawianie alertów dotyczących Azure Event Grid metryk i dzienników aktywności
W tym artykule opisano sposób tworzenia alertów dotyczących Azure Event Grid metryk i operacji dziennika aktywności. Możesz tworzyć alerty dotyczące metryk publikowania i dostarczania dla zasobów Azure Event Grid (tematów i domen). Aby uzyskać informacje dotyczące systemu, [Utwórz alerty przy użyciu strony **metryki**](#create-alerts-using-the-metrics-page).

## <a name="create-alerts-on-dead-lettered-events"></a>Tworzenie alertów dotyczących zdarzeń utraconych
Poniższa procedura pokazuje, jak utworzyć alert dotyczący metryki **zdarzeń utraconych** dla niestandardowo tematu. W tym przykładzie wiadomość e-mail jest wysyłana do właściciela grupy zasobów platformy Azure, gdy liczba utraconych zdarzeń dla tematu przekracza 10. 

1. Na stronie **tematu Event Grid** tematu wybierz pozycję **alerty** w menu po lewej stronie, a następnie wybierz pozycję **+ Nowa reguła alertu**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Strona alertów — przycisk Nowa reguła alertu":::
2. Na stronie **Tworzenie reguły alertu** upewnij się, że wybrany został temat dla zasobu. Następnie kliknij pozycję **Wybierz warunek**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Strona alertów — wybierz warunek":::    
3. Na stronie **Konfigurowanie logiki sygnału** wykonaj następujące kroki:
    1. Wybierz metrykę lub wpis dziennika aktywności. W tym przykładzie wybrano **zdarzenia z utraconymi wiadomościami** . 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Wybierz zdarzenia utraconych wiadomości":::        
    2. Wybierz wymiary (opcjonalnie). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Konfigurowanie logiki sygnału":::        

        > [!NOTE]
        > Możesz wybrać **+** przycisk **EventSubscriptionName** , aby określić nazwę subskrypcji zdarzenia do filtrowania zdarzeń. 
    3. Przewiń w dół. W sekcji **logika alertu** wybierz **operator**, **typ agregacji** i wprowadź **wartość progową**, a następnie wybierz pozycję **gotowe**. W tym przykładzie alert jest wyzwalany, gdy łączna liczba utraconych zdarzeń jest większa niż 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Logika alertu":::                
4. Na stronie **Tworzenie reguły alertu** kliknij pozycję **Wybierz grupę akcji**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Przycisk Wybierz grupę akcji":::
5. Wybierz pozycję **Utwórz grupę akcji** na pasku narzędzi, aby utworzyć nową grupę akcji. Możesz również wybrać istniejącą grupę akcji.        
6. Na stronie **Dodawanie grupy akcji** wykonaj następujące kroki:
    1. Wprowadź **nazwę grupy akcji**.
    1. Wprowadź **krótką nazwę** grupy akcji.
    1. Wybierz **subskrypcję platformy Azure** , w której chcesz utworzyć grupę akcji.
    1. Wybierz **grupę zasobów platformy Azure** , w której chcesz utworzyć grupę akcji.
    1. Wprowadź **nazwę akcji**. 
    1. Wybierz **Typ akcji**. W tym przykładzie wybrano **rolę Azure Resource Manager poczty e-mail** , a w odróżnieniu od roli **właściciele** . 
    1. Wybierz **przycisk OK** , aby zamknąć stronę. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Strona dodawania grupy akcji":::                   
7. Na stronie **Tworzenie reguły alertu** wprowadź nazwę reguły alertu, a następnie wybierz pozycję **Utwórz regułę alertu**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Nazwa reguły alertu":::  
8. Teraz na stronie **alerty** w temacie zostanie wyświetlony link umożliwiający zarządzanie regułami alertów, jeśli nie ma jeszcze żadnych alertów. W przypadku alertów wybierz pozycję **reguły alertów Menedżera** na pasku narzędzi.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Zarządzanie alertami":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>Tworzenie alertów dotyczących innych operacji związanych z metrykami lub dziennikiem aktywności
W poprzedniej sekcji pokazano, jak tworzyć alerty dotyczące utraconych zdarzeń. Czynności związane z tworzeniem alertów dotyczących innych miar lub operacji dziennika aktywności są podobne. 

Na przykład aby utworzyć alert dotyczący zdarzenia błędu dostarczania, na stronie **Konfigurowanie logiki sygnałów** wybierz pozycję **Dostarcz zdarzenia zakończone niepowodzeniem** . 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="Wybieranie zdarzeń zakończonych niepowodzeniem":::


## <a name="create-alerts-using-the-metrics-page"></a>Tworzenie alertów przy użyciu strony metryki
Alerty można także tworzyć przy użyciu strony **metryki** . Kroki są podobne. W przypadku tematów systemowych można używać tylko strony **metryk** do tworzenia alertów, ponieważ strona **alerty** nie jest dostępna. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Strona metryk — przycisk Utwórz alert":::   
    

> [!NOTE]
> Ten artykuł nie obejmuje wszystkich różnych kroków i kombinacji, których można użyć do utworzenia alertu. Aby zapoznać się z omówieniem alertów, zobacz [Omówienie alertów](../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
