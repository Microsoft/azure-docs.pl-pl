---
title: Tworzenie alertów dziennika aktywności dla laboratoriów w Azure DevTest Labs
description: Ten artykuł zawiera instrukcje tworzenia alertów dziennika aktywności dla laboratorium w Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f774e3291961c58f55a9ed24026535e076235b98
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588768"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Tworzenie alertów dziennika aktywności dla laboratoriów w Azure DevTest Labs
W tym artykule wyjaśniono, jak utworzyć alerty dziennika aktywności dla laboratoriów w Azure DevTest Labs (na przykład: Kiedy maszyna wirtualna jest tworzona lub po usunięciu maszyny wirtualnej).

## <a name="create-alerts"></a>Tworzenie alertów
W tym przykładzie utworzysz alert dla wszystkich operacji administracyjnych w laboratorium z akcją, która wysyła wiadomość e-mail do właścicieli subskrypcji. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku wyszukiwania Azure Portal wpisz **monitor**, a następnie wybierz pozycję **monitor** z listy wyników. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Wyszukaj monitor":::        
1. W menu po lewej stronie wybierz pozycję **alerty** , a następnie wybierz pozycję **Nowa reguła alertu** na pasku narzędzi. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Strona alerty":::    
1. Na stronie **Tworzenie reguły alertu** kliknij pozycję **Wybierz zasób**. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Wybierz zasób dla alertu":::        
1. Wybierz pozycję **DevTest Labs** for **Filter według typu zasobu**, wybierz laboratorium z listy, a następnie wybierz pozycję **gotowe**.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Wybierz swoje laboratorium jako zasób":::
1. Na stronie **Tworzenie reguły alertu** kliknij pozycję **Wybierz warunek**. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Link wybierania warunku":::    
1. Na stronie **Konfiguruj logikę sygnału** wybierz Sygnał obsługiwany przez DevTest Labs. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Wybierz sygnał":::
1. Filtruj według **poziomu zdarzenia** (verbose, informacyjny, ostrzeżenie, błąd, krytyczne, wszystkie), **stan** (niepowodzenie, rozpoczęte, zakończone powodzeniem) i **kto zainicjował** zdarzenie. 
1. Wybierz pozycję **gotowe** , aby zakończyć konfigurowanie warunku. 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Konfigurowanie logiki sygnałów — gotowe":::
1. Określono dla zakresu (laboratorium) i warunek dla alertu. Teraz należy określić grupę akcji z akcjami do uruchomienia, gdy warunek zostanie spełniony. Na stronie **Tworzenie reguły alertu** wybierz **pozycję Wybierz grupę akcji**. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Wybierz łącze grupy akcji":::
1. Wybierz łącze **Utwórz grupę akcji** na pasku narzędzi. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Utwórz łącze do grupy akcji":::
1. Na stronie **Dodawanie grupy akcji** wykonaj następujące kroki:
    1. Wprowadź **nazwę** grupy akcji.
    1. Wprowadź **krótką nazwę** grupy akcji. 
    1. Wybierz **grupę zasobów** , w której ma zostać utworzony alert. 
    1. Wprowadź **nazwę akcji**. 
    1. Wybierz **Typ akcji** (w tym przykładzie **wiadomość e-mail Azure Resource Manager rolę**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Strona dodawania grupy akcji":::
    1. Na stronie **rola Azure Resource Manager e-mail** wybierz rolę. W tym przykładzie jest to **właściciel**. Następnie wybierz przycisk **OK**. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Wybierz rolę":::            
    1. Na stronie **Dodaj grupę akcji** wybierz **przycisk OK** . 
1. Teraz na stronie **Tworzenie reguły alertu** wprowadź nazwę reguły alertu, a następnie wybierz przycisk **OK**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Tworzenie reguły alertu — gotowe":::

## <a name="view-alerts"></a>Wyświetlanie alertów 
1. Zostaną wyświetlone alerty dotyczące **alertów** dla wszystkich operacji administracyjnych (w tym przykładzie). Wyświetlanie alertów może potrwać trochę czasu. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="Przechwytywanie ekranu wyświetla alerty na pulpicie nawigacyjnym.":::
1. Jeśli wybierzesz pozycję liczba w kolumnie (na przykład: **całkowita liczba alertów**), zobaczysz zgłoszone alerty. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Wszystkie alerty":::
1. W przypadku wybrania alertu zostaną wyświetlone szczegółowe informacje o nim. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Szczegóły alertu":::
1. W tym przykładzie otrzymasz również wiadomość e-mail z zawartością, jak pokazano w następującym przykładzie: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="Wiadomość e-mail z alertem":::

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej na temat tworzenia grup akcji przy użyciu różnych typów akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](../azure-monitor/alerts/action-groups.md).
- Aby dowiedzieć się więcej o dziennikach aktywności, zobacz  [Dziennik aktywności platformy Azure](../azure-monitor/essentials/activity-log.md).
- Aby dowiedzieć się więcej na temat ustawiania alertów dotyczących dzienników aktywności, zobacz [alerty dotyczące dziennika aktywności](../azure-monitor/alerts/activity-log-alerts.md).

