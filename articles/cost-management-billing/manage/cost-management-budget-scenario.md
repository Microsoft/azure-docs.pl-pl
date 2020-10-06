---
title: Scenariusz dotyczący budżetów rozliczeń i zarządzania kosztami na platformie Azure
description: Dowiedz się, jak zamykać maszyny wirtualne w oparciu o określone progi budżetu przy użyciu usługi Azure Automation.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 50451acdbd1c88b6ae703ed25de9cee1f3e48216
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446452"
---
# <a name="manage-costs-with-azure-budgets"></a>Zarządzanie kosztami przy użyciu budżetów platformy Azure

Kontrola kosztów to kluczowy składnik umożliwiający maksymalizowanie wartości inwestycji w chmurze. Istnieje kilka scenariuszy, w których widoczność kosztów, raportowanie i aranżacja oparta na kosztach mają kluczowe znaczenie dla zachowania ciągłości operacji biznesowych. [Interfejsy API usługi Azure Cost Management](https://docs.microsoft.com/rest/api/consumption/) udostępniają zestaw interfejsów API do obsługi każdego z tych scenariuszy. Interfejsy API udostępniają szczegółowe informacje dotyczące użycia, co pozwala na wyświetlanie szczegółowych kosztów na poziomie wystąpienia.

Budżety są często używane jako część kontroli kosztów. Budżety mogą być objęte zakresem na platformie Azure. Na przykład możesz zawęzić widok budżetu na podstawie subskrypcji, grup zasobów lub kolekcji zasobów. Oprócz używania interfejsu API budżetów do powiadamiania użytkownika za pośrednictwem poczty e-mail, gdy próg budżetu zostanie osiągnięty, można użyć [grup akcji usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) do wyzwolenia zorganizowanego zestawu akcji w wyniku zdarzenia budżetu.

Typowy scenariusz budżetów dla klienta uruchamiającego obciążenie inne niż krytyczne może wystąpić, gdy chce on zarządzać budżetem, a także uzyskać informacje o przewidywalnym koszcie podczas przeglądania faktury miesięcznej. Ten scenariusz wymaga opartej na kosztach aranżacji zasobów, które są częścią środowiska platformy Azure. W tym scenariuszu ustawiono miesięczny budżet dla subskrypcji wynoszący 1000 USD. Ponadto progi powiadomień zostały ustawione tak, aby wyzwalać kilka aranżacji. Ten scenariusz rozpoczyna się od progu kosztów wynoszącego 80%, co spowoduje zatrzymanie wszystkich maszyn wirtualnych w grupie zasobów **Opcjonalna**. Następnie przy progu kosztów wynoszącym 100% wszystkie wystąpienia maszyn wirtualnych zostaną zatrzymane.

Aby skonfigurować ten scenariusz, wykonaj następujące akcje opisane w poszczególnych sekcjach tego samouczka.

Akcje opisane w tym samouczku umożliwiają:

- Tworzenie elementu runbook usługi Azure Automation w celu zatrzymywania maszyn wirtualnych za pomocą elementów webhook.
- Tworzenie aplikacji logiki platformy Azure, która ma być wyzwalana na podstawie wartości progu budżetu, i wywoływanie elementu runbook z właściwymi parametrami.
- Tworzenie grupy akcji usługi Azure Monitor, która zostanie skonfigurowana do wyzwalania aplikacji logiki platformy Azure po osiągnięciu progu budżetu.
- Tworzenie budżetu platformy Azure z żądanymi progami i łączenie go z grupą akcji.

## <a name="create-an-azure-automation-runbook"></a>Tworzenie elementu runbook usługi Azure Automation

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) to usługa, która umożliwia tworzenie skryptów do obsługi większości zadań zarządzania zasobami oraz uruchamianie tych zadań zgodnie z harmonogramem lub na żądanie. W ramach tego scenariusza utworzysz [element runbook usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-types), który będzie używany do zatrzymywania maszyn wirtualnych. W celu utworzenia tego scenariusza użyjesz graficznego elementu runbook [Zatrzymaj maszyny wirtualne platformy Azure w wersji 2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) z [galerii](https://docs.microsoft.com/azure/automation/automation-runbook-gallery). Po zaimportowaniu elementu runbook do konta platformy Azure i jego opublikowaniu możesz zatrzymać maszyny wirtualne po osiągnięciu progu budżetu.

### <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń konta Azure.
2. Wybierz przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu witryny Azure Portal.
3. Wybierz pozycję **Narzędzia do zarządzania** > **Automation**.
   > [!NOTE]
   > Jeśli nie masz jeszcze konta platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/).
4. Wprowadź informacje o koncie. W obszarze **Utwórz konto Uruchom jako platformy Azure** wybierz pozycję **Tak**, aby ustawienia wymagane do uproszczenia uwierzytelniania na platformie Azure zostały automatycznie włączone.
5. Po zakończeniu wybierz przycisk **Utwórz**, aby rozpocząć wdrażanie konta usługi Automation.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importowanie elementu runbook Zatrzymaj maszyny wirtualne platformy Azure w wersji 2

Używając [elementu runbook usługi Azure Automation runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types), zaimportuj graficzny element runbook [Zatrzymaj maszyny wirtualne platformy Azure w wersji 2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) z galerii.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń konta Azure.
1. Otwórz konto usługi Automation, wybierając pozycję **Wszystkie usługi** > **Konta usługi Automation**. Następnie wybierz swoje konto usługi Automation.
1. Wybierz pozycję **Galeria elementów Runbook** w sekcji **Automatyzacja procesu**.
1. Ustaw **źródło galerii** na **Centrum skryptów** i wybierz przycisk **OK**.
1. Znajdź i wybierz element galerii [Zatrzymaj maszyny wirtualne platformy Azure w wersji 2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) w witrynie Azure Portal.
1. Wybierz pozycję **Importuj**, aby wyświetlić obszar **Importowanie**, i wybierz pozycję **OK**. Zostanie wyświetlony obszar przeglądu elementu runbook.
1. Gdy element runbook ukończy proces importowania, wybierz pozycję **Edytuj**, aby wyświetlić edytor graficznych elementów runbook i opcję publikowania.  
    ![Azure — edytowanie graficznych elementów runbook](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
1. Wybierz pozycję **Opublikuj**, aby opublikować element runbook, a następnie wybierz pozycję **Tak** po wyświetleniu monitu. Podczas publikowania elementu runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został utworzony.
    Aby uzyskać więcej informacji o publikowaniu elementu runbook, zobacz sekcję dotyczącą [tworzenia graficznego elementu runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Tworzenie elementów webhook dla elementu runbook

Przy użyciu graficznego elementu runbook [Zatrzymaj maszyny wirtualne platformy Azure w wersji 2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) tworzysz dwa elementy webhook, aby uruchomić element runbook w usłudze Azure Automation za pośrednictwem pojedynczego żądania HTTP. Pierwszy element webhook wywołuje element runbook przy progu budżetu wynoszącym 80% z nazwą grupy zasobów jako parametrem, umożliwiając zatrzymanie opcjonalnych maszyn wirtualnych. Następnie drugi element webhook wywołuje element runbook bez parametrów (przy wartości 100%), co zatrzymuje wszystkie pozostałe wystąpienia maszyn wirtualnych.

1. Na stronie **Elementy runbook** w witrynie [Azure Portal](https://portal.azure.com/) wybierz element runbook **StopAzureV2Vm**, który wyświetla obszar przeglądu elementu runbook.
1. Wybierz pozycję **Element webhook** u góry strony, aby otworzyć obszar **Dodawanie elementu webhook**.
1. Wybierz pozycję **Utwórz nowy element webhook**, aby otworzyć obszar **Tworzenie nowego elementu webhook**.
1. Ustaw **nazwę** elementu webhook na **Optional** (Opcjonalne). Właściwość **Włączono** musi mieć wartość **Tak**. Nie musisz zmieniać wartości **Wygasa**. Aby uzyskać więcej informacji na temat właściwości elementu webhook, zobacz [Właściwości elementu webhook](../../automation/automation-webhooks.md#webhook-properties).
1. Obok wartości adresu URL wybierz ikonę kopiowania, aby skopiować adres URL elementu webhook.
   > [!IMPORTANT]
   > Zapisz adres URL elementu webhook o nazwie **Optional** (Opcjonalne) w bezpiecznym miejscu. Ten adres URL zostanie użyty w dalszej części tego samouczka. Ze względów bezpieczeństwa po utworzeniu elementu webhook nie można ponownie wyświetlić ani pobrać adresu URL.
1. Wybierz przycisk **OK**, aby utworzyć nowy element webhook.
1. Wybierz pozycję **Skonfiguruj parametry i parametry uruchomieniowe**, aby wyświetlić wartości parametrów dla elementu runbook.
   > [!NOTE]
   > Jeśli element runbook ma parametry obowiązkowe, nie można utworzyć elementu webhook, chyba że zostaną podane wartości.
1. Wybierz przycisk **OK**, aby zaakceptować wartości parametrów elementu webhook.
1. Wybierz pozycję **Utwórz**, aby utworzyć element webhook.
1. Następnie wykonaj powyższe kroki, aby utworzyć drugi element webhook o nazwie **Complete** (Ukończenie).
    > [!IMPORTANT]
    > Pamiętaj, aby zapisać obydwa adresy URL elementu webhook do użycia w dalszej części tego samouczka. Ze względów bezpieczeństwa po utworzeniu elementu webhook nie można ponownie wyświetlić ani pobrać adresu URL.

Teraz powinny istnieć dwa skonfigurowane elementy webhook dostępne przy użyciu zapisanych adresów URL.

![Elementy webhook — Optional (Opcjonalne) i Complete (Ukończone)](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Konfiguracja usługi Azure Automation została zakończona. Możesz przetestować elementy webhook przy użyciu prostego testu Postman, aby sprawdzić, czy element webhook działa. Następnie musisz utworzyć aplikację logiki na potrzeby aranżacji.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Tworzenie aplikacji logiki platformy Azure na potrzeby aranżacji

Usługa Logic Apps ułatwia tworzenie, planowanie i automatyzowanie procesów jako przepływów pracy, aby można było integrować aplikacje, dane, systemy i usługi między przedsiębiorstwami i organizacjami. W tym scenariuszu tworzona [aplikacja logiki](https://docs.microsoft.com/azure/logic-apps/) wykona więcej pracy niż tylko wywołanie utworzonego elementu webhook usługi Automation.

Można skonfigurować budżety, aby wyzwolić powiadomienie po osiągnięciu określonego progu. Można określić wiele progów w celu otrzymywania powiadomień, a aplikacja logiki będzie demonstrować zdolność wykonywania różnych akcji w oparciu o osiągnięty próg. W tym przykładzie skonfigurujesz scenariusz, w którym otrzymasz kilka powiadomień: pierwsze powiadomienie będzie dostępne po osiągnięciu 80% budżetu, a drugie powiadomienie po osiągnięciu 100% budżetu. Aplikacja logiki zostanie użyta do zamknięcia wszystkich maszyn wirtualnych w grupie zasobów. Najpierw próg **opcjonalny** zostanie osiągnięty przy wartości 80%, a następnie drugi próg zostanie osiągnięty, gdy wszystkie maszyny wirtualne w subskrypcji zostaną zamknięte.

Aplikacje logiki umożliwiają dostarczenie przykładowego schematu dla wyzwalacza HTTP, ale wymagają ustawienia nagłówka **Content-Type**. Ponieważ grupa akcji nie ma niestandardowych nagłówków dla elementu webhook, musisz przeanalizować ładunek w osobnym kroku. Użyjesz akcji **Przeanalizuj** i zastosujesz dla niej przykładowy ładunek.

### <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

Aplikacja logiki wykona kilka akcji. Poniższa lista zawiera ogólny zestaw akcji, które wykona aplikacja logiki:

- Rozpoznanie momentu odebrania żądania HTTP
- Przeanalizowanie przekazanych danych JSON w celu określenia wartości progowej, która została osiągnięta
- Użycie instrukcji warunkowej w celu sprawdzenia, czy wielkość progowa osiągnęła co najmniej 80% zakresu budżetu, ale nie więcej niż 100%.
  - Jeśli ta wielkość progowa została osiągnięta, instrukcja HTTP POST jest wysyłana przy użyciu elementu webhook o nazwie **Optional** (Opcjonalne). Ta akcja spowoduje zamknięcie maszyn wirtualnych w grupie „Opcjonalne”.
- Użycie instrukcji warunkowej w celu sprawdzenia, czy wielkość progu osiągnęła lub przekroczyła 100% wartości budżetu.
  - Jeśli ta wielkość progowa została osiągnięta, instrukcja HTTP POST jest wysyłana przy użyciu elementu webhook o nazwie **Complete** (Ukończone). Ta akcja spowoduje zamknięcie wszystkich pozostałych maszyn wirtualnych.

Następujące kroki są niezbędne do utworzenia aplikacji logiki, która będzie wykonywać powyższe czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz pozycję **Utwórz zasób** > **Integracja** > **Aplikacja logiki**.  
    ![Azure — wybieranie zasobu Aplikacja logiki](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
1. W obszarze **Tworzenie aplikacji logiki** podaj szczegóły niezbędne do utworzenia aplikacji logiki, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** i wybierz pozycję **Utwórz**.  
    ![Azure — Tworzenie aplikacji logiki](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Gdy platforma Azure wdroży aplikację logiki, zostanie otwarty **Projektant aplikacji usługi Logic Apps** wyświetlający obszar z wprowadzającym wideo i najczęściej używanymi wyzwalaczami.

### <a name="add-a-trigger"></a>Dodawanie wyzwalacza

Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej i wykonującej przepływ pracy. akcje to wszystkie kroki wykonywane po aktywowaniu wyzwalacza.

1. W części **Szablony** obszaru **Projektant aplikacji usługi Logic Apps** wybierz pozycję **Pusta aplikacja logiki**.
1. Dodaj [wyzwalacz](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts), wprowadzając ciąg „Żądanie HTTP” w polu wyszukiwania **Projektanta aplikacji usługi Logic Apps**, aby znaleźć i wybrać wyzwalacz o nazwie **Żądanie — po odebraniu żądania HTTP**.  
    ![Azure — Aplikacja logiki — Wyzwalacz HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
1. Wybierz pozycję **Nowy krok** > **Dodaj akcję**.  
    ![Azure — Nowy krok — Dodaj akcję](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
1. Wyszukaj ciąg „Przeanalizuj kod JSON” w polu wyszukiwania **Projektanta aplikacji usługi Logic Apps**, aby znaleźć i wybrać [akcję](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) **Operacje na danych — Przeanalizuj dane JSON**.  
    ![Azure — Aplikacja logiki — Dodawanie akcji analizy danych JSON](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
1. Wprowadź „Ładunek” jako nazwę **zawartości** dla ładunku Przeanalizuj dane JSON” użyj tagu „Treść” z zawartości dynamicznej.
1. Wybierz opcję **Użyj przykładowego ładunku do wygenerowania schematu** w polu **Przeanalizuj dane JSON**.  
    ![Azure — Aplikacja logiki — Używanie przykładowych danych JSON w celu wygenerowania schematu](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
1. Wklej następujący przykładowy ładunek JSON w polu tekstowym: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
   Pole tekstowe będzie wyglądać następująco:  
    ![Azure — Aplikacja logiki — Przykładowy ładunek JSON](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
1. Wybierz pozycję **Done** (Gotowe).

### <a name="add-the-first-conditional-action"></a>Dodawanie pierwszej akcji warunkowej

Użycie instrukcji warunkowej w celu sprawdzenia, czy wielkość progowa osiągnęła co najmniej 80% zakresu budżetu, ale nie więcej niż 100%. Jeśli ta wielkość progowa została osiągnięta, instrukcja HTTP POST jest wysyłana przy użyciu elementu webhook o nazwie **Optional** (Opcjonalne). Ta akcja spowoduje zamknięcie maszyn wirtualnych w grupie **Opcjonalne**.

1. Wybierz pozycję **Nowy krok** > **Dodaj warunek**.  
    ![Azure — Aplikacja logiki — Dodaj warunek](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
1. W polu **Warunek** wybierz pole tekstowe zawierające frazę `Choose a value`, aby wyświetlić listę dostępnych wartości.  
    ![Azure — Aplikacja logiki — pole Warunek](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)
1. Wybierz pozycję **Wyrażenie** w górnej części listy i wprowadź następujące wyrażenie w edytorze wyrażeń: `float()`  
    ![Azure — Aplikacja logiki — Wyrażenie float](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)
1. Wybierz pozycję **Zawartość dynamiczna**, umieść kursor wewnątrz nawiasu () i wybierz pozycję **NotificationThresholdAmount** z listy, aby wypełnić ukończone wyrażenie.
   Wyrażenie będzie następujące:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Wybierz przycisk **OK**, aby ustawić wyrażenie.
1. Wybierz pozycję **jest większe lub równe** w polu listy rozwijanej **Warunek**.
1. W polu **Wybierz wartość** warunku wprowadź `.8`.  
    ![Zrzut ekranu przedstawia okno dialogowe Warunek z wybranymi wartościami.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)
1. Wybierz pozycję **Dodaj** > **Dodaj wiersz** w polu Warunek, aby dodać dodatkową część warunku.
1. W polu **Warunek** wybierz pole tekstowe zawierające frazę `Choose a value`.
1. Wybierz pozycję **Wyrażenie** w górnej części listy i wprowadź następujące wyrażenie w edytorze wyrażeń: `float()`
1. Wybierz pozycję **Zawartość dynamiczna**, umieść kursor wewnątrz nawiasu () i wybierz pozycję **NotificationThresholdAmount** z listy, aby wypełnić ukończone wyrażenie.
1. Wybierz przycisk **OK**, aby ustawić wyrażenie.
1. Wybierz pozycję **jest mniejsze niż** w polu listy rozwijanej **Warunek**.
1. W polu **Wybierz wartość** warunku wprowadź `1`.  
    ![Zrzut ekranu przedstawia okno dialogowe Warunek z wybranymi dwoma warunkami.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)
1. W polu **W przypadku wartości true** wybierz pozycję **Dodaj akcję**. Zostanie dodana akcja HTTP POST, która spowoduje wyłączenie opcjonalnych maszyn wirtualnych.  
    ![Azure — Aplikacja logiki — Dodaj akcję](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)
1. Wprowadź **HTTP**, aby wyszukać akcję HTTP, i wybierz akcję **HTTP — HTTP**.  
    ![Azure — Aplikacja logiki — Dodaj akcję HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)
1. Wybierz pozycję **Post** dla wartości **Metoda**.
1. Wprowadź adres URL elementu webhook o nazwie **Optional** (Opcjonalne), który został utworzony wcześniej w tym samouczku, jako wartość identyfikatora **URI**.  
    ![Azure — Aplikacja logiki — Identyfikator URI akcji HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)
1. Wybierz pozycję **Dodaj akcję** w polu **W przypadku wartości true**. Dodasz akcję poczty e-mail, która spowoduje wysłanie wiadomości e-mail z powiadomieniem odbiorcy o wyłączeniu opcjonalnych maszyn wirtualnych.
1. Wyszukaj ciąg „wyślij wiadomość e-mail” i wybierz akcję *wyślij wiadomość e-mail* na podstawie używanej usługi poczty e-mail.  
    ![Azure — Aplikacja logiki — Akcja Wyślij wiadomość e-mail](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**. W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**. Jeśli nie masz jeszcze połączenia, pojawi się monit o zalogowanie się do swojego konta poczty e-mail. Usługa Logic Apps utworzy połączenie z kontem e-mail.
   Musisz zezwolić aplikacji logiki na dostęp do informacji w poczcie e-mail.  
    ![Azure — Aplikacja logiki — Informacje o dostępie](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)
1. Dodaj tekst w polach **Do**, **Temat** i **Treść** wiadomości e-mail, która powiadamia o adresata o zamknięciu opcjonalnych maszyn wirtualnych. Użyj nazwy **BudgetName** i zawartości dynamicznej **NotificationThresholdAmount** do wypełnienia pól tematu i treści. 
    ![Azure — Aplikacja logiki — Szczegóły wiadomości e-mail](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Dodawanie drugiej akcji warunkowej

Użycie instrukcji warunkowej w celu sprawdzenia, czy wielkość progu osiągnęła lub przekroczyła 100% wartości budżetu. Jeśli ta wielkość progowa została osiągnięta, instrukcja HTTP POST jest wysyłana przy użyciu elementu webhook o nazwie **Complete** (Ukończone). Ta akcja spowoduje zamknięcie wszystkich pozostałych maszyn wirtualnych.

1. Wybierz pozycję **Nowy krok** > **Dodaj warunek**.  
    ![Zrzut ekranu przedstawia okno dialogowe W przypadku wartości true z wywołaniem opcji Dodaj akcję.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)
1. W polu **Warunek** wybierz pole tekstowe zawierające frazę `Choose a value`, aby wyświetlić listę dostępnych wartości.
1. Wybierz pozycję **Wyrażenie** w górnej części listy i wprowadź następujące wyrażenie w edytorze wyrażeń: `float()`
1. Wybierz pozycję **Zawartość dynamiczna**, umieść kursor wewnątrz nawiasu () i wybierz pozycję **NotificationThresholdAmount** z listy, aby wypełnić ukończone wyrażenie.
   Wyrażenie będzie wyglądać następująco:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Wybierz przycisk **OK**, aby ustawić wyrażenie.
1. Wybierz pozycję **jest większe lub równe** w polu listy rozwijanej **Warunek**.
1. W polu **Wybierz wartość** warunku wprowadź `1`.  
    ![Azure — Aplikacja logiki — Ustawianie wartości warunku](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)
1. W polu **W przypadku wartości true** wybierz pozycję **Dodaj akcję**. Zostanie dodana akcja HTTP POST, która spowoduje wyłączenie wszystkich pozostałych maszyn wirtualnych.  
    ![Zrzut ekranu przedstawia okno dialogowe W przypadku wartości true, w którym można dodać akcję HTTP POST.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)
1. Wprowadź **HTTP**, aby wyszukać akcję HTTP, i wybierz akcję **HTTP — HTTP**.
1. Wybierz pozycję **Post** dla wartości **Metoda**.
1. Wprowadź adres URL elementu webhook o nazwie **Complete** (Ukończone), który został utworzony wcześniej w tym samouczku, jako wartość identyfikatora **URI**.  
    ![Zrzut ekranu przedstawia okno dialogowe HTTP, w którym można wprowadzić wartość adresu URL.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)
1. Wybierz pozycję **Dodaj akcję** w polu **W przypadku wartości true**. Dodasz akcję poczty e-mail, która spowoduje wysłanie wiadomości e-mail z powiadomieniem odbiorcy o wyłączeniu pozostałych maszyn wirtualnych.
1. Wyszukaj ciąg „wyślij wiadomość e-mail” i wybierz akcję *wyślij wiadomość e-mail* na podstawie używanej usługi poczty e-mail.
1. Dodaj tekst w polach **Do**, **Temat** i **Treść** wiadomości e-mail, która powiadamia o adresata o zamknięciu opcjonalnych maszyn wirtualnych. Użyj nazwy **BudgetName** i zawartości dynamicznej **NotificationThresholdAmount** do wypełnienia pól tematu i treści.  
    ![Azure — Aplikacja logiki — Szczegóły wysyłanej wiadomości e-mail](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)
1. Wybierz przycisk **Zapisz** w górnej części obszaru **Projektant aplikacji logiki**.

### <a name="logic-app-summary"></a>Podsumowanie aplikacji logiki

Oto jak aplikacja logiki będzie wyglądać po wykonaniu wszystkich czynności. W najbardziej typowych scenariuszach, w których nie jest potrzebna jakakolwiek aranżacja oparta na progach, można bezpośrednio wywołać skrypt automatyzacji z usługi **Monitor** i pominąć krok **Aplikacja logiki**.

![Azure — Aplikacja logiki — Kompletny widok](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Po zapisaniu aplikacji logiki został wygenerowany adres URL, który będzie można wywołać. Tego adresu URL będziesz potrzebować w kolejnej sekcji tego samouczka.

## <a name="create-an-azure-monitor-action-group"></a>Tworzenie grupy akcji usługi Azure Monitor

Grupa akcji to kolekcja preferencji powiadomień zdefiniowanych przez użytkownika. Po wyzwoleniu alertu określona grupa akcji może odebrać alert za pośrednictwem powiadomienia. Alert platformy Azure aktywnie zgłasza powiadomienie na podstawie określonych warunków i zapewnia możliwość podjęcia akcji. Alert może używać danych z wielu źródeł, m.in. metryk i dzienników.

Grupy akcji to jedyny punkt końcowy, który zostanie zintegrowany z budżetem. Możesz skonfigurować powiadomienia w wielu kanałach, ale w tym scenariuszu skoncentrujesz się na aplikacji logiki utworzonej wcześniej w tym samouczku.

### <a name="create-an-action-group-in-azure-monitor"></a>Tworzenie grupy akcji w usłudze Azure Monitor

Podczas tworzenia grupy akcji wskażesz aplikację logiki utworzoną wcześniej w tym samouczku.

1. Jeśli jeszcze nie zalogowano się do witryny [Azure Portal](https://portal.azure.com/), zaloguj się i wybierz pozycję **wszystkie usługi** > **Monitor**.
1. Wybierz pozycję **Alerty**, a następnie wybierz pozycję **Zarządzaj akcjami**.
1. Wybierz pozycję **Dodaj grupę akcji** w obszarze **Grupy akcji**.
1. Dodaj i zweryfikuj następujące elementy:
    - Nazwa grupy akcji
    - Krótka nazwa
    - Subskrypcja
    - Grupa zasobów  
    ![Azure — Aplikacja logiki — Dodaj grupę akcji](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)
1. W okienku **Dodaj grupę akcji** dodaj akcję LogicApp. Nadaj akcji nazwę **Budget-BudgetLA**. W okienku **Aplikacja logiki** wybierz pozycje **Subskrypcja** i **Grupa zasobów**. Następnie wybierz **aplikację logiki** utworzoną wcześniej w tym samouczku.
1. Wybierz przycisk **OK**, aby ustawić aplikację logiki. Następnie wybierz przycisk **OK** w okienku **Dodaj grupę akcji**, aby utworzyć grupę akcji.

Wszystkie składniki pomocnicze potrzebne do efektywnego organizowania budżetu są gotowe. Teraz wystarczy utworzyć budżet i skonfigurować go tak, aby korzystał z utworzonej grupy akcji.

## <a name="create-the-azure-budget"></a>Tworzenie budżetu platformy Azure

Budżet można utworzyć w witrynie Azure Portal przy użyciu [funkcji budżetu](../costs/tutorial-acm-create-budgets.md) w usłudze Cost Management. Można też utworzyć budżet przy użyciu interfejsów API REST, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. Poniższa procedura używa interfejsu API REST. Przed wywołaniem interfejsu API REST będzie potrzebny token autoryzacji. Aby utworzyć token autoryzacji, możesz użyć projektu [ARMClient](https://github.com/projectkudu/ARMClient). Projekt **ARMClient** umożliwia samodzielne uwierzytelnianie w usłudze Azure Resource Manager i pobieranie tokenu na potrzeby wywoływania interfejsów API.

### <a name="create-an-authentication-token"></a>Tworzenie tokenu uwierzytelniania

1. Przejdź do projektu [ARMClient](https://github.com/projectkudu/ARMClient) w witrynie GitHub.
1. Sklonuj repozytorium, aby uzyskać kopię lokalną.
1. Otwórz projekt w programie Visual Studio i skompiluj go.
1. Gdy kompilacja zakończy się pomyślnie, plik wykonywalny powinien znajdować się w folderze *\bin\debug*.
1. Uruchom projekt ARMClient. Otwórz wiersz polecenia i przejdź do folderu *\bin\debug* w katalogu głównym projektu.
1. Aby zalogować się i uwierzytelnić, wprowadź następujące polecenie w wierszu polecenia:<br>
    `ARMClient login prod`
1. Skopiuj **identyfikator GUID subskrypcji** z danych wyjściowych.
1. Aby skopiować token autoryzacji do schowka, wprowadź następujące polecenie w wierszu polecenia, ale pamiętaj, aby użyć skopiowanego identyfikatora subskrypcji z powyższego kroku: <br>
    `ARMClient token <subscription GUID from previous step>`

    Po ukończeniu kroku powyżej zobaczysz:<br>
    **Token został pomyślnie skopiowany do schowka.**
1. Zapisz token, który ma być używany na potrzeby kroków w następnej sekcji tego samouczka.

### <a name="create-the-budget"></a>Tworzenie budżetu

Następnie skonfigurujesz rozwiązanie **Postman** w celu utworzenia budżetu, wywołując interfejsy API REST użycia platformy Azure. Postman to środowisko deweloperskie interfejsu API. Pliki środowiska i kolekcji zostaną zaimportowane do rozwiązania Postman. Kolekcja zawiera zgrupowane definicje żądań HTTP, które wywołują interfejsy API REST użycia platformy Azure. Plik środowiska zawiera zmienne, które są używane przez kolekcję.

1. Pobierz i otwórz [klienta REST rozwiązania Postman](https://www.getpostman.com/) w celu wykonania interfejsów API REST.
1. W środowisku Postman utwórz nowe żądanie.  
    ![Postman — Tworzenie nowego żądania](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)
1. Zapisz nowe żądanie jako kolekcję, tak aby nie zawierało żadnych danych.  
    ![Postman — Zapisywanie nowego żądania](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)
1. Zmień żądanie z `Get` na akcję `Put`.
1. Zmodyfikuj następujący adres URL, zastępując element `{subscriptionId}`**identyfikatorem subskrypcji** użytym w poprzedniej sekcji tego samouczka. Ponadto zmodyfikuj adres URL, tak aby zawierał wartość „SampleBudget” jako wartość elementu `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
1. Wybierz kartę **Headers** (Nagłówki) w narzędziu Postman.
1. Dodaj nowy **klucz** o nazwie „Authorization” (Autoryzacja).
1. Ustaw **wartość** na token, który został utworzony przy użyciu projektu ArmClient na końcu ostatniej sekcji.
1. Wybierz kartę **Body** (Treść) w narzędziu Postman.
1. Wybierz opcję przycisku **raw**.
1. W polu tekstowym wklej poniższą przykładową definicję budżetu. Musisz zastąpić parametry `subscriptionID`, `resourcegroupname` i `actiongroupname` identyfikatorem subskrypcji, unikatową nazwą grupy zasobów i nazwą grupy akcji, które zostały utworzone zarówno w adresie URL, jak i w treści żądania:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {},
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [],
                    "contactRoles": [],
                    "contactGroups": [
                        "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                },
               "Actual_EqualTo_100_Percent": {
                       "operator": "EqualTo",
                       "threshold": 100,
                       "contactGroups": [
                           "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                       ]
                   }
               }
            }
        }
    ```
1. Naciśnij przycisk **Send** (Wyślij), aby wysłać żądanie.

Masz teraz wszystkie elementy potrzebne do wywołania [interfejsu API budżetów](https://docs.microsoft.com/rest/api/consumption/budgets). Dokumentacja interfejsu API budżetów zawiera dodatkowe szczegóły dotyczące konkretnych żądań, w tym:

- **budgetName** — można obsługiwać wiele budżetów.  Nazwy budżetów muszą być unikatowe.
- **category** — musi mieć wartość **Cost** (Koszt) lub **Usage** (Użycie). Interfejs API obsługuje zarówno budżety kosztów, jak i użycia.
- **timeGrain** — budżet miesięczny, kwartalny lub roczny. Kwota jest resetowana na końcu okresu.
- **filters** — filtry umożliwiają zawężenie budżetu do określonego zestawu zasobów w ramach wybranego zakresu. Filtr może być na przykład kolekcją grup zasobów dla budżetu na poziomie subskrypcji.
- **notifications** — określa szczegóły i progi powiadomień. Można skonfigurować wiele progów i podać adres e-mail lub grupę akcji, aby otrzymać powiadomienie.

## <a name="summary"></a>Podsumowanie

Po ukończeniu tego samouczka potrafisz już wykonywać następujące czynności:

- Jak utworzyć element runbook usługi Azure Automation w celu zatrzymywania maszyn wirtualnych.
- Jak utworzyć aplikację logiki platformy Azure, która ma być wyzwalana na podstawie wartości progu budżetu, i wywoływać element runbook z właściwymi parametrami.
- Jak utworzyć grupę akcji usługi Azure Monitor, która została skonfigurowana do wyzwalania aplikacji logiki platformy Azure po osiągnięciu progu budżetu.
- Jak utworzyć budżet platformy Azure z żądanymi progami i połączyć go z grupą akcji.

Masz teraz w pełni funkcjonalny budżet dla subskrypcji, który spowoduje wyłączenie maszyn wirtualnych po osiągnięciu skonfigurowanych progów budżetu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat scenariuszy rozliczania na platformie Azure, zobacz [Billing and cost management automation scenarios](cost-management-automation-scenarios.md) (Scenariusze automatyzacji dotyczące rozliczeń i zarządzania kosztami).
