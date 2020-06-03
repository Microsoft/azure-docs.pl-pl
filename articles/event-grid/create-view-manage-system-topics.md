---
title: Twórz, Wyświetlaj i Zarządzaj tematami systemowymi w Azure Event Grid
description: W tym artykule przedstawiono sposób wyświetlania istniejącego tematu systemu, tworzenia Azure Event Grid tematów systemu przy użyciu Azure Portal.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: ac22afb351973397960e66c2a8fe86031e0b213a
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316656"
---
# <a name="create-view-and-manage-system-topics-in-azure-event-grid"></a>Twórz, Wyświetlaj i Zarządzaj tematami systemowymi w Azure Event Grid
W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Tworzenie tematu systemu
- Wyświetl wszystkie istniejące tematy dotyczące systemu 
- Usuwanie tematu systemu
- Tworzenie subskrypcji zdarzeń dla tematu systemu


## <a name="create-a-system-topic"></a>Tworzenie tematu systemu
Temat systemowy dla zasobu platformy Azure można utworzyć na dwa sposoby:

- Korzystając ze strony zasobu, na przykład strony konto magazynu lub Event Hubs stronie obszaru nazw. 
- Korzystając ze strony **Tematy dotyczące systemu Event Grid** . 

Zapoznaj się z [tym przewodnikiem Szybki Start](blob-event-quickstart-portal.md) , aby zapoznać się z przykładem tworzenia tematu systemu przy użyciu strony zasobów (karta**zdarzenia** na stronie zasobu w Azure Portal). Poniższe kroki dotyczą tworzenia tematu systemu przy użyciu strony tematy dotyczące **systemu Event Grid** . 

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W polu wyszukiwania w górnej części wpisz **Event Grid tematy systemowe**, a następnie naciśnij klawisz **Enter**. 

    ![Wyszukaj tematy systemowe](./media/create-view-manage-system-topics/search-system-topics.png)
3. Na stronie **Tematy dotyczące systemu Event Grid** wybierz pozycję **+ Dodaj** na pasku narzędzi.

    ![Dodawanie tematu systemu — przycisk paska narzędzi](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. Na stronie **Tworzenie tematu systemu Event Grid** wykonaj następujące czynności:
    1. Wybierz **Typ tematu**. W poniższym przykładzie wybrano opcję **konta magazynu** . 
    2. Wybierz **subskrypcję platformy Azure** , która ma zasób konta magazynu. 
    3. Wybierz **grupę zasobów** , w której znajduje się konto magazynu. 
    4. Wybierz **konto magazynu**. 
    5. Wprowadź **nazwę** tematu systemowego, który ma zostać utworzony. 
    
        > [!NOTE]
        > Ta nazwa tematu systemu służy do wyszukiwania metryk i dzienników diagnostycznych.
    6. Wybierz pozycję **Przegląd + utwórz**.

        ![Tworzenie tematu systemu](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Przejrzyj ustawienia i wybierz pozycję **Utwórz**. 
        
        ![Przeglądanie i tworzenie tematu systemu](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Po pomyślnym wdrożeniu wybierz pozycję **Przejdź do zasobu** , aby wyświetlić stronę **tematu System Event Grid** dla utworzonego tematu systemu. 

        ![Strona tematu systemu](./media/create-view-manage-system-topics/system-topic-page.png)

## <a name="view-all-system-topics"></a>Wyświetl wszystkie tematy dotyczące systemu
Wykonaj następujące kroki, aby wyświetlić wszystkie istniejące Event Grid tematy dotyczące systemu. 

> [!NOTE]
> Wcześniej podczas tworzenia subskrypcji dla zdarzenia zgłoszonego przez źródła platformy Azure usługa Event Grid automatycznie utworzyła temat systemowy z losowo wygenerowaną nazwą. Teraz można określić nazwę tematu systemu podczas tworzenia tematu. Ten zasób tematu systemu służy do odnajdywania metryk i dzienników diagnostycznych.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W polu wyszukiwania w górnej części wpisz **Event Grid tematy systemowe**, a następnie naciśnij klawisz **Enter**. 

    ![Wyszukaj tematy systemowe](./media/create-view-manage-system-topics/search-system-topics.png)
3. Na stronie **Tematy dotyczące systemu Event Grid** zobaczysz wszystkie tematy systemowe. 

    ![Lista tematów systemowych](./media/create-view-manage-system-topics/list-system-topics.png)
4. Wybierz z listy temat dotyczący **systemu** , aby wyświetlić jego szczegóły. 

    ![Szczegóły tematu systemu](./media/create-view-manage-system-topics/system-topic-details.png)

    Na tej stronie przedstawiono szczegółowe informacje dotyczące tematu systemu, takie jak następujące informacje: 
    - Zewnętrz. Nazwa zasobu, w którym został utworzony temat systemowy.
    - Typ źródła. Typ zasobu. Na przykład: `Microsoft.Storage.StorageAccounts` , `Microsoft.EventHub.Namespaces` , `Microsoft.Resources.ResourceGroups` i tak dalej.
    - Wszystkie subskrypcje utworzone dla tematu systemowego.

    Ta strona umożliwia wykonywanie takich operacji, jak następujące:
    - Utwórz subskrypcję zdarzeń wybierz pozycję **+ subskrypcja zdarzeń** na pasku narzędzi. 
    - Usuń subskrypcję zdarzeń. Na pasku narzędzi wybierz pozycję **Usuń** . 
    - Dodaj Tagi dla tematu systemowego. Wybierz pozycję **Tagi** w menu po lewej stronie, a następnie określ nazwy tagów i wartości. 


## <a name="delete-a-system-topic"></a>Usuwanie tematu systemu
1. Postępuj zgodnie z instrukcjami w sekcji [Wyświetl tematy dotyczące systemu](#view-all-system-topics) , aby wyświetlić wszystkie tematy dotyczące systemu, a następnie wybierz temat systemowy, który chcesz usunąć z listy. 
2. Na stronie **temat System Event Grid** wybierz pozycję **Usuń** na pasku narzędzi. 

    ![Temat systemowy — przycisk usuwania](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. Na stronie potwierdzenie wybierz pozycję **OK** , aby potwierdzić usunięcie. Spowoduje to usunięcie tematu systemowego oraz wszystkich subskrypcji zdarzeń dla tematu systemowego.  

## <a name="create-an-event-subscription"></a>Tworzenie subskrypcji zdarzeń
1. Postępuj zgodnie z instrukcjami w sekcji [Wyświetl tematy dotyczące systemu](#view-all-system-topics) , aby wyświetlić wszystkie tematy dotyczące systemu, a następnie wybierz temat systemowy, który chcesz usunąć z listy. 
2. Na stronie **temat systemu Event Grid** wybierz pozycję **+ subskrypcja zdarzeń** z paska narzędzi. 

    ![Temat systemu — przycisk Dodaj subskrypcję zdarzenia](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Upewnij się, że **Typ tematu**, **zasób źródłowy**i **nazwa tematu** są wypełniane automatycznie. Wprowadź nazwę, wybierz **Typ punktu końcowego**i określ **punkt końcowy**. Następnie wybierz pozycję **Utwórz** , aby utworzyć subskrypcję zdarzeń. 

    ![Temat systemu — Tworzenie subskrypcji zdarzeń](./media/create-view-manage-system-topics/create-event-subscription.png)


## <a name="next-steps"></a>Następne kroki
Zobacz [Tematy systemowe w Azure Event Grid](system-topics.md) sekcji, aby dowiedzieć się więcej o tematach systemowych i typach tematów obsługiwanych przez Azure Event Grid. 
