---
title: Tworzenie zautomatyzowanych przepływów pracy opartych na zatwierdzaniu
description: Samouczek — tworzenie opartego na zatwierdzaniu zautomatyzowanego przepływu pracy, który przetwarza subskrypcje listy adresowej przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: c6186e6e9f60e852f77943834bcd1ae3d526491d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777279"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Samouczek: tworzenie zautomatyzowanych przepływów pracy opartych na zatwierdzaniu przy użyciu Azure Logic Apps

W tym samouczku pokazano, jak utworzyć przykładową aplikację [logiki,](../logic-apps/logic-apps-overview.md) która automatyzuje przepływ pracy oparty na zatwierdzaniu. W szczególności ta przykładowa aplikacja logiki przetwarza żądania subskrypcji dla listy adresowej zarządzanej przez [usługę MailChimp.](https://mailchimp.com/) Ta aplikacja logiki obejmuje różne kroki, które rozpoczynają się od monitorowania konta e-mail pod uwagę żądań, wysyła te żądania do zatwierdzenia, sprawdza, czy żądanie otrzymuje zatwierdzenie, dodaje zatwierdzone elementy członkowskie do listy adresowej i potwierdza, czy nowi członkowie są dodawana do listy.

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

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto MailChimp, na którym wcześniej utworzono listę o nazwie "test-members-ML", na której aplikacja logiki może dodawać adresy e-mail zatwierdzonych członków. Jeśli nie masz konta, utwórz bezpłatne konto, [a](https://login.mailchimp.com/signup/)następnie dowiedz się, jak utworzyć [listę mailChimp.](https://us17.admin.mailchimp.com/lists/#)

* Konto e-mail od dostawcy poczty e-mail obsługiwane przez usługę Logic Apps, takie jak Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](/connectors/). W tym przewodniku Szybki start jest używana usługa Office 365 Outlook z kontem służbowym. Jeśli używasz innego konta e-mail, ogólne kroki pozostają takie same, ale interfejs użytkownika może się nieco różnić.

* Konto e-mail w usłudze Office 365 Outlook lub Outlook.com, które obsługuje przepływy pracy zatwierdzania. W tym samouczku jest używana usługa Office 365 Outlook. Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

* Jeśli aplikacja logiki musi komunikować się za pośrednictwem zapory, która ogranicza  ruch [](logic-apps-limits-and-config.md#inbound) do określonych adresów IP, musi zezwalać na dostęp zarówno dla przychodzących, jak i wychodzących adresów [IP](logic-apps-limits-and-config.md#outbound) używanych przez usługę Logic Apps lub środowisko uruchomieniowe w regionie świadczenia usługi Azure, w którym istnieje aplikacja logiki. Jeśli aplikacja logiki używa również łączników zarządzanych, takich jak łącznik office 365 Outlook lub łącznik  SQL, lub łączników niestandardowych, [](/connectors/custom-connectors/)zapora musi również zezwalać na dostęp dla wszystkich wychodzących adresów [IP](logic-apps-limits-and-config.md#outbound) łącznika zarządzanego w regionie platformy Azure aplikacji logiki. [](../connectors/managed.md)

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure. Na stronie głównej platformy Azure wybierz **pozycję Utwórz zasób.**

1. W menu Azure Marketplace integration logic app (Integracja **aplikacji**  >  **logiki).**

   ![Zrzut ekranu przedstawiający Azure Marketplace z wybranymi opcjami "Integracja" i "Aplikacja logiki".](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. W **okienku Aplikacja logiki** podaj informacje opisane tutaj dotyczące aplikacji logiki, którą chcesz utworzyć.

   ![Zrzut ekranu przedstawiający okienko tworzenia aplikacji logiki i informacje, które należy podać dla nowej aplikacji logiki.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Subskrypcja** | <*Nazwa subskrypcji platformy Azure*> | Twoja nazwa subskrypcji platformy Azure. W tym przykładzie użyto wartości `Pay-As-You-Go`. |
   | **Grupa zasobów** | LA-MailingList-RG | Nazwa grupy zasobów [platformy Azure](../azure-resource-manager/management/overview.md), która służy do organizowania powiązanych zasobów. Ten przykład tworzy nową grupę zasobów o nazwie `LA-MailingList-RG` . |
   | **Nazwa** | LA-MailingList | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry, łączniki ( ), podkreślenia `-` ( `_` ), nawiasy ( , ) i `(` `)` kropki ( `.` ). W tym przykładzie użyto wartości `LA-MailingList`. |
   | **Lokalizacja** | Zachodnie stany USA | Region, w którym mają być przechowywane informacje o aplikacji logiki. W tym przykładzie użyto wartości `West US`. |
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Gdy wszystko będzie gotowe, wybierz pozycję **Przejrzyj i utwórz**. Po zweryfikowaniu informacji o aplikacji logiki przez platformę Azure wybierz pozycję **Utwórz**.

1. Po wdrożeniu aplikacji na platformie Azure wybierz **pozycję Przejdź do zasobu.**

   Platforma Azure otwiera Logic Apps wyboru szablonu, w którym przedstawiono wprowadzenie wideo, często używane wyzwalacze i wzorce szablonów aplikacji logiki.

1. Przewiń w dół po sekcji wideo i typowych wyzwalaczy do sekcji **Szablony,** a następnie wybierz pozycję **Pusta aplikacja logiki.**

   ![Zrzut ekranu przedstawiający okienko wyboru Logic Apps z wybraną pustą aplikacją logiki.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Następnie dodaj wyzwalacz programu [Outlook, który](../logic-apps/logic-apps-overview.md#logic-app-concepts) nasłuchuje przychodzących wiadomości e-mail z żądaniami subskrypcji. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest wyzwalany w przypadku wystąpienia określonego zdarzenia lub gdy nowe dane spełniają określony warunek. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Dodawanie wyzwalacza w celu monitorowania wiadomości e-mail

1. W polu wyszukiwania Logic Apps Designer wprowadź i wybierz wyzwalacz o nazwie Po przybyciu nowej `when email arrives` wiadomości **e-mail.**

   * W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**.
   * W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**.

   Ten przykład jest kontynuowany przez wybranie usługi Office 365 Outlook.

   ![Zrzut ekranu przedstawiający Logic Apps wyszukiwania projektanta aplikacji, które zawiera termin wyszukiwania "po przybyciu wiadomości e-mail" oraz wyzwalacz "Po przybyciu nowej wiadomości e-mail".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Jeśli nie masz jeszcze połączenia, po wyświetleniu monitu zaloguj się i uwierzytelnij dostęp do swojego konta e-mail.

   Azure Logic Apps tworzy połączenie z kontem e-mail.

1. W wyzwalaczu podaj kryteria sprawdzania nowej wiadomości e-mail.

   1. Określ folder do sprawdzania wiadomości e-mail i zachowaj wartości domyślne pozostałych właściwości.

      ![Zrzut ekranu przedstawiający projektanta z akcją "Po przybyciu nowej wiadomości e-mail" i akcję "Folder" ustawioną na "Skrzynka odbiorcza".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Dodaj właściwość Filtr **tematu** wyzwalacza, aby można było filtrować wiadomości e-mail na podstawie wiersza tematu. Otwórz listę **Dodaj nowy parametr** i wybierz pozycję Filtr **tematu.**

      ![Zrzut ekranu przedstawiający otwartą listę "Dodaj nowy parametr" z wybraną pozycję "Filtr tematu".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Aby uzyskać więcej informacji na temat właściwości tego wyzwalacza, zobacz Office 365 Outlook connector reference (Informacje o łączniku usługi [Office 365 Outlook)](/connectors/office365/) lub Outlook.com connector (Informacje o [łączniku usługi Outlook.com).](/connectors/outlook/)

   1. Gdy właściwość pojawi się w wyzwalaczu, wprowadź następujący tekst: `subscribe-test-members-ML`

      ![Zrzut ekranu przedstawiający właściwość "Filtr tematu" z wprowadzonym tekstem "subscribe-test-members-ML".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Aby na razie ukryć szczegóły wyzwalacza, zwiń kształt, klikając wewnątrz paska tytułu kształtu.

   ![Zrzut ekranu przedstawiający zwinięty kształt wyzwalacza.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz.**

Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem przychodzących wiadomości e-mail. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="send-approval-email"></a>Wysyłanie wiadomości e-mail dotyczącej zatwierdzenia

Teraz, gdy wyzwalacz jest gotowy, dodaj [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts) wysyłającą wiadomość e-mail w celu zatwierdzenia lub odrzucenia żądania.

1. W projektancie Logic Apps w obszarze **wyzwalacza Po** przybyciu nowej wiadomości e-mail wybierz **pozycję Nowy krok.**

1. W **obszarze Wybierz operację** w polu wyszukiwania wprowadź , a następnie wybierz akcję `send approval` o nazwie Wyślij wiadomość **e-mail z zatwierdzeniem**.

   ![Zrzut ekranu przedstawiający listę "Wybierz operację" przefiltrowane według akcji "zatwierdzenia" i wybraną akcję "Wyślij wiadomość e-mail o zatwierdzeniu".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Teraz wprowadź wartości określonych właściwości pokazanych i opisanych tutaj. pozostawienie wartości domyślnych wszystkim innym osobom. Aby uzyskać więcej informacji na temat tych właściwości, zobacz Office 365 Outlook connector reference (Informacje o łączniku usługi [Office 365 Outlook)](/connectors/office365/) [lub Outlook.com łącznika usługi](/connectors/outlook/).

   ![Zrzut ekranu przedstawiający właściwości "Wyślij wiadomość e-mail z zatwierdzeniem"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Do** | <*adres e-mail zatwierdzenia*> | Adres e-mail osoby zatwierdzającej. Do celów testowych możesz użyć własnego adresu e-mail. W tym przykładzie użyto fikcyjnego `sophiaowen@fabrikam.com` adresu e-mail. |
   | **Temat** | `Approve member request for test-members-ML` | Opisowy temat wiadomości e-mail |
   | **Opcje użytkownika** | `Approve, Reject` | Upewnij się, że ta właściwość określa opcje odpowiedzi,  które może wybrać osoba zatwierdzająca, które są domyślnie zatwierdź **lub** odrzuć. |
   ||||

   > [!NOTE]
   > Po kliknięciu wewnątrz niektórych pól edycji zostanie wyświetlona lista zawartości dynamicznej, którą można na razie zignorować. Ta lista zawiera dane wyjściowe z poprzednich akcji, które można wybrać jako dane wejściowe dla kolejnych akcji w przepływie pracy.
 
1. Zapisz aplikację logiki.

Następnie dodaj warunek, który sprawdza wybraną odpowiedź osoby zatwierdzającej.

## <a name="check-approval-response"></a>Sprawdzanie odpowiedzi na żądanie zatwierdzenia

1. W obszarze **akcji Wyślij wiadomość e-mail dotyczącej** zatwierdzenia wybierz **pozycję Nowy krok.**

1. W **obszarze Wybierz operację** wybierz pozycję **Wbudowany**. W polu wyszukiwania wprowadź `condition` i wybierz akcję o nazwie **Warunek**.

   ![Zrzut ekranu przedstawiający pole wyszukiwania "Wybierz operację" z wybranymi opcjami "Wbudowane" i "warunek" jako terminem wyszukiwania, podczas gdy zostanie wyświetlona akcja "Warunek".](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Na pasku **tytułu** Warunek wybierz przycisk **wielokropka** (**...**), a następnie wybierz pozycję **Zmień nazwę**. Zmień nazwę warunku na następujący opis: `If request approved`

   ![Zrzut ekranu przedstawiający przycisk wielokropka wybrany z otwartą listą "Ustawienia" i wybranym poleceniem "Zmień nazwę".](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Skompilowanie warunku, który sprawdza, czy osoba zatwierdzająca wybrała opcję **Zatwierdź.**

   1. Po lewej stronie warunku kliknij wewnątrz **pola Wybierz wartość.**

   1. Z wyświetlonej listy zawartości dynamicznej w obszarze **Wyślij wiadomość e-mail z zatwierdzeniem** wybierz **właściwość SelectedOption.**

      ![Zrzut ekranu przedstawiający dynamiczną listę zawartości, na której w sekcji "Wyślij wiadomość e-mail z zatwierdzeniem" zostaną wyświetlone wybrane dane wyjściowe "SelectedOption".](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. W środkowym polu porównania wybierz operator **jest równy.**

   1. Po prawej stronie warunku w **polu Wybierz wartość** wprowadź tekst `Approve` .

      Gdy wszystko będzie gotowe, warunek będzie wyglądać podobnie do tego przykładu:

      ![Zrzut ekranu przedstawiający gotowy warunek dla przykładu zatwierdzonego żądania](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Zapisz aplikację logiki.

Następnie określ akcję, która będzie wykonywana przez aplikację logiki, gdy osoba sprawdzająca zatwierdzi żądanie. 

## <a name="add-member-to-mailchimp-list"></a>Dodawanie elementu członkowskiego do listy MailChimp

Teraz dodaj akcję, która doda zatwierdzony element członkowski do listy adresowej.

1. W gałęzi True **warunku** wybierz pozycję **Dodaj akcję**.

1. W polu **wyszukiwania Wybierz operację** wybierz pozycję **Wszystkie.** W polu wyszukiwania wprowadź `mailchimp` i wybierz akcję o nazwie **Dodaj członka do listy**.

   ![Zrzut ekranu przedstawiający pole "Wybierz operację" z wybranym terminem wyszukiwania "mailchimp" i akcją "Dodaj członka do listy".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Jeśli nie masz jeszcze połączenia z kontem MailChimp, zostanie wyświetlony monit o zalogowanie.

1. W akcji **Dodaj członka do listy** podaj informacje, jak pokazano i opisano tutaj:

   ![Zrzut ekranu przedstawiający informacje o akcji "Dodaj członka do listy".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Identyfikator listy** | Tak | <*nazwa listy adresowej*> | Wybierz nazwę listy adresowej MailChimp. W tym przykładzie użyto wartości `test-members-ML`. |
   | **Adres e-mail** | Tak | <*nowy członek — adres e-mail*> | Na liście zawartości dynamicznej, która zostanie otwarta, w sekcji **Po** przybyciu nowej wiadomości e-mail wybierz pozycję Od, **która** jest wyjściem wyzwalacza i określa adres e-mail nowego członka. |
   | **Stan** | Tak | <*stan subskrypcji członka*> | Wybierz stan subskrypcji do ustawienia dla nowego członka. W tym przykładzie jest wybierana . `subscribed` <p>Aby uzyskać więcej informacji, zobacz [Zarządzanie subskrybentami za pomocą interfejsu API MailChimp](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   Aby uzyskać więcej informacji na temat właściwości akcji Dodaj członka **do** listy, zobacz odwołanie do [łącznika MailChimp](/connectors/mailchimp/).

1. Zapisz aplikację logiki.

Następnie dodaj warunek, który pozwoli sprawdzić, czy nowy element członkowski został pomyślnie dołączony do listy adresowej. Dzięki temu aplikacja logiki może powiadomić Cię, czy ta operacja zakończyła się powodzeniem, czy niepowodzeniem.

## <a name="check-for-success-or-failure"></a>Sprawdzanie pod kątem powodzenia lub niepowodzenia

1. W gałęzi **True** w obszarze akcji **Dodaj członka do listy** wybierz pozycję Dodaj **akcję**.

1. W **obszarze Wybierz operację** wybierz pozycję **Wbudowany**. W polu wyszukiwania wprowadź `condition` i wybierz akcję o nazwie **Warunek**.

1. Zmień nazwę warunku na następujący opis: `If add member succeeded`

1. Utwórz warunek sprawdzający, czy dołączenie zatwierdzonego elementu członkowskiego do listy adresowej zakończyło się powodzeniem lub niepowodzeniem:

   1. Po lewej stronie warunku kliknij wewnątrz **pola Wybierz wartość.** Z wyświetlonej listy zawartości dynamicznej w sekcji Dodaj członka **do listy** wybierz **właściwość** Status.

      Na przykład warunek wygląda podobnie do tego przykładu:

      ![Zrzut ekranu przedstawiający pole "Wybierz wartość" po lewej stronie warunku z wprowadzonym stanem.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. W środkowym polu porównania wybierz operator **jest równy.**

   1. Po prawej stronie warunku w polu **Wybierz wartość** wprowadź ten tekst: `subscribed`

      Gdy wszystko będzie gotowe, warunek będzie wyglądać podobnie do tego przykładu:

      ![Zrzut ekranu przedstawiający ukończony warunek sprawdzania pomyślnej lub zakończonej niepowodzeniem subskrypcji.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Następnie skonfiguruj wiadomości e-mail do wysyłania, gdy dołączenie zatwierdzonego członka do listy adresowej zakończy się powodzeniem lub niepowodzeniem.

## <a name="send-email-if-member-added"></a>Wysyłanie wiadomości e-mail w przypadku dodania elementu członkowskiego

1. W obszarze **warunku Jeśli dodawanie członka zakończyło się pomyślnie** w gałęzi **True** wybierz pozycję **Dodaj akcję**.

   ![Zrzut ekranu przedstawiający gałąź "Prawda" warunku "Jeśli dodawanie członka zakończyło się pomyślnie" z wybraną akcję "Dodaj akcję".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. W polu **wyszukiwania Wybierz operację** wprowadź , a `outlook send email` następnie wybierz akcję o nazwie Wyślij wiadomość **e-mail.**

   ![Zrzut ekranu przedstawiający pole wyszukiwania "Wybierz operację" z wprowadzonym poleceniem "outlook send email" i akcją "Wyślij wiadomość e-mail" wybraną dla powiadomienia.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Zmień nazwę akcji na następujący opis: `Send email on success`

1. W akcji **Wyślij wiadomość e-mail** w przypadku powodzenia podaj informacje, jak pokazano i opisano tutaj:

   ![Zrzut ekranu przedstawiający akcję "Wyślij wiadomość e-mail w przypadku powodzenia" i informacje podane dla wiadomości e-mail z informacją o sukcesie.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Treść** | Tak | <*treść wiadomości e-mail dotyczącej powodzenia*> | Treść wiadomości e-mail z informacją o powodzeniu. W tym samouczku wykonaj następujące kroki: <p>1. Wprowadź ten tekst ze spacją na końcowej stronie: `New member has joined "test-members-ML":` <p>2. Z wyświetlonej listy zawartości dynamicznej wybierz właściwość **Adres e-mail.** <p>**Uwaga:** Jeśli ta właściwość nie jest wyświetlana, obok nagłówka sekcji **Dodawanie** członka do listy wybierz **pozycję Zobacz więcej.** <p>3. W następnym wierszu wprowadź ten tekst ze spacją na końcowej stronie: `Member opt-in status: ` <p>4. Z listy zawartości dynamicznej w obszarze Dodaj członka **do listy** wybierz **właściwość** Status. |
   | **Temat** | Tak | <*powodzenie — temat wiadomości e-mail*> | Temat wiadomości e-mail z informacją o powodzeniu. W tym samouczku wykonaj następujące kroki: <p>1. Wprowadź ten tekst ze spacją na końcowej stronie: `Success! Member added to "test-members-ML": ` <p>2. Z listy zawartości dynamicznej w obszarze Dodaj członka **do listy** wybierz właściwość **Adres e-mail.** |
   | **Do** | Tak | <*Twój adres e-mail*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o powodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. |
   |||||

1. Zapisz aplikację logiki.

## <a name="send-email-if-member-not-added"></a>Wysyłanie wiadomość e-mail w przypadku nie dodania elementu członkowskiego

1. W warunku **Jeśli dodawanie członka zakończyło się pomyślnie** w gałęzi **Fałsz** wybierz **pozycję Dodaj akcję**.

   ![Zrzut ekranu przedstawiający gałąź "Fałsz" warunku "Jeśli dodawanie członka zakończyło się pomyślnie" z wybraną akcję Dodaj akcję.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. W polu **wyszukiwania Wybierz operację** wprowadź `outlook send email` , a następnie wybierz akcję o nazwie **Wyślij wiadomość e-mail.**

   ![Zrzut ekranu przedstawiający pole wyszukiwania "Wybierz operację" z wprowadzonym polem "Wyślij wiadomość e-mail w programie Outlook" i wybraną akcją "Wyślij wiadomość e-mail".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Zmień nazwę akcji na następujący opis: `Send email on failure`

1. Podaj informacje o tej akcji, jak pokazano i opisano tutaj:

   ![Zrzut ekranu przedstawiający akcję "Wyślij wiadomość e-mail w przypadku niepowodzenia" i informacje podane dla wiadomości e-mail z informacją o niepowodzeniu.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Treść** | Tak | <*treść wiadomości e-mail z błędami*> | Treść wiadomości e-mail z informacją o niepowodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`Member might already exist. Check your MailChimp account.` |
   | **Temat** | Tak | <*subject-for-failure-email*> | Temat wiadomości e-mail z informacją o niepowodzeniu. W tym samouczku wykonaj następujące kroki: <p>1. Wprowadź ten tekst ze spacją na końcowej stronie: `Failed, member not added to "test-members-ML": ` <p>2. Z listy zawartości dynamicznej w obszarze Dodaj członka **do listy** wybierz właściwość **Adres e-mail.** |
   | **Do** | Tak | <*Twój adres e-mail*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o niepowodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. |
   |||||

1. Zapisz aplikację logiki. 

Następnie przetestuj aplikację logiki, która powinna wyglądać następująco:

![Zrzut ekranu przedstawiający przykład ukończonego przepływu pracy aplikacji logiki.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

1. Wyślij do siebie wiadomość e-mail z żądaniem dołączenia do listy adresowej. Poczekaj, aż żądanie pojawi się w skrzynce odbiorczej.

1. Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. 

   Jeśli Twoja wiadomość e-mail posiada temat zgodny z filtrem tematu wyzwalacza, aplikacja logiki wyśle wiadomość e-mail z prośbą o zatwierdzenie żądania subskrypcji.

1. W wyświetlonym e-mail z zatwierdzeniem wybierz pozycję **Zatwierdź**.

1. Jeśli adres e-mail subskrybenta nie istnieje na liście adresowej, aplikacja logiki dodaje adres e-mail tej osoby i wysyła do Ciebie wiadomość e-mail podobną do przykładowej:

   ![Zrzut ekranu przedstawiający przykładową wiadomość e-mail dla pomyślnej subskrypcji.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Jeśli aplikacja logiki nie może dodać subskrybenta, otrzymasz wiadomość e-mail podobną do przykładowej:

   ![Zrzut ekranu przedstawiający przykładową wiadomość e-mail dla subskrypcji, która zakończyła się niepowodzeniem.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje. Udało Ci się utworzyć i uruchomić aplikację logiki, która integruje informacje z różnych usług platformy Azure, usług firmy Microsoft i innych aplikacji SaaS.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aplikacja logiki będzie nadal działać do momentu wyłączenia lub usunięcia aplikacji. Gdy przykładowa aplikacja logiki nie jest już potrzebna, usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby.

1. W Azure Portal wyszukiwania aplikacji wprowadź nazwę utworzonej grupy zasobów. W wynikach w obszarze **Grupy zasobów** wybierz grupę zasobów.

   W tym przykładzie utworzono grupę zasobów o nazwie `LA-MailingList-RG` .

   ![Zrzut ekranu przedstawiający pole wyszukiwania platformy Azure z wprowadzonym polem wyszukiwania "la-mailinglist-rg" i wybraną listą **LA-MailingList-RG**.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Jeśli na stronie głównej platformy Azure zostanie pokazana grupa zasobów w obszarze **Ostatnie zasoby,** możesz wybrać tę grupę na stronie głównej.

1. W menu grupy zasobów sprawdź, czy **wybrano pozycję** Przegląd. Na pasku **narzędzi** okienka Przegląd wybierz pozycję **Usuń grupę zasobów.**

   ![Zrzut ekranu przedstawiający okienko "Przegląd" grupy zasobów, na pasku narzędzi okienka jest zaznaczona opcja "Usuń grupę zasobów".](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. W wyświetlonym okienku potwierdzenia wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń.**

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono aplikację logiki, która obsługuje zatwierdzenia żądań listy adresowej. Możesz teraz dowiedzieć się, w jaki sposób utworzyć aplikację logiki, która przetwarza i przechowuje załączniki wiadomości e-mail przez integrowanie usług platformy Azure, takich jak Azure Storage i Azure Functions.

> [!div class="nextstepaction"]
> [Przetwarzanie załączników wiadomości e-mail](../logic-apps/tutorial-process-email-attachments-workflow.md)
