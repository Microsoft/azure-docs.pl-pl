---
title: Tworzenie zadań automatyzacji w celu monitorowania zasobów platformy Azure i zarządzania nimi
description: Konfigurowanie zautomatyzowanych zadań, które ułatwiają zarządzanie zasobami platformy Azure i monitorowanie kosztów przez tworzenie przepływów pracy uruchamianych w Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 0a98f9e4b108d2498fa19bc0b041f9d52272c7d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774923"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Zarządzanie zasobami platformy Azure i monitorowanie kosztów przez tworzenie zadań automatyzacji (wersja zapoznawcza)

> [!IMPORTANT]
> Ta możliwość jest dostępna w publicznej wersji zapoznawczej, nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aby ułatwić zarządzanie zasobami [platformy Azure,](../azure-resource-manager/management/overview.md#terminology) można utworzyć automatyczne zadania zarządzania dla określonego zasobu lub grupy zasobów przy użyciu szablonów zadań automatyzacji, które różnią się dostępnością w zależności od typu zasobu. Na przykład dla konta [usługi Azure Storage](../storage/common/storage-account-overview.md)można skonfigurować zadanie automatyzacji, które wysyła miesięczny koszt tego konta magazynu. W przypadku [maszyny wirtualnej platformy Azure](https://azure.microsoft.com/services/virtual-machines/)można utworzyć zadanie automatyzacji, które włącza lub wyłącza maszynę wirtualną zgodnie ze wstępnie zdefiniowanym harmonogramem.

W tle zadanie automatyzacji jest w rzeczywistości przepływem pracy, który działa w usłudze [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i jest rozliczany przy użyciu tych samych stawek cenowych [i](https://azure.microsoft.com/pricing/details/logic-apps/) modelu [cen.](../logic-apps/logic-apps-pricing.md) Po utworzeniu zadania możesz wyświetlić i edytować bazowy przepływ pracy, otwierając zadanie w Projektancie aplikacji logiki. Po zakończeniu co najmniej jednego uruchomienia zadania można przejrzeć stan, historię, dane wejściowe i dane wyjściowe dla każdego uruchomienia.

Poniżej znajdują się obecnie dostępne szablony zadań w tej wersji zapoznawczej:

| Typ zasobu | Szablony zadań automatyzacji |
|---------------|---------------------------|
| Grupa zasobów platformy Azure | **Po usunięciu zasobu** |
| Wszystkie zasoby platformy Azure | **Wysyłanie miesięcznego kosztu zasobu** |
| Maszyny wirtualne platformy Azure | Dodatkowo: <p>- **Wyłączanie maszyny wirtualnej** <br>- **Uruchamianie maszyny wirtualnej** |
| Konta usługi Azure Storage | Dodatkowo: <p>- **Usuwanie starych obiektów blob** |
| Azure Cosmos DB | Dodatkowo <p>- **Wysyłanie wyniku zapytania za pośrednictwem poczty e-mail** |
|||

W tym artykule pokazano, jak wykonać następujące zadania:

* [Utwórz zadanie automatyzacji dla](#create-automation-task) określonego zasobu platformy Azure.

* [Przejrzyj historię zadania,](#review-task-history)która zawiera stan uruchomienia, dane wejściowe, dane wyjściowe i inne informacje historyczne.

* [Edytuj zadanie,](#edit-task) aby zaktualizować zadanie lub dostosować podstawowy przepływ pracy zadania w Projektancie aplikacji logiki.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Czym różnią się zadania automatyzacji od Azure Automation?

Obecnie zadanie automatyzacji można tworzyć tylko na poziomie zasobu, wyświetlać historię przebiegów zadania i edytować podstawowy przepływ pracy aplikacji logiki zadania, który jest obsługiwany przez usługę [Azure Logic Apps](../logic-apps/logic-apps-overview.md) logiki. Zadania automatyzacji są bardziej podstawowe i uproszczone [niż Azure Automation](../automation/automation-intro.md).

Z drugiej Azure Automation to oparta na chmurze usługa automatyzacji i konfiguracji, która obsługuje spójne zarządzanie w środowiskach platformy Azure i poza platformą Azure. Usługa obejmuje [](../automation/automation-intro.md#process-automation) automatyzację procesów w celu organizowania procesów przy użyciu elementów [Runbook,](../automation/automation-runbook-execution.md)zarządzania konfiguracją ze śledzeniem zmian i spisem, [](../automation/change-tracking/overview.md)zarządzanie aktualizacjami, współdzielone możliwości i funkcje heterogeniczne. Automatyzacja zapewnia pełną kontrolę podczas wdrażania, obsługi i likwidowania obciążeń i zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Zasób platformy Azure, którym chcesz zarządzać. W tym artykule jako przykładu użyto konta usługi Azure Storage.

* Konto usługi Office 365, jeśli chcesz postępować zgodnie z przykładem, który wysyła wiadomość e-mail przy użyciu usługi Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Tworzenie zadania automatyzacji

1. W [Azure Portal](https://portal.azure.com)znajdź zasób, którym chcesz zarządzać.

1. W menu zasobu przewiń do sekcji **Automatyzacja,** a następnie wybierz pozycję **Zadania**

   ![Zrzut ekranu przedstawiający menu Azure Portal zasobów konta magazynu, w którym w sekcji "Automatyzacja" wybrano element menu "Zadania".](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. W **okienku** Zadania wybierz pozycję **Dodaj,** aby wybrać szablon zadania.

   ![Zrzut ekranu przedstawiający okienko "Zadania" konta magazynu, w którym na pasku narzędzi wybrano pozycję "Dodaj".](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. W **okienku Dodawanie** zadania w obszarze Wybierz **szablon** wybierz szablon zadania, które chcesz utworzyć. Jeśli następna strona nie jest wyświetlana, wybierz pozycję **Dalej: Uwierzytelnianie**.

   Ten przykład jest kontynuowany przez wybranie szablonu **zadania Wyślij miesięczny koszt** zasobu.

   ![Zrzut ekranu przedstawiający opcje "Wyślij miesięczny koszt zasobu" i "Dalej: uwierzytelnianie"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. W **obszarze** Uwierzytelnianie w  **sekcji Połączenia** wybierz pozycję Utwórz dla każdego połączenia wyświetlanego w zadaniu, aby można było podać poświadczenia uwierzytelniania dla wszystkich połączeń. Typy połączeń w poszczególnych zadaniach różnią się w zależności od zadania.

   W tym przykładzie pokazano tylko jedno z połączeń wymaganych przez to zadanie.

   ![Zrzut ekranu przedstawiający wybraną opcję "Utwórz" dla Azure Resource Manager połączenia](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Po wyświetleniu monitu zaloguj się przy użyciu poświadczeń konta platformy Azure.

   ![Zrzut ekranu przedstawiający wybór opcji "Zaloguj się"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Każde pomyślnie uwierzytelnione połączenie wygląda podobnie do tego przykładu:

   ![Zrzut ekranu przedstawiający pomyślnie utworzone połączenie](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Po uwierzytelnieniu wszystkich połączeń wybierz pozycję **Dalej: Konfiguracja,** jeśli następna strona nie zostanie wyświetlony.

1. W **obszarze** Konfiguracja podaj nazwę zadania i wszelkie inne informacje wymagane dla zadania. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   > [!NOTE]
   > Nie można zmienić nazwy zadania po utworzeniu, dlatego należy wziąć pod uwagę nazwę, która nadal ma zastosowanie w przypadku [edytowania bazowego przepływu pracy](#edit-task-workflow). Zmiany wprowadzone w bazowym przepływie pracy dotyczą tylko utworzonego zadania, a nie szablonu zadania.
   >
   > Jeśli na przykład nadasz zadaniu nazwę , ale później edytujesz podstawowy przepływ pracy, aby był uruchamiany co tydzień, nie możesz zmienić nazwy zadania na `Send monthly cost` `Send weekly cost` .

   Zadania, które wysyłają powiadomienia e-mail, wymagają adresu e-mail.

   ![Zrzut ekranu przedstawiający wymagane informacje dla wybranego zadania](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   Utworzone zadanie, automatycznie działające i uruchomione, jest teraz wyświetlane na liście **Zadań automatyzacji.**

   ![Zrzut ekranu przedstawiający listę zadań automatyzacji](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Jeśli zadanie nie pojawia się natychmiast, spróbuj odświeżyć listę zadań lub zaczekaj chwilę na odświeżenie. Na pasku narzędzi wybierz pozycję **Odśwież.**

   Po zakończeniu wybranego zadania otrzymasz wiadomość e-mail, która wygląda podobnie do tego przykładu:

   ![Zrzut ekranu przedstawiający powiadomienie e-mail wysłane według zadania](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Przeglądanie historii zadań

Aby wyświetlić historię przebiegów zadania wraz ze stanami, wejściami, wyjściami i innymi informacjami, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)zasobu, który ma historię zadań, którą chcesz przejrzeć.

1. W menu zasobu w obszarze Ustawienia **wybierz** pozycję **Zadania automatyzacji.**

1. Na liście zadań znajdź zadanie, które chcesz przejrzeć. W kolumnie Przebiegi **tego** zadania wybierz pozycję **Wyświetl**.

   ![Zrzut ekranu przedstawiający zadanie i wybraną opcję "Wyświetl"](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   Okienko **Historia przebiegów** zawiera wszystkie uruchomienia zadania wraz z ich stanami, godzinami rozpoczęcia, identyfikatorami i czasami trwania.

   ![Zrzut ekranu przedstawiający przebiegi zadania, jego stan i inne informacje](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   W tym miejscu możliwe stany uruchomienia:

   | Stan | Opis |
   |--------|-------------|
   | **Anulowane** | Zadanie zostało anulowane podczas uruchamiania. |
   | **Niepowodzenie** | Zadanie ma co najmniej jedną akcję, która zakończyła się niepowodzeniem, ale nie istniały żadne kolejne akcje do obsługi błędu. |
   | **Uruchomienie** | Zadanie jest aktualnie uruchomione. |
   | **Powodzenie** | Wszystkie akcje zakończyły się pomyślnie. Zadanie nadal może zakończyć się pomyślnie, jeśli akcja nie powiodła się, ale istniała kolejna akcja do obsługi błędu. |
   | **Oczekiwanie** | Przebieg nie został jeszcze uruchomiony i został wstrzymany, ponieważ wcześniejsze wystąpienie zadania jest nadal uruchomione. |
   |||

   Aby uzyskać więcej informacji, zobacz [Review runs history (Przeglądanie historii przebiegów).](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Aby wyświetlić stany i inne informacje dla każdego kroku w uruchomieniu, wybierz ten przebieg.

   Zostanie **otwarte okienko uruchamiania aplikacji logiki** z uruchomionym podstawowym przepływem pracy.

   * Przepływ pracy zawsze rozpoczyna się od [*wyzwalacza*](../connectors/apis-list.md#triggers). W przypadku tego zadania przepływ pracy rozpoczyna się od [ **wyzwalacza Cykl.**](../connectors/connectors-native-recurrence.md)

   * Każdy krok pokazuje swój stan i czas trwania uruchomienia. Uruchamianie kroków, które mają czas trwania 0 sekund, trwało mniej niż 1 sekundę.

   ![Zrzut ekranu przedstawiający każdy krok w uruchomieniu, stan i czas trwania uruchomienia](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Aby przejrzeć dane wejściowe i wyjściowe dla każdego kroku, wybierz krok rozwijany.

   W tym przykładzie przedstawiono dane wejściowe dla wyzwalacza Cykl, który nie ma żadnych danych wyjściowych, ponieważ wyzwalacz określa tylko czas uruchomienia przepływu pracy i nie dostarcza żadnych danych wyjściowych dla kolejnych akcji do przetwarzania.

   ![Zrzut ekranu przedstawiający rozwinięty wyzwalacz i dane wejściowe](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Z kolei akcja Wyślij **wiadomość e-mail** ma dane wejściowe z wcześniejszych akcji przepływu pracy i danych wyjściowych.

   ![Zrzut ekranu przedstawiający rozwiniętą akcję, dane wejściowe i wyjściowe](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Aby dowiedzieć się, jak można tworzyć własne zautomatyzowane przepływy pracy, aby można było integrować aplikacje, dane, usługi i systemy z wyjątkiem kontekstu zadań automatyzacji dla zasobów platformy Azure, zobacz Szybki start: tworzenie pierwszego przepływu pracy integracji przy użyciu usługi [Azure Logic Apps — Azure Portal.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Edytowanie zadania

Aby zmienić zadanie, dostępne są następujące opcje:

* [Edytuj zadanie "w tekście",](#edit-task-inline) aby można było zmienić właściwości zadania, takie jak informacje o połączeniu lub informacje o konfiguracji, na przykład adres e-mail.

* [Edytuj podstawowy przepływ pracy zadania w](#edit-task-workflow) Projektancie aplikacji logiki.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Edytowanie zadania w tekście

1. W [Azure Portal](https://portal.azure.com)znajdź zasób, który ma zadanie, które chcesz zaktualizować.

1. W menu zasobu w obszarze **Automatyzacja** wybierz pozycję **Zadania.**

1. Na liście zadań znajdź zadanie, które chcesz zaktualizować. Otwórz menu wielokropka (**...**) zadania, a następnie wybierz pozycję **Edytuj w wierszu**.

   ![Zrzut ekranu przedstawiający otwarte menu wielokropka i wybraną opcję "Edytuj w wierszu"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Domyślnie zostanie wyświetlona **karta Uwierzytelnianie** z wyświetlonymi istniejącymi połączeniami.

1. Aby dodać nowe poświadczenia uwierzytelniania lub wybrać inne istniejące poświadczenia uwierzytelniania dla połączenia, otwórz menu wielokropka połączenia (**...**) i wybierz pozycję Dodaj nowe połączenie lub, jeśli są dostępne, różne poświadczenia uwierzytelniania. 

   ![Zrzut ekranu przedstawiający kartę Uwierzytelnianie, istniejące połączenia i wybrane menu wielokropka](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Aby zaktualizować inne właściwości zadania, wybierz pozycję **Dalej: Konfiguracja**.

   W przypadku zadania w tym przykładzie jedyną właściwością dostępną do edycji jest adres e-mail.

   ![Zrzut ekranu przedstawiający kartę Konfiguracja](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Edytowanie podstawowego przepływu pracy zadania

Po zmianie bazowego przepływu pracy dla zadania automatyzacji zmiany mają wpływ tylko na utworzone wystąpienie zadania, a nie na szablon, który tworzy zadanie. Po wdaniu i zapisaniu zmian nazwa podana dla oryginalnego zadania może nie dokładnie opisywać zadania, dlatego może być konieczne ponowne utworzenie zadania pod inną nazwą.

> [!TIP]
> Najlepszym rozwiązaniem jest sklonowanie źródłowego przepływu pracy, aby można było edytować skopiowaną wersję. Dzięki temu można wprowadzać i testować zmiany na kopii, podczas gdy oryginalne zadanie automatyzacji będzie nadal działać i działać bez ryzyka zakłóceń lub przerwania istniejących funkcji. Po zakończeniu zmian i pomyślnym zakończeniu pracy nowej wersji można wyłączyć lub usunąć oryginalne zadanie automatyzacji i użyć sklonowanej wersji zadania automatyzacji. Poniższe kroki zawierają informacje o instrukcje klonowania przepływu pracy.

1. W [Azure Portal](https://portal.azure.com)znajdź zasób, który zawiera zadanie, które chcesz zaktualizować.

1. W menu zasobu w obszarze **Automatyzacja** wybierz pozycję **Zadania**.

1. Na liście zadań znajdź zadanie, które chcesz zaktualizować. Otwórz menu wielokropka (**...**) zadania, a następnie wybierz pozycję **Otwórz w Logic Apps**.

   ![Zrzut ekranu przedstawiający otwarte menu wielokropka i wybraną opcję "Otwórz w Logic Apps"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Podstawowy przepływ pracy zadania zostanie otwarty w usłudze  Azure Logic Apps i zostanie otwarte okienko Przegląd, w którym można wyświetlić tę samą historię przebiegów, która jest dostępna dla zadania.

   ![Zrzut ekranu przedstawiający zadanie w widoku Azure Logic Apps z wybranym okienkiem Przegląd](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Aby otworzyć podstawowy przepływ pracy w Projektancie aplikacji logiki, w menu aplikacji logiki wybierz pozycję **Projektant aplikacji logiki.**

   ![Zrzut ekranu przedstawiający wybraną opcję menu "Projektant aplikacji logiki" i powierzchnię projektanta z bazowym przepływem pracy](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Teraz możesz edytować właściwości wyzwalacza i akcji przepływu pracy, a także edytować wyzwalacz i akcje, które definiują sam przepływ pracy. Jednak zgodnie z najlepszym rozwiązaniem wykonaj kroki klonowania przepływu pracy, aby można było wprowadzać zmiany na kopii, podczas gdy oryginalny przepływ pracy będzie nadal działać i działać.

1. Aby zamiast tego sklonować przepływ pracy i edytować skopiowaną wersję, wykonaj następujące kroki:

   1. W menu przepływu pracy aplikacji logiki wybierz pozycję **Przegląd.**

   1. Na pasku narzędzi okienka przeglądu wybierz pozycję **Clone (Sklonuj).**

   1. W okienku tworzenia aplikacji logiki w **obszarze Nazwa** wprowadź nową nazwę skopiowanego przepływu pracy aplikacji logiki.

      Z wyjątkiem **stanu aplikacji logiki** inne właściwości nie są dostępne do edycji. 
      
   1. W **obszarze Stan aplikacji logiki** wybierz pozycję **Wyłączone,** aby sklonowany przepływ pracy nie był uruchamiany podczas wprowadzania zmian. Przepływ pracy można włączyć, gdy wszystko będzie gotowe do testowania zmian.

   1. Po zakończeniu aprowizowania sklonowanego przepływu pracy przez platformę Azure znajdź i otwórz ten przepływ pracy w Projektancie aplikacji logiki.

1. Aby wyświetlić właściwości wyzwalacza lub akcji, rozwiń ten wyzwalacz lub akcję.

   Na przykład możesz zmienić wyzwalacz Cykl tak, aby był uruchamiany co tydzień, a nie co miesiąc.

   ![Zrzut ekranu przedstawiający rozwinięty wyzwalacz Cyklicznie z otwartą listą Częstotliwość, aby wyświetlić dostępne opcje częstotliwości](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Aby uzyskać więcej informacji na temat wyzwalacza Cyklicznie, zobacz Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy za [pomocą wyzwalacza Cykl.](../connectors/connectors-native-recurrence.md) Aby uzyskać więcej informacji na temat innych wyzwalaczy i akcji, których można użyć, zobacz [Łączniki dla Azure Logic Apps](../connectors/apis-list.md).

1. Aby zapisać zmiany, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający pasek narzędzi projektanta i wybrane polecenie "Zapisz"](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Aby przetestować i uruchomić zaktualizowany przepływ pracy, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   Po zakończeniu uruchamiania projektant pokazuje szczegóły uruchomienia przepływu pracy.

   ![Zrzut ekranu przedstawiający szczegóły uruchomienia przepływu pracy w projektancie](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Aby wyłączyć przepływ pracy, aby zadanie nie było nadal uruchomione, zobacz Zarządzanie aplikacjami logiki w [Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Wyraź opinię

Chcemy się z Tobędą słyszeć! Aby zgłosić usterki, przekazać opinię lub zadać pytania dotyczące tej funkcji w wersji zapoznawczej, skontaktuj się z [Azure Logic Apps zespołem.](mailto:logicappspm@microsoft.com)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacjami logiki w Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md)
