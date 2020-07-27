---
title: Tworzenie zautomatyzowanych przepływów pracy opartych na zatwierdzaniu
description: Samouczek — Tworzenie zautomatyzowanego przepływu pracy opartego na zatwierdzaniu, który przetwarza subskrypcje listy adresowej za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: d9d2f29ffc34c203e5f3b3ebf094e73fb9cdfb75
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132402"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Samouczek: tworzenie automatycznych przepływów pracy opartych na zatwierdzaniu przy użyciu Azure Logic Apps

W tym samouczku pokazano, jak utworzyć [aplikację logiki](../logic-apps/logic-apps-overview.md) , która automatyzuje przepływ pracy oparty na zatwierdzaniu. Ta aplikacja logiki przetwarza żądania subskrypcji dla listy adresowej zarządzanej przez usługę [MailChimp](https://mailchimp.com/) . Ta aplikacja logiki monitoruje konto e-mail pod kątem tych żądań, wysyła te żądania do zatwierdzenia i dodaje zatwierdzone elementy członkowskie do listy adresowej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenia pustej aplikacji logiki.
> * Dodawania wyzwalacza monitorującego wiadomości e-mail pod kątem żądań subskrypcji.
> * Dodawania akcji, która wysyła wiadomości e-mail do zatwierdzenia lub odrzucenia tych żądań.
> * Dodawania warunku sprawdzającego odpowiedzi na żądania zatwierdzenia.
> * Dodawania akcji dodającej zatwierdzone elementy członkowskie do listy adresowej.
> * Dodawania warunku sprawdzającego, czy te elementy członkowskie zostały pomyślnie dołączone do listy.
> * Dodawania akcji, która wysyła wiadomości e-mail z potwierdzeniem, czy te elementy członkowskie zostały pomyślnie dołączone do listy.

Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Omówienie ukończonej aplikacji logiki wysokiego poziomu](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

* Konto MailChimp zawierające listę o nazwie "test-Members-ML", w której aplikacja logiki może dodawać adresy e-mail dla zatwierdzonych członków. Jeśli nie masz konta, [zarejestruj się w celu uzyskania bezpłatnego konta](https://login.mailchimp.com/signup/), a następnie Dowiedz się, [jak utworzyć listę MailChimp](https://us17.admin.mailchimp.com/lists/#).

* Konto e-mail w pakiecie Office 365 Outlook lub Outlook.com, które obsługuje przepływy pracy zatwierdzania. W tym artykule wykorzystano konto usługi Office 365 Outlook. Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym menu platformy Azure wybierz pozycję **Utwórz zasób**  >  **Integration**  >  **aplikacja logiki**.

   ![Tworzenie nowego zasobu aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. W obszarze **Tworzenie aplikacji logiki** wprowadź następujące informacje na temat aplikacji logiki. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   ![Podaj informacje o aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | LA-MailingList | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( `(` , `)` ) i kropki ( `.` ). W tym przykładzie zastosowano "LA-MailingList". |
   | **Subskrypcja** | <*Twoja nazwa — subskrypcja — Azure*> | Nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | LA-MailingList-RG | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md), która jest używana do organizowania powiązanych zasobów. W tym przykładzie zastosowano "LA-MailingList-RG". |
   | **Lokalizacja** | Zachodnie stany USA | Region tnie, w którym mają być przechowywane informacje o aplikacji logiki. W tym przykładzie zastosowano "zachodnie stany USA". |
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Po wdrożeniu aplikacji przez platformę Azure na pasku narzędzi platformy Azure wybierz pozycję **powiadomienia**  >  **Przejdź do pozycji zasób** dla wdrożonej aplikacji logiki.

   ![Przejdź do nowego zasobu aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   Zostanie otwarty projektant Logic Apps i zostanie wyświetlona strona z wprowadzeniem wideo i często używanymi wyzwalaczami i wzorcami aplikacji logiki. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybierz szablon pustej aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts), który będzie nasłuchiwać pod kątem przychodzących wiadomości e-mail z żądaniami subskrypcji. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest uruchamiany po wystąpieniu określonego zdarzenia lub gdy nowe dane spełniają określony warunek. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Dodawanie wyzwalacza w celu monitorowania wiadomości e-mail

1. W Projektancie aplikacji logiki w polu wyszukiwania wprowadź `when email arrives` jako filtr. Z listy **wyzwalacze** wybierz opcję **po odebraniu nowej wiadomości e-mail** dla dostawcy poczty e-mail.

   Ten przykład używa wyzwalacza programu Outlook pakietu Office 365:

   ![Wybierz wyzwalacz "po nadejściu nowej wiadomości e-mail" dla dostawcy poczty e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Dla kont służbowych platformy Azure wybierz pozycję Office 365 Outlook.
   * Dla osobistych kont Microsoft wybierz pozycję Outlook.com.

1. Jeśli zostanie wyświetlony monit, zaloguj się do swojego konta e-mail przy użyciu swoich poświadczeń, aby Logic Apps można było utworzyć połączenie z kontem e-mail.

1. W wyzwalaczu Podaj kryteria sprawdzania nowej wiadomości e-mail.

   1. Określ folder, interwał i częstotliwość sprawdzania wiadomości e-mail.

      ![Określanie folderu, interwału i częstotliwości sprawdzania wiadomości e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Właściwość | Wartość | Opis |
      |----------|-------|-------------|
      | **Folder** | `Inbox` | Folder poczty e-mail do monitorowania |
      | **Interwał** | `1` | Liczba interwałów do odczekania między sprawdzaniami |
      | **Częstotliwość** | `Hour` | Jednostka czasu cyklu |
      ||||

   1. Teraz Dodaj kolejną właściwość do wyzwalacza, aby można było filtrować według wiersza tematu wiadomości e-mail. Otwórz **listę Dodaj nowy parametr**, a następnie wybierz właściwość **Filtr podmiotu** .

      ![Dodaj właściwość "subject Filter" do wyzwalacza](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Aby uzyskać więcej informacji na temat właściwości tego wyzwalacza, zobacz [informacje dotyczące łącznika programu Outlook pakietu Office 365](/connectors/office365/) lub [informacje dotyczące łącznika Outlook.com](/connectors/outlook/).

   1. Po wyświetleniu właściwości w wyzwalaczu wprowadź następujący tekst:`subscribe-test-members-ML`

      ![Wprowadź tekst dla właściwości "subject Filter"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Aby na razie ukryć szczegóły wyzwalacza, kliknij pasek tytułu wyzwalacza.

   ![Zwinięty kształt w celu ukrycia szczegółów](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem przychodzących wiadomości e-mail. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="send-approval-email"></a>Wysyłanie wiadomości e-mail dotyczącej zatwierdzenia

Teraz, gdy wyzwalacz jest gotowy, dodaj [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts) wysyłającą wiadomość e-mail w celu zatwierdzenia lub odrzucenia żądania.

1. W obszarze wyzwalacza wybierz pozycję **nowy krok**. 

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `approval` jako filtr. Z listy Akcje wybierz akcję **Wyślij wiadomość e-mail** dotyczącą zatwierdzenia dla dostawcy poczty e-mail. 

   W tym przykładzie jest stosowana akcja programu Outlook pakietu Office 365:

   ![Wybierz akcję "Wyślij wiadomość e-mail dotyczącą zatwierdzenia"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Podaj informacje dotyczące tej akcji zgodnie z opisem: 

   ![Wyślij właściwości wiadomości e-mail dotyczącej zatwierdzenia](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Do** | <*adres e-mail użytkownika*> | Adres e-mail osoby zatwierdzającej. Do celów testowych możesz użyć własnego adresu e-mail. W tym przykładzie jest użyty fikcyjny sophia.owen@fabrikam.com adres e-mail "". |
   | **Temat** | `Approve member request for test-members-ML` | Opisowy temat wiadomości e-mail |
   | **Opcje użytkownika** | `Approve, Reject` | Opcje odpowiedzi, które osoba zatwierdzająca może wybrać. Domyślnie osoba zatwierdzająca może wybrać opcję "Zatwierdź" lub "Odrzuć" jako odpowiedź. |
   ||||

   Na razie zignoruj listę zawartości dynamicznej, która pojawia się po kliknięciu wewnątrz określonych pól edycji. Ta lista umożliwia wybranie dostępnych danych wyjściowych z poprzednich akcji, których można użyć jako danych wejściowych w przepływie pracy.

   Aby uzyskać więcej informacji o właściwościach tej akcji, zobacz [informacje dotyczące łącznika programu Outlook pakietu Office 365](/connectors/office365/) lub [odwołanie do łącznika Outlook.com](/connectors/outlook/).
 
1. Zapisz aplikację logiki.

Następnie Dodaj warunek, aby sprawdzić, czy wybrana odpowiedź osoby zatwierdzającej.

## <a name="check-approval-response"></a>Sprawdzanie odpowiedzi na żądanie zatwierdzenia

1. W obszarze Akcja **Wyślij wiadomość e-mail** dotyczącą zatwierdzenia wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz akcję**wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `condition` jako filtr. Z listy Akcje wybierz akcję **warunek** .

   ![Znajdź i wybierz akcję "Condition"](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Zmień nazwę warunku na lepszy opis.

   1. Na pasku tytułu warunku wybierz przycisk **wielokropka** (**...**), > **zmienić nazwę**.

      ![Opis zmiany nazwy warunku](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Zmień nazwę warunku na następujący opis: `If request approved`

1. Kompiluj warunek, który sprawdza, czy osoba zatwierdzająca zabrała **zatwierdzenie**.

   1. W warunku kliknij wewnątrz pola **Wybierz wartość** po lewej stronie warunku.

   1. Z wyświetlonej listy zawartość dynamiczna w obszarze **wysyłanie wiadomości e-mail z potwierdzeniem**wybierz właściwość **SelectedOption** .

      ![Z listy zawartości dynamicznej wybierz pozycję "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. W środkowym polu porównania wybierz operator **is równe** .

   1. W polu **Wybierz wartość** z prawej strony warunku wprowadź następujący tekst:`Approve`

      Gdy wszystko będzie gotowe, warunek będzie wyglądać podobnie do tego przykładu:

      ![Gotowy warunek dla zaakceptowanego przykładu](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Zapisz aplikację logiki.

Następnie określ akcję, która będzie wykonywana przez aplikację logiki, gdy osoba sprawdzająca zatwierdzi żądanie. 

## <a name="add-member-to-mailchimp-list"></a>Dodawanie elementu członkowskiego do listy MailChimp

Teraz Dodaj akcję, która powoduje dodanie zatwierdzonego elementu członkowskiego do listy adresowej.

1. W gałęzi warunku w **przypadku wartości true** wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wprowadź `mailchimp` wartość jako filtr, a następnie wybierz akcję **Dodaj członka do listy** .

   ![Wybierz akcję "Dodaj członka do listy"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Jeśli zostanie wyświetlony monit o dostęp do konta usługi MailChimp, zaloguj się przy użyciu poświadczeń usługi MailChimp.

1. Podaj informacje dotyczące tej akcji, jak pokazano i opisano tutaj:

   ![Podawanie informacji dla pozycji „Dodawanie elementu członkowskiego do listy”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Identyfikator listy** | Tak | `test-members-ML` | Nazwa listy adresowej MailChimp. W tym przykładzie zastosowano "test-Members-ML". |
   | **Stan** | Tak | `subscribed` | Wybierz stan subskrypcji dla nowego elementu członkowskiego. Ten przykład używa "subskrybujd". <p>Aby uzyskać więcej informacji, zobacz [Zarządzanie subskrybentami za pomocą interfejsu API MailChimp](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **Adres e-mail** | Tak | <*New-member-email-address*> | Z listy zawartość dynamiczna wybierz pozycję **z** pozycji w obszarze **po nadejściu nowej wiadomości**e-mail, która przekaże adres w polu Nowy element członkowski. |
   ||||

   Aby uzyskać więcej informacji na temat właściwości tej akcji, zobacz [informacje dotyczące łącznika MailChimp](/connectors/mailchimp/).

1. Zapisz aplikację logiki.

Następnie dodaj warunek, który pozwoli sprawdzić, czy nowy element członkowski został pomyślnie dołączony do listy adresowej. W ten sposób aplikacja logiki powiadomi Cię, czy operacja zakończyła się powodzeniem lub niepowodzeniem.

## <a name="check-for-success-or-failure"></a>Sprawdzanie pod kątem powodzenia lub niepowodzenia

1. W gałęzi w **przypadku wartości true** w obszarze **Dodawanie elementu członkowskiego do listy** wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `condition` jako filtr. Z listy Akcje wybierz pozycję **warunek**.

1. Zmień nazwę warunku na następujący opis: `If add member succeeded`

1. Utwórz warunek sprawdzający, czy dołączenie zatwierdzonego elementu członkowskiego do listy adresowej zakończyło się powodzeniem lub niepowodzeniem:

   1. W warunku kliknij wewnątrz pola **Wybierz wartość** , która znajduje się po lewej stronie warunku. Z listy zawartość dynamiczna w obszarze **Dodawanie elementu członkowskiego do listy**wybierz właściwość **stan** .

      Na przykład Twój warunek wygląda podobnie do tego przykładu:

      ![W obszarze „Dodawanie elementu członkowskiego do listy” wybierz „Stan”](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. W środkowym polu porównania wybierz operator **is równe** .

   1. W polu **Wybierz wartość** z prawej strony warunku wprowadź następujący tekst:`subscribed`

      Gdy wszystko będzie gotowe, warunek będzie wyglądać podobnie do tego przykładu:

      ![Zakończono warunek dla zasubskrybowanego przykładu](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Następnie skonfiguruj wiadomości e-mail, które mają być wysyłane, gdy dołączenie zatwierdzonego elementu członkowskiego do listy adresowej zakończy się powodzeniem lub niepowodzeniem.

## <a name="send-email-if-member-added"></a>Wysyłanie wiadomości e-mail w przypadku dodania elementu członkowskiego

1. W **przypadku pomyślnego stanu Dodawanie elementu członkowskiego** w gałęzi w **przypadku wartości true** wybierz pozycję **Dodaj akcję**.

   ![W gałęzi "w przypadku wartości true" Wybierz pozycję "Dodaj akcję"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź `outlook send email` jako filtr, a następnie wybierz akcję **Wyślij wiadomość e-mail** .

   ![Dodaj akcję "Wyślij wiadomość e-mail"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Zmień nazwę akcji na następujący opis: `Send email on success`

1. Podaj informacje dotyczące tej akcji, jak pokazano i opisano:

   ![Podaj informacje dla wiadomości e-mail z informacją o powodzeniu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Do** | Tak | <*adres e-mail użytkownika*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o powodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. |
   | **Temat** | Tak | <*podmiot — dla sukcesu — adres e-mail*> | Temat wiadomości e-mail z informacją o powodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`Success! Member added to "test-members-ML": ` <p>Z listy zawartość dynamiczna w obszarze **Dodawanie elementu członkowskiego do listy**wybierz właściwość **adres e-mail** . |
   | **Treść** | Tak | <*treść — dla sukcesu — adres e-mail*> | Treść wiadomości e-mail z informacją o powodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`New member has joined "test-members-ML":` <p>Z listy zawartość dynamiczna wybierz właściwość **adres e-mail** . <p>W następnym wierszu wprowadź następujący tekst:`Member opt-in status: ` <p> Z listy zawartość dynamiczna w obszarze **Dodawanie elementu członkowskiego do listy**wybierz właściwość **stan** . |
   |||||

1. Zapisz aplikację logiki.

## <a name="send-email-if-member-not-added"></a>Wysyłanie wiadomość e-mail w przypadku nie dodania elementu członkowskiego

1. W **przypadku pomyślnego stanu Dodawanie elementu członkowskiego** w gałęzi w **przypadku wartości false** wybierz pozycję **Dodaj akcję**.

   ![W gałęzi "w przypadku wartości false" Wybierz pozycję "Dodaj akcję"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź `outlook send email` jako filtr, a następnie wybierz akcję **Wyślij wiadomość e-mail** .

   ![Dodawanie akcji „Wysłanie wiadomości e-mail”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Zmień nazwę akcji na następujący opis: `Send email on failure`

1. Podaj informacje dotyczące tej akcji, jak pokazano i opisano tutaj:

   ![Podaj informacje dla wiadomości e-mail z informacją o niepowodzeniu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Do** | Tak | <*adres e-mail użytkownika*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o niepowodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. |
   | **Temat** | Tak | <*podmiot — dla niepowodzenia — poczta e-mail*> | Temat wiadomości e-mail z informacją o niepowodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`Failed, member not added to "test-members-ML": ` <p>Z listy zawartość dynamiczna w obszarze **Dodawanie elementu członkowskiego do listy**wybierz właściwość **adres e-mail** . |
   | **Treść** | Tak | <*treść — do-niepowodzenie — poczta e-mail*> | Treść wiadomości e-mail z informacją o niepowodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Zapisz aplikację logiki. 

Następnie przetestuj aplikację logiki, która powinna wyglądać następująco:

![Przykład gotowego przepływu pracy aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

1. Wyślij do siebie wiadomość e-mail z żądaniem dołączenia do listy adresowej. Poczekaj, aż żądanie pojawi się w skrzynce odbiorczej.

1. Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. 

   Jeśli Twoja wiadomość e-mail posiada temat zgodny z filtrem tematu wyzwalacza, aplikacja logiki wyśle wiadomość e-mail z prośbą o zatwierdzenie żądania subskrypcji.

1. W wiadomości e-mail dotyczącej zatwierdzenia wybierz pozycję **Zatwierdź**.

1. Jeśli adres e-mail subskrybenta nie istnieje na liście adresowej, aplikacja logiki dodaje adres e-mail tej osoby i wysyła do Ciebie wiadomość e-mail podobną do przykładowej:

   ![Przykładowa pomyślna subskrypcja poczty e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Jeśli aplikacja logiki nie może dodać subskrybenta, otrzymasz wiadomość e-mail podobną do przykładowej:

   ![Przykładowa wiadomość e-mail — subskrypcja zakończona niepowodzeniem](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje. Udało Ci się utworzyć i uruchomić aplikację logiki, która integruje informacje z różnych usług platformy Azure, usług firmy Microsoft i innych aplikacji SaaS.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przykładowa aplikacja logiki nie jest już potrzebna, Usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby. 

1. W menu głównym platformy Azure przejdź do pozycji **Grupy zasobów** i wybierz grupę zasobów aplikacji logiki.

1. W menu Grupa zasobów wybierz pozycję **Przegląd**  >  **Usuń grupę zasobów**. 

   ![„Omówienie” > „Usuń grupę zasobów”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Wprowadź nazwę grupy zasobów jako potwierdzenie, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzyliśmy aplikację logiki, która zarządza zatwierdzaniem żądań dotyczących listy adresowej. Możesz teraz dowiedzieć się, w jaki sposób utworzyć aplikację logiki, która przetwarza i przechowuje załączniki wiadomości e-mail przez integrowanie usług platformy Azure, takich jak Azure Storage i Azure Functions.

> [!div class="nextstepaction"]
> [Przetwarzanie załączników wiadomości e-mail](../logic-apps/tutorial-process-email-attachments-workflow.md)
