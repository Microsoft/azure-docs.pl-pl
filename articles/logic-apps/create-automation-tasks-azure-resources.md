---
title: Tworzenie zadań automatyzacji do zarządzania i monitorowania zasobów platformy Azure
description: Konfigurowanie zautomatyzowanych zadań, które ułatwiają zarządzanie zasobami platformy Azure i monitorowanie kosztów dzięki tworzeniu przepływów pracy uruchamianych na Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: deli, jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 2b3b40b5958df52dabf92155a1de809578f1d374
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201124"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Zarządzanie zasobami platformy Azure i monitorowanie kosztów dzięki tworzeniu zadań automatyzacji (wersja zapoznawcza)

> [!IMPORTANT]
> Ta możliwość jest dostępna w publicznej wersji zapoznawczej, nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aby łatwiej zarządzać [zasobami platformy Azure](../azure-resource-manager/management/overview.md#terminology) , można utworzyć zautomatyzowane zadania zarządzania dla określonego zasobu lub grupy zasobów przy użyciu szablonów zadań automatyzacji, które różnią się w zależności od typu zasobu. Na przykład dla [konta usługi Azure Storage](../storage/common/storage-account-overview.md)można skonfigurować zadanie automatyzacji, które wyśle miesięczny koszt dla tego konta magazynu. W przypadku [maszyny wirtualnej platformy Azure](https://azure.microsoft.com/services/virtual-machines/)można utworzyć zadanie automatyzacji, które włącza lub wyłącza tę maszynę wirtualną zgodnie ze wstępnie zdefiniowanym harmonogramem.

W tle zadanie automatyzacji jest w rzeczywistości przepływem pracy, który jest uruchamiany w usłudze [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i jest rozliczany przy użyciu tych samych [stawek cenowych](https://azure.microsoft.com/pricing/details/logic-apps/) i [modelu cen](../logic-apps/logic-apps-pricing.md). Po utworzeniu zadania można wyświetlić i edytować podstawowy przepływ pracy, otwierając zadanie w Projektancie aplikacji logiki. Po zakończeniu wykonywania zadania co najmniej jednego przebiegu można sprawdzić stan, historię, dane wejściowe i wyjścia dla każdego przebiegu.

Oto obecnie dostępne szablony zadań w tej wersji zapoznawczej:

| Typ zasobu | Szablony zadań automatyzacji |
|---------------|---------------------------|
| Grupa zasobów platformy Azure | **Po usunięciu zasobu** |
| Wszystkie zasoby platformy Azure | **Wyślij koszt miesięczny dla zasobu** |
| Maszyny wirtualne platformy Azure | Dodatkowo: <p>- **Wyłącz maszynę wirtualną** <br>- **Uruchom maszynę wirtualną** |
| Konta usługi Azure Storage | Dodatkowo: <p>- **Usuń stare obiekty blob** |
| Azure Cosmos DB | Ponadto <p>- **Wyślij wynik zapytania za pośrednictwem poczty e-mail** |
|||

W tym artykule przedstawiono sposób wykonywania następujących zadań:

* [Utwórz zadanie automatyzacji](#create-automation-task) dla określonego zasobu platformy Azure.

* [Przejrzyj historię zadania](#review-task-history), w tym informacje o stanie uruchomienia, danych wejściowych, wyjściach i innych informacjach historycznych.

* [Edytuj zadanie](#edit-task) , aby można było zaktualizować zadanie lub dostosować przepływ pracy zadania podstawowego w Projektancie aplikacji logiki.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Jak zadania automatyzacji różnią się od Azure Automation?

Obecnie można utworzyć zadanie automatyzacji tylko na poziomie zasobów, wyświetlić historię uruchomień zadania i edytować podstawowy przepływ pracy aplikacji logiki, który jest obsługiwany przez usługę [Azure Logic Apps](../logic-apps/logic-apps-overview.md) . Zadania automatyzacji są bardziej podstawowe i lekkie niż [Azure Automation](../automation/automation-intro.md).

Porównując Azure Automation to oparta na chmurze usługa automatyzacji i konfiguracji, która obsługuje spójne zarządzanie w środowiskach platformy Azure i innych niż platformy Azure. Usługa składa się z [automatyzacji procesów związanych z organizowaniem procesów](../automation/automation-intro.md#process-automation) przy użyciu [elementów Runbook](../automation/automation-runbook-execution.md), zarządzania konfiguracją [i funkcji śledzenia zmian oraz spisu](../automation/change-tracking/overview.md), zarządzania aktualizacjami, funkcji udostępnionych i niejednorodnych. Automatyzacja zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Zasób platformy Azure, który ma być zarządzany. W tym artykule jest stosowane konto usługi Azure Storage jako przykład.

* Konto pakietu Office 365, jeśli chcesz postępować zgodnie z przykładem, który wyśle wiadomość e-mail przy użyciu pakietu Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Tworzenie zadania automatyzacji

1. W [Azure Portal](https://portal.azure.com)Znajdź zasób, którym chcesz zarządzać.

1. W menu zasób przewiń do sekcji **Automatyzacja** , a następnie wybierz pozycję **zadania** .

   ![Zrzut ekranu przedstawiający menu Azure Portal i zasób konta magazynu, w którym sekcja "Automatyzacja" ma wybrany element menu "zadania".](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. W okienku **zadania** wybierz pozycję **Dodaj** , aby można było wybrać szablon zadania.

   ![Zrzut ekranu przedstawiający okienko "zadania" konta magazynu, w którym zaznaczono pasek narzędzi "Dodaj"](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. W okienku **Dodawanie zadania** w obszarze **Wybierz szablon**wybierz szablon zadania, które chcesz utworzyć, a następnie wybierz pozycję **Dalej: uwierzytelnianie**.

   Ten przykład kontynuuje, wybierając szablon zadania **Wyślij koszt miesięczny dla zasobu** .

   ![Zrzut ekranu pokazujący wybrane opcje, "Wyślij koszt miesięczny dla zasobu" i "dalej: uwierzytelnianie"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. W obszarze **uwierzytelnianie**w sekcji **połączenia** wybierz pozycję **Utwórz** dla każdego połączenia, aby zapewnić poświadczenia uwierzytelniania dla tego połączenia. Typy połączeń w poszczególnych zadaniach różnią się w zależności od zadania.

   ![Zrzut ekranu przedstawiający wybraną opcję "Utwórz" dla połączenia Azure Resource Manager](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Po wyświetleniu monitu zaloguj się przy użyciu poświadczeń konta platformy Azure.

   ![Zrzut ekranu pokazujący wybór "Logowanie"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Każde pomyślne uwierzytelnione połączenie wygląda podobnie do tego przykładu:

   ![Zrzut ekranu pokazujący pomyślne utworzenie połączenia](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Po uwierzytelnieniu wszystkich wymaganych połączeń wybierz pozycję **Dalej: Konfiguracja**.

1. W obszarze **Konfiguracja**Podaj nazwę zadania i inne informacje wymagane do wykonania zadania. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   > [!NOTE]
   > Nie można zmienić nazwy zadania po utworzeniu, dlatego należy rozważyć nazwę, która nadal ma zastosowanie [w przypadku edytowania źródłowego przepływu pracy](#edit-task-workflow). Zmiany wprowadzone w bazowym przepływie pracy mają zastosowanie tylko do zadania, które zostało utworzone, a nie szablonu zadania.
   >
   > Na przykład, jeśli nazwa zadania zostanie `Send monthly cost` zmieniona, ale później zmienisz źródłowy przepływ pracy w celu uruchomienia co tydzień, nie możesz zmienić nazwy zadania na `Send weekly cost` .

   Zadania wysyłające powiadomienia e-mail wymagają adresu e-mail.

   ![Zrzut ekranu pokazujący wymagane informacje dotyczące wybranego zadania](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   Utworzone zadanie, które jest automatycznie na żywo i uruchomione, zostanie wyświetlone na liście **zadania automatyzacji** .

   ![Zrzut ekranu pokazujący listę zadań automatyzacji](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Jeśli zadanie nie zostanie wyświetlone natychmiast, spróbuj odświeżyć listę zadań lub zaczekaj nieco przed odświeżeniem. Na pasku narzędzi wybierz pozycję **Odśwież**.

   Po uruchomieniu wybranego zadania otrzymasz wiadomość e-mail, która wygląda następująco:

   ![Zrzut ekranu przedstawiający powiadomienie e-mail wysłane przez zadanie](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Przejrzyj historię zadań

Aby wyświetlić historię uruchamiania zadań wraz z ich stanami, danymi wejściowymi, wynikami i innymi informacjami, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)Znajdź zasób, który ma historię zadań, którą chcesz przejrzeć.

1. W menu zasób w obszarze **Ustawienia**wybierz pozycję **zadania automatyzacji**.

1. Na liście zadania Znajdź zadanie, które chcesz przejrzeć. W kolumnie **uruchomienia** tego zadania wybierz pozycję **Widok**.

   ![Zrzut ekranu pokazujący zadanie i wybraną opcję "widok"](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   W okienku **historia uruchomień** są wyświetlane wszystkie uruchomienia zadania wraz z ich stanami, godzinami początkowymi, identyfikatorami i okresami uruchomienia.

   ![Zrzut ekranu, który pokazuje uruchomienia zadania, ich Stany i inne informacje](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Oto możliwe stany przebiegu:

   | Stan | Opis |
   |--------|-------------|
   | **Zerwan** | Zadanie zostało anulowane podczas działania. |
   | **Awarii** | Zadanie ma co najmniej jedną akcję, która nie powiodła się, ale nie istniały żadne kolejne akcje do obsłużenia błędu. |
   | **Uruchomienie** | Zadanie jest obecnie uruchomione. |
   | **Powiodło się** | Wszystkie akcje zostały wykonane pomyślnie. Zadanie można nadal zakończyć pomyślnie, jeśli akcja zakończyła się niepowodzeniem, ale w celu obsłużenia błędu istniała kolejna Akcja. |
   | **Oczekiwanie** | Przebieg nie został jeszcze uruchomiony i jest wstrzymany, ponieważ wcześniejsze wystąpienie zadania jest nadal uruchomione. |
   |||

   Aby uzyskać więcej informacji, zobacz [Przegląd historii uruchamiania](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Aby wyświetlić stan i inne informacje dla każdego kroku w przebiegu, wybierz pozycję Uruchom.

   Zostanie otwarte okienko **uruchamiania aplikacji logiki** i zostanie wyświetlony źródłowy przepływ pracy, który został uruchomiony.

   * Przepływ pracy zawsze rozpoczyna się od [*wyzwalacza*](../connectors/apis-list.md#triggers-actions). W przypadku tego zadania przepływ pracy rozpoczyna się od [wyzwalacza **cyklu** ](../connectors/connectors-native-recurrence.md).

   * Każdy krok pokazuje jego stan i czas trwania. Wykonanie kroków, które mają czas trwania 0 sekund, trwało mniej niż 1 sekundę.

   ![Zrzut ekranu pokazujący każdy krok w czasie trwania przebiegu, stanu i uruchomienia](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Aby przejrzeć dane wejściowe i wyjściowe dla każdego kroku, wybierz krok, który rozwija się.

   Ten przykład pokazuje dane wejściowe dla wyzwalacza cyklicznego, który nie ma żadnych danych wyjściowych, ponieważ wyzwalacz określa tylko czas działania przepływu pracy i nie udostępnia żadnych danych wyjściowych dla kolejnych akcji do przetworzenia.

   ![Zrzut ekranu pokazujący rozszerzony wyzwalacz i dane wejściowe](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Z kolei Akcja **Wyślij wiadomość e-mail** zawiera dane wejściowe z wcześniejszych akcji w przepływie pracy i w danych wyjściowych.

   ![Zrzut ekranu pokazujący rozwiniętą akcję, dane wejściowe i wyjściowe](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Aby dowiedzieć się, jak utworzyć własne automatyczne przepływy pracy, aby umożliwić integrację aplikacji, danych, usług i systemów poza kontekstem zadań automatyzacji dla zasobów platformy Azure, zobacz [Szybki Start: Tworzenie pierwszego przepływu pracy integracji przy użyciu Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Edytuj zadanie

Aby zmienić zadanie, dostępne są następujące opcje:

* [Edytuj zadanie "inline"](#edit-task-inline) , aby można było zmienić właściwości zadania, takie jak informacje o połączeniu lub informacje o konfiguracji, na przykład adres e-mail.

* [Edytuj podstawowy przepływ pracy zadania](#edit-task-workflow) w Projektancie aplikacji logiki.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Edytuj zadanie w postaci wbudowanej

1. W [Azure Portal](https://portal.azure.com)Znajdź zasób zawierający zadanie, które chcesz zaktualizować.

1. W menu zasób w obszarze **Automatyzacja**wybierz pozycję **zadania**.

1. Na liście zadania Znajdź zadanie, które chcesz zaktualizować. Otwórz menu wielokropka zadania (**...**), a następnie wybierz pozycję **Edytuj w wierszu**.

   ![Zrzut ekranu pokazujący otwarte menu wielokropka i wybraną opcję "Edytuj w wierszu"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Domyślnie zostanie wyświetlona karta **uwierzytelnianie** z istniejącymi połączeniami.

1. Aby dodać nowe poświadczenia uwierzytelniania lub wybrać inne istniejące poświadczenia uwierzytelniania dla połączenia, otwórz menu wielokropka połączenia (**...**), a następnie wybierz opcję **Dodaj nowe połączenie** lub jeśli dostępne, inne poświadczenia uwierzytelniania.

   ![Zrzut ekranu pokazujący kartę Uwierzytelnianie, istniejące połączenia i wybrane menu wielokropka](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Aby zaktualizować inne właściwości zadania, wybierz pozycję **Dalej: Konfiguracja**.

   Dla zadania w tym przykładzie jedyną właściwością dostępną do edycji jest adres e-mail.

   ![Zrzut ekranu przedstawiający kartę Konfiguracja](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Edytuj podstawowy przepływ pracy zadania

W przypadku zmiany podstawowego przepływu pracy dla zadania automatyzacji zmiany dotyczą tylko utworzonego wystąpienia zadania, a nie szablonu, który tworzy zadanie. Po wprowadzeniu i zapisaniu zmian nazwa podana dla oryginalnego zadania może nie dokładnie opisać zadania, dlatego może być konieczne ponowne utworzenie zadania z inną nazwą.

> [!TIP]
> Najlepszym rozwiązaniem jest klonowanie bazowego przepływu pracy, aby można było edytować skopiowaną wersję. Dzięki temu można wprowadzać i testować zmiany w kopii, gdy oryginalne zadanie automatyzacji nadal działa i działa bez ryzyka zakłócenia lub przerwania istniejących funkcji. Po zakończeniu wprowadzania zmian i upewnieniu się, że nowa wersja zostanie uruchomiona pomyślnie, można wyłączyć lub usunąć oryginalne zadanie automatyzacji i użyć sklonowanej wersji zadania automatyzacji. Poniższe kroki zawierają informacje dotyczące sposobu klonowania przepływu pracy.

1. W [Azure Portal](https://portal.azure.com)Znajdź zasób zawierający zadanie, które chcesz zaktualizować.

1. W menu zasób w obszarze **Automatyzacja**wybierz pozycję **zadania**.

1. Na liście zadania Znajdź zadanie, które chcesz zaktualizować. Otwórz menu wielokropka zadania (**...**), a następnie wybierz pozycję **Otwórz w Logic Apps**.

   ![Zrzut ekranu pokazujący otwarte menu wielokropka i wybraną opcję "Otwórz w Logic Apps"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Podstawowy przepływ pracy zadania zostanie otwarty w usłudze Azure Logic Apps i zostanie wyświetlone okienko **Przegląd** , w którym można wyświetlić tę samą historię uruchamiania, która jest dostępna dla tego zadania.

   ![Zrzut ekranu pokazujący zadanie w widoku Azure Logic Apps z wybranym okienkiem przegląd](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Aby otworzyć podstawowy przepływ pracy w Projektancie aplikacji logiki, w menu aplikacji logiki wybierz pozycję **Projektant aplikacji logiki**.

   ![Zrzut ekranu przedstawiający wybraną opcję menu "projektant aplikacji logiki" oraz powierzchnię projektanta z podstawowym przepływem pracy](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Teraz można edytować właściwości wyzwalacza i akcji przepływu pracy oraz edytować wyzwalacz i akcje, które definiują przepływ pracy. Jednak zgodnie z najlepszymi rozwiązaniami wykonaj kroki, aby sklonować przepływ pracy, tak aby można było wprowadzić zmiany w kopii, podczas gdy oryginalny przepływ pracy będzie nadal działał i uruchamiany.

1. Aby sklonować przepływ pracy i edytować skopiowaną wersję, wykonaj następujące czynności:

   1. W menu przepływu pracy aplikacji logiki wybierz pozycję **Przegląd**.

   1. Na pasku narzędzi okienka przegląd wybierz pozycję **Klonuj**.

   1. W okienku Tworzenie aplikacji logiki w obszarze **Nazwa**wprowadź nową nazwę dla skopiowanego przepływu pracy aplikacji logiki.

      Oprócz **stanu aplikacji logiki**inne właściwości nie są dostępne do edycji. 
      
   1. W obszarze **stan aplikacji logiki**wybierz pozycję **wyłączone** , aby sklonowany przepływ pracy nie był uruchamiany podczas wprowadzania zmian. Możesz włączyć przepływ pracy, gdy wszystko będzie gotowe do testowania zmian.

   1. Po zakończeniu aprowizacji sklonowanego przepływu pracy na platformie Azure Znajdź i Otwórz ten przepływ pracy w Projektancie aplikacji logiki.

1. Aby wyświetlić właściwości wyzwalacza lub akcji, rozwiń ten wyzwalacz lub akcję.

   Na przykład można zmienić wyzwalacz cykliczny, aby był uruchamiany co tydzień, a nie co miesiąc.

   ![Zrzut ekranu pokazujący rozszerzony wyzwalacz cyklu z otwartą listą częstotliwości, aby wyświetlić dostępne opcje częstotliwości](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Aby uzyskać więcej informacji na temat wyzwalacza cyklu, zobacz [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy z wyzwalaczem cyklu](../connectors/connectors-native-recurrence.md). Aby uzyskać więcej informacji na temat innych wyzwalaczy i akcji, których można użyć, zobacz [Łączniki dla Azure Logic Apps](../connectors/apis-list.md).

1. Aby zapisać zmiany, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   ![Zrzut ekranu pokazujący pasek narzędzi projektanta i wybrane polecenie "Zapisz"](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Aby przetestować i uruchomić zaktualizowany przepływ pracy, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   Po zakończeniu przebiegu Projektant wyświetli szczegóły przebiegu przepływu pracy.

   ![Zrzut ekranu pokazujący szczegóły uruchamiania przepływu pracy w projektancie](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Aby wyłączyć przepływ pracy w taki sposób, aby zadanie nie było nadal wykonywane, zobacz [Zarządzanie aplikacjami logiki w Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Przekazywanie opinii

Chcemy poznać Twoją opinię! Aby zgłosić błędy, przekazać opinię lub zadawać pytania dotyczące tej funkcji w wersji zapoznawczej, [skontaktuj się z zespołem Azure Logic Apps](mailto:logicapps@microsoft.com).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacjami logiki w Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md)
