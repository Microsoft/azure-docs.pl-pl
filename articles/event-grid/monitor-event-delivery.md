---
title: Wyświetlanie metryk Azure Event Grid i Ustawianie alertów
description: W tym artykule opisano, jak używać Azure Portal do wyświetlania metryk dotyczących Azure Event Grid tematów i subskrypcji, a następnie tworzyć na nich alerty.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: spelluru
ms.openlocfilehash: e74d2d8982cac961aa65d6576c80a92cb53ce387
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100596"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorowanie dostarczania komunikatów Event Grid 
W tym artykule opisano, jak używać portalu do wyświetlania metryk dotyczących tematów i subskrypcji Event Grid oraz tworzenia na nich alertów. 

## <a name="metrics"></a>Metryki

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

Aby dowiedzieć się więcej o metrykach, zobacz [metryki w Azure monitor](../azure-monitor/platform/data-platform-metrics.md)

Na przykład zapoznaj się z wykresem metryk dla metryki **opublikowanych zdarzeń** .

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Metryka opublikowanych zdarzeń":::


## <a name="view-subscription-metrics"></a>Wyświetl metryki subskrypcji
1. Przejdź do strony **tematu Event Grid** , wykonując czynności opisane w poprzedniej sekcji. 
2. Wybierz subskrypcję w dolnym okienku, jak pokazano w poniższym przykładzie. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Wybierz subskrypcję zdarzeń":::    

    Możesz również wyszukać **Event Grid subskrypcje** na pasku wyszukiwania w Azure Portal, wybrać **Typ tematu**, **subskrypcję**i **lokalizację** , aby wyświetlić subskrypcję zdarzeń. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Wybierz subskrypcję zdarzeń ze strony subskrypcji Event Grid":::        

    W przypadku tematów niestandardowych wybierz **Event Grid tematy** jako **Typ tematu**. W obszarze Tematy systemowe wybierz typ zasobu platformy Azure, na przykład **konta magazynu (BLOB, GPv2)**. 
3. Zapoznaj się z metrykami subskrypcji na stronie głównej subskrypcji na wykresie. Metryki **Ogólne**, **Błędy**, **opóźnienia**i **utraconych liter** można zobaczyć w ciągu ostatnich 1 godziny, 6 godzin, 12 godzin, 1 dzień, 7 dni lub 30 dni. 

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

Aby dowiedzieć się więcej o metrykach, zobacz [metryki w Azure monitor](../azure-monitor/platform/data-platform-metrics.md)


## <a name="set-alerts"></a>Ustawianie alertów
Można ustawić alerty dotyczące metryk dla tematów i domen w Azure Portal. 

Poniższa procedura pokazuje, jak utworzyć alert dotyczący metryki **zdarzeń utraconych** dla niestandardowo tematu. W tym przykładzie wiadomość e-mail jest wysyłana do właściciela grupy zasobów platformy Azure, gdy liczba utraconych zdarzeń dla tematu przekracza 10. 

1. Na stronie **tematu Event Grid** tematu wybierz pozycję **alerty** w menu po lewej stronie, a następnie wybierz pozycję **+ Nowa reguła alertu**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Strona alertów — przycisk Nowa reguła alertu":::
    
    
    Alerty można także tworzyć przy użyciu strony **metryki** . Kroki są podobne. 

    :::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Strona metryk — przycisk Utwórz alert":::   
    
2. Na stronie **Tworzenie reguły alertu** upewnij się, że wybrany został temat dla zasobu. Następnie kliknij pozycję **Wybierz warunek**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Strona alertów — wybierz warunek":::    
3. Na stronie **Konfigurowanie logiki sygnału** wykonaj następujące kroki:
    1. Wybierz metrykę lub wpis dziennika aktywności. W tym przykładzie wybrano **zdarzenia z utraconymi wiadomościami** . 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Wybierz zdarzenia utraconych wiadomości":::        
    2. Wybierz wymiary (opcjonalnie). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Konfigurowanie logiki sygnału":::        
    3. Przewiń w dół. W sekcji **logika alertu** wybierz **operator**, **typ agregacji**i wprowadź **wartość progową**, a następnie wybierz pozycję **gotowe**. W tym przykładzie alert jest wyzwalany, gdy łączna liczba utraconych zdarzeń jest większa niż 10. 
    
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

    > [!NOTE]
    > Ten artykuł nie obejmuje wszystkich różnych kroków i kombinacji, których można użyć do utworzenia alertu. Aby zapoznać się z omówieniem alertów, zobacz [Omówienie alertów](../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
