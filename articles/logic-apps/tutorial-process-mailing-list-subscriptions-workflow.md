---
title: Tworzenie zautomatyzowanych przepływów pracy opartych na zatwierdzaniu
description: Samouczek — Tworzenie zautomatyzowanego przepływu pracy opartego na zatwierdzaniu, który przetwarza subskrypcje listy adresowej za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/07/2020
ms.openlocfilehash: 1690b8d143b86e5caa691f5f8f479f715f57f0c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054656"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Samouczek: tworzenie automatycznych przepływów pracy opartych na zatwierdzaniu przy użyciu Azure Logic Apps

W tym samouczku przedstawiono sposób tworzenia przykładowej [aplikacji logiki](../logic-apps/logic-apps-overview.md) , która automatyzuje przepływ pracy oparty na zatwierdzaniu. Ta przykładowa aplikacja logiki przetwarza żądania subskrypcji dla listy adresowej zarządzanej przez usługę [MailChimp](https://mailchimp.com/) . Ta aplikacja logiki obejmuje różne kroki, które rozpoczynają się od monitorowania konta e-mail dla żądań, wysyłają te żądania do zatwierdzenia, sprawdzają, czy żądanie jest akceptowane, dodaje zatwierdzone elementy członkowskie do listy adresowej i potwierdza, czy nowe składowe zostały dodane do listy.

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

* Konto MailChimp, w którym wcześniej utworzono listę o nazwie "test-Members-ML", w której aplikacja logiki może dodawać adresy e-mail dla zatwierdzonych członków. Jeśli nie masz konta, [zarejestruj się w celu uzyskania bezpłatnego konta](https://login.mailchimp.com/signup/), a następnie Dowiedz się, [jak utworzyć listę MailChimp](https://us17.admin.mailchimp.com/lists/#).

* Konto e-mail od dostawcy poczty e-mail obsługiwanego przez Logic Apps, takie jak Office 365 Outlook, Outlook.com lub gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](/connectors/). Ten przewodnik Szybki Start korzysta z pakietu Office 365 Outlook z kontem służbowym. Jeśli używasz innego konta e-mail, ogólne kroki pozostają takie same, ale interfejs użytkownika może się nieco różnić.

* Konto e-mail w pakiecie Office 365 Outlook lub Outlook.com, które obsługuje przepływy pracy zatwierdzania. W tym samouczku jest używana usługa Office 365 Outlook. Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

* Jeśli aplikacja logiki musi komunikować się przez zaporę, która ogranicza ruch do określonych adresów IP, zapora musi zezwolić na dostęp *zarówno* do [przychodzącego](logic-apps-limits-and-config.md#inbound) , jak i [wychodzącego](logic-apps-limits-and-config.md#outbound) adresu IP używanego przez usługę Logic Apps lub środowisko uruchomieniowe w regionie platformy Azure, w którym znajduje się aplikacja logiki. Jeśli aplikacja logiki używa również łączników [zarządzanych](../connectors/apis-list.md#managed-api-connectors), takich jak łącznik usługi Office 365 Outlook lub łącznik SQL, lub używa [łączników niestandardowych](/connectors/custom-connectors/), zapora musi również zezwolić na dostęp *wszystkich* [wychodzących adresów IP łącznika zarządzanego](logic-apps-limits-and-config.md#outbound) w regionie platformy Azure aplikacji logiki.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure. Na stronie głównej platformy Azure wybierz pozycję **Utwórz zasób**.

1. W menu portalu Azure Marketplace wybierz pozycję **Integration**  >  **Logic App**.

   ![Zrzut ekranu pokazujący menu portalu Azure Marketplace z wybraną opcją "Integracja" i "aplikacja logiki".](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. W okienku **aplikacja logiki** Podaj tutaj informacje o aplikacji logiki, którą chcesz utworzyć.

   ![Zrzut ekranu pokazujący okienko tworzenia aplikacji logiki oraz informacje, które mają zostać podane dla nowej aplikacji logiki.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Subskrypcja** | <*Azure — nazwa subskrypcji*> | Nazwa subskrypcji platformy Azure. W tym przykładzie użyto wartości `Pay-As-You-Go`. |
   | **Grupa zasobów** | LA-MailingList-RG | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md), która jest używana do organizowania powiązanych zasobów. Ten przykład tworzy nową grupę zasobów o nazwie `LA-MailingList-RG` . |
   | **Nazwa** | LA-MailingList | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( `(` , `)` ) i kropki ( `.` ). W tym przykładzie użyto wartości `LA-MailingList`. |
   | **Lokalizacja** | Zachodnie stany USA | Region, w którym są przechowywane informacje o aplikacji logiki. W tym przykładzie użyto wartości `West US`. |
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**. Po sprawdzeniu przez platformę Azure informacji o aplikacji logiki wybierz pozycję **Utwórz**.

1. Po wdrożeniu aplikacji przez platformę Azure wybierz pozycję **Przejdź do zasobu**.

   Na platformie Azure zostanie otwarte okienko wybór szablonów Logic Apps, w którym wyświetlane są filmy wideo z wprowadzeniem, często używane wyzwalacze i wzorce szablonów aplikacji logiki.

1. Przewiń w dół sekcje wideo i typowe wyzwalacze do sekcji **Szablony** , a następnie wybierz pozycję **pusta aplikacja logiki**.

   ![Zrzut ekranu, który pokazuje okienko Logic Apps wyboru szablonów z wybraną opcją "pusta aplikacja logiki".](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Następnie Dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) programu Outlook, który nasłuchuje przychodzących wiadomości e-mail z żądaniami subskrypcji. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest uruchamiany po wystąpieniu określonego zdarzenia lub gdy nowe dane spełniają określony warunek. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Dodawanie wyzwalacza w celu monitorowania wiadomości e-mail

1. W polu wyszukiwania programu Logic Apps Designer wprowadź `when email arrives` i wybierz wyzwalacz o nazwie **po nadejściu nowej wiadomości e-mail**.

   * W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**.
   * W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**.

   Ten przykład kontynuuje, wybierając pozycję Office 365 Outlook.

   ![Zrzut ekranu przedstawiający pole wyszukiwania programu Logic Apps Designer zawierające termin wyszukiwania "po nadejściu wiadomości e-mail" i "po nadejściu nowej wiadomości e-mail".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Jeśli nie masz jeszcze połączenia, zaloguj się i Uwierzytelnij dostęp do konta e-mail po wyświetleniu monitu.

   Azure Logic Apps tworzy połączenie z kontem e-mail.

1. W wyzwalaczu Podaj kryteria sprawdzania nowej wiadomości e-mail.

   1. Określ folder do sprawdzania wiadomości e-mail i Zachowaj inne właściwości ustawione na ich wartości domyślne.

      ![Zrzut ekranu przedstawiający projektanta z akcją "po nadejściu nowej wiadomości e-mail" i "folder" ustawionym na "Skrzynka odbiorcza".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Dodaj właściwość **filtru podmiotu** wyzwalacza, aby można było filtrować wiadomości e-mail na podstawie wiersza tematu. Otwórz listę **Dodaj nowy parametr** , a następnie wybierz opcję **Filtr podmiotu**.

      ![Zrzut ekranu pokazujący otwartą listę "Dodaj nowy parametr" z wybraną opcją "filtr tematu".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Aby uzyskać więcej informacji na temat właściwości tego wyzwalacza, zobacz [informacje dotyczące łącznika programu Outlook pakietu Office 365](/connectors/office365/) lub [informacje dotyczące łącznika Outlook.com](/connectors/outlook/).

   1. Po wyświetleniu właściwości w wyzwalaczu wprowadź następujący tekst: `subscribe-test-members-ML`

      ![Zrzut ekranu przedstawiający Właściwość "filtr tematu" z tekstem "Subskrybuj-test-Members-ML" wprowadzonym.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Aby ukryć szczegóły wyzwalacza teraz, Zwiń kształt, klikając wewnątrz paska tytułu kształtu.

   ![Zrzut ekranu pokazujący zwinięty kształt wyzwalacza.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem przychodzących wiadomości e-mail. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="send-approval-email"></a>Wysyłanie wiadomości e-mail dotyczącej zatwierdzenia

Teraz, gdy wyzwalacz jest gotowy, dodaj [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts) wysyłającą wiadomość e-mail w celu zatwierdzenia lub odrzucenia żądania.

1. W projektancie Logic Apps w obszarze **po nadejściu nowej wiadomości e-mail** wybierz kolejno pozycje **nowy krok**.

1. W obszarze **Wybierz operację** w polu wyszukiwania wprowadź `send approval` i wybierz akcję o nazwie **Wyślij wiadomość e-mail z zatwierdzeniem**.

   ![Zrzut ekranu przedstawiający listę "Wybieranie operacji" przefiltrowaną przez akcje "zatwierdzenie" i wybraną akcję "Wyślij wiadomość e-mail dotyczącą zatwierdzenia".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Teraz wprowadź wartości dla określonych właściwości wyświetlanych i opisanych poniżej. pozostawienie wszystkich pozostałych wartości domyślnych. Aby uzyskać więcej informacji o tych właściwościach, zobacz [informacje dotyczące łącznika programu Outlook pakietu Office 365](/connectors/office365/) lub [informacje dotyczące łącznika Outlook.com](/connectors/outlook/).

   ![Zrzut ekranu przedstawiający właściwości "Wyślij wiadomość e-mail dotyczącą zatwierdzenia"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Do** | <*zatwierdzenie — adres e-mail*> | Adres e-mail osoby zatwierdzającej. Do celów testowych możesz użyć własnego adresu e-mail. Ten przykład używa fikcyjnego `sophiaowen@fabrikam.com` adresu e-mail. |
   | **Temat** | `Approve member request for test-members-ML` | Opisowy temat wiadomości e-mail |
   | **Opcje użytkownika** | `Approve, Reject` | Upewnij się, że ta właściwość określa opcje odpowiedzi, które osoba zatwierdzająca może wybrać, które są domyślnie **zatwierdzane** lub **odrzucane** . |
   ||||

   > [!NOTE]
   > Po kliknięciu wewnątrz niektórych pól edycji zostanie wyświetlona lista zawartość dynamiczna, którą można teraz zignorować. Ta lista zawiera dane wyjściowe z poprzednich akcji, które są dostępne, aby wybrać jako dane wejściowe do kolejnych akcji w przepływie pracy.
 
1. Zapisz aplikację logiki.

Następnie Dodaj warunek, który sprawdza, czy wybrana odpowiedź osoby zatwierdzającej.

## <a name="check-approval-response"></a>Sprawdzanie odpowiedzi na żądanie zatwierdzenia

1. W obszarze Akcja **Wyślij wiadomość e-mail** dotyczącą zatwierdzenia wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz operację** wybierz pozycję **wbudowane**. W polu wyszukiwania wpisz `condition` , a następnie wybierz akcję o nazwie **warunek**.

   ![Zrzut ekranu pokazujący pole wyszukiwania "Wybierz operację" z opcją "wbudowane" wybrane i "warunek" jako termin wyszukiwania, podczas gdy zostanie wyświetlona akcja "warunek".](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Na pasku tytułu **warunku** wybierz przycisk **wielokropka** (**...**), a następnie wybierz polecenie **Zmień nazwę**. Zmień nazwę warunku na następujący opis: `If request approved`

   ![Zrzut ekranu pokazujący przycisk wielokropka wybrany z otwartą listą "Ustawienia" i wybraną polecenie "Zmień nazwę".](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Kompiluj warunek, który sprawdza, czy osoba zatwierdzająca zabrała **zatwierdzenie**.

   1. Na lewej stronie warunku kliknij wewnątrz pola **Wybierz wartość** .

   1. Z wyświetlonej listy zawartość dynamiczna w obszarze **wysyłanie wiadomości e-mail z potwierdzeniem** wybierz właściwość **SelectedOption** .

      ![Zrzut ekranu pokazujący listę zawartości dynamicznej, w której w sekcji "wysyłanie wiadomości e-mail dotyczącej zatwierdzenia" pojawia się opcja "SelectedOption".](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. W środkowym polu porównania wybierz operator **is równe** .

   1. Po prawej stronie warunku w polu **Wybierz wartość** wprowadź tekst `Approve` .

      Gdy wszystko będzie gotowe, warunek będzie wyglądać podobnie do tego przykładu:

      ![Zrzut ekranu pokazujący ukończony warunek dla zaakceptowanego przykładowego żądania](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Zapisz aplikację logiki.

Następnie określ akcję, która będzie wykonywana przez aplikację logiki, gdy osoba sprawdzająca zatwierdzi żądanie. 

## <a name="add-member-to-mailchimp-list"></a>Dodawanie elementu członkowskiego do listy MailChimp

Teraz Dodaj akcję, która powoduje dodanie zatwierdzonego elementu członkowskiego do listy adresowej.

1. W gałęzi **prawdy** warunku wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania **Wybierz operację** zaznacz **wszystko**. W polu wyszukiwania wpisz `mailchimp` , a następnie wybierz akcję o nazwie **Dodaj członka do listy**.

   ![Zrzut ekranu przedstawiający okno "Wybieranie operacji" z wyszukiwanym terminem "MailChimp" oraz wybraną akcję "Dodaj członka do listy".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Jeśli nie masz jeszcze połączenia z kontem usługi MailChimp, zostanie wyświetlony monit o zalogowanie się.

1. W akcji **Dodaj członka do listy** podaj informacje, jak pokazano i opisano tutaj:

   ![Zrzut ekranu pokazujący akcję "Dodawanie elementu członkowskiego do listy".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Identyfikator listy** | Tak | <*Nazwa listy adresowej*> | Wybierz nazwę dla listy adresowej MailChimp. W tym przykładzie użyto wartości `test-members-ML`. |
   | **Adres e-mail** | Tak | <*New-member-email-address*> | Z listy zawartość dynamiczna, która zostanie otwarta, w sekcji **po nadejściu nowej wiadomości e-mail** wybierz pozycję **z**, która jest wyprowadzana z wyzwalacza, a następnie określ adres e-mail dla nowego elementu członkowskiego. |
   | **Stan** | Tak | <*element członkowski-subskrypcja — stan*> | Wybierz stan subskrypcji ustawiony dla nowego elementu członkowskiego. Ten przykład wybiera `subscribed` . <p>Aby uzyskać więcej informacji, zobacz [Zarządzanie subskrybentami za pomocą interfejsu API MailChimp](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   Aby uzyskać więcej informacji na temat właściwości akcji **Dodawanie elementu członkowskiego do listy** , zobacz [informacje dotyczące łącznika MailChimp](/connectors/mailchimp/).

1. Zapisz aplikację logiki.

Następnie dodaj warunek, który pozwoli sprawdzić, czy nowy element członkowski został pomyślnie dołączony do listy adresowej. Dzięki temu aplikacja logiki może powiadomić użytkownika o tym, czy ta operacja zakończyła się powodzeniem, czy niepowodzeniem.

## <a name="check-for-success-or-failure"></a>Sprawdzanie pod kątem powodzenia lub niepowodzenia

1. W gałęzi **prawdy** w obszarze **Dodawanie elementu członkowskiego do listy** wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz operację** wybierz pozycję **wbudowane**. W polu wyszukiwania wpisz `condition` , a następnie wybierz akcję o nazwie **warunek**.

1. Zmień nazwę warunku na następujący opis: `If add member succeeded`

1. Utwórz warunek sprawdzający, czy dołączenie zatwierdzonego elementu członkowskiego do listy adresowej zakończyło się powodzeniem lub niepowodzeniem:

   1. Na lewej stronie warunku kliknij wewnątrz pola **Wybierz wartość** . Z wyświetlonej listy zawartość dynamiczna w sekcji **Dodawanie elementu członkowskiego do listy** wybierz właściwość **stan** .

      Na przykład Twój warunek wygląda podobnie do tego przykładu:

      ![Zrzut ekranu przedstawiający lewą stronę warunku "Wybierz wartość" z wprowadzonym stanem "status".](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. W środkowym polu porównania wybierz operator **is równe** .

   1. Po prawej stronie warunku w polu **Wybierz wartość** wprowadź następujący tekst: `subscribed`

      Gdy wszystko będzie gotowe, warunek będzie wyglądać podobnie do tego przykładu:

      ![Zrzut ekranu pokazujący ukończony warunek sprawdzania powodzenia lub nieudanej subskrypcji.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Następnie skonfiguruj wiadomości e-mail do wysłania, gdy zatwierdzony element członkowski powiedzie się lub zakończy się niepowodzeniem w przyłączeniu do listy adresowej.

## <a name="send-email-if-member-added"></a>Wysyłanie wiadomości e-mail w przypadku dodania elementu członkowskiego

1. W obszarze w **przypadku pomyślnego dodania elementu członkowskiego** w gałęzi **prawdy** wybierz pozycję **Dodaj akcję**.

   ![Zrzut ekranu, na którym jest wyświetlany element "true" elementu "If Add member" z opcją "Dodaj akcję".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. W polu wyszukiwania **Wybierz operację** wprowadź `outlook send email` i wybierz akcję o nazwie **Wyślij wiadomość e-mail**.

   ![Zrzut ekranu pokazujący pole wyszukiwania "Wybierz operację" z wprowadzoną opcją "Wyślij wiadomość e-mail programu Outlook" oraz akcję "Wyślij wiadomość e-mail" wybraną dla powiadomienia.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Zmień nazwę akcji na następujący opis: `Send email on success`

1. W akcji **Wyślij wiadomość e-mail dotyczącą sukcesu** podaj informacje, jak pokazano poniżej:

   ![Zrzut ekranu pokazujący akcję "Wyślij wiadomość e-mail na sukces" i informacje podane dla wiadomości e-mail z informacją o powodzeniu.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Treść** | Tak | <*powodzenie — adres e-mail — treść*> | Treść wiadomości e-mail z informacją o powodzeniu. W tym samouczku wykonaj następujące kroki: <p>1. Wprowadź ten tekst z końcowym miejscem: `New member has joined "test-members-ML":` <p>2. z wyświetlonej listy zawartości dynamicznej wybierz właściwość **adres e-mail** . <p>**Uwaga**: Jeśli ta właściwość nie zostanie wyświetlona obok nagłówka sekcji **Dodaj członka do listy** , wybierz pozycję **Zobacz więcej**. <p>3. w następnym wierszu wprowadź ten tekst z końcowym miejscem: `Member opt-in status: ` <p>4. z listy zawartości dynamicznej w obszarze **Dodawanie elementu członkowskiego do listy** wybierz właściwość **stan** . |
   | **Temat** | Tak | <*powodzenie — adres e-mail — temat*> | Temat wiadomości e-mail z informacją o powodzeniu. W tym samouczku wykonaj następujące kroki: <p>1. Wprowadź ten tekst z końcowym miejscem: `Success! Member added to "test-members-ML": ` <p>2. z listy zawartości dynamicznej w obszarze **Dodawanie elementu członkowskiego do listy** wybierz właściwość **adres e-mail** . |
   | **Do** | Tak | <*adres e-mail użytkownika*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o powodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. |
   |||||

1. Zapisz aplikację logiki.

## <a name="send-email-if-member-not-added"></a>Wysyłanie wiadomość e-mail w przypadku nie dodania elementu członkowskiego

1. W obszarze w **przypadku powodzenia Dodawanie elementu członkowskiego** w gałęzi **Fałsz** wybierz pozycję **Dodaj akcję**.

   ![Zrzut ekranu pokazujący, że "Jeśli dodano element członkowski pomyślnie", zostanie wyświetlona gałąź "fałsz" z opcją "Dodaj akcję".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. W polu wyszukiwania **Wybierz operację** wprowadź `outlook send email` i wybierz akcję o nazwie **Wyślij wiadomość e-mail**.

   ![Zrzut ekranu pokazujący pole wyszukiwania "Wybierz operację" z wprowadzoną opcją "Wyślij wiadomość e-mail w programie Outlook" oraz wybraną akcję "Wyślij wiadomość e-mail".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Zmień nazwę akcji na następujący opis: `Send email on failure`

1. Podaj informacje dotyczące tej akcji, jak pokazano i opisano tutaj:

   ![Zrzut ekranu pokazujący akcję "Wyślij wiadomość e-mail w przypadku niepowodzenia" i informacje podane dla wiadomości e-mail z informacją o niepowodzeniu.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Treść** | Tak | <*treść — do-niepowodzenie — poczta e-mail*> | Treść wiadomości e-mail z informacją o niepowodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`Member might already exist. Check your MailChimp account.` |
   | **Temat** | Tak | <*podmiot — dla niepowodzenia — poczta e-mail*> | Temat wiadomości e-mail z informacją o niepowodzeniu. W tym samouczku wykonaj następujące kroki: <p>1. Wprowadź ten tekst z końcowym miejscem: `Failed, member not added to "test-members-ML": ` <p>2. z listy zawartości dynamicznej w obszarze **Dodawanie elementu członkowskiego do listy** wybierz właściwość **adres e-mail** . |
   | **Do** | Tak | <*adres e-mail użytkownika*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o niepowodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. |
   |||||

1. Zapisz aplikację logiki. 

Następnie przetestuj aplikację logiki, która powinna wyglądać następująco:

![Zrzut ekranu pokazujący przykład gotowego przepływu pracy aplikacji logiki.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

1. Wyślij do siebie wiadomość e-mail z żądaniem dołączenia do listy adresowej. Poczekaj, aż żądanie pojawi się w skrzynce odbiorczej.

1. Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. 

   Jeśli Twoja wiadomość e-mail posiada temat zgodny z filtrem tematu wyzwalacza, aplikacja logiki wyśle wiadomość e-mail z prośbą o zatwierdzenie żądania subskrypcji.

1. Na otrzymanej wiadomości e-mail dotyczącej zatwierdzenia wybierz pozycję **Zatwierdź**.

1. Jeśli adres e-mail subskrybenta nie istnieje na liście adresowej, aplikacja logiki dodaje adres e-mail tej osoby i wysyła do Ciebie wiadomość e-mail podobną do przykładowej:

   ![Zrzut ekranu pokazujący przykładową wiadomość e-mail dotyczącą pomyślnej subskrypcji.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Jeśli aplikacja logiki nie może dodać subskrybenta, otrzymasz wiadomość e-mail podobną do przykładowej:

   ![Zrzut ekranu pokazujący przykładową wiadomość e-mail dotyczącą nieudanej subskrypcji.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje. Udało Ci się utworzyć i uruchomić aplikację logiki, która integruje informacje z różnych usług platformy Azure, usług firmy Microsoft i innych aplikacji SaaS.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aplikacja logiki kontynuuje działanie do momentu wyłączenia lub usunięcia aplikacji. Jeśli przykładowa aplikacja logiki nie jest już potrzebna, Usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby.

1. W polu wyszukiwania Azure Portal wprowadź nazwę utworzonej grupy zasobów. Z wyników w obszarze **grupy zasobów** wybierz grupę zasobów.

   Ten przykład utworzył grupę zasobów o nazwie `LA-MailingList-RG` .

   ![Zrzut ekranu pokazujący pole wyszukiwania platformy Azure z zaznaczoną pozycją "La-mailinglist-RG" i * * LA-MailingList-RG * *.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Jeśli na stronie głównej platformy Azure zostanie wyświetlona Grupa zasobów w obszarze **ostatnie zasoby**, możesz wybrać grupę ze strony głównej.

1. W menu Grupa zasobów Sprawdź, czy jest zaznaczona wartość **Przegląd** . Na pasku narzędzi okienka **Przegląd** wybierz pozycję **Usuń grupę zasobów**.

   ![Zrzut ekranu przedstawiający okienko "przegląd" grupy zasobów i na pasku narzędzi okienka jest zaznaczona wartość "Usuń grupę zasobów".](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. W wyświetlonym okienku potwierdzenia wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz aplikację logiki, która obsługuje zatwierdzenia dla żądań listy adresowej. Możesz teraz dowiedzieć się, w jaki sposób utworzyć aplikację logiki, która przetwarza i przechowuje załączniki wiadomości e-mail przez integrowanie usług platformy Azure, takich jak Azure Storage i Azure Functions.

> [!div class="nextstepaction"]
> [Przetwarzanie załączników wiadomości e-mail](../logic-apps/tutorial-process-email-attachments-workflow.md)
