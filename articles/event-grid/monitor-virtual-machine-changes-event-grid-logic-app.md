---
title: Monitorowanie zmian maszyn wirtualnych — Azure Event Grid & Logic Apps
description: Sprawdź zmiany w maszynach wirtualnych za pomocą Azure Event Grid i Logic Apps
services: logic-apps, event-grid
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: estfan, LADocs
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 1a5d8c36382433024efd1f1cc6ba9fd878d28ddc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92329529"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Samouczek: Monitorowanie zmian maszyn wirtualnych przy użyciu usług Azure Event Grid i Logic Apps

Aby monitorować konkretne zdarzenia występujące w zasobach platformy Azure lub zasobach innych firm i odpowiadać na nie, można automatyzować i uruchamiać zadania jako przepływ pracy przez utworzenie [aplikacji logiki](../logic-apps/logic-apps-overview.md) korzystającej z minimalnej ilości kodu. Te zasoby mogą publikować zdarzenia w usłudze [Azure Event Grid](../event-grid/overview.md). Z kolei usługa Event Grid wypycha te zdarzenia do subskrybentów, którzy mają kolejki, elementy webhook lub [centra zdarzeń](../event-hubs/event-hubs-about.md) jako punkty końcowe. Jako subskrybent aplikacja logiki może oczekiwać na te zdarzenia z usługi Event Grid przed uruchomieniem zautomatyzowanych przepływów pracy w celu wykonania zadań.

Na przykład poniżej przedstawiono niektóre zdarzenia, które wydawcy mogą wysyłać do subskrybentów za pośrednictwem usługi Azure Event Grid:

* Utworzenie, odczyt, aktualizacja lub usunięcie zasobu. Można na przykład monitorować zmiany, które mogą spowodować naliczenie opłat w ramach subskrypcji platformy Azure i wpłynąć na rachunek.

* Dodanie lub usunięcie osoby z subskrypcji platformy Azure.

* Aplikacja wykonuje określoną akcję.

* Pojawienie się nowego komunikatu w kolejce.

W tym samouczku przedstawiono tworzenie aplikacji logiki, która monitoruje zmiany w maszynie wirtualnej i wysyła wiadomości e-mail o tych zmianach. Podczas tworzenia aplikacji logiki z subskrypcją zdarzeń dla zasobu platformy Azure zdarzenia przepływają z tego zasobu za pośrednictwem usługi Event Grid do aplikacji logiki. Samouczek przeprowadzi Cię przez tworzenie tej aplikacji logiki:

![Zrzut ekranu programu Logic Apps Designer pokazujący przepływ pracy służący do monitorowania maszyn wirtualnych przy użyciu Event Grid.](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji logiki, która monitoruje zdarzenia z usługi Event Grid.
> * Dodawanie warunku, który konkretnie wyszukuje zmiany maszyny wirtualnej.
> * Wysyłanie wiadomości e-mail, gdy maszyna wirtualna ulegnie zmianie.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Konto e-mail od dostawcy poczty e-mail obsługiwanego przez Logic Apps do wysyłania powiadomień, takich jak Office 365 Outlook, Outlook.com lub gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](/connectors/).

  W tym samouczku jest stosowane konto Office 365 Outlook. Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

  > [!IMPORTANT]
  > Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika z tylko określonymi usługami zatwierdzonymi przez firmę Google lub możesz [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* [Maszyna wirtualna](https://azure.microsoft.com/services/virtual-machines) sama we własnej grupie zasobów platformy Azure. Jeśli jeszcze tego nie zrobiono, Utwórz maszynę wirtualną za pomocą [samouczka Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md). Aby maszyna wirtualna publikowała zdarzenia, [nie trzeba nic robić](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Tworzenie pustej aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym menu platformy Azure wybierz pozycję **Utwórz zasób**  >    >  **aplikacja logiki**.

   ![Zrzut ekranu przedstawiający Azure Portal, pokazujący przycisk służący do tworzenia zasobu aplikacji logiki.](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. W obszarze **aplikacja logiki** podaj informacje o zasobie aplikacji logiki. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   ![Zrzut ekranu przedstawiający menu tworzenia aplikacji logiki, pokazujący szczegóły, takie jak nazwa, subskrypcja, Grupa zasobów i lokalizacja.](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | <*Logic-App-Name*> | Podaj unikatową nazwę aplikacji logiki. |
   | **Subskrypcja** | Tak | <*Azure — nazwa subskrypcji*> | Wybierz tę samą subskrypcję platformy Azure dla wszystkich usług w tym samouczku. |
   | **Grupa zasobów** | Tak | <*Azure-Resource-Group*> | Nazwa grupy zasobów platformy Azure dla aplikacji logiki, którą można wybrać dla wszystkich usług w tym samouczku. |
   | **Lokalizacja** | Tak | <*Platforma Azure — region*> | Wybierz ten sam region dla wszystkich usług w tym samouczku. |
   |||

1. Po wdrożeniu aplikacji logiki przez platformę Azure Projektant Logic Apps wyświetli stronę z wprowadzeniem wideo i często używanymi wyzwalaczami. Przewiń poza wideo i wyzwalacze.

1. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Zrzut ekranu przedstawiający szablony Logic Apps, w którym jest wyświetlany wybór służący do tworzenia pustej aplikacji logiki.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Projektant Logic Apps wyświetla teraz [*wyzwalacze*](../logic-apps/logic-apps-overview.md#logic-app-concepts) , których można użyć do uruchomienia aplikacji logiki. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, Azure Logic Apps tworzy wystąpienie przepływu pracy, które uruchamia aplikację logiki.

## <a name="add-an-event-grid-trigger"></a>Dodawanie wyzwalacza Event Grid

Teraz Dodaj wyzwalacz Event Grid, który służy do monitorowania grupy zasobów dla maszyny wirtualnej.

1. W projektancie w polu wyszukiwania wprowadź `event grid` jako filtr. Z listy Wyzwalacze wybierz wyzwalacz **gdy występuje zdarzenie zasobu** .

   ![Zrzut ekranu programu Logic Apps Designer, który pokazuje wybór wyzwalacza Event Grid dla zdarzenia zasobu.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Po wyświetleniu monitu zaloguj się, aby Azure Event Grid przy użyciu poświadczeń konta platformy Azure. Na liście **dzierżawców** , która zawiera Azure Active Directory dzierżawy skojarzonej z subskrypcją platformy Azure, sprawdź, czy jest wyświetlana prawidłowa dzierżawa, na przykład:

   ![Zrzut ekranu programu Logic Apps Designer z wyświetlonym monitem logowania platformy Azure w celu nawiązania połączenia z Event Grid.](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Jeśli logujesz się za pomocą osobistego konta Microsoft, takiego jak @outlook.com lub @hotmail.com, wyzwalacz usługi Event Grid może nie być wyświetlany poprawnie. Aby obejść ten krok, wybierz pozycję [Połącz z](../active-directory/develop/howto-create-service-principal-portal.md)jednostką usługi lub Uwierzytelnij się jako członek Azure Active Directory, który jest skojarzony z subskrypcją platformy Azure, na przykład *User-Name* @emailoutlook.onmicrosoft.com .

1. Teraz Zasubskrybuj aplikację logiki do zdarzeń od wydawcy. Podaj szczegółowe informacje o subskrypcji zdarzeń zgodnie z opisem w poniższej tabeli, na przykład:

   ![Zrzut ekranu programu Logic Apps Designer, pokazujący Edytor szczegółów dla wyzwalacza w przypadku wystąpienia zdarzenia zasobu.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   | -------- | -------- | ----- | ----------- |
   | **Subskrypcja** | Tak | <*Event-Publisher-Azure-Subscription-Name*> | Wybierz nazwę subskrypcji platformy Azure, która jest skojarzona z *wydawcą zdarzeń*. Na potrzeby tego samouczka wybierz nazwę subskrypcji platformy Azure dla swojej maszyny wirtualnej. |
   | **Typ zasobu** | Tak | <*Event-Publisher-Azure-Resource-Type*> | Wybierz typ zasobu platformy Azure dla wydawcy zdarzeń. Aby uzyskać więcej informacji na temat typów zasobów platformy Azure, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md). Na potrzeby tego samouczka wybierz `Microsoft.Resources.ResourceGroups` wartość do monitorowania grup zasobów platformy Azure. |
   | **Nazwa zasobu** |  Tak | <*Event-Publisher-Azure-Resource-Name*> | Wybierz nazwę zasobu platformy Azure dla wydawcy zdarzeń. Ta lista różni się w zależności od wybranego typu zasobu. Na potrzeby tego samouczka wybierz nazwę grupy zasobów platformy Azure, która obejmuje daną maszynę wirtualną. |
   | **Element typu zdarzenia** |  Nie | <*typy zdarzeń*> | Wybierz co najmniej jeden typ zdarzenia, który ma być filtrowany i wysyłany do swojej siatki zdarzeń. Można na przykład opcjonalnie dodać te typy zdarzeń do wykrywania, kiedy zasoby są zmieniane lub usuwane: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Więcej informacji można znaleźć w następujących tematach: <p><p>- [Schemat zdarzeń Azure Event Grid dla grup zasobów](../event-grid/event-schema-resource-groups.md) <br>- [Zrozumienie filtrowania zdarzeń](../event-grid/event-filtering.md) <br>- [Filtruj zdarzenia dla Event Grid](../event-grid/how-to-filter-events.md) |
   | Aby dodać właściwości opcjonalne, wybierz pozycję **Dodaj nowy parametr**, a następnie wybierz odpowiednie właściwości. | Nie | {patrz opisy} | * **Filtr prefiksu**: w tym samouczku pozostaw pustą właściwość. Zachowanie domyślne dopasowuje wszystkie wartości. Można jednak określić ciąg prefiksu jako filtr, na przykład ścieżkę i parametr dla konkretnego zasobu. <p>* **Filtr sufiksu**: w tym samouczku pozostaw tę właściwość pustą. Zachowanie domyślne dopasowuje wszystkie wartości. Można jednak określić ciąg sufiksu jako filtr, na przykład rozszerzenie nazwy pliku, aby uwzględniać tylko określone typy plików. <p>* **Nazwa subskrypcji**: na potrzeby tego samouczka możesz podać unikatową nazwę subskrypcji zdarzenia. |
   |||

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. Aby zwinąć i ukryć szczegóły akcji w aplikacji logiki, wybierz pasek tytułu akcji.

   ![Zrzut ekranu programu Logic Apps Designer z widocznym przyciskiem Zapisz, aby zapisać zmiany przepływu pracy.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Podczas zapisywania aplikacji logiki z wyzwalaczem usługi Event Grid platforma Azure automatycznie tworzy dla tej aplikacji logiki subskrypcję zdarzeń wybranego zasobu. Dlatego gdy zasób publikuje zdarzenie w usłudze Event Grid, usługa ta automatycznie wypycha je do aplikacji logiki. Zdarzenie to wyzwala aplikację logiki, a następnie tworzy i uruchamia wystąpienie przepływu pracy, który zdefiniujesz w następnych krokach.

Aplikacja logiki jest teraz aktywna i nasłuchuje zdarzeń z usługi Event Grid, ale nie robi nic, dopóki nie dodasz akcji do przepływu pracy.

## <a name="add-a-condition"></a>Dodawanie warunku

Jeśli chcesz, aby aplikacja logiki działała tylko wtedy, gdy wystąpi określone zdarzenie lub operacja, Dodaj warunek, który sprawdza `Microsoft.Compute/virtualMachines/write` operację. Jeśli ten warunek zostanie spełniony, aplikacja logiki wyśle Ci wiadomość e-mail zawierającą szczegóły zaktualizowanej maszyny wirtualnej.

1. W Projektancie aplikacji logiki w obszarze wyzwalacza zdarzenia wybierz pozycję **nowy krok**.

   ![Zrzut ekranu programu Logic Apps Designer, pokazujący przycisk umożliwiający dodanie nowego kroku do przepływu pracy.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `condition` jako filtr. Z listy Akcje wybierz akcję **warunek** .

   ![Zrzut ekranu programu Logic Apps Designer, pokazujący przycisk służący do dodawania akcji warunku.](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Projektant aplikacji logiki dodaje pusty warunek do przepływu pracy, w tym ścieżki akcji do wykonania na podstawie tego, czy warunek jest spełniony (wartość true), czy nie (wartość false).

   ![Zrzut ekranu programu Logic Apps Designer, który pokazuje pusty warunek dodany do przepływu pracy.](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Zmień nazwę tytułu warunku na `If a virtual machine in your resource group has changed` . Na pasku tytułu warunku wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Zmień nazwę**.

   ![Zrzut ekranu przedstawiający projektanta Logic Apps, pokazujący menu kontekstowe edytora warunków z wybraną opcją zmiany nazwy.](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Utwórz warunek, który sprawdza zdarzenie `body` dla obiektu, `data` w którym `operationName` Właściwość jest równa `Microsoft.Compute/virtualMachines/write` operacji. Dowiedz się więcej o [schemacie zdarzeń usługi Event Grid](../event-grid/event-schema.md).

   1. W pierwszym wierszu w obszarze **Oraz** kliknij wewnątrz pola po lewej stronie. Na wyświetlonej liście zawartości dynamicznej wybierz pozycję **wyrażenie**.

      ![Zrzut ekranu programu Logic Apps Designer, pokazujący warunek z wybranym edytorem wyrażeń.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. W edytorze wyrażeń wprowadź wyrażenie, które zwraca nazwę operacji z wyzwalacza, a następnie wybierz **przycisk OK**:

      `triggerBody()?['data']['operationName']`

      Na przykład:

      ![Zrzut ekranu programu Logic Apps Designer, pokazujący Edytor warunków z wyrażeniem do wyodrębnienia nazwy operacji.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. W środkowym polu zachowaj operator **jest równe**.

   1. W prawym polu wprowadź tę wartość, która jest operacją określoną do monitorowania:

      `Microsoft.Compute/virtualMachines/write`

   Gotowy warunek wygląda teraz następująco:

   ![Zrzut ekranu programu Logic Apps Designer pokazujący warunek porównujący operację.](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Jeśli przełączysz się z widoku projektu do widoku kodu i wrócisz do widoku projektu, wyrażenie określone w warunku jest rozpoznawane jako **Data. OperationName** token:

   ![Zrzut ekranu programu Logic Apps Designer pokazujący warunek z rozpoznanymi tokenami.](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Zapisz aplikację logiki.

## <a name="send-email-notifications"></a>Wysyłanie powiadomień w wiadomościach e-mail

Teraz Dodaj [*akcję*](../logic-apps/logic-apps-overview.md#logic-app-concepts) , aby otrzymać wiadomość e-mail, gdy określony warunek ma wartość true.

1. W polu **W przypadku wartości true** warunku wybierz pozycję **Dodaj akcję**.

   ![Zrzut ekranu przedstawiający Edytor warunków projektanta Logic Apps, pokazujący przycisk służący do dodawania akcji, gdy warunek ma wartość true.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `send an email` jako filtr. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. Następnie wybierz akcję „Wyślij wiadomość e-mail” dla Twojego łącznika. Na przykład:

   * Dla konta służbowego platformy Azure wybierz łącznik usługi Office 365 Outlook.

   * Dla osobistych kont Microsoft wybierz łącznik usługi Outlook.com.

   * Dla kont usługi Gmail wybierz łącznik usługi Gmail.

   Ten samouczek jest kontynuowany przy użyciu łącznika programu Outlook pakietu Office 365. Jeśli używasz innego dostawcy, kroki pozostają takie same, ale interfejs użytkownika może wyglądać nieco inaczej.

   ![Zrzut ekranu programu Logic Apps Designer z informacjami o wyszukiwaniu akcji Wyślij wiadomość E-mail w programie Office 365 Outlook Connector.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Jeśli nie masz jeszcze połączenia z dostawcą poczty e-mail, po wyświetleniu monitu o uwierzytelnienie zaloguj się do swojego konta e-mail.

1. Zmień nazwę akcji Wyślij pocztą e-mail na ten tytuł: `Send email when virtual machine updated`

1. Podaj informacje o wiadomości e-mail zgodnie z opisem w poniższej tabeli:

   ![Zrzut ekranu przedstawiający projektanta Logic Apps, pokazujący, że zawartość dynamiczna jest dodany do wiersza tematu wiadomości e-mail w celu spełnienia warunków.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Aby wybrać dane wyjściowe z poprzednich kroków w przepływie pracy, kliknij wewnątrz pola edycji, aby wyświetlić listę zawartości dynamicznej, lub wybierz pozycję **Dodaj zawartość dynamiczną**. Aby uzyskać więcej wyników, wybierz pozycję **Zobacz więcej** dla każdej sekcji na liście. Aby zamknąć listę zawartości dynamicznej, ponownie wybierz pozycję **Dodaj zawartość dynamiczną** .

   | Właściwość | Wymagany | Wartość | Opis |
   | -------- | -------- | ----- | ----------- |
   | **Do** | Tak | <*\@domena odbiorcy*> | Wprowadź adres e-mail adresata. Do celów testowych możesz użyć własnego adresu e-mail. |
   | **Temat** | Tak | `Resource updated:`**Temat** | Wprowadź zawartość w polu tematu wiadomości e-mail. Na potrzeby tego samouczka wprowadź określony tekst, a następnie wybierz pole **podmiotu** zdarzenia. Tutaj temat wiadomości e-mail zawiera nazwę zaktualizowanego zasobu (maszyny wirtualnej). |
   | **Treść** | Tak | `Resource:` **Temat** <p>`Event type:` **Typ zdarzenia**<p>`Event ID:` **ID (Identyfikator)**<p>`Time:`**Czas zdarzenia** | Wprowadź zawartość w polu treści wiadomości e-mail. Na potrzeby tego samouczka wprowadź określony tekst i wybierz pola **temat** zdarzenia, **Typ zdarzenia**, **Identyfikator** i **czas zdarzenia** , tak aby poczta e-mail zawierała zasób, który wygenerował zdarzenie, typ zdarzenia, sygnaturę czasową zdarzenia i identyfikator zdarzenia dla aktualizacji. W tym samouczku zasób jest grupą zasobów platformy Azure wybraną w wyzwalaczu. <p>Aby dodać puste wiersze w zawartości, naciśnij klawisze Shift + Enter. |
   ||||

   > [!NOTE]
   > Jeśli wybierzesz pole reprezentujące tablicę, projektant automatycznie dodaje pętlę **Dla każdego** wokół akcji, która odwołuje się do tablicy. W ten sposób Twoja aplikacja logiki wykonuje tę akcję dla każdego elementu tablicy.

   Teraz Twoja akcja wiadomości e-mail może wyglądać tak jak w tym przykładzie:

   ![Zrzut ekranu programu Logic Apps Designer prezentujący wybrane dane wyjściowe do wysłania w wiadomości e-mail po zaktualizowaniu maszyny wirtualnej.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Gotowa aplikacja logiki może wyglądać jak w tym przykładzie:

   ![Zrzut ekranu przedstawiający projektanta Logic Apps, pokazujący utworzoną aplikację logiki ze szczegółowymi informacjami na temat wyzwalacza i akcji.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Zapisz aplikację logiki. Aby zwinąć i ukryć szczegóły każdej akcji w aplikacji logiki, wybierz pasek tytułu akcji.

   Aplikacja logiki jest teraz aktywna, ale czeka na zmiany maszyny wirtualnej, zanim wykona jakiekolwiek akcje. Aby teraz przetestować Twoją aplikację logiki, przejdź do następnej sekcji.

## <a name="test-your-logic-app-workflow"></a>Testowanie przepływu pracy aplikacji logiki

1. Aby sprawdzić, czy aplikacja logiki otrzymuje określone zdarzenia, zaktualizuj maszynę wirtualną.

   Możesz na przykład zmienić rozmiar maszyny wirtualnej w witrynie Azure Portal lub [zmienić rozmiar maszyny wirtualnej za pomocą programu Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Po kilku chwilach powinna nadejść wiadomość e-mail. Na przykład:

   ![Zrzut ekranu przedstawiający przykład wiadomości e-mail programu Outlook z informacjami o aktualizacji maszyny wirtualnej.](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Aby przejrzeć historię uruchamiania i wyzwalania dla aplikacji logiki, w menu aplikacji logiki wybierz pozycję **Przegląd**. Aby wyświetlić więcej szczegółów dotyczących przebiegu, wybierz wiersz dla tego przebiegu.

   ![Zrzut ekranu przedstawiający stronę omówienia aplikacji logiki, w której jest zaznaczone wybrane uruchomienie.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Aby wyświetlić dane wejściowe i wyjściowe dla każdego kroku, rozwiń krok, który chcesz przejrzeć. Te informacje ułatwiają diagnozowanie i debugowanie problemów w aplikacji logiki.

   ![Zrzut ekranu historii przebiegów aplikacji logiki, pokazujący szczegóły dla każdego przebiegu.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Gratulujemy utworzenia i uruchomienia aplikacji logiki, która monitoruje zdarzenia zasobów za pomocą usługi Event Grid i wysyła Ci wiadomości e-mail w przypadku wystąpienia tych zdarzeń. Wiesz już, jak łatwe jest tworzenie przepływów pracy automatyzujących procesy oraz integrowanie systemów i usług w chmurze.

Za pomocą usług Event Grid i Logic Apps możesz także monitorować inne zmiany, na przykład:

* Maszyna wirtualna pobiera prawa do kontroli dostępu opartej na rolach (Azure RBAC).
* Wprowadzenie zmian w sieciowej grupie zabezpieczeń dla interfejsu sieciowego (karty sieciowej).
* Dodanie lub usunięcie dysków dla maszyny wirtualnej.
* Przypisanie publicznego adresu IP do karty sieciowej maszyny wirtualnej.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek używa zasobów i wykonuje akcje, które mogą spowodować naliczenie opłat w ramach Twojej subskrypcji platformy Azure. Gdy ukończysz pracę z samouczkiem i testowanie, upewnij się, że zostały wyłączone lub usunięte wszelkie zasoby, dla których nie chcesz naliczenia opłat.

* Aby zatrzymać aplikację logiki bez usuwania swojej pracy, wyłącz ją. W menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Wyłącz**.

  ![Zrzut ekranu przedstawiający przegląd aplikacji logiki, w którym jest wyświetlany przycisk Wyłącz wybrany, aby wyłączyć aplikację logiki.](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Jeśli nie widzisz menu aplikacji logiki, spróbuj wrócić do pulpitu nawigacyjnego platformy Azure i ponownie otworzyć aplikację logiki.

* Aby trwale usunąć aplikację logiki, w menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Usuń**. Potwierdź, że chcesz usunąć aplikację logiki, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i kierowanie zdarzeń niestandardowych za pomocą usługi Event Grid](../event-grid/custom-event-quickstart.md)
